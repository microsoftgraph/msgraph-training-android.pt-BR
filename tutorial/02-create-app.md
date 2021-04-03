<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="f037c-101">Comece criando um novo projeto do Android Studio.</span><span class="sxs-lookup"><span data-stu-id="f037c-101">Begin by creating a new Android Studio project.</span></span>

1. <span data-ttu-id="f037c-102">Abra o Android Studio e selecione **Iniciar um novo projeto do Android Studio** na tela de boas-vindas.</span><span class="sxs-lookup"><span data-stu-id="f037c-102">Open Android Studio, and select **Start a new Android Studio project** on the welcome screen.</span></span>

1. <span data-ttu-id="f037c-103">Na caixa de diálogo Criar **Novo Projeto,** selecione **Atividade Vazia** e, em seguida, selecione **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="f037c-103">In the **Create New Project** dialog, select **Empty Activity**, then select **Next**.</span></span>

    ![Uma captura de tela da caixa de diálogo Criar Novo Projeto no Android Studio](./images/choose-project.png)

1. <span data-ttu-id="f037c-105">Na caixa **de diálogo Configurar seu** projeto, de configure o **nome** como , verifique se o campo Idioma está definido como , e verifique se o nível `Graph Tutorial` mínimo da API está definido como  `Java`  `API 29: Android 10.0 (Q)` .</span><span class="sxs-lookup"><span data-stu-id="f037c-105">In the **Configure your project** dialog, set the **Name** to `Graph Tutorial`, ensure the **Language** field is set to `Java`, and ensure the **Minimum API level** is set to `API 29: Android 10.0 (Q)`.</span></span> <span data-ttu-id="f037c-106">Modifique **o nome do pacote e** salve o local **conforme** necessário.</span><span class="sxs-lookup"><span data-stu-id="f037c-106">Modify the **Package name** and **Save location** as needed.</span></span> <span data-ttu-id="f037c-107">Selecione **Concluir**.</span><span class="sxs-lookup"><span data-stu-id="f037c-107">Select **Finish**.</span></span>

    ![Uma captura de tela da caixa de diálogo Configurar seu projeto](./images/configure-project.png)

> [!IMPORTANT]
> <span data-ttu-id="f037c-109">O código e as instruções neste tutorial usam o nome do pacote **com.example.graphtutorial**.</span><span class="sxs-lookup"><span data-stu-id="f037c-109">The code and instructions in this tutorial use the package name **com.example.graphtutorial**.</span></span> <span data-ttu-id="f037c-110">Se você usar um nome de pacote diferente ao criar o projeto, certifique-se de usar o nome do pacote onde quer que você veja esse valor.</span><span class="sxs-lookup"><span data-stu-id="f037c-110">If you use a different package name when creating the project, be sure to use your package name wherever you see this value.</span></span>

## <a name="install-dependencies"></a><span data-ttu-id="f037c-111">Instalar dependências</span><span class="sxs-lookup"><span data-stu-id="f037c-111">Install dependencies</span></span>

<span data-ttu-id="f037c-112">Antes de continuar, instale algumas dependências adicionais que você usará mais tarde.</span><span class="sxs-lookup"><span data-stu-id="f037c-112">Before moving on, install some additional dependencies that you will use later.</span></span>

