<!-- markdownlint-disable MD002 MD041 -->

Neste exercício, você estenderá o aplicativo do exercício anterior para dar suporte à autenticação com o Azure AD. Isso é necessário para obter o token de acesso OAuth necessário para chamar o Microsoft Graph. Para fazer isso, você integrará a Biblioteca de Autenticação [da Microsoft (MSAL) para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) ao aplicativo.

1. Clique com o botão direito do mouse na **pasta res** e selecione **Novo,** em seguida, **Diretório de Recursos do Android.**

1. Altere **o tipo de recurso** para e selecione `raw` **OK**.

1. Clique com o botão direito do mouse **na nova** pasta bruta e selecione **Novo,** em **seguida, Arquivo.**

1. Nome do arquivo `msal_config.json` e selecione **OK**.

1. Adicione o seguinte ao **arquivomsal_config.json.**

    :::code language="json" source="../demo/GraphTutorial/msal_config.json.example":::

1. Substitua pela ID do aplicativo do registro do aplicativo e substitua pelo nome `YOUR_APP_ID_HERE` `com.example.graphtutorial` do pacote do projeto.

    > [!IMPORTANT]
    > Se você estiver usando o controle de código-fonte, como git, agora seria um bom momento para excluir o arquivo do controle de código-fonte para evitar o vazamento inadvertida de sua `msal_config.json` ID de aplicativo.

## <a name="implement-sign-in"></a>Implementar a login

Nesta seção, você atualizará o manifesto para permitir que a MSAL use um navegador para autenticar o usuário, registre seu URI de redirecionamento como sendo manipulado pelo aplicativo, crie uma classe auxiliar de autenticação e atualize o aplicativo para entrar e sair.

1. Expanda **a pasta de aplicativos/manifestos** e abra **AndroidManifest.xml**. Adicione os seguintes elementos acima do `application` elemento.

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

    > [!NOTE]
    > Essas permissões são necessárias para a biblioteca MSAL autenticar o usuário.

1. Adicione o seguinte elemento dentro do `application` elemento, substituindo a `YOUR_PACKAGE_NAME_HERE` cadeia de caracteres pelo nome do pacote.

    ```xml
    <!--Intent filter to capture authorization code response from the default browser on the
        device calling back to the app after interactive sign in -->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data
                android:scheme="msauth"
                android:host="YOUR_PACKAGE_NAME_HERE"
                android:path="/callback" />
        </intent-filter>
    </activity>
    ```

1. Clique com o botão direito do **mouse na pasta app/java/com.example.graphtutorial** e selecione **New**, em seguida, **Classe Java.** Altere **o tipo** para **interface.** Nome da interface `IAuthenticationHelperCreatedListener` e selecione **OK**.

1. Abra o novo arquivo e substitua seu conteúdo pelo seguinte.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/IAuthenticationHelperCreatedListener.java" id="ListenerSnippet":::

1. Clique com o botão direito do **mouse na pasta app/java/com.example.graphtutorial** e selecione **New**, em seguida, **Classe Java.** Nome da classe `AuthenticationHelper` e selecione **OK**.

1. Abra o novo arquivo e substitua seu conteúdo pelo seguinte.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/AuthenticationHelper.java" id="AuthHelperSnippet":::

1. Abra **MainActivity** e adicione as instruções a `import` seguir.

    ```java
    import android.util.Log;

    import com.microsoft.identity.client.AuthenticationCallback;
    import com.microsoft.identity.client.IAuthenticationResult;
    import com.microsoft.identity.client.exception.MsalClientException;
    import com.microsoft.identity.client.exception.MsalException;
    import com.microsoft.identity.client.exception.MsalServiceException;
    import com.microsoft.identity.client.exception.MsalUiRequiredException;
    ```

1. Adicione as seguintes propriedades de membro à `MainActivity` classe.

    ```java
    private AuthenticationHelper mAuthHelper = null;
    private boolean mAttemptInteractiveSignIn = false;
    ```

1. Adicione o seguinte código ao final da função `onCreate`.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="InitialLoginSnippet":::

1. Adicione as funções a seguir à `MainActivity` classe.

    ```java
    // Silently sign in - used if there is already a
    // user account in the MSAL cache
    private void doSilentSignIn(boolean shouldAttemptInteractive) {
        mAttemptInteractiveSignIn = shouldAttemptInteractive;
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
            public void onSuccess(IAuthenticationResult authenticationResult) {
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
                    if (mAttemptInteractiveSignIn) {
                        doInteractiveSignIn();
                    }
                } else if (exception instanceof MsalClientException) {
                    if (exception.getErrorCode() == "no_current_account" ||
                        exception.getErrorCode() == "no_account_found") {
                        Log.d("AUTH", "No current account, interactive login required");
                        if (mAttemptInteractiveSignIn) {
                            doInteractiveSignIn();
                        }
                    } else {
                        // Exception inside MSAL, more info inside MsalError.java
                        Log.e("AUTH", "Client error authenticating", exception);
                    }
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

1. Substitua as funções `signIn` e `signOut` existentes pelo seguinte.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="SignInAndOutSnippet":::

    > [!NOTE]
    > Observe que o `signIn` método faz uma login silencioso (via `doSilentSignIn` ). O retorno de chamada para esse método fará uma login interativa se o silencioso falhar. Isso evita ter que solicitar ao usuário sempre que ele iniciar o aplicativo.

1. Salve suas alterações e execute o aplicativo.

1. Quando você toca no item **de** menu Entrar, um navegador é aberto na página de logon do Azure AD. Entrar com sua conta.

Depois que o aplicativo é retomado, você deverá ver um token de acesso impresso no log de depuração no Android Studio.

![Uma captura de tela da janela Logcat no Android Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a>Obter detalhes do usuário

Nesta seção, você criará uma classe auxiliar para manter todas as chamadas para o Microsoft Graph e atualizar a classe para usar essa nova classe para obter o usuário `MainActivity` conectado.

1. Clique com o botão direito do **mouse na pasta app/java/com.example.graphtutorial** e selecione **New**, em seguida, **Classe Java.** Nome da classe `GraphHelper` e selecione **OK**.

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
            mClient.me().buildRequest()
                    .select("displayName,mail,mailboxSettings,userPrincipalName")
                    .get(callback);
        }
    }
    ```

    > [!NOTE]
    > Considere o que esse código faz.
    >
    > - Ele implementa a `IAuthenticationProvider` interface para inserir o token de acesso no cabeçalho em `Authorization` solicitações HTTP de saída.
    > - Ele expõe uma função para obter as informações do usuário conectado do `getUser` ponto de extremidade do `/me` Graph.

1. Adicione as instruções `import` a seguir à parte superior do arquivo **MainActivity.**

    ```java
    import com.microsoft.graph.concurrency.ICallback;
    import com.microsoft.graph.core.ClientException;
    import com.microsoft.graph.models.extensions.User;
    ```

1. Adicione a função a seguir à `MainActivity` classe para gerar um para a chamada do `ICallback` Graph.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="GetUserCallbackSnippet":::

1. Remova as seguintes linhas que configuram o nome de usuário e o email:

    ```java
    // For testing
    mUserName = "Megan Bowen";
    mUserEmail = "meganb@contoso.com";
    ```

1. Substitua a `onSuccess` substituição no que se `AuthenticationCallback` segue.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="OnSuccessSnippet":::

1. Salve suas alterações e execute o aplicativo. Depois que a interface do usuário for atualizada com o nome de exibição e o endereço de email do usuário.
