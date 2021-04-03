<!-- markdownlint-disable MD002 MD041 -->

Neste exercício, você estenderá o aplicativo do exercício anterior para dar suporte à autenticação com o Azure AD. Isso é necessário para obter o token de acesso OAuth necessário para chamar o Microsoft Graph. Para fazer isso, você integrará a Biblioteca de Autenticação [da Microsoft (MSAL) para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) ao aplicativo.

1. Clique com o botão direito do mouse **na pasta res** e selecione **Novo**, em seguida, Diretório de Recursos **do Android**.

1. Altere **o tipo de recurso** para e selecione `raw` **OK**.

1. Clique com o botão direito do mouse **na nova pasta bruta** e selecione **Novo**, em **seguida, Arquivo**.

1. Nomeia o `msal_config.json` arquivo e selecione **OK**.

1. Adicione o seguinte ao **arquivomsal_config.jsno** arquivo.

    :::code language="json" source="../demo/GraphTutorial/msal_config.json.example":::

1. Substitua `YOUR_APP_ID_HERE` pela ID do aplicativo do registro do aplicativo e substitua pelo nome do pacote do seu `com.example.graphtutorial` projeto.

    > [!IMPORTANT]
    > Se você estiver usando o controle de origem, como git, agora seria um bom momento para excluir o arquivo do controle de origem para evitar o vazamento `msal_config.json` inadvertida da ID do aplicativo.

## <a name="implement-sign-in"></a>Implementar login

Nesta seção, você atualizará o manifesto para permitir que o MSAL use um navegador para autenticar o usuário, registrar seu URI de redirecionamento como sendo manipulado pelo aplicativo, criar uma classe auxiliar de autenticação e atualizar o aplicativo para entrar e sair.

1. Expanda **a pasta aplicativo/manifestos** e abra **AndroidManifest.xml**. Adicione os elementos a seguir acima do `application` elemento.

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

    > [!NOTE]
    > Essas permissões são necessárias para que a biblioteca MSAL autenture o usuário.

1. Adicione o elemento a seguir dentro `application` do elemento, substituindo a `YOUR_PACKAGE_NAME_HERE` cadeia de caracteres pelo nome do pacote.

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

1. Clique com o botão direito do **mouse na pasta app/java/com.example.graphtutorial** e selecione **Novo**, depois **Java Classe**. Altere **o Tipo** para **Interface**. Nomeia a interface `IAuthenticationHelperCreatedListener` e selecione **OK**.

1. Abra o novo arquivo e substitua seu conteúdo pelo seguinte.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/IAuthenticationHelperCreatedListener.java" id="ListenerSnippet":::

1. Clique com o botão direito do **mouse na pasta app/java/com.example.graphtutorial** e selecione **Novo**, depois **Java Classe**. Nomeia a `AuthenticationHelper` classe e selecione **OK**.

1. Abra o novo arquivo e substitua seu conteúdo pelo seguinte.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/AuthenticationHelper.java" id="AuthHelperSnippet":::

1. Abra **MainActivity** e adicione as instruções `import` a seguir.

    ```java
    import android.util.Log;

    import com.microsoft.identity.client.IAuthenticationResult;
    import com.microsoft.identity.client.exception.MsalClientException;
    import com.microsoft.identity.client.exception.MsalServiceException;
    import com.microsoft.identity.client.exception.MsalUiRequiredException;
    ```

1. Adicione a seguinte propriedade membro à `MainActivity` classe.

    ```java
    private AuthenticationHelper mAuthHelper = null;
    ```

1. Adicione o seguinte código ao final da função `onCreate`.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="InitialLoginSnippet":::

