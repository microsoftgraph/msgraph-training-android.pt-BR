<!-- markdownlint-disable MD002 MD041 -->

Neste exercício, você estenderá o aplicativo do exercício anterior para oferecer suporte à autenticação com o Azure AD. Isso é necessário para obter o token de acesso OAuth necessário para chamar o Microsoft Graph. Para fazer isso, você integrará a [biblioteca de autenticação da Microsoft (MSAL) para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) no aplicativo.

1. Clique com o botão direito do mouse na pasta **app/res/Values** e selecione **novo**e, em seguida, **valores recurso arquivo**.

1. Nomeie o arquivo `oauth_strings` e selecione **OK**.

1. Adicione os seguintes valores ao `resources` elemento.

    ```xml
    <string name="oauth_app_id">YOUR_APP_ID_HERE</string>
    <string name="oauth_redirect_uri">msalYOUR_APP_ID_HERE</string>
    <string-array name="oauth_scopes">
        <item>User.Read</item>
        <item>Calendars.Read</item>
    </string-array>
    ```

    Substitua `YOUR_APP_ID_HERE` pela ID do aplicativo do registro do aplicativo.

> [!IMPORTANT]
> Se você estiver usando o controle de origem como o Git, agora seria uma boa hora para excluir `oauth_strings.xml` o arquivo do controle de origem para evitar vazar inadvertidamente sua ID de aplicativo.

## <a name="implement-sign-in"></a>Implementar logon

Nesta seção, você atualizará o manifesto para permitir que o MSAL use um navegador para autenticar o usuário, registrar o URI de redirecionamento como manipulado pelo aplicativo, criar uma classe auxiliar de autenticação e atualizar o aplicativo para entrar e sair.

1. Expanda a pasta **aplicativos/manifestos** e abra **AndroidManifest. xml**. Adicione os seguintes elementos acima do `application` elemento.

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

    > [!NOTE]
    > Essas permissões são necessárias para que a biblioteca do MSAL autentique o usuário.

1. Adicione o elemento a seguir dentro `application` do elemento.

    ```xml
    <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />

            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />

            <data
                android:host="auth"
                android:scheme="@string/oauth_redirect_uri" />
        </intent-filter>
    </activity>
    ```

1. Clique com o botão direito do mouse na pasta **app/Java/com. example. graphtutorial** e selecione **nova**e, em seguida, **classe Java**. Nomeie a classe `AuthenticationHelper` e selecione **OK**.

1. Abra o novo arquivo e substitua seu conteúdo pelo seguinte.

    ```java
    package com.example.graphtutorial;

    import android.app.Activity;
    import android.content.Context;
    import android.content.Intent;

    import com.microsoft.identity.client.AuthenticationCallback;
    import com.microsoft.identity.client.IAccount;
    import com.microsoft.identity.client.PublicClientApplication;

    // Singleton class - the app only needs a single instance
    // of PublicClientApplication
    public class AuthenticationHelper {
        private static AuthenticationHelper INSTANCE = null;
        private PublicClientApplication mPCA = null;
        private String[] mScopes;

        private AuthenticationHelper(Context ctx) {
            String appId = ctx.getResources().getString(R.string.oauth_app_id);
            mScopes = ctx.getResources().getStringArray(R.array.oauth_scopes);
            mPCA = new PublicClientApplication(ctx, appId);
        }

        public static synchronized AuthenticationHelper getInstance(Context ctx) {
            if (INSTANCE == null) {
                INSTANCE = new AuthenticationHelper(ctx);
            }

            return INSTANCE;
        }

        // Version called from fragments. Does not create an
        // instance if one doesn't exist
        public static synchronized AuthenticationHelper getInstance() {
            if (INSTANCE == null) {
                throw new IllegalStateException(
                        "AuthenticationHelper has not been initialized from MainActivity");
            }

            return INSTANCE;
        }

        public boolean hasAccount() {
            return !mPCA.getAccounts().isEmpty();
        }

        public void handleRedirect(int requestCode, int resultCode, Intent data) {
            mPCA.handleInteractiveRequestRedirect(requestCode, resultCode, data);
        }

        public void acquireTokenInteractively(Activity activity, AuthenticationCallback callback) {
            mPCA.acquireToken(activity, mScopes, callback);
        }

        public void acquireTokenSilently(AuthenticationCallback callback) {
            mPCA.acquireTokenSilentAsync(mScopes, mPCA.getAccounts().get(0), callback);
        }

        public void signOut() {
            for (IAccount account : mPCA.getAccounts()) {
                mPCA.removeAccount(account);
            }
        }
    }
    ```

