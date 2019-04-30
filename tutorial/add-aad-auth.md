<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="886d1-101">Neste exercício, você estenderá o aplicativo do exercício anterior para oferecer suporte à autenticação com o Azure AD.</span><span class="sxs-lookup"><span data-stu-id="886d1-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="886d1-102">Isso é necessário para obter o token de acesso OAuth necessário para chamar o Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="886d1-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="886d1-103">Nesta etapa, você integrará a [biblioteca de autenticação da Microsoft (MSAL) para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="886d1-103">In this step you will integrate the [Microsoft Authentication Library (MSAL) for Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) into the application.</span></span>

<span data-ttu-id="886d1-104">Clique com o botão direito do mouse na pasta **app/res/Values** e escolha **novo**e, em seguida, **valores do arquivo de recurso**.</span><span class="sxs-lookup"><span data-stu-id="886d1-104">Right-click the **app/res/values** folder and choose **New**, then **Values resource file**.</span></span> <span data-ttu-id="886d1-105">Nomeie o arquivo `oauth_strings` e escolha **OK**.</span><span class="sxs-lookup"><span data-stu-id="886d1-105">Name the file `oauth_strings` and choose **OK**.</span></span> <span data-ttu-id="886d1-106">Adicione os seguintes valores ao `resources` elemento.</span><span class="sxs-lookup"><span data-stu-id="886d1-106">Add the following values to the `resources` element.</span></span>

```xml
<string name="oauth_app_id">YOUR_APP_ID_HERE</string>
<string name="oauth_redirect_uri">msalYOUR_APP_ID_HERE</string>
<string-array name="oauth_scopes">
    <item>User.Read</item>
    <item>Calendars.Read</item>
</string-array>
```

> [!IMPORTANT]
> <span data-ttu-id="886d1-107">Se você estiver usando o controle de origem como o Git, agora seria uma boa hora para excluir `oauth_strings.xml` o arquivo do controle de origem para evitar vazar inadvertidamente sua ID de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="886d1-107">If you're using source control such as git, now would be a good time to exclude the `oauth_strings.xml` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="886d1-108">Implementar logon</span><span class="sxs-lookup"><span data-stu-id="886d1-108">Implement sign-in</span></span>

<span data-ttu-id="886d1-109">Expanda a pasta **aplicativos/manifestos** e abra **AndroidManifest. xml**.</span><span class="sxs-lookup"><span data-stu-id="886d1-109">Expand the **app/manifests** folder and open **AndroidManifest.xml**.</span></span> <span data-ttu-id="886d1-110">Adicione os seguintes elementos acima do `application` elemento.</span><span class="sxs-lookup"><span data-stu-id="886d1-110">Add the following elements above the `application` element.</span></span>

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

<span data-ttu-id="886d1-111">Essas permissões são necessárias para que a biblioteca do MSAL autentique o usuário.</span><span class="sxs-lookup"><span data-stu-id="886d1-111">These permissions are required in order for the MSAL library to authenticate the user.</span></span>

<span data-ttu-id="886d1-112">Agora, adicione o elemento a seguir `application` dentro do elemento.</span><span class="sxs-lookup"><span data-stu-id="886d1-112">Now add the following element inside the `application` element.</span></span>

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

<span data-ttu-id="886d1-113">Isso permite que o MSAL use um navegador para autenticar o usuário e registra seu URI de redirecionamento como manipulado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="886d1-113">This allows MSAL to use a browser to authenticate the user, and registers your redirect URI as being handled by the app.</span></span>

### <a name="implement-an-authentication-helper"></a><span data-ttu-id="886d1-114">Implementar um auxiliar de autenticação</span><span class="sxs-lookup"><span data-stu-id="886d1-114">Implement an authentication helper</span></span>

<span data-ttu-id="886d1-115">Clique com o botão direito do mouse na pasta **app/Java/com. example. graphtutorial** e escolha **nova**e, em seguida, **classe Java**.</span><span class="sxs-lookup"><span data-stu-id="886d1-115">Right-click the **app/java/com.example.graphtutorial** folder and choose **New**, then **Java Class**.</span></span> <span data-ttu-id="886d1-116">Nomeie a classe `AuthenticationHelper` e escolha **OK**.</span><span class="sxs-lookup"><span data-stu-id="886d1-116">Name the class `AuthenticationHelper` and choose **OK**.</span></span> <span data-ttu-id="886d1-117">Abra o novo arquivo e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="886d1-117">Open the new file and replace its contents with the following.</span></span>

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

<span data-ttu-id="886d1-118">Agora atualize `MainActivity` para usar essa nova classe.</span><span class="sxs-lookup"><span data-stu-id="886d1-118">Now update `MainActivity` to use this new class.</span></span> <span data-ttu-id="886d1-119">Abra o **MainActivity** e adicione as `import` instruções a seguir.</span><span class="sxs-lookup"><span data-stu-id="886d1-119">Open **MainActivity** and add the following `import` statements.</span></span>

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

<span data-ttu-id="886d1-120">Em seguida, adicione a propriedade do membro a `MainActivity` seguir à classe.</span><span class="sxs-lookup"><span data-stu-id="886d1-120">Next, add the following member property to the `MainActivity` class.</span></span>

```java
private AuthenticationHelper mAuthHelper = null;
```

<span data-ttu-id="886d1-121">Atualização `onCreate` a ser `mAuthHelper`definida.</span><span class="sxs-lookup"><span data-stu-id="886d1-121">Update `onCreate` to set `mAuthHelper`.</span></span> <span data-ttu-id="886d1-122">Adicione o seguinte ao final da `onCreate` função.</span><span class="sxs-lookup"><span data-stu-id="886d1-122">Add the following to the end of the `onCreate` function.</span></span>

```java
// Get the authentication helper
mAuthHelper = AuthenticationHelper.getInstance(getApplicationContext());
```

<span data-ttu-id="886d1-123">Adicione uma substituição para `onActivityResult` lidar com as respostas de autenticação.</span><span class="sxs-lookup"><span data-stu-id="886d1-123">Add an override for `onActivityResult` to handle authentication responses.</span></span>

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    mAuthHelper.handleRedirect(requestCode, resultCode, data);
}
```

<span data-ttu-id="886d1-124">Agora, adicione as seguintes funções à `MainActivity` classe.</span><span class="sxs-lookup"><span data-stu-id="886d1-124">Now, add the following functions to the `MainActivity` class.</span></span>

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

<span data-ttu-id="886d1-125">Por fim, substitua o `signIn` existente `signOut` e as funções pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="886d1-125">Finally, replace the existing `signIn` and `signOut` functions with the following.</span></span>

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

<span data-ttu-id="886d1-126">Observe que o `signIn` método primeiro verifica se há uma conta de usuário já no cache do MSAL.</span><span class="sxs-lookup"><span data-stu-id="886d1-126">Notice that the `signIn` method first checks if there is a user account already in the MSAL cache.</span></span> <span data-ttu-id="886d1-127">Se houver, ele tentará atualizar seus tokens de forma silenciosa, evitando ter que avisar o usuário toda vez que iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="886d1-127">If there is, it attempts to refresh its tokens silently, avoiding having to prompt the user every time they launch the app.</span></span>

<span data-ttu-id="886d1-128">Salve suas alterações e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="886d1-128">Save your changes and run the app.</span></span> <span data-ttu-id="886d1-129">Quando você toca no item de menu **entrar** , um navegador é aberto na página de logon do Azure AD.</span><span class="sxs-lookup"><span data-stu-id="886d1-129">When you tap the **Sign in** menu item, a browser opens to the Azure AD login page.</span></span> <span data-ttu-id="886d1-130">Entre com sua conta.</span><span class="sxs-lookup"><span data-stu-id="886d1-130">Sign in with your account.</span></span> <span data-ttu-id="886d1-131">Depois que o aplicativo reiniciar, você verá um token de acesso impresso no log de depuração no Android Studio.</span><span class="sxs-lookup"><span data-stu-id="886d1-131">Once the app resumes, you should see an access token printed in the debug log in Android Studio.</span></span>

![Uma captura de tela da janela do logcat no Android Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="886d1-133">Obter detalhes do usuário</span><span class="sxs-lookup"><span data-stu-id="886d1-133">Get user details</span></span>

<span data-ttu-id="886d1-134">Comece criando uma classe auxiliar para manter todas as chamadas para o Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="886d1-134">Start by creating a helper class to hold all of the calls to Microsoft Graph.</span></span> <span data-ttu-id="886d1-135">Clique com o botão direito do mouse na pasta **app/Java/com. example. graphtutorial** e escolha **nova**e, em seguida, **classe Java**.</span><span class="sxs-lookup"><span data-stu-id="886d1-135">Right-click the **app/java/com.example.graphtutorial** folder and choose **New**, then **Java Class**.</span></span> <span data-ttu-id="886d1-136">Nomeie a classe `GraphHelper` e escolha **OK**.</span><span class="sxs-lookup"><span data-stu-id="886d1-136">Name the class `GraphHelper` and choose **OK**.</span></span> <span data-ttu-id="886d1-137">Abra o novo arquivo e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="886d1-137">Open the new file and replace its contents with the following.</span></span>

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

<span data-ttu-id="886d1-138">Observe o que esse código faz.</span><span class="sxs-lookup"><span data-stu-id="886d1-138">Note what this code does.</span></span>

- <span data-ttu-id="886d1-139">Ele implementa a `IAuthenticationProvider` interface para inserir o token de acesso no `Authorization` cabeçalho das solicitações HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="886d1-139">It implements the `IAuthenticationProvider` interface to insert the access token in the `Authorization` header on outgoing HTTP requests.</span></span>
- <span data-ttu-id="886d1-140">Ele expõe uma `getUser` função para obter as informações do usuário conectado do ponto de extremidade `/me` do gráfico.</span><span class="sxs-lookup"><span data-stu-id="886d1-140">It exposes a `getUser` function to get the logged-in user's information from the `/me` Graph endpoint.</span></span>

<span data-ttu-id="886d1-141">Agora, atualize `MainActivity` a classe para usar essa nova classe para obter o usuário conectado.</span><span class="sxs-lookup"><span data-stu-id="886d1-141">Now update the `MainActivity` class to use this new class to get the logged-in user.</span></span> <span data-ttu-id="886d1-142">Adicione as seguintes `import` instruções à parte superior do arquivo **MainActivity** .</span><span class="sxs-lookup"><span data-stu-id="886d1-142">Add the following `import` statements to the top of the **MainActivity** file.</span></span>

```java
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.models.extensions.IGraphServiceClient;
import com.microsoft.graph.models.extensions.User;
```

<span data-ttu-id="886d1-143">Em seguida, adicione a função a seguir `MainActivity` à classe para gerar `ICallback` um para a chamada de gráfico.</span><span class="sxs-lookup"><span data-stu-id="886d1-143">Next, add the following function to the `MainActivity` class to generate an `ICallback` for the Graph call.</span></span>

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

<span data-ttu-id="886d1-144">Remova as seguintes linhas que definem o nome de usuário e o email:</span><span class="sxs-lookup"><span data-stu-id="886d1-144">Remove the following lines that set the user name and email:</span></span>

```java
// For testing
mUserName = "Megan Bowen";
mUserEmail = "meganb@contoso.com";
```

<span data-ttu-id="886d1-145">Por fim, substitua `onSuccess` a substituição `AuthenticationCallback` pela seguinte.</span><span class="sxs-lookup"><span data-stu-id="886d1-145">Finally, replace the `onSuccess` override in the `AuthenticationCallback` with the following.</span></span>

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

<span data-ttu-id="886d1-146">Se você salvar suas alterações e executar o aplicativo agora, após entrar, a interface do usuário será atualizada com o nome de exibição e o endereço de email do usuário.</span><span class="sxs-lookup"><span data-stu-id="886d1-146">If you save your changes and run the app now, after sign-in the UI is updated with the user's display name and email address.</span></span>