- <span data-ttu-id="f037c-113">`com.google.android.material:material` para disponibilizar o [modo de](https://material.io/develop/android/components/navigation-view/) exibição de navegação para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f037c-113">`com.google.android.material:material` to make the [navigation view](https://material.io/develop/android/components/navigation-view/) available to the app.</span></span>
- <span data-ttu-id="f037c-114">[Biblioteca de Autenticação da Microsoft (MSAL) para Android para](https://github.com/AzureAD/microsoft-authentication-library-for-android) lidar com a autenticação do Azure AD e o gerenciamento de tokens.</span><span class="sxs-lookup"><span data-stu-id="f037c-114">[Microsoft Authentication Library (MSAL) for Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) to handle Azure AD authentication and token management.</span></span>
- <span data-ttu-id="f037c-115">[SDK do Microsoft Graph para Java](https://github.com/microsoftgraph/msgraph-sdk-java) para fazer chamadas para o Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="f037c-115">[Microsoft Graph SDK for Java](https://github.com/microsoftgraph/msgraph-sdk-java) for making calls to the Microsoft Graph.</span></span>

1. <span data-ttu-id="f037c-116">Expanda **Scripts de Gradle** e abra **build.gradle (Módulo: Graph_Tutorial.app)**.</span><span class="sxs-lookup"><span data-stu-id="f037c-116">Expand **Gradle Scripts**, then open **build.gradle (Module: Graph_Tutorial.app)**.</span></span>

1. <span data-ttu-id="f037c-117">Adicione as seguintes linhas dentro do `dependencies` valor.</span><span class="sxs-lookup"><span data-stu-id="f037c-117">Add the following lines inside the `dependencies` value.</span></span>

    :::code language="gradle" source="../demo/GraphTutorial/app/build.gradle" id="DependenciesSnippet":::

1. <span data-ttu-id="f037c-118">Adicione um `packagingOptions` valor dentro do valor em `android` **build.gradle (Módulo: Graph_Tutorial.app)**.</span><span class="sxs-lookup"><span data-stu-id="f037c-118">Add a `packagingOptions` value inside the `android` value in **build.gradle (Module: Graph_Tutorial.app)**.</span></span>

    ```Gradle
    packagingOptions {
        pickFirst 'META-INF/*'
    }
    ```

1. <span data-ttu-id="f037c-119">Adicione o repositório do Azure Maven para a biblioteca MicrosoftDeviceSDK, uma dependência do MSAL.</span><span class="sxs-lookup"><span data-stu-id="f037c-119">Add the Azure Maven repository for the MicrosoftDeviceSDK library, a dependency of MSAL.</span></span> <span data-ttu-id="f037c-120">Abra **build.gradle (Project: Graph_Tutorial)**.</span><span class="sxs-lookup"><span data-stu-id="f037c-120">Open **build.gradle (Project: Graph_Tutorial)**.</span></span> <span data-ttu-id="f037c-121">Adicione o seguinte ao `repositories` valor dentro do `allprojects` valor.</span><span class="sxs-lookup"><span data-stu-id="f037c-121">Add the following to the `repositories` value inside the `allprojects` value.</span></span>

    ```Gradle
    maven {
        url 'https://pkgs.dev.azure.com/MicrosoftDeviceSDK/DuoSDK-Public/_packaging/Duo-SDK-Feed/maven/v1'
    }
    ```

1. <span data-ttu-id="f037c-122">Salve suas alterações.</span><span class="sxs-lookup"><span data-stu-id="f037c-122">Save your changes.</span></span> <span data-ttu-id="f037c-123">No menu **Arquivo,** selecione **Sincronizar Projeto com Arquivos gradle**.</span><span class="sxs-lookup"><span data-stu-id="f037c-123">On the **File** menu, select **Sync Project with Gradle Files**.</span></span>

## <a name="design-the-app"></a><span data-ttu-id="f037c-124">Design do aplicativo</span><span class="sxs-lookup"><span data-stu-id="f037c-124">Design the app</span></span>

<span data-ttu-id="f037c-125">O aplicativo usará uma gaveta de navegação para navegar entre diferentes exibições.</span><span class="sxs-lookup"><span data-stu-id="f037c-125">The application will use a navigation drawer to navigate between different views.</span></span> <span data-ttu-id="f037c-126">Nesta etapa, você atualizará a atividade para usar um layout da gaveta de navegação e adicionará fragmentos para os exibições.</span><span class="sxs-lookup"><span data-stu-id="f037c-126">In this step you will update the activity to use a navigation drawer layout, and add fragments for the views.</span></span>

### <a name="create-a-navigation-drawer"></a><span data-ttu-id="f037c-127">Criar uma gaveta de navegação</span><span class="sxs-lookup"><span data-stu-id="f037c-127">Create a navigation drawer</span></span>

<span data-ttu-id="f037c-128">Nesta seção, você criará ícones para o menu de navegação do aplicativo, criará um menu para o aplicativo e atualizará o tema e o layout do aplicativo para ser compatível com uma gaveta de navegação.</span><span class="sxs-lookup"><span data-stu-id="f037c-128">In this section you will create icons for the app's navigation menu, create a menu for the application, and update the application's theme and layout to be compatible with a navigation drawer.</span></span>

#### <a name="create-icons"></a><span data-ttu-id="f037c-129">Criar ícones</span><span class="sxs-lookup"><span data-stu-id="f037c-129">Create icons</span></span>

1. <span data-ttu-id="f037c-130">Clique com o botão direito do **mouse na pasta app/res/drawable** e selecione **Novo**, em seguida, **Vector Asset**.</span><span class="sxs-lookup"><span data-stu-id="f037c-130">Right-click the **app/res/drawable** folder and select **New**, then **Vector Asset**.</span></span>

1. <span data-ttu-id="f037c-131">Clique no botão ícone ao lado de **Clip Art**.</span><span class="sxs-lookup"><span data-stu-id="f037c-131">Click the icon button next to **Clip Art**.</span></span>

1. <span data-ttu-id="f037c-132">Na janela **Selecionar Ícone,** digite na barra de pesquisa e selecione o `home` **ícone** Home e selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="f037c-132">In the **Select Icon** window, type `home` in the search bar, then select the **Home** icon and select **OK**.</span></span>

1. <span data-ttu-id="f037c-133">Altere **o nome** para `ic_menu_home` .</span><span class="sxs-lookup"><span data-stu-id="f037c-133">Change the **Name** to `ic_menu_home`.</span></span>

    ![Uma captura de tela da janela Configurar Ativo Vetor](./images/create-icon.png)

1. <span data-ttu-id="f037c-135">Selecione **Próximo** **e,** em seguida, Concluir .</span><span class="sxs-lookup"><span data-stu-id="f037c-135">Select **Next**, then **Finish**.</span></span>

1. <span data-ttu-id="f037c-136">Repita a etapa anterior para criar mais quatro ícones.</span><span class="sxs-lookup"><span data-stu-id="f037c-136">Repeat the previous step to create four more icons.</span></span>

    - <span data-ttu-id="f037c-137">Nome: `ic_menu_calendar` , Ícone: `event`</span><span class="sxs-lookup"><span data-stu-id="f037c-137">Name: `ic_menu_calendar`, Icon: `event`</span></span>
    - <span data-ttu-id="f037c-138">Nome: `ic_menu_add_event` , Ícone: `add box`</span><span class="sxs-lookup"><span data-stu-id="f037c-138">Name: `ic_menu_add_event`, Icon: `add box`</span></span>
    - <span data-ttu-id="f037c-139">Nome: `ic_menu_signout` , Ícone: `exit to app`</span><span class="sxs-lookup"><span data-stu-id="f037c-139">Name: `ic_menu_signout`, Icon: `exit to app`</span></span>
    - <span data-ttu-id="f037c-140">Nome: `ic_menu_signin` , Ícone: `person add`</span><span class="sxs-lookup"><span data-stu-id="f037c-140">Name: `ic_menu_signin`, Icon: `person add`</span></span>

#### <a name="create-the-menu"></a><span data-ttu-id="f037c-141">Criar o menu</span><span class="sxs-lookup"><span data-stu-id="f037c-141">Create the menu</span></span>

1. <span data-ttu-id="f037c-142">Clique com o botão direito do mouse **na pasta res** e selecione **Novo**, em seguida, Diretório de Recursos **do Android**.</span><span class="sxs-lookup"><span data-stu-id="f037c-142">Right-click the **res** folder and select **New**, then **Android Resource Directory**.</span></span>

1. <span data-ttu-id="f037c-143">Altere **o tipo de recurso** para e selecione `menu` **OK**.</span><span class="sxs-lookup"><span data-stu-id="f037c-143">Change the **Resource type** to `menu` and select **OK**.</span></span>

1. <span data-ttu-id="f037c-144">Clique com o botão direito do mouse na nova **pasta de menu** e selecione **Novo**, em **seguida, menu arquivo de recurso**.</span><span class="sxs-lookup"><span data-stu-id="f037c-144">Right-click the new **menu** folder and select **New**, then **Menu resource file**.</span></span>

1. <span data-ttu-id="f037c-145">Nomeia o `drawer_menu` arquivo e selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="f037c-145">Name the file `drawer_menu` and select **OK**.</span></span>

1. <span data-ttu-id="f037c-146">Quando o arquivo for aberto, selecione a guia **Código** para exibir o XML e substitua todo o conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="f037c-146">When the file opens, select the **Code** tab to view the XML, then replace the entire contents with the following.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/menu/drawer_menu.xml":::

#### <a name="update-application-theme-and-layout"></a><span data-ttu-id="f037c-147">Atualizar o tema e o layout do aplicativo</span><span class="sxs-lookup"><span data-stu-id="f037c-147">Update application theme and layout</span></span>

1. <span data-ttu-id="f037c-148">Abra o **arquivo app/res/values/themes.xml** e adicione as seguintes linhas dentro do `style` elemento.</span><span class="sxs-lookup"><span data-stu-id="f037c-148">Open the **app/res/values/themes.xml** file and add the following lines inside the `style` element.</span></span>

    ```xml
    <item name="windowActionBar">false</item>
    <item name="windowNoTitle">true</item>
    ```

1. <span data-ttu-id="f037c-149">Abra o **arquivo app/res/values-night/themes.xml** e adicione as seguintes linhas dentro do `style` elemento.</span><span class="sxs-lookup"><span data-stu-id="f037c-149">Open the **app/res/values-night/themes.xml** file and add the following lines inside the `style` element.</span></span>

    ```xml
    <item name="windowActionBar">false</item>
    <item name="windowNoTitle">true</item>
    ```

1. <span data-ttu-id="f037c-150">Clique com o botão direito **do mouse na pasta aplicativo/res/layout.**</span><span class="sxs-lookup"><span data-stu-id="f037c-150">Right-click the **app/res/layout** folder.</span></span>

1. <span data-ttu-id="f037c-151">Selecione **Novo** e, em seguida, **arquivo de recurso layout.**</span><span class="sxs-lookup"><span data-stu-id="f037c-151">Select **New**, then **Layout resource file**.</span></span>

1. <span data-ttu-id="f037c-152">Nomee o `nav_header` arquivo e altere **o elemento Root** para , `LinearLayout` em seguida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="f037c-152">Name the file `nav_header` and change the **Root element** to `LinearLayout`, then select **OK**.</span></span>

1. <span data-ttu-id="f037c-153">Abra o **nav_header.xml** e selecione a **guia** Código. Substitua todo o conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="f037c-153">Open the **nav_header.xml** file and select the **Code** tab. Replace the entire contents with the following.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/nav_header.xml":::

1. <span data-ttu-id="f037c-154">Abra o **arquivo app/res/layout/activity_main.xml** e atualize o layout para um substituindo o `DrawerLayout` XML existente pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="f037c-154">Open the **app/res/layout/activity_main.xml** file and update the layout to a `DrawerLayout` by replacing the existing XML with the following.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/activity_main.xml":::

1. <span data-ttu-id="f037c-155">Abra **app/res/values/strings.xml** e adicione os seguintes elementos dentro do `resources` elemento.</span><span class="sxs-lookup"><span data-stu-id="f037c-155">Open **app/res/values/strings.xml** and add the following elements inside the `resources` element.</span></span>

    ```xml
    <string name="navigation_drawer_open">Open navigation drawer</string>
    <string name="navigation_drawer_close">Close navigation drawer</string>
    ```

1. <span data-ttu-id="f037c-156">Abra o **arquivo app/java/com.example/graphtutorial/MainActivity** e substitua todo o conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="f037c-156">Open the **app/java/com.example/graphtutorial/MainActivity** file and replace the entire contents with the following.</span></span>

    ```java
    package com.example.graphtutorial;

    import android.os.Bundle;
    import android.view.Menu;
    import android.view.MenuItem;
    import android.view.View;
    import android.widget.FrameLayout;
    import android.widget.ProgressBar;
    import android.widget.TextView;
    import androidx.annotation.NonNull;
    import androidx.appcompat.app.ActionBarDrawerToggle;
    import androidx.appcompat.app.AppCompatActivity;
    import androidx.appcompat.widget.Toolbar;
    import androidx.core.view.GravityCompat;
    import androidx.drawerlayout.widget.DrawerLayout;
    import com.google.android.material.navigation.NavigationView;

    public class MainActivity extends AppCompatActivity implements NavigationView.OnNavigationItemSelectedListener {
        private static final String SAVED_IS_SIGNED_IN = "isSignedIn";
        private static final String SAVED_USER_NAME = "userName";
        private static final String SAVED_USER_EMAIL = "userEmail";
        private static final String SAVED_USER_TIMEZONE = "userTimeZone";

        private DrawerLayout mDrawer;
        private NavigationView mNavigationView;
        private View mHeaderView;
        private boolean mIsSignedIn = false;
        private String mUserName = null;
        private String mUserEmail = null;
        private String mUserTimeZone = null;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            // Set the toolbar
            Toolbar toolbar = findViewById(R.id.toolbar);
            setSupportActionBar(toolbar);

            mDrawer = findViewById(R.id.drawer_layout);

            // Add the hamburger menu icon
            ActionBarDrawerToggle toggle = new ActionBarDrawerToggle(this, mDrawer, toolbar,
                    R.string.navigation_drawer_open, R.string.navigation_drawer_close);
            mDrawer.addDrawerListener(toggle);
            toggle.syncState();

            mNavigationView = findViewById(R.id.nav_view);

            // Set user name and email
            mHeaderView = mNavigationView.getHeaderView(0);
            setSignedInState(mIsSignedIn);

            // Listen for item select events on menu
            mNavigationView.setNavigationItemSelectedListener(this);

            if (savedInstanceState == null) {
                // Load the home fragment by default on startup
                openHomeFragment(mUserName);
            } else {
                // Restore state
                mIsSignedIn = savedInstanceState.getBoolean(SAVED_IS_SIGNED_IN);
                mUserName = savedInstanceState.getString(SAVED_USER_NAME);
                mUserEmail = savedInstanceState.getString(SAVED_USER_EMAIL);
                mUserTimeZone = savedInstanceState.getString(SAVED_USER_TIMEZONE);
                setSignedInState(mIsSignedIn);
            }
        }

        @Override
        protected void onSaveInstanceState(@NonNull Bundle outState) {
            super.onSaveInstanceState(outState);
            outState.putBoolean(SAVED_IS_SIGNED_IN, mIsSignedIn);
            outState.putString(SAVED_USER_NAME, mUserName);
            outState.putString(SAVED_USER_EMAIL, mUserEmail);
            outState.putString(SAVED_USER_TIMEZONE, mUserTimeZone);
        }

        @Override
        public boolean onNavigationItemSelected(@NonNull MenuItem menuItem) {
            // TEMPORARY
            return false;
        }

        @Override
        public void onBackPressed() {
            if (mDrawer.isDrawerOpen(GravityCompat.START)) {
                mDrawer.closeDrawer(GravityCompat.START);
            } else {
                super.onBackPressed();
            }
        }

        public void showProgressBar()
        {
            FrameLayout container = findViewById(R.id.fragment_container);
            ProgressBar progressBar = findViewById(R.id.progressbar);
            container.setVisibility(View.GONE);
            progressBar.setVisibility(View.VISIBLE);
        }

        public void hideProgressBar()
        {
            FrameLayout container = findViewById(R.id.fragment_container);
            ProgressBar progressBar = findViewById(R.id.progressbar);
            progressBar.setVisibility(View.GONE);
            container.setVisibility(View.VISIBLE);
        }

        // Update the menu and get the user's name and email
        private void setSignedInState(boolean isSignedIn) {
            mIsSignedIn = isSignedIn;

            mNavigationView.getMenu().clear();
            mNavigationView.inflateMenu(R.menu.drawer_menu);

            Menu menu = mNavigationView.getMenu();

            // Hide/show the Sign in, Calendar, and Sign Out buttons
            if (isSignedIn) {
                menu.removeItem(R.id.nav_signin);
            } else {
                menu.removeItem(R.id.nav_home);
                menu.removeItem(R.id.nav_calendar);
                menu.removeItem(R.id.nav_create_event);
                menu.removeItem(R.id.nav_signout);
            }

            // Set the user name and email in the nav drawer
            TextView userName = mHeaderView.findViewById(R.id.user_name);
            TextView userEmail = mHeaderView.findViewById(R.id.user_email);

            if (isSignedIn) {
                // For testing
                mUserName = "Lynne Robbins";
                mUserEmail = "lynner@contoso.com";
                mUserTimeZone = "Pacific Standard Time";

                userName.setText(mUserName);
                userEmail.setText(mUserEmail);
            } else {
                mUserName = null;
                mUserEmail = null;
                mUserTimeZone = null;

                userName.setText("Please sign in");
                userEmail.setText("");
            }
        }
    }
    ```

### <a name="add-fragments"></a><span data-ttu-id="f037c-157">Adicionar fragmentos</span><span class="sxs-lookup"><span data-stu-id="f037c-157">Add fragments</span></span>

<span data-ttu-id="f037c-158">Nesta seção, você criará fragmentos para as exibições de início e calendário.</span><span class="sxs-lookup"><span data-stu-id="f037c-158">In this section you will create fragments for the home and calendar views.</span></span>

1. <span data-ttu-id="f037c-159">Clique com o botão direito do **mouse na pasta aplicativo/res/layout** e selecione **Novo**, em **seguida, Arquivo de recurso layout.**</span><span class="sxs-lookup"><span data-stu-id="f037c-159">Right-click the **app/res/layout** folder and select **New**, then **Layout resource file**.</span></span>

1. <span data-ttu-id="f037c-160">Nomee o `fragment_home` arquivo e altere **o elemento Root** para , `RelativeLayout` em seguida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="f037c-160">Name the file `fragment_home` and change the **Root element** to `RelativeLayout`, then select **OK**.</span></span>

1. <span data-ttu-id="f037c-161">Abra o **arquivofragment_home.xml** e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="f037c-161">Open the **fragment_home.xml** file and replace its contents with the following.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/fragment_home.xml":::

1. <span data-ttu-id="f037c-162">Clique com o botão direito do **mouse na pasta aplicativo/res/layout** e selecione **Novo**, em **seguida, Arquivo de recurso layout.**</span><span class="sxs-lookup"><span data-stu-id="f037c-162">Right-click the **app/res/layout** folder and select **New**, then **Layout resource file**.</span></span>

1. <span data-ttu-id="f037c-163">Nomee o `fragment_calendar` arquivo e altere **o elemento Root** para , `RelativeLayout` em seguida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="f037c-163">Name the file `fragment_calendar` and change the **Root element** to `RelativeLayout`, then select **OK**.</span></span>

1. <span data-ttu-id="f037c-164">Abra o **arquivofragment_calendar.xml** e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="f037c-164">Open the **fragment_calendar.xml** file and replace its contents with the following.</span></span>

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_centerInParent="true"
            android:text="Calendar"
            android:textSize="30sp" />

    </RelativeLayout>
    ```

1. <span data-ttu-id="f037c-165">Clique com o botão direito do **mouse na pasta aplicativo/res/layout** e selecione **Novo**, em **seguida, Arquivo de recurso layout.**</span><span class="sxs-lookup"><span data-stu-id="f037c-165">Right-click the **app/res/layout** folder and select **New**, then **Layout resource file**.</span></span>

1. <span data-ttu-id="f037c-166">Nomee o `fragment_new_event` arquivo e altere **o elemento Root** para , `RelativeLayout` em seguida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="f037c-166">Name the file `fragment_new_event` and change the **Root element** to `RelativeLayout`, then select **OK**.</span></span>

1. <span data-ttu-id="f037c-167">Abra o **arquivofragment_new_event.xml** e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="f037c-167">Open the **fragment_new_event.xml** file and replace its contents with the following.</span></span>

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_centerInParent="true"
            android:text="New Event"
            android:textSize="30sp" />

    </RelativeLayout>
    ```

1. <span data-ttu-id="f037c-168">Clique com o botão direito do **mouse na pasta app/java/com.example.graphtutorial** e selecione **Novo**, depois **Java Classe**.</span><span class="sxs-lookup"><span data-stu-id="f037c-168">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span>

1. <span data-ttu-id="f037c-169">Nomeia a `HomeFragment` classe e selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="f037c-169">Name the class `HomeFragment`, then select **OK**.</span></span>

1. <span data-ttu-id="f037c-170">Abra o **arquivo HomeFragment** e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="f037c-170">Open the **HomeFragment** file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/HomeFragment.java" id="HomeSnippet":::

1. <span data-ttu-id="f037c-171">Clique com o botão direito do **mouse na pasta app/java/com.example.graphtutorial** e selecione **Novo**, depois **Java Classe**.</span><span class="sxs-lookup"><span data-stu-id="f037c-171">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span>

1. <span data-ttu-id="f037c-172">Nomeia a `CalendarFragment` classe e selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="f037c-172">Name the class `CalendarFragment`, then select **OK**.</span></span>

1. <span data-ttu-id="f037c-173">Abra o **arquivo CalendarFragment** e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="f037c-173">Open the **CalendarFragment** file and replace its contents with the following.</span></span>

    ```java
    package com.example.graphtutorial;

    import android.os.Bundle;
    import android.view.LayoutInflater;
    import android.view.View;
    import android.view.ViewGroup;
    import androidx.annotation.NonNull;
    import androidx.annotation.Nullable;
    import androidx.fragment.app.Fragment;

    public class CalendarFragment extends Fragment {
        private static final String TIME_ZONE = "timeZone";

        private String mTimeZone;

        public CalendarFragment() {}

        public static CalendarFragment createInstance(String timeZone) {
            CalendarFragment fragment = new CalendarFragment();

            // Add the provided time zone to the fragment's arguments
            Bundle args = new Bundle();
            args.putString(TIME_ZONE, timeZone);
            fragment.setArguments(args);
            return fragment;
        }

        @Override
        public void onCreate(@Nullable Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            if (getArguments() != null) {
                mTimeZone = getArguments().getString(TIME_ZONE);
            }
        }

        @Nullable
        @Override
        public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
            return inflater.inflate(R.layout.fragment_calendar, container, false);
        }
    }
    ```

1. <span data-ttu-id="f037c-174">Clique com o botão direito do **mouse na pasta app/java/com.example.graphtutorial** e selecione **Novo**, depois **Java Classe**.</span><span class="sxs-lookup"><span data-stu-id="f037c-174">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span>

1. <span data-ttu-id="f037c-175">Nomeia a `NewEventFragment` classe e selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="f037c-175">Name the class `NewEventFragment`, then select **OK**.</span></span>

1. <span data-ttu-id="f037c-176">Abra o **arquivo NewEventFragment** e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="f037c-176">Open the **NewEventFragment** file and replace its contents with the following.</span></span>

    ```java
    package com.example.graphtutorial;

    import android.os.Bundle;
    import android.view.LayoutInflater;
    import android.view.View;
    import android.view.ViewGroup;
    import androidx.annotation.NonNull;
    import androidx.annotation.Nullable;
    import androidx.fragment.app.Fragment;

    public class NewEventFragment extends Fragment {
        private static final String TIME_ZONE = "timeZone";

        private String mTimeZone;

        public NewEventFragment() {}

        public static NewEventFragment createInstance(String timeZone) {
            NewEventFragment fragment = new NewEventFragment();

            // Add the provided time zone to the fragment's arguments
            Bundle args = new Bundle();
            args.putString(TIME_ZONE, timeZone);
            fragment.setArguments(args);
            return fragment;
        }

        @Override
        public void onCreate(@Nullable Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            if (getArguments() != null) {
                mTimeZone = getArguments().getString(TIME_ZONE);
            }
        }

        @Nullable
        @Override
        public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
            return inflater.inflate(R.layout.fragment_new_event, container, false);
        }
    }
    ```

1. <span data-ttu-id="f037c-177">Abra o **arquivo MainActivity.java** e adicione as seguintes funções à classe.</span><span class="sxs-lookup"><span data-stu-id="f037c-177">Open the **MainActivity.java** file and add the the following functions to the class.</span></span>

    ```java
    // Load the "Home" fragment
    public void openHomeFragment(String userName) {
        HomeFragment fragment = HomeFragment.createInstance(userName);
        getSupportFragmentManager().beginTransaction()
                .replace(R.id.fragment_container, fragment)
                .commit();
        mNavigationView.setCheckedItem(R.id.nav_home);
    }

    // Load the "Calendar" fragment
    private void openCalendarFragment(String timeZone) {
        CalendarFragment fragment = CalendarFragment.createInstance(timeZone);
        getSupportFragmentManager().beginTransaction()
                .replace(R.id.fragment_container, fragment)
                .commit();
        mNavigationView.setCheckedItem(R.id.nav_calendar);
    }

    // Load the "New Event" fragment
    private void openNewEventFragment(String timeZone) {
        NewEventFragment fragment = NewEventFragment.createInstance(timeZone);
        getSupportFragmentManager().beginTransaction()
                .replace(R.id.fragment_container, fragment)
                .commit();
        mNavigationView.setCheckedItem(R.id.nav_create_event);
    }

    private void signIn() {
        setSignedInState(true);
        openHomeFragment(mUserName);
    }

    private void signOut() {
        setSignedInState(false);
        openHomeFragment(mUserName);
    }
    ```

1. <span data-ttu-id="f037c-178">Substitua a função `onNavigationItemSelected` existente pela seguinte.</span><span class="sxs-lookup"><span data-stu-id="f037c-178">Replace the existing `onNavigationItemSelected` function with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="OnNavItemSelectedSnippet":::

1. <span data-ttu-id="f037c-179">Salve todas as alterações.</span><span class="sxs-lookup"><span data-stu-id="f037c-179">Save all of your changes.</span></span>

1. <span data-ttu-id="f037c-180">No menu **Executar,** selecione **Executar 'app'**.</span><span class="sxs-lookup"><span data-stu-id="f037c-180">On the **Run** menu, select **Run 'app'**.</span></span>

<span data-ttu-id="f037c-181">O menu do aplicativo deve funcionar para navegar entre os  dois fragmentos e alterar quando você tocar nos botões Entrar ou **Sair.**</span><span class="sxs-lookup"><span data-stu-id="f037c-181">The app's menu should work to navigate between the two fragments and change when you tap the **Sign in** or **Sign out** buttons.</span></span>

![Captura de tela do aplicativo](./images/app-screens.png)