1. Abra o **MainActivity** e adicione as `import` instruções a seguir.

    ```java
    import android.content.Intent;
    import android.support.annotation.Nullable;
    import android.util.Log;

    import com.microsoft.identity.client.AuthenticationCallback;
    import com.microsoft.identity.client.AuthenticationResult;
    import com.microsoft.identity.client.exception.MsalClientException;
    import com.microsoft.identity.client.exception.MsalException;
    import com.microsoft.identity.client.exception.MsalServiceException;
    import com.microsoft.identity.client.exception.MsalUiRequiredException;
    ```

1. Adicione a propriedade do membro a seguir `MainActivity` à classe.

    ```java
    private AuthenticationHelper mAuthHelper = null;
    ```

1. Adicione o seguinte ao final da `onCreate` função.

    ```java
    // Get the authentication helper
    mAuthHelper = AuthenticationHelper.getInstance(getApplicationContext());
    ```

1. Adicione uma substituição para `onActivityResult` lidar com as respostas de autenticação.

    ```java
    @Override
    protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
        super.onActivityResult(requestCode, resultCode, data);

        mAuthHelper.handleRedirect(requestCode, resultCode, data);
    }
    ```

1. Adicione as seguintes funções à `MainActivity` classe.

    ```java
    // Silently sign in - used if there is already a
    // user account in the MSAL cache
    private void doSilentSignIn() {
        mAuthHelper.acquireTokenSilently(getAuthCallback());
    }

    // Prompt the user to sign in
    private void doInteractiveSignIn() {
        mAuthHelper.acquireTokenInteractively(this, getAuthCallback());
    }

    // Handles the authentication result
    public AuthenticationCallback getAuthCallback() {
        return new AuthenticationCallback() {

            @Override
            public void onSuccess(AuthenticationResult authenticationResult) {
                // Log the token for debug purposes
                String accessToken = authenticationResult.getAccessToken();
                Log.d("AUTH", String.format("Access token: %s", accessToken));
                hideProgressBar();

                setSignedInState(true);
                openHomeFragment(mUserName);
            }

            @Override
            public void onError(MsalException exception) {
                // Check the type of exception and handle appropriately
                if (exception instanceof MsalUiRequiredException) {
                    Log.d("AUTH", "Interactive login required");
                    doInteractiveSignIn();

                } else if (exception instanceof MsalClientException) {
                    // Exception inside MSAL, more info inside MsalError.java
                    Log.e("AUTH", "Client error authenticating", exception);
                } else if (exception instanceof MsalServiceException) {
                    // Exception when communicating with the auth server, likely config issue
                    Log.e("AUTH", "Service error authenticating", exception);
                }
                hideProgressBar();
            }

            @Override
            public void onCancel() {
                // User canceled the authentication
                Log.d("AUTH", "Authentication canceled");
                hideProgressBar();
            }
        };
    }
    ```

1. Substitua o existente `signIn` e `signOut` as funções pelo seguinte.

    ```java
    private void signIn() {
        showProgressBar();
        if (mAuthHelper.hasAccount()) {
            doSilentSignIn();
        } else {
            doInteractiveSignIn();
        }
    }

    private void signOut() {
        mAuthHelper.signOut();

        setSignedInState(false);
        openHomeFragment(mUserName);
    }
    ```

    > [!NOTE]
    > Observe que o `signIn` método primeiro verifica se há uma conta de usuário já no cache do MSAL. Se houver, ele tentará atualizar seus tokens de forma silenciosa, evitando ter que avisar o usuário toda vez que iniciar o aplicativo.

1. Salve suas alterações e execute o aplicativo.

1. Quando você toca no item de menu **entrar** , um navegador é aberto na página de logon do Azure AD. Entrar com sua conta.

Depois que o aplicativo reiniciar, você verá um token de acesso impresso no log de depuração no Android Studio.

