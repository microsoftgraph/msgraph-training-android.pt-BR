<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="d054a-101">Neste exercício, você estenderá o aplicativo do exercício anterior para dar suporte à autenticação com o Azure AD.</span><span class="sxs-lookup"><span data-stu-id="d054a-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="d054a-102">Isso é necessário para obter o token de acesso OAuth necessário para chamar o Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="d054a-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="d054a-103">Para fazer isso, você integrará a Biblioteca de Autenticação [da Microsoft (MSAL) para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d054a-103">To do this, you will integrate the [Microsoft Authentication Library (MSAL) for Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) into the application.</span></span>

1. <span data-ttu-id="d054a-104">Clique com o botão direito do mouse **na pasta res** e selecione **Novo**, em seguida, Diretório de Recursos **do Android**.</span><span class="sxs-lookup"><span data-stu-id="d054a-104">Right-click the **res** folder and select **New**, then **Android Resource Directory**.</span></span>

1. <span data-ttu-id="d054a-105">Altere **o tipo de recurso** para e selecione `raw` **OK**.</span><span class="sxs-lookup"><span data-stu-id="d054a-105">Change the **Resource type** to `raw` and select **OK**.</span></span>

1. <span data-ttu-id="d054a-106">Clique com o botão direito do mouse **na nova pasta bruta** e selecione **Novo**, em **seguida, Arquivo**.</span><span class="sxs-lookup"><span data-stu-id="d054a-106">Right-click the new **raw** folder and select **New**, then **File**.</span></span>

1. <span data-ttu-id="d054a-107">Nomeia o `msal_config.json` arquivo e selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="d054a-107">Name the file `msal_config.json` and select **OK**.</span></span>

1. <span data-ttu-id="d054a-108">Adicione o seguinte ao **arquivomsal_config.jsno** arquivo.</span><span class="sxs-lookup"><span data-stu-id="d054a-108">Add the following to the **msal_config.json** file.</span></span>

    :::code language="json" source="../demo/GraphTutorial/msal_config.json.example":::

1. <span data-ttu-id="d054a-109">Substitua `YOUR_APP_ID_HERE` pela ID do aplicativo do registro do aplicativo e substitua pelo nome do pacote do seu `com.example.graphtutorial` projeto.</span><span class="sxs-lookup"><span data-stu-id="d054a-109">Replace `YOUR_APP_ID_HERE` with the app ID from your app registration, and replace `com.example.graphtutorial` with your project's package name.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="d054a-110">Se você estiver usando o controle de origem, como git, agora seria um bom momento para excluir o arquivo do controle de origem para evitar o vazamento `msal_config.json` inadvertida da ID do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d054a-110">If you're using source control such as git, now would be a good time to exclude the `msal_config.json` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="d054a-111">Implementar login</span><span class="sxs-lookup"><span data-stu-id="d054a-111">Implement sign-in</span></span>

<span data-ttu-id="d054a-112">Nesta seção, você atualizará o manifesto para permitir que o MSAL use um navegador para autenticar o usuário, registrar seu URI de redirecionamento como sendo manipulado pelo aplicativo, criar uma classe auxiliar de autenticação e atualizar o aplicativo para entrar e sair.</span><span class="sxs-lookup"><span data-stu-id="d054a-112">In this section you will update the manifest to allow MSAL to use a browser to authenticate the user, register your redirect URI as being handled by the app, create an authentication helper class, and update the app to sign in and sign out.</span></span>

1. <span data-ttu-id="d054a-113">Expanda **a pasta aplicativo/manifestos** e abra **AndroidManifest.xml**.</span><span class="sxs-lookup"><span data-stu-id="d054a-113">Expand the **app/manifests** folder and open **AndroidManifest.xml**.</span></span> <span data-ttu-id="d054a-114">Adicione os elementos a seguir acima do `application` elemento.</span><span class="sxs-lookup"><span data-stu-id="d054a-114">Add the following elements above the `application` element.</span></span>

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

    > [!NOTE]
    > <span data-ttu-id="d054a-115">Essas permissões são necessárias para que a biblioteca MSAL autenture o usuário.</span><span class="sxs-lookup"><span data-stu-id="d054a-115">These permissions are required in order for the MSAL library to authenticate the user.</span></span>

1. <span data-ttu-id="d054a-116">Adicione o elemento a seguir dentro `application` do elemento, substituindo a `YOUR_PACKAGE_NAME_HERE` cadeia de caracteres pelo nome do pacote.</span><span class="sxs-lookup"><span data-stu-id="d054a-116">Add the following element inside the `application` element, replacing the `YOUR_PACKAGE_NAME_HERE` string with your package name.</span></span>

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

1. <span data-ttu-id="d054a-117">Clique com o botão direito do **mouse na pasta app/java/com.example.graphtutorial** e selecione **Novo**, depois **Java Classe**.</span><span class="sxs-lookup"><span data-stu-id="d054a-117">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="d054a-118">Altere **o Tipo** para **Interface**.</span><span class="sxs-lookup"><span data-stu-id="d054a-118">Change the **Kind** to **Interface**.</span></span> <span data-ttu-id="d054a-119">Nomeia a interface `IAuthenticationHelperCreatedListener` e selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="d054a-119">Name the interface `IAuthenticationHelperCreatedListener` and select **OK**.</span></span>

1. <span data-ttu-id="d054a-120">Abra o novo arquivo e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="d054a-120">Open the new file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/IAuthenticationHelperCreatedListener.java" id="ListenerSnippet":::

1. <span data-ttu-id="d054a-121">Clique com o botão direito do **mouse na pasta app/java/com.example.graphtutorial** e selecione **Novo**, depois **Java Classe**.</span><span class="sxs-lookup"><span data-stu-id="d054a-121">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="d054a-122">Nomeia a `AuthenticationHelper` classe e selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="d054a-122">Name the class `AuthenticationHelper` and select **OK**.</span></span>

1. <span data-ttu-id="d054a-123">Abra o novo arquivo e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="d054a-123">Open the new file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/AuthenticationHelper.java" id="AuthHelperSnippet":::

1. <span data-ttu-id="d054a-124">Abra **MainActivity** e adicione as instruções `import` a seguir.</span><span class="sxs-lookup"><span data-stu-id="d054a-124">Open **MainActivity** and add the following `import` statements.</span></span>

    ```java
    import android.util.Log;

    import com.microsoft.identity.client.IAuthenticationResult;
    import com.microsoft.identity.client.exception.MsalClientException;
    import com.microsoft.identity.client.exception.MsalServiceException;
    import com.microsoft.identity.client.exception.MsalUiRequiredException;
    ```

1. <span data-ttu-id="d054a-125">Adicione a seguinte propriedade membro à `MainActivity` classe.</span><span class="sxs-lookup"><span data-stu-id="d054a-125">Add the following member property to the `MainActivity` class.</span></span>

    ```java
    private AuthenticationHelper mAuthHelper = null;
    ```

1. <span data-ttu-id="d054a-126">Adicione o seguinte código ao final da função `onCreate`.</span><span class="sxs-lookup"><span data-stu-id="d054a-126">Add the following code to the end of the `onCreate` function.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="InitialLoginSnippet":::

1. <span data-ttu-id="d054a-127">Adicione as seguintes funções à `MainActivity` classe.</span><span class="sxs-lookup"><span data-stu-id="d054a-127">Add the following functions to the `MainActivity` class.</span></span>

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

1. <span data-ttu-id="d054a-128">Substitua as funções `signIn` `signOut` existentes e existentes pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="d054a-128">Replace the existing `signIn` and `signOut` functions with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="SignInAndOutSnippet":::

    > [!NOTE]
    > <span data-ttu-id="d054a-129">Observe que o `signIn` método faz uma login silencioso (via `doSilentSignIn` ).</span><span class="sxs-lookup"><span data-stu-id="d054a-129">Notice that the `signIn` method does a silent sign-in (via `doSilentSignIn`).</span></span> <span data-ttu-id="d054a-130">O retorno de chamada para este método fará uma login interativa se o silencioso falhar.</span><span class="sxs-lookup"><span data-stu-id="d054a-130">The callback for this method will do an interactive sign-in if the silent one fails.</span></span> <span data-ttu-id="d054a-131">Isso evita ter que solicitar ao usuário sempre que ele iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d054a-131">This avoids having to prompt the user every time they launch the app.</span></span>

1. <span data-ttu-id="d054a-132">Salve suas alterações e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d054a-132">Save your changes and run the app.</span></span>

1. <span data-ttu-id="d054a-133">Quando você toca **no** item de menu Entrar, um navegador é aberto para a página de logon do Azure AD.</span><span class="sxs-lookup"><span data-stu-id="d054a-133">When you tap the **Sign in** menu item, a browser opens to the Azure AD login page.</span></span> <span data-ttu-id="d054a-134">Entrar com sua conta.</span><span class="sxs-lookup"><span data-stu-id="d054a-134">Sign in with your account.</span></span>

<span data-ttu-id="d054a-135">Depois que o aplicativo é retomado, você deve ver um token de acesso impresso no log de depuração no Android Studio.</span><span class="sxs-lookup"><span data-stu-id="d054a-135">Once the app resumes, you should see an access token printed in the debug log in Android Studio.</span></span>

![Uma captura de tela da janela Logcat no Android Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="d054a-137">Obter detalhes do usuário</span><span class="sxs-lookup"><span data-stu-id="d054a-137">Get user details</span></span>

<span data-ttu-id="d054a-138">Nesta seção, você criará uma classe auxiliar para manter todas as chamadas para o Microsoft Graph e atualizar a classe para usar essa nova classe para obter o usuário `MainActivity` conectado.</span><span class="sxs-lookup"><span data-stu-id="d054a-138">In this section you will create a helper class to hold all of the calls to Microsoft Graph and update the `MainActivity` class to use this new class to get the logged-in user.</span></span>

1. <span data-ttu-id="d054a-139">Clique com o botão direito do **mouse na pasta app/java/com.example.graphtutorial** e selecione **Novo**, depois **Java Classe**.</span><span class="sxs-lookup"><span data-stu-id="d054a-139">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="d054a-140">Nomeia a `GraphHelper` classe e selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="d054a-140">Name the class `GraphHelper` and select **OK**.</span></span>

1. <span data-ttu-id="d054a-141">Abra o novo arquivo e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="d054a-141">Open the new file and replace its contents with the following.</span></span>

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
    > <span data-ttu-id="d054a-142">Considere o que esse código faz.</span><span class="sxs-lookup"><span data-stu-id="d054a-142">Consider what this code does.</span></span>
    >
    > - <span data-ttu-id="d054a-143">Ele expõe uma função para obter as informações do usuário conectado do `getUser` ponto de extremidade do `/me` Graph.</span><span class="sxs-lookup"><span data-stu-id="d054a-143">It exposes a `getUser` function to get the logged-in user's information from the `/me` Graph endpoint.</span></span>
    >   - <span data-ttu-id="d054a-144">Ele usa `.select` para solicitar apenas as propriedades do usuário que o aplicativo precisa.</span><span class="sxs-lookup"><span data-stu-id="d054a-144">It uses `.select` to request only the properties of the user that the application needs.</span></span>

1. <span data-ttu-id="d054a-145">Remova as seguintes linhas que definirão o nome de usuário e o email:</span><span class="sxs-lookup"><span data-stu-id="d054a-145">Remove the following lines that set the user name and email:</span></span>

    ```java
    // For testing
    mUserName = "Lynne Robbins";
    mUserEmail = "lynner@contoso.com";
    mUserTimeZone = "Pacific Standard Time";
    ```

1. <span data-ttu-id="d054a-146">Substitua a função `handleSignInSuccess` existente pela seguinte.</span><span class="sxs-lookup"><span data-stu-id="d054a-146">Replace the existing `handleSignInSuccess` function with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="HandleSignInSuccessSnippet":::

1. <span data-ttu-id="d054a-147">Salve suas alterações e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d054a-147">Save your changes and run the app.</span></span> <span data-ttu-id="d054a-148">Depois que a interface do usuário for atualizada com o nome de exibição e o endereço de email do usuário.</span><span class="sxs-lookup"><span data-stu-id="d054a-148">After sign-in the UI is updated with the user's display name and email address.</span></span>