1. Adicione as seguintes funções à `MainActivity` classe.

    ```java
    // Silently sign in - used if there is already a
    // user account in the MSAL cache
    private void doSilentSignIn(boolean shouldAttemptInteractive) {
        mAuthHelper.acquireTokenSilently()
            .thenAccept(authenticationResult -> {
                handleSignInSuccess(authenticationResult);
            })
            .exceptionally(exception -> {
                // Check the type of exception and handle appropriately
                Throwable cause = exception.getCause();
                if (cause instanceof MsalUiRequiredException) {
                    Log.d("AUTH", "Interactive login required");
                    if (shouldAttemptInteractive) doInteractiveSignIn();
                } else if (cause instanceof MsalClientException) {
                    MsalClientException clientException = (MsalClientException)cause;
                    if (clientException.getErrorCode() == "no_current_account" ||
                        clientException.getErrorCode() == "no_account_found") {
                        Log.d("AUTH", "No current account, interactive login required");
                        if (shouldAttemptInteractive) doInteractiveSignIn();
                    }
                } else {
                    handleSignInFailure(cause);
                }
                hideProgressBar();
                return null;
            });
    }

    // Prompt the user to sign in
    private void doInteractiveSignIn() {
        mAuthHelper.acquireTokenInteractively(this)
            .thenAccept(authenticationResult -> {
                handleSignInSuccess(authenticationResult);
            })
            .exceptionally(exception -> {
                handleSignInFailure(exception);
                hideProgressBar();
                return null;
            });
    }

    // Handles the authentication result
    private void handleSignInSuccess(IAuthenticationResult authenticationResult) {
        // Log the token for debug purposes
        String accessToken = authenticationResult.getAccessToken();
        Log.d("AUTH", String.format("Access token: %s", accessToken));

        hideProgressBar();

        setSignedInState(true);
        openHomeFragment(mUserName);
    }

    private void handleSignInFailure(Throwable exception) {
        if (exception instanceof MsalServiceException) {
            // Exception when communicating with the auth server, likely config issue
            Log.e("AUTH", "Service error authenticating", exception);
        } else if (exception instanceof MsalClientException) {
            // Exception inside MSAL, more info inside MsalError.java
            Log.e("AUTH", "Client error authenticating", exception);
        } else {
            Log.e("AUTH", "Unhandled exception authenticating", exception);
        }
    }
    ```

1. Substitua as funções `signIn` `signOut` existentes e existentes pelo seguinte.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="SignInAndOutSnippet":::

    > [!NOTE]
    > Observe que o `signIn` método faz uma login silencioso (via `doSilentSignIn` ). O retorno de chamada para este método fará uma login interativa se o silencioso falhar. Isso evita ter que solicitar ao usuário sempre que ele iniciar o aplicativo.

1. Salve suas alterações e execute o aplicativo.

1. Quando você toca **no** item de menu Entrar, um navegador é aberto para a página de logon do Azure AD. Entrar com sua conta.

Depois que o aplicativo é retomado, você deve ver um token de acesso impresso no log de depuração no Android Studio.

![Uma captura de tela da janela Logcat no Android Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a>Obter detalhes do usuário

Nesta seção, você criará uma classe auxiliar para manter todas as chamadas para o Microsoft Graph e atualizar a classe para usar essa nova classe para obter o usuário `MainActivity` conectado.

1. Clique com o botão direito do **mouse na pasta app/java/com.example.graphtutorial** e selecione **Novo**, depois **Java Classe**. Nomeia a `GraphHelper` classe e selecione **OK**.

1. Abra o novo arquivo e substitua seu conteúdo pelo seguinte.

    ```java
    package com.example.graphtutorial;

    import com.microsoft.graph.models.extensions.User;
    import com.microsoft.graph.requests.GraphServiceClient;

    import java.util.concurrent.CompletableFuture;

    // Singleton class - the app only needs a single instance
    // of the Graph client
    public class GraphHelper implements IAuthenticationProvider {
        private static GraphHelper INSTANCE = null;
        private GraphServiceClient mClient = null;

        private GraphHelper() {
            AuthenticationHelper authProvider = AuthenticationHelper.getInstance();

            mClient = GraphServiceClient.builder()
                .authenticationProvider(authProvider).buildClient();
        }

        public static synchronized GraphHelper getInstance() {
            if (INSTANCE == null) {
                INSTANCE = new GraphHelper();
            }

            return INSTANCE;
        }

        public CompletableFuture<User> getUser() {
            // GET /me (logged in user)
            return mClient.me().buildRequest()
                .select("displayName,mail,mailboxSettings,userPrincipalName")
                .getAsync();
        }
    }
    ```

    > [!NOTE]
    > Considere o que esse código faz.
    >
    > - Ele expõe uma função para obter as informações do usuário conectado do `getUser` ponto de extremidade do `/me` Graph.
    >   - Ele usa `.select` para solicitar apenas as propriedades do usuário que o aplicativo precisa.

1. Remova as seguintes linhas que definirão o nome de usuário e o email:

    ```java
    // For testing
    mUserName = "Lynne Robbins";
    mUserEmail = "lynner@contoso.com";
    mUserTimeZone = "Pacific Standard Time";
    ```

1. Substitua a função `handleSignInSuccess` existente pela seguinte.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="HandleSignInSuccessSnippet":::

1. Salve suas alterações e execute o aplicativo. Depois que a interface do usuário for atualizada com o nome de exibição e o endereço de email do usuário.