![Uma captura de tela da janela do logcat no Android Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a>Obter detalhes do usuário

Nesta seção, você criará uma classe auxiliar para manter todas as chamadas para o Microsoft Graph e atualizar a `MainActivity` classe para usar essa nova classe para obter o usuário conectado.

1. Clique com o botão direito do mouse na pasta **app/Java/com. example. graphtutorial** e selecione **nova**e, em seguida, **classe Java**.

1. Nomeie a classe `GraphHelper` e selecione **OK**.

1. Abra o novo arquivo e substitua seu conteúdo pelo seguinte.

    ```java
    package com.example.graphtutorial;

    import com.microsoft.graph.authentication.IAuthenticationProvider;
    import com.microsoft.graph.concurrency.ICallback;
    import com.microsoft.graph.http.IHttpRequest;
    import com.microsoft.graph.models.extensions.IGraphServiceClient;
    import com.microsoft.graph.models.extensions.User;
    import com.microsoft.graph.requests.extensions.GraphServiceClient;

    // Singleton class - the app only needs a single instance
    // of the Graph client
    public class GraphHelper implements IAuthenticationProvider {
        private static GraphHelper INSTANCE = null;
        private IGraphServiceClient mClient = null;
        private String mAccessToken = null;

        private GraphHelper() {
            mClient = GraphServiceClient.builder()
                    .authenticationProvider(this).buildClient();
        }

        public static synchronized GraphHelper getInstance() {
            if (INSTANCE == null) {
                INSTANCE = new GraphHelper();
            }

            return INSTANCE;
        }

        // Part of the Graph IAuthenticationProvider interface
        // This method is called before sending the HTTP request
        @Override
        public void authenticateRequest(IHttpRequest request) {
            // Add the access token in the Authorization header
            request.addHeader("Authorization", "Bearer " + mAccessToken);
        }

        public void getUser(String accessToken, ICallback<User> callback) {
            mAccessToken = accessToken;

            // GET /me (logged in user)
            mClient.me().buildRequest().get(callback);
        }
    }
    ```

    > [!NOTE]
    > Considere o que esse código faz.
    >
    > - Ele implementa a `IAuthenticationProvider` interface para inserir o token de acesso no `Authorization` cabeçalho das solicitações HTTP de saída.
    > - Ele expõe uma `getUser` função para obter as informações do usuário conectado do ponto de extremidade `/me` do gráfico.

1. Adicione as seguintes `import` instruções à parte superior do arquivo **MainActivity** .

    ```java
    import com.microsoft.graph.concurrency.ICallback;
    import com.microsoft.graph.core.ClientException;
    import com.microsoft.graph.models.extensions.IGraphServiceClient;
    import com.microsoft.graph.models.extensions.User;
    ```

1. Adicione a função a seguir à `MainActivity` classe para gerar um `ICallback` para a chamada de gráfico.

    ```java
    private ICallback<User> getUserCallback() {
        return new ICallback<User>() {
            @Override
            public void success(User user) {
                Log.d("AUTH", "User: " + user.displayName);

                mUserName = user.displayName;
                mUserEmail = user.mail == null ? user.userPrincipalName : user.mail;

                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        hideProgressBar();

                        setSignedInState(true);
                        openHomeFragment(mUserName);
                    }
                });

            }

            @Override
            public void failure(ClientException ex) {
                Log.e("AUTH", "Error getting /me", ex);
                mUserName = "ERROR";
                mUserEmail = "ERROR";

                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        hideProgressBar();

                        setSignedInState(true);
                        openHomeFragment(mUserName);
                    }
                });
            }
        };
    }
    ```

1. Remova as seguintes linhas que definem o nome de usuário e o email:

    ```java
    // For testing
    mUserName = "Megan Bowen";
    mUserEmail = "meganb@contoso.com";
    ```

1. Substitua a `onSuccess` substituição `AuthenticationCallback` pela seguinte.

    ```java
    @Override
    public void onSuccess(AuthenticationResult authenticationResult) {
        // Log the token for debug purposes
        String accessToken = authenticationResult.getAccessToken();
        Log.d("AUTH", String.format("Access token: %s", accessToken));

        // Get Graph client and get user
        GraphHelper graphHelper = GraphHelper.getInstance();
        graphHelper.getUser(accessToken, getUserCallback());
    }
    ```

Se você salvar suas alterações e executar o aplicativo agora, após entrar, a interface do usuário será atualizada com o nome de exibição e o endereço de email do usuário.
