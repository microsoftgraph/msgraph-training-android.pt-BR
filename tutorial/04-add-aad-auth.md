<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="71cdc-101">Neste exercício, você estenderá o aplicativo do exercício anterior para oferecer suporte à autenticação com o Azure AD.</span><span class="sxs-lookup"><span data-stu-id="71cdc-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="71cdc-102">Isso é necessário para obter o token de acesso OAuth necessário para chamar o Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="71cdc-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="71cdc-103">Para fazer isso, você integrará a [biblioteca de autenticação da Microsoft (MSAL) para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="71cdc-103">To do this, you will integrate the [Microsoft Authentication Library (MSAL) for Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) into the application.</span></span>

1. <span data-ttu-id="71cdc-104">Clique com o botão direito do mouse na pasta **res** e selecione **novo**e, em seguida, **diretório de recursos do Android**.</span><span class="sxs-lookup"><span data-stu-id="71cdc-104">Right-click the **res** folder and select **New**, then **Android Resource Directory**.</span></span>

1. <span data-ttu-id="71cdc-105">Altere o **tipo** de recurso `raw` para e selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="71cdc-105">Change the **Resource type** to `raw` and select **OK**.</span></span>

1. <span data-ttu-id="71cdc-106">Clique com o botão direito do mouse na nova pasta **RAW** e selecione **novo**e, em seguida, **arquivo**.</span><span class="sxs-lookup"><span data-stu-id="71cdc-106">Right-click the new **raw** folder and select **New**, then **File**.</span></span>

1. <span data-ttu-id="71cdc-107">Nomeie o arquivo `msal_config.json` e selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="71cdc-107">Name the file `msal_config.json` and select **OK**.</span></span>

1. <span data-ttu-id="71cdc-108">Adicione o seguinte ao arquivo **msal_config. JSON** .</span><span class="sxs-lookup"><span data-stu-id="71cdc-108">Add the following to the **msal_config.json** file.</span></span>

    ```json
    {
      "client_id" : "YOUR_APP_ID_HERE",
      "redirect_uri" : "msauth://YOUR_PACKAGE_NAME_HERE/callback",
      "broker_redirect_uri_registered": false,
      "account_mode": "SINGLE",
      "authorities" : [
        {
          "type": "AAD",
          "audience": {
            "type": "AzureADandPersonalMicrosoftAccount"
          },
          "default": true
        }
      ]
    }
    ```

    <span data-ttu-id="71cdc-109">Substitua `YOUR_APP_ID_HERE` pela ID do aplicativo do registro do aplicativo e substitua `YOUR_PACKAGE_NAME_HERE` pelo nome do pacote do seu projeto.</span><span class="sxs-lookup"><span data-stu-id="71cdc-109">Replace `YOUR_APP_ID_HERE` with the app ID from your app registration, and replace `YOUR_PACKAGE_NAME_HERE` with your project's package name.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="71cdc-110">Se você estiver usando o controle de origem como o Git, agora seria uma boa hora para excluir `msal_config.json` o arquivo do controle de origem para evitar vazar inadvertidamente sua ID de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="71cdc-110">If you're using source control such as git, now would be a good time to exclude the `msal_config.json` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="71cdc-111">Implementar logon</span><span class="sxs-lookup"><span data-stu-id="71cdc-111">Implement sign-in</span></span>

<span data-ttu-id="71cdc-112">Nesta seção, você atualizará o manifesto para permitir que o MSAL use um navegador para autenticar o usuário, registrar o URI de redirecionamento como manipulado pelo aplicativo, criar uma classe auxiliar de autenticação e atualizar o aplicativo para entrar e sair.</span><span class="sxs-lookup"><span data-stu-id="71cdc-112">In this section you will update the manifest to allow MSAL to use a browser to authenticate the user, register your redirect URI as being handled by the app, create an authentication helper class, and update the app to sign in and sign out.</span></span>

1. <span data-ttu-id="71cdc-113">Expanda a pasta **aplicativos/manifestos** e abra **AndroidManifest. xml**.</span><span class="sxs-lookup"><span data-stu-id="71cdc-113">Expand the **app/manifests** folder and open **AndroidManifest.xml**.</span></span> <span data-ttu-id="71cdc-114">Adicione os seguintes elementos acima do `application` elemento.</span><span class="sxs-lookup"><span data-stu-id="71cdc-114">Add the following elements above the `application` element.</span></span>

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

    > [!NOTE]
    > <span data-ttu-id="71cdc-115">Essas permissões são necessárias para que a biblioteca do MSAL autentique o usuário.</span><span class="sxs-lookup"><span data-stu-id="71cdc-115">These permissions are required in order for the MSAL library to authenticate the user.</span></span>

1. <span data-ttu-id="71cdc-116">Adicione o seguinte elemento dentro do `application` elemento, substituindo `YOUR_PACKAGE_NAME_HERE` a cadeia de caracteres com o nome do pacote.</span><span class="sxs-lookup"><span data-stu-id="71cdc-116">Add the following element inside the `application` element, replacing the `YOUR_PACKAGE_NAME_HERE` string with your package name.</span></span>

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

1. <span data-ttu-id="71cdc-117">Clique com o botão direito do mouse na pasta **app/Java/com. example. graphtutorial** e selecione **nova**e, em seguida, **classe Java**.</span><span class="sxs-lookup"><span data-stu-id="71cdc-117">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="71cdc-118">Nomeie a classe `AuthenticationHelper` e selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="71cdc-118">Name the class `AuthenticationHelper` and select **OK**.</span></span>

1. <span data-ttu-id="71cdc-119">Abra o novo arquivo e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="71cdc-119">Open the new file and replace its contents with the following.</span></span>

    ```java
    package com.example.graphtutorial;

    import android.app.Activity;
    import android.content.Context;
    import android.util.Log;
    import com.microsoft.identity.client.AuthenticationCallback;
    import com.microsoft.identity.client.IPublicClientApplication;
    import com.microsoft.identity.client.ISingleAccountPublicClientApplication;
    import com.microsoft.identity.client.PublicClientApplication;
    import com.microsoft.identity.client.exception.MsalException;

    // Singleton class - the app only needs a single instance
    // of PublicClientApplication
    public class AuthenticationHelper {
        private static AuthenticationHelper INSTANCE = null;
        private ISingleAccountPublicClientApplication mPCA = null;
        private String[] mScopes = { "User.Read", "Calendars.Read" };

        private AuthenticationHelper(Context ctx) {
            PublicClientApplication.createSingleAccountPublicClientApplication(ctx, R.raw.msal_config,
                new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
                    @Override
                    public void onCreated(ISingleAccountPublicClientApplication application) {
                        mPCA = application;
                    }

                    @Override
                    public void onError(MsalException exception) {
                        Log.e("AUTHHELPER", "Error creating MSAL application", exception);
                    }
                });
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

        public void acquireTokenInteractively(Activity activity, AuthenticationCallback callback) {
            mPCA.signIn(activity, null, mScopes, callback);
        }

        public void acquireTokenSilently(AuthenticationCallback callback) {
            // Get the authority from MSAL config
            String authority = mPCA.getConfiguration().getDefaultAuthority().getAuthorityURL().toString();
            mPCA.acquireTokenSilentAsync(mScopes, authority, callback);
        }

        public void signOut() {
            mPCA.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
                @Override
                public void onSignOut() {
                    Log.d("AUTHHELPER", "Signed out");
                }

                @Override
                public void onError(@NonNull MsalException exception) {
                    Log.d("AUTHHELPER", "MSAL error signing out", exception);
                }
            });
        }
    }
    ```

1. <span data-ttu-id="71cdc-120">Abra o **MainActivity** e adicione as `import` instruções a seguir.</span><span class="sxs-lookup"><span data-stu-id="71cdc-120">Open **MainActivity** and add the following `import` statements.</span></span>

    ```java
    import android.util.Log;

    import com.microsoft.identity.client.AuthenticationCallback;
    import com.microsoft.identity.client.IAuthenticationResult;
    import com.microsoft.identity.client.exception.MsalClientException;
    import com.microsoft.identity.client.exception.MsalException;
    import com.microsoft.identity.client.exception.MsalServiceException;
    import com.microsoft.identity.client.exception.MsalUiRequiredException;
    ```

1. <span data-ttu-id="71cdc-121">Adicione a propriedade do membro a seguir `MainActivity` à classe.</span><span class="sxs-lookup"><span data-stu-id="71cdc-121">Add the following member property to the `MainActivity` class.</span></span>

    ```java
    private AuthenticationHelper mAuthHelper = null;
    ```

1. <span data-ttu-id="71cdc-122">Adicione o seguinte ao final da `onCreate` função.</span><span class="sxs-lookup"><span data-stu-id="71cdc-122">Add the following to the end of the `onCreate` function.</span></span>

    ```java
    // Get the authentication helper
    mAuthHelper = AuthenticationHelper.getInstance(getApplicationContext());
    ```

1. <span data-ttu-id="71cdc-123">Adicione as seguintes funções à `MainActivity` classe.</span><span class="sxs-lookup"><span data-stu-id="71cdc-123">Add the following functions to the `MainActivity` class.</span></span>

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
                    doInteractiveSignIn();

                } else if (exception instanceof MsalClientException) {
                    if (exception.getErrorCode() == "no_current_account") {
                        Log.d("AUTH", "No current account, interactive login required");
                        doInteractiveSignIn();
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

1. <span data-ttu-id="71cdc-124">Substitua o existente `signIn` e `signOut` as funções pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="71cdc-124">Replace the existing `signIn` and `signOut` functions with the following.</span></span>

    ```java
    private void signIn() {
        showProgressBar();
        // Attempt silent sign in first
        // if this fails, the callback will handle doing
        // interactive sign in
        doSilentSignIn();
    }

    private void signOut() {
        mAuthHelper.signOut();

        setSignedInState(false);
        openHomeFragment(mUserName);
    }
    ```

    > [!NOTE]
    > <span data-ttu-id="71cdc-125">Observe que o `signIn` método faz uma entrada silenciosa (via `doSilentSignIn`).</span><span class="sxs-lookup"><span data-stu-id="71cdc-125">Notice that the `signIn` method does a silent sign-in (via `doSilentSignIn`).</span></span> <span data-ttu-id="71cdc-126">O retorno de chamada para esse método fará uma entrada interativa se o um falhar.</span><span class="sxs-lookup"><span data-stu-id="71cdc-126">The callback for this method will do an interactive sign-in if the silent one fails.</span></span> <span data-ttu-id="71cdc-127">Isso evita que o usuário seja avisado toda vez que iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="71cdc-127">This avoids having to prompt the user every time they launch the app.</span></span>

1. <span data-ttu-id="71cdc-128">Salve suas alterações e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="71cdc-128">Save your changes and run the app.</span></span>

1. <span data-ttu-id="71cdc-129">Quando você toca no item de menu **entrar** , um navegador é aberto na página de logon do Azure AD.</span><span class="sxs-lookup"><span data-stu-id="71cdc-129">When you tap the **Sign in** menu item, a browser opens to the Azure AD login page.</span></span> <span data-ttu-id="71cdc-130">Entrar com sua conta.</span><span class="sxs-lookup"><span data-stu-id="71cdc-130">Sign in with your account.</span></span>

<span data-ttu-id="71cdc-131">Depois que o aplicativo reiniciar, você verá um token de acesso impresso no log de depuração no Android Studio.</span><span class="sxs-lookup"><span data-stu-id="71cdc-131">Once the app resumes, you should see an access token printed in the debug log in Android Studio.</span></span>

![Uma captura de tela da janela do logcat no Android Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="71cdc-133">Obter detalhes do usuário</span><span class="sxs-lookup"><span data-stu-id="71cdc-133">Get user details</span></span>

<span data-ttu-id="71cdc-134">Nesta seção, você criará uma classe auxiliar para manter todas as chamadas para o Microsoft Graph e atualizar a `MainActivity` classe para usar essa nova classe para obter o usuário conectado.</span><span class="sxs-lookup"><span data-stu-id="71cdc-134">In this section you will create a helper class to hold all of the calls to Microsoft Graph and update the `MainActivity` class to use this new class to get the logged-in user.</span></span>

1. <span data-ttu-id="71cdc-135">Clique com o botão direito do mouse na pasta **app/Java/com. example. graphtutorial** e selecione **nova**e, em seguida, **classe Java**.</span><span class="sxs-lookup"><span data-stu-id="71cdc-135">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span>

1. <span data-ttu-id="71cdc-136">Nomeie a classe `GraphHelper` e selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="71cdc-136">Name the class `GraphHelper` and select **OK**.</span></span>

1. <span data-ttu-id="71cdc-137">Abra o novo arquivo e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="71cdc-137">Open the new file and replace its contents with the following.</span></span>

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
    > <span data-ttu-id="71cdc-138">Considere o que esse código faz.</span><span class="sxs-lookup"><span data-stu-id="71cdc-138">Consider what this code does.</span></span>
    >
    > - <span data-ttu-id="71cdc-139">Ele implementa a `IAuthenticationProvider` interface para inserir o token de acesso no `Authorization` cabeçalho das solicitações HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="71cdc-139">It implements the `IAuthenticationProvider` interface to insert the access token in the `Authorization` header on outgoing HTTP requests.</span></span>
    > - <span data-ttu-id="71cdc-140">Ele expõe uma `getUser` função para obter as informações do usuário conectado do ponto de extremidade `/me` do gráfico.</span><span class="sxs-lookup"><span data-stu-id="71cdc-140">It exposes a `getUser` function to get the logged-in user's information from the `/me` Graph endpoint.</span></span>

1. <span data-ttu-id="71cdc-141">Adicione as seguintes `import` instruções à parte superior do arquivo **MainActivity** .</span><span class="sxs-lookup"><span data-stu-id="71cdc-141">Add the following `import` statements to the top of the **MainActivity** file.</span></span>

    ```java
    import com.microsoft.graph.concurrency.ICallback;
    import com.microsoft.graph.core.ClientException;
    import com.microsoft.graph.models.extensions.IGraphServiceClient;
    import com.microsoft.graph.models.extensions.User;
    ```

1. <span data-ttu-id="71cdc-142">Adicione a função a seguir à `MainActivity` classe para gerar um `ICallback` para a chamada de gráfico.</span><span class="sxs-lookup"><span data-stu-id="71cdc-142">Add the following function to the `MainActivity` class to generate an `ICallback` for the Graph call.</span></span>

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

1. <span data-ttu-id="71cdc-143">Remova as seguintes linhas que definem o nome de usuário e o email:</span><span class="sxs-lookup"><span data-stu-id="71cdc-143">Remove the following lines that set the user name and email:</span></span>

    ```java
    // For testing
    mUserName = "Megan Bowen";
    mUserEmail = "meganb@contoso.com";
    ```

1. <span data-ttu-id="71cdc-144">Substitua a `onSuccess` substituição `AuthenticationCallback` pela seguinte.</span><span class="sxs-lookup"><span data-stu-id="71cdc-144">Replace the `onSuccess` override in the `AuthenticationCallback` with the following.</span></span>

    ```java
    @Override
    public void onSuccess(IAuthenticationResult authenticationResult) {
        // Log the token for debug purposes
        String accessToken = authenticationResult.getAccessToken();
        Log.d("AUTH", String.format("Access token: %s", accessToken));

        // Get Graph client and get user
        GraphHelper graphHelper = GraphHelper.getInstance();
        graphHelper.getUser(accessToken, getUserCallback());
    }
    ```

<span data-ttu-id="71cdc-145">Se você salvar suas alterações e executar o aplicativo agora, após entrar, a interface do usuário será atualizada com o nome de exibição e o endereço de email do usuário.</span><span class="sxs-lookup"><span data-stu-id="71cdc-145">If you save your changes and run the app now, after sign-in the UI is updated with the user's display name and email address.</span></span>
