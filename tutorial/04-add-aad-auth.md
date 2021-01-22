<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="c11dd-101">Neste exercício, você estenderá o aplicativo do exercício anterior para dar suporte à autenticação com o Azure AD.</span><span class="sxs-lookup"><span data-stu-id="c11dd-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="c11dd-102">Isso é necessário para obter o token de acesso OAuth necessário para chamar o Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="c11dd-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="c11dd-103">Para fazer isso, você integrará a Biblioteca de Autenticação [da Microsoft (MSAL) para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c11dd-103">To do this, you will integrate the [Microsoft Authentication Library (MSAL) for Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) into the application.</span></span>

1. <span data-ttu-id="c11dd-104">Clique com o botão direito do mouse na **pasta res** e selecione **Novo,** em seguida, **Diretório de Recursos do Android.**</span><span class="sxs-lookup"><span data-stu-id="c11dd-104">Right-click the **res** folder and select **New**, then **Android Resource Directory**.</span></span>

1. <span data-ttu-id="c11dd-105">Altere **o tipo de recurso** para e selecione `raw` **OK**.</span><span class="sxs-lookup"><span data-stu-id="c11dd-105">Change the **Resource type** to `raw` and select **OK**.</span></span>

1. <span data-ttu-id="c11dd-106">Clique com o botão direito do mouse **na nova** pasta bruta e selecione **Novo,** em **seguida, Arquivo.**</span><span class="sxs-lookup"><span data-stu-id="c11dd-106">Right-click the new **raw** folder and select **New**, then **File**.</span></span>

1. <span data-ttu-id="c11dd-107">Nome do arquivo `msal_config.json` e selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="c11dd-107">Name the file `msal_config.json` and select **OK**.</span></span>

1. <span data-ttu-id="c11dd-108">Adicione o seguinte ao **arquivomsal_config.json.**</span><span class="sxs-lookup"><span data-stu-id="c11dd-108">Add the following to the **msal_config.json** file.</span></span>

    :::code language="json" source="../demo/GraphTutorial/msal_config.json.example":::

1. <span data-ttu-id="c11dd-109">Substitua pela ID do aplicativo do registro do aplicativo e substitua pelo nome `YOUR_APP_ID_HERE` `com.example.graphtutorial` do pacote do projeto.</span><span class="sxs-lookup"><span data-stu-id="c11dd-109">Replace `YOUR_APP_ID_HERE` with the app ID from your app registration, and replace `com.example.graphtutorial` with your project's package name.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="c11dd-110">Se você estiver usando o controle de código-fonte, como git, agora seria um bom momento para excluir o arquivo do controle de código-fonte para evitar o vazamento inadvertida de sua `msal_config.json` ID de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c11dd-110">If you're using source control such as git, now would be a good time to exclude the `msal_config.json` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="c11dd-111">Implementar a login</span><span class="sxs-lookup"><span data-stu-id="c11dd-111">Implement sign-in</span></span>

<span data-ttu-id="c11dd-112">Nesta seção, você atualizará o manifesto para permitir que a MSAL use um navegador para autenticar o usuário, registre seu URI de redirecionamento como sendo manipulado pelo aplicativo, crie uma classe auxiliar de autenticação e atualize o aplicativo para entrar e sair.</span><span class="sxs-lookup"><span data-stu-id="c11dd-112">In this section you will update the manifest to allow MSAL to use a browser to authenticate the user, register your redirect URI as being handled by the app, create an authentication helper class, and update the app to sign in and sign out.</span></span>

1. <span data-ttu-id="c11dd-113">Expanda **a pasta de aplicativos/manifestos** e abra **AndroidManifest.xml**.</span><span class="sxs-lookup"><span data-stu-id="c11dd-113">Expand the **app/manifests** folder and open **AndroidManifest.xml**.</span></span> <span data-ttu-id="c11dd-114">Adicione os seguintes elementos acima do `application` elemento.</span><span class="sxs-lookup"><span data-stu-id="c11dd-114">Add the following elements above the `application` element.</span></span>

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

    > [!NOTE]
    > <span data-ttu-id="c11dd-115">Essas permissões são necessárias para a biblioteca MSAL autenticar o usuário.</span><span class="sxs-lookup"><span data-stu-id="c11dd-115">These permissions are required in order for the MSAL library to authenticate the user.</span></span>

1. <span data-ttu-id="c11dd-116">Adicione o seguinte elemento dentro do `application` elemento, substituindo a `YOUR_PACKAGE_NAME_HERE` cadeia de caracteres pelo nome do pacote.</span><span class="sxs-lookup"><span data-stu-id="c11dd-116">Add the following element inside the `application` element, replacing the `YOUR_PACKAGE_NAME_HERE` string with your package name.</span></span>

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

1. <span data-ttu-id="c11dd-117">Clique com o botão direito do **mouse na pasta app/java/com.example.graphtutorial** e selecione **New**, em seguida, **Classe Java.**</span><span class="sxs-lookup"><span data-stu-id="c11dd-117">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="c11dd-118">Altere **o tipo** para **interface.**</span><span class="sxs-lookup"><span data-stu-id="c11dd-118">Change the **Kind** to **Interface**.</span></span> <span data-ttu-id="c11dd-119">Nome da interface `IAuthenticationHelperCreatedListener` e selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="c11dd-119">Name the interface `IAuthenticationHelperCreatedListener` and select **OK**.</span></span>

1. <span data-ttu-id="c11dd-120">Abra o novo arquivo e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="c11dd-120">Open the new file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/IAuthenticationHelperCreatedListener.java" id="ListenerSnippet":::

1. <span data-ttu-id="c11dd-121">Clique com o botão direito do **mouse na pasta app/java/com.example.graphtutorial** e selecione **New**, em seguida, **Classe Java.**</span><span class="sxs-lookup"><span data-stu-id="c11dd-121">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="c11dd-122">Nome da classe `AuthenticationHelper` e selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="c11dd-122">Name the class `AuthenticationHelper` and select **OK**.</span></span>

1. <span data-ttu-id="c11dd-123">Abra o novo arquivo e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="c11dd-123">Open the new file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/AuthenticationHelper.java" id="AuthHelperSnippet":::

1. <span data-ttu-id="c11dd-124">Abra **MainActivity** e adicione as instruções a `import` seguir.</span><span class="sxs-lookup"><span data-stu-id="c11dd-124">Open **MainActivity** and add the following `import` statements.</span></span>

    ```java
    import android.util.Log;

    import com.microsoft.identity.client.AuthenticationCallback;
    import com.microsoft.identity.client.IAuthenticationResult;
    import com.microsoft.identity.client.exception.MsalClientException;
    import com.microsoft.identity.client.exception.MsalException;
    import com.microsoft.identity.client.exception.MsalServiceException;
    import com.microsoft.identity.client.exception.MsalUiRequiredException;
    ```

1. <span data-ttu-id="c11dd-125">Adicione as seguintes propriedades de membro à `MainActivity` classe.</span><span class="sxs-lookup"><span data-stu-id="c11dd-125">Add the following member properties to the `MainActivity` class.</span></span>

    ```java
    private AuthenticationHelper mAuthHelper = null;
    private boolean mAttemptInteractiveSignIn = false;
    ```

1. <span data-ttu-id="c11dd-126">Adicione o seguinte código ao final da função `onCreate`.</span><span class="sxs-lookup"><span data-stu-id="c11dd-126">Add the following code to the end of the `onCreate` function.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="InitialLoginSnippet":::

1. <span data-ttu-id="c11dd-127">Adicione as funções a seguir à `MainActivity` classe.</span><span class="sxs-lookup"><span data-stu-id="c11dd-127">Add the following functions to the `MainActivity` class.</span></span>

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

1. <span data-ttu-id="c11dd-128">Substitua as funções `signIn` e `signOut` existentes pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="c11dd-128">Replace the existing `signIn` and `signOut` functions with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="SignInAndOutSnippet":::

    > [!NOTE]
    > <span data-ttu-id="c11dd-129">Observe que o `signIn` método faz uma login silencioso (via `doSilentSignIn` ).</span><span class="sxs-lookup"><span data-stu-id="c11dd-129">Notice that the `signIn` method does a silent sign-in (via `doSilentSignIn`).</span></span> <span data-ttu-id="c11dd-130">O retorno de chamada para esse método fará uma login interativa se o silencioso falhar.</span><span class="sxs-lookup"><span data-stu-id="c11dd-130">The callback for this method will do an interactive sign-in if the silent one fails.</span></span> <span data-ttu-id="c11dd-131">Isso evita ter que solicitar ao usuário sempre que ele iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c11dd-131">This avoids having to prompt the user every time they launch the app.</span></span>

1. <span data-ttu-id="c11dd-132">Salve suas alterações e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c11dd-132">Save your changes and run the app.</span></span>

1. <span data-ttu-id="c11dd-133">Quando você toca no item **de** menu Entrar, um navegador é aberto na página de logon do Azure AD.</span><span class="sxs-lookup"><span data-stu-id="c11dd-133">When you tap the **Sign in** menu item, a browser opens to the Azure AD login page.</span></span> <span data-ttu-id="c11dd-134">Entrar com sua conta.</span><span class="sxs-lookup"><span data-stu-id="c11dd-134">Sign in with your account.</span></span>

<span data-ttu-id="c11dd-135">Depois que o aplicativo é retomado, você deverá ver um token de acesso impresso no log de depuração no Android Studio.</span><span class="sxs-lookup"><span data-stu-id="c11dd-135">Once the app resumes, you should see an access token printed in the debug log in Android Studio.</span></span>

![Uma captura de tela da janela Logcat no Android Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="c11dd-137">Obter detalhes do usuário</span><span class="sxs-lookup"><span data-stu-id="c11dd-137">Get user details</span></span>

<span data-ttu-id="c11dd-138">Nesta seção, você criará uma classe auxiliar para manter todas as chamadas para o Microsoft Graph e atualizar a classe para usar essa nova classe para obter o usuário `MainActivity` conectado.</span><span class="sxs-lookup"><span data-stu-id="c11dd-138">In this section you will create a helper class to hold all of the calls to Microsoft Graph and update the `MainActivity` class to use this new class to get the logged-in user.</span></span>

1. <span data-ttu-id="c11dd-139">Clique com o botão direito do **mouse na pasta app/java/com.example.graphtutorial** e selecione **New**, em seguida, **Classe Java.**</span><span class="sxs-lookup"><span data-stu-id="c11dd-139">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="c11dd-140">Nome da classe `GraphHelper` e selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="c11dd-140">Name the class `GraphHelper` and select **OK**.</span></span>

1. <span data-ttu-id="c11dd-141">Abra o novo arquivo e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="c11dd-141">Open the new file and replace its contents with the following.</span></span>

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
    > <span data-ttu-id="c11dd-142">Considere o que esse código faz.</span><span class="sxs-lookup"><span data-stu-id="c11dd-142">Consider what this code does.</span></span>
    >
    > - <span data-ttu-id="c11dd-143">Ele implementa a `IAuthenticationProvider` interface para inserir o token de acesso no cabeçalho em `Authorization` solicitações HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="c11dd-143">It implements the `IAuthenticationProvider` interface to insert the access token in the `Authorization` header on outgoing HTTP requests.</span></span>
    > - <span data-ttu-id="c11dd-144">Ele expõe uma função para obter as informações do usuário conectado do `getUser` ponto de extremidade do `/me` Graph.</span><span class="sxs-lookup"><span data-stu-id="c11dd-144">It exposes a `getUser` function to get the logged-in user's information from the `/me` Graph endpoint.</span></span>

1. <span data-ttu-id="c11dd-145">Adicione as instruções `import` a seguir à parte superior do arquivo **MainActivity.**</span><span class="sxs-lookup"><span data-stu-id="c11dd-145">Add the following `import` statements to the top of the **MainActivity** file.</span></span>

    ```java
    import com.microsoft.graph.concurrency.ICallback;
    import com.microsoft.graph.core.ClientException;
    import com.microsoft.graph.models.extensions.User;
    ```

1. <span data-ttu-id="c11dd-146">Adicione a função a seguir à `MainActivity` classe para gerar um para a chamada do `ICallback` Graph.</span><span class="sxs-lookup"><span data-stu-id="c11dd-146">Add the following function to the `MainActivity` class to generate an `ICallback` for the Graph call.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="GetUserCallbackSnippet":::

1. <span data-ttu-id="c11dd-147">Remova as seguintes linhas que configuram o nome de usuário e o email:</span><span class="sxs-lookup"><span data-stu-id="c11dd-147">Remove the following lines that set the user name and email:</span></span>

    ```java
    // For testing
    mUserName = "Megan Bowen";
    mUserEmail = "meganb@contoso.com";
    ```

1. <span data-ttu-id="c11dd-148">Substitua a `onSuccess` substituição no que se `AuthenticationCallback` segue.</span><span class="sxs-lookup"><span data-stu-id="c11dd-148">Replace the `onSuccess` override in the `AuthenticationCallback` with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="OnSuccessSnippet":::

1. <span data-ttu-id="c11dd-149">Salve suas alterações e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c11dd-149">Save your changes and run the app.</span></span> <span data-ttu-id="c11dd-150">Depois que a interface do usuário for atualizada com o nome de exibição e o endereço de email do usuário.</span><span class="sxs-lookup"><span data-stu-id="c11dd-150">After sign-in the UI is updated with the user's display name and email address.</span></span>
