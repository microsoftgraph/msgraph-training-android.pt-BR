<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="6ca88-101">Comece criando um novo projeto do Android Studio.</span><span class="sxs-lookup"><span data-stu-id="6ca88-101">Begin by creating a new Android Studio project.</span></span>

1. <span data-ttu-id="6ca88-102">Abra Android Studio e selecione **Iniciar um novo projeto do Android Studio** na tela de boas-vindas.</span><span class="sxs-lookup"><span data-stu-id="6ca88-102">Open Android Studio, and select **Start a new Android Studio project** on the welcome screen.</span></span>

1. <span data-ttu-id="6ca88-103">Na caixa de diálogo **criar novo projeto** , selecione **atividade vazia**e, em seguida, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="6ca88-103">In the **Create New Project** dialog, select **Empty Activity**, then select **Next**.</span></span>

    ![Uma captura de tela da caixa de diálogo Criar novo projeto no Android Studio](./images/choose-project.png)

1. <span data-ttu-id="6ca88-105">Na caixa de diálogo **Configurar o projeto** , defina \*\*\*\* o nome `Graph Tutorial`como, verifique se o campo de idioma `Java`está definido como e se o nível de **API mínimo** está definido como. \*\*\*\* `API 29: Android 10.0 (Q)`</span><span class="sxs-lookup"><span data-stu-id="6ca88-105">In the **Configure your project** dialog, set the **Name** to `Graph Tutorial`, ensure the **Language** field is set to `Java`, and ensure the **Minimum API level** is set to `API 29: Android 10.0 (Q)`.</span></span> <span data-ttu-id="6ca88-106">Modifique o **nome do pacote** e **salve o local** conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="6ca88-106">Modify the **Package name** and **Save location** as needed.</span></span> <span data-ttu-id="6ca88-107">Selecione **Concluir**.</span><span class="sxs-lookup"><span data-stu-id="6ca88-107">Select **Finish**.</span></span>

    ![Uma captura de tela da caixa de diálogo Configurar seu projeto](./images/configure-project.png)

> [!IMPORTANT]
> <span data-ttu-id="6ca88-109">O código e as instruções neste tutorial usam o nome de pacote **com. example. graphtutorial**.</span><span class="sxs-lookup"><span data-stu-id="6ca88-109">The code and instructions in this tutorial use the package name **com.example.graphtutorial**.</span></span> <span data-ttu-id="6ca88-110">Se você usar um nome de pacote diferente ao criar o projeto, certifique-se de usar o nome do pacote onde quer que você veja esse valor.</span><span class="sxs-lookup"><span data-stu-id="6ca88-110">If you use a different package name when creating the project, be sure to use your package name wherever you see this value.</span></span>

## <a name="install-dependencies"></a><span data-ttu-id="6ca88-111">Instalar dependências</span><span class="sxs-lookup"><span data-stu-id="6ca88-111">Install dependencies</span></span>

<span data-ttu-id="6ca88-112">Antes de prosseguir, instale algumas dependências adicionais que serão usadas posteriormente.</span><span class="sxs-lookup"><span data-stu-id="6ca88-112">Before moving on, install some additional dependencies that you will use later.</span></span>

- <span data-ttu-id="6ca88-113">`com.google.android.material:material`para tornar o [modo de exibição de navegação](https://material.io/develop/android/components/navigation-view/) disponível para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6ca88-113">`com.google.android.material:material` to make the [navigation view](https://material.io/develop/android/components/navigation-view/) available to the app.</span></span>
- <span data-ttu-id="6ca88-114">A [biblioteca de autenticação da Microsoft (MSAL) para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) para lidar com a autenticação do Azure AD e o gerenciamento de tokens.</span><span class="sxs-lookup"><span data-stu-id="6ca88-114">[Microsoft Authentication Library (MSAL) for Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) to handle Azure AD authentication and token management.</span></span>
- <span data-ttu-id="6ca88-115">[SDK do Microsoft Graph para Java](https://github.com/microsoftgraph/msgraph-sdk-java) para fazer chamadas para o Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="6ca88-115">[Microsoft Graph SDK for Java](https://github.com/microsoftgraph/msgraph-sdk-java) for making calls to the Microsoft Graph.</span></span>

1. <span data-ttu-id="6ca88-116">Expanda **scripts do gradle**e, em seguida, abra o arquivo **Build. gradle (módulo: app)** .</span><span class="sxs-lookup"><span data-stu-id="6ca88-116">Expand **Gradle Scripts**, then open the **build.gradle (Module: app)** file.</span></span>

1. <span data-ttu-id="6ca88-117">Adicione as seguintes linhas dentro do `dependencies` valor.</span><span class="sxs-lookup"><span data-stu-id="6ca88-117">Add the following lines inside the `dependencies` value.</span></span>

    ```Gradle
    implementation 'com.google.android.material:material:1.0.0'
    implementation 'com.microsoft.identity.client:msal:1.0.0'
    implementation 'com.microsoft.graph:microsoft-graph:1.6.0'
    ```

1. <span data-ttu-id="6ca88-118">Adicione um `packagingOptions` valor dentro do `android` valor no arquivo **Build. gradle (Module: app)** .</span><span class="sxs-lookup"><span data-stu-id="6ca88-118">Add a `packagingOptions` value inside the `android` value in the **build.gradle (Module: app)** file.</span></span>

    ```Gradle
    packagingOptions {
        pickFirst 'META-INF/jersey-module-version'
    }
    ```

1. <span data-ttu-id="6ca88-119">Salve suas alterações.</span><span class="sxs-lookup"><span data-stu-id="6ca88-119">Save your changes.</span></span> <span data-ttu-id="6ca88-120">No menu **arquivo** , selecione **sincronizar projeto com arquivos do gradle**.</span><span class="sxs-lookup"><span data-stu-id="6ca88-120">On the **File** menu, select **Sync Project with Gradle Files**.</span></span>

## <a name="design-the-app"></a><span data-ttu-id="6ca88-121">Projetar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="6ca88-121">Design the app</span></span>

<span data-ttu-id="6ca88-122">O aplicativo usará uma gaveta de navegação para navegar entre diferentes modos de exibição.</span><span class="sxs-lookup"><span data-stu-id="6ca88-122">The application will use a navigation drawer to navigate between different views.</span></span> <span data-ttu-id="6ca88-123">Nesta etapa, você atualizará a atividade para usar um layout de gaveta de navegação e adicionará fragmentos para os modos de exibição.</span><span class="sxs-lookup"><span data-stu-id="6ca88-123">In this step you will update the activity to use a navigation drawer layout, and add fragments for the views.</span></span>

### <a name="create-a-navigation-drawer"></a><span data-ttu-id="6ca88-124">Criar uma gaveta de navegação</span><span class="sxs-lookup"><span data-stu-id="6ca88-124">Create a navigation drawer</span></span>

<span data-ttu-id="6ca88-125">Nesta seção, você criará ícones para o menu de navegação do aplicativo, criará um menu para o aplicativo e atualizará o tema e o layout do aplicativo para serem compatíveis com uma gaveta de navegação.</span><span class="sxs-lookup"><span data-stu-id="6ca88-125">In this section you will create icons for the app's navigation menu, create a menu for the application, and update the application's theme and layout to be compatible with a navigation drawer.</span></span>

#### <a name="create-icons"></a><span data-ttu-id="6ca88-126">Criar ícones</span><span class="sxs-lookup"><span data-stu-id="6ca88-126">Create icons</span></span>

1. <span data-ttu-id="6ca88-127">Clique com o botão direito do mouse na pasta **app/res/Drawable** e selecione **novo**e, em seguida, **ativo vetorial**.</span><span class="sxs-lookup"><span data-stu-id="6ca88-127">Right-click the **app/res/drawable** folder and select **New**, then **Vector Asset**.</span></span>

1. <span data-ttu-id="6ca88-128">Clique no botão de ícone ao lado de **Clip-Art**.</span><span class="sxs-lookup"><span data-stu-id="6ca88-128">Click the icon button next to **Clip Art**.</span></span>

1. <span data-ttu-id="6ca88-129">Na janela **selecionar ícone** , digite `home` na barra de pesquisa e, em seguida, selecione o ícone **página inicial** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="6ca88-129">In the **Select Icon** window, type `home` in the search bar, then select the **Home** icon and select **OK**.</span></span>

1. <span data-ttu-id="6ca88-130">Altere o **nome** para `ic_menu_home`.</span><span class="sxs-lookup"><span data-stu-id="6ca88-130">Change the **Name** to `ic_menu_home`.</span></span>

    ![Uma captura de tela da janela Configurar vetor de ativos](./images/create-icon.png)

1. <span data-ttu-id="6ca88-132">Escolha **Avançar**e **concluir**.</span><span class="sxs-lookup"><span data-stu-id="6ca88-132">Select **Next**, then **Finish**.</span></span>

1. <span data-ttu-id="6ca88-133">Repita a etapa anterior para criar mais três ícones.</span><span class="sxs-lookup"><span data-stu-id="6ca88-133">Repeat the previous step to create three more icons.</span></span>

    - <span data-ttu-id="6ca88-134">Nome: `ic_menu_calendar`, ícone:`event`</span><span class="sxs-lookup"><span data-stu-id="6ca88-134">Name: `ic_menu_calendar`, Icon: `event`</span></span>
    - <span data-ttu-id="6ca88-135">Nome: `ic_menu_signout`, ícone:`exit to app`</span><span class="sxs-lookup"><span data-stu-id="6ca88-135">Name: `ic_menu_signout`, Icon: `exit to app`</span></span>
    - <span data-ttu-id="6ca88-136">Nome: `ic_menu_signin`, ícone:`person add`</span><span class="sxs-lookup"><span data-stu-id="6ca88-136">Name: `ic_menu_signin`, Icon: `person add`</span></span>

#### <a name="create-the-menu"></a><span data-ttu-id="6ca88-137">Criar o menu</span><span class="sxs-lookup"><span data-stu-id="6ca88-137">Create the menu</span></span>

1. <span data-ttu-id="6ca88-138">Clique com o botão direito do mouse na pasta **res** e selecione **novo**e, em seguida, **diretório de recursos do Android**.</span><span class="sxs-lookup"><span data-stu-id="6ca88-138">Right-click the **res** folder and select **New**, then **Android Resource Directory**.</span></span>

1. <span data-ttu-id="6ca88-139">Altere o **tipo** de recurso `menu` para e selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="6ca88-139">Change the **Resource type** to `menu` and select **OK**.</span></span>

1. <span data-ttu-id="6ca88-140">Clique com o botão direito do mouse na nova pasta de **menu** e selecione **novo**e, em seguida, **arquivo de recurso de menu**.</span><span class="sxs-lookup"><span data-stu-id="6ca88-140">Right-click the new **menu** folder and select **New**, then **Menu resource file**.</span></span>

1. <span data-ttu-id="6ca88-141">Nomeie o arquivo `drawer_menu` e selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="6ca88-141">Name the file `drawer_menu` and select **OK**.</span></span>

1. <span data-ttu-id="6ca88-142">Quando o arquivo for aberto, selecione a guia **texto** para exibir o XML e substitua todo o conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="6ca88-142">When the file opens, select the **Text** tab to view the XML, then replace the entire contents with the following.</span></span>

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <menu xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        tools:showIn="navigation_view">

        <group android:checkableBehavior="single">
            <item
                android:id="@+id/nav_home"
                android:icon="@drawable/ic_menu_home"
                android:title="Home" />

            <item
                android:id="@+id/nav_calendar"
                android:icon="@drawable/ic_menu_calendar"
                android:title="Calendar" />
        </group>

        <item android:title="Account">
            <menu>
                <item
                    android:id="@+id/nav_signin"
                    android:icon="@drawable/ic_menu_signin"
                    android:title="Sign In" />

                <item
                    android:id="@+id/nav_signout"
                    android:icon="@drawable/ic_menu_signout"
                    android:title="Sign Out" />
            </menu>
        </item>

    </menu>
    ```

#### <a name="update-application-theme-and-layout"></a><span data-ttu-id="6ca88-143">Atualizar tema e layout do aplicativo</span><span class="sxs-lookup"><span data-stu-id="6ca88-143">Update application theme and layout</span></span>

1. <span data-ttu-id="6ca88-144">Abra o arquivo **app/res/Values/Styles. xml** e substitua `Theme.AppCompat.Light.DarkActionBar` por `Theme.AppCompat.Light.NoActionBar`.</span><span class="sxs-lookup"><span data-stu-id="6ca88-144">Open the **app/res/values/styles.xml** file and replace `Theme.AppCompat.Light.DarkActionBar` with `Theme.AppCompat.Light.NoActionBar`.</span></span>

1. <span data-ttu-id="6ca88-145">Adicione as seguintes linhas dentro do `style` elemento.</span><span class="sxs-lookup"><span data-stu-id="6ca88-145">Add the following lines inside the `style` element.</span></span>

    ```xml
    <item name="windowActionBar">false</item>
    <item name="windowNoTitle">true</item>
    <item name="android:statusBarColor">@android:color/transparent</item>
    ```

1. <span data-ttu-id="6ca88-146">Clique com o botão direito do mouse na pasta **app/res/layout** .</span><span class="sxs-lookup"><span data-stu-id="6ca88-146">Right-click the **app/res/layout** folder.</span></span>

1. <span data-ttu-id="6ca88-147">Selecione **novo**e, em seguida, **arquivo de recurso de layout**.</span><span class="sxs-lookup"><span data-stu-id="6ca88-147">Select **New**, then **Layout resource file**.</span></span>

1. <span data-ttu-id="6ca88-148">Nomeie o arquivo `nav_header` e altere o **elemento raiz** para `LinearLayout`e, em seguida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="6ca88-148">Name the file `nav_header` and change the **Root element** to `LinearLayout`, then select **OK**.</span></span>

1. <span data-ttu-id="6ca88-149">Abra o arquivo **nav_header. xml** e selecione a guia **texto** . Substitua todo o conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="6ca88-149">Open the **nav_header.xml** file and select the **Text** tab. Replace the entire contents with the following.</span></span>

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="176dp"
        android:background="@color/colorPrimary"
        android:gravity="bottom"
        android:orientation="vertical"
        android:padding="16dp"
        android:theme="@style/ThemeOverlay.AppCompat.Dark">

        <ImageView
            android:id="@+id/user_profile_pic"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:src="@mipmap/ic_launcher" />

        <TextView
            android:id="@+id/user_name"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:paddingTop="8dp"
            android:text="Test User"
            android:textAppearance="@style/TextAppearance.AppCompat.Body1" />

        <TextView
            android:id="@+id/user_email"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="test@contoso.com" />

    </LinearLayout>
    ```

1. <span data-ttu-id="6ca88-150">Abra o arquivo **app/res/layout/activity_main. xml** e atualize o layout para um `DrawerLayout` substituindo o XML existente pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="6ca88-150">Open the **app/res/layout/activity_main.xml** file and update the layout to a `DrawerLayout` by replacing the existing XML with the following.</span></span>

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <androidx.drawerlayout.widget.DrawerLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        xmlns:tools="http://schemas.android.com/tools"
        android:id="@+id/drawer_layout"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:fitsSystemWindows="true"
        tools:context=".MainActivity"
        tools:openDrawer="start">

        <RelativeLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="vertical">

            <ProgressBar
                android:id="@+id/progressbar"
                android:layout_width="75dp"
                android:layout_height="75dp"
                android:layout_centerInParent="true"
                android:visibility="gone"/>

            <androidx.appcompat.widget.Toolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="?attr/actionBarSize"
                android:background="@color/colorPrimary"
                android:elevation="4dp"
                android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar" />

            <FrameLayout
                android:id="@+id/fragment_container"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:layout_below="@+id/toolbar" />
        </RelativeLayout>

        <com.google.android.material.navigation.NavigationView
            android:id="@+id/nav_view"
            android:layout_width="wrap_content"
            android:layout_height="match_parent"
            android:layout_gravity="start"
            app:headerLayout="@layout/nav_header"
            app:menu="@menu/drawer_menu" />

    </androidx.drawerlayout.widget.DrawerLayout>
    ```

1. <span data-ttu-id="6ca88-151">Abra **app/res/Values/Strings. xml** e adicione os seguintes elementos dentro `resources` do elemento.</span><span class="sxs-lookup"><span data-stu-id="6ca88-151">Open **app/res/values/strings.xml** and add the following elements inside the `resources` element.</span></span>

    ```xml
    <string name="navigation_drawer_open">Open navigation drawer</string>
    <string name="navigation_drawer_close">Close navigation drawer</string>
    ```

1. <span data-ttu-id="6ca88-152">Abra o arquivo **app/Java/com. example/graphtutorial/MainActivity** e substitua todo o conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="6ca88-152">Open the **app/java/com.example/graphtutorial/MainActivity** file and replace the entire contents with the following.</span></span>

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
        private DrawerLayout mDrawer;
        private NavigationView mNavigationView;
        private View mHeaderView;
        private boolean mIsSignedIn = false;
        private String mUserName = null;
        private String mUserEmail = null;

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

            Menu menu = mNavigationView.getMenu();

            // Hide/show the Sign in, Calendar, and Sign Out buttons
            menu.findItem(R.id.nav_signin).setVisible(!isSignedIn);
            menu.findItem(R.id.nav_calendar).setVisible(isSignedIn);
            menu.findItem(R.id.nav_signout).setVisible(isSignedIn);

            // Set the user name and email in the nav drawer
            TextView userName = mHeaderView.findViewById(R.id.user_name);
            TextView userEmail = mHeaderView.findViewById(R.id.user_email);

            if (isSignedIn) {
                // For testing
                mUserName = "Megan Bowen";
                mUserEmail = "meganb@contoso.com";

                userName.setText(mUserName);
                userEmail.setText(mUserEmail);
            } else {
                mUserName = null;
                mUserEmail = null;

                userName.setText("Please sign in");
                userEmail.setText("");
            }
        }
    }
    ```

### <a name="add-fragments"></a><span data-ttu-id="6ca88-153">Adicionar fragmentos</span><span class="sxs-lookup"><span data-stu-id="6ca88-153">Add fragments</span></span>

<span data-ttu-id="6ca88-154">Nesta seção, você criará fragmentos para os modos de exibição página inicial e calendário.</span><span class="sxs-lookup"><span data-stu-id="6ca88-154">In this section you will create fragments for the home and calendar views.</span></span>

1. <span data-ttu-id="6ca88-155">Clique com o botão direito do mouse na pasta **app/res/layout** e selecione **novo**e, em seguida, **arquivo de recurso de layout**.</span><span class="sxs-lookup"><span data-stu-id="6ca88-155">Right-click the **app/res/layout** folder and select **New**, then **Layout resource file**.</span></span>

1. <span data-ttu-id="6ca88-156">Nomeie o arquivo `fragment_home` e altere o **elemento raiz** para `RelativeLayout`e, em seguida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="6ca88-156">Name the file `fragment_home` and change the **Root element** to `RelativeLayout`, then select **OK**.</span></span>

1. <span data-ttu-id="6ca88-157">Abra o arquivo **fragment_home. xml** e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="6ca88-157">Open the **fragment_home.xml** file and replace its contents with the following.</span></span>

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <LinearLayout
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_centerInParent="true"
            android:orientation="vertical">

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_gravity="center_horizontal"
                android:text="Welcome!"
                android:textSize="30sp" />

            <TextView
                android:id="@+id/home_page_username"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_gravity="center_horizontal"
                android:paddingTop="8dp"
                android:text="Please sign in"
                android:textSize="20sp" />
        </LinearLayout>

    </RelativeLayout>
    ```

1. <span data-ttu-id="6ca88-158">Clique com o botão direito do mouse na pasta **app/res/layout** e selecione **novo**e, em seguida, **arquivo de recurso de layout**.</span><span class="sxs-lookup"><span data-stu-id="6ca88-158">Right-click the **app/res/layout** folder and select **New**, then **Layout resource file**.</span></span>

1. <span data-ttu-id="6ca88-159">Nomeie o arquivo `fragment_calendar` e altere o **elemento raiz** para `RelativeLayout`e, em seguida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="6ca88-159">Name the file `fragment_calendar` and change the **Root element** to `RelativeLayout`, then select **OK**.</span></span>

1. <span data-ttu-id="6ca88-160">Abra o arquivo **fragment_calendar. xml** e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="6ca88-160">Open the **fragment_calendar.xml** file and replace its contents with the following.</span></span>

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

1. <span data-ttu-id="6ca88-161">Clique com o botão direito do mouse na pasta **app/Java/com. example. graphtutorial** e selecione **nova**e, em seguida, **classe Java**.</span><span class="sxs-lookup"><span data-stu-id="6ca88-161">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span>

1. <span data-ttu-id="6ca88-162">Nomeie a classe `HomeFragment` e defina a **superclasse** como `androidx.fragment.app.Fragment`e, em seguida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="6ca88-162">Name the class `HomeFragment` and set the **Superclass** to `androidx.fragment.app.Fragment`, then select **OK**.</span></span>

1. <span data-ttu-id="6ca88-163">Abra o arquivo **HomeFragment** e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="6ca88-163">Open the **HomeFragment** file and replace its contents with the following.</span></span>

    ```java
    package com.example.graphtutorial;

    import android.os.Bundle;
    import android.view.LayoutInflater;
    import android.view.View;
    import android.view.ViewGroup;
    import android.widget.TextView;
    import androidx.annotation.NonNull;
    import androidx.annotation.Nullable;
    import androidx.fragment.app.Fragment;

    public class HomeFragment extends Fragment {
        private static final String USER_NAME = "userName";

        private String mUserName;

        public HomeFragment() {

        }

        public static HomeFragment createInstance(String userName) {
            HomeFragment fragment = new HomeFragment();

            // Add the provided username to the fragment's arguments
            Bundle args = new Bundle();
            args.putString(USER_NAME, userName);
            fragment.setArguments(args);
            return fragment;
        }

        @Override
        public void onCreate(@Nullable Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            if (getArguments() != null) {
                mUserName = getArguments().getString(USER_NAME);
            }
        }

        @Nullable
        @Override
        public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
            View homeView = inflater.inflate(R.layout.fragment_home, container, false);

            // If there is a username, replace the "Please sign in" with the username
            if (mUserName != null) {
                TextView userName = homeView.findViewById(R.id.home_page_username);
                userName.setText(mUserName);
            }

            return homeView;
        }
    }
    ```

1. <span data-ttu-id="6ca88-164">Clique com o botão direito do mouse na pasta **app/Java/com. example. graphtutorial** e selecione **nova**e, em seguida, **classe Java**.</span><span class="sxs-lookup"><span data-stu-id="6ca88-164">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span>

1. <span data-ttu-id="6ca88-165">Nomeie a classe `CalendarFragment` e defina a **superclasse** como `androidx.fragment.app.Fragment`e, em seguida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="6ca88-165">Name the class `CalendarFragment` and set the **Superclass** to `androidx.fragment.app.Fragment`, then select **OK**.</span></span>

1. <span data-ttu-id="6ca88-166">Abra o arquivo **CalendarFragment** e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="6ca88-166">Open the **CalendarFragment** file and replace its contents with the following.</span></span>

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

        @Nullable
        @Override
        public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
            return inflater.inflate(R.layout.fragment_calendar, container, false);
        }
    }
    ```

1. <span data-ttu-id="6ca88-167">Abra o arquivo **MainActivity. java** e adicione as funções a seguir à classe.</span><span class="sxs-lookup"><span data-stu-id="6ca88-167">Open the **MainActivity.java** file and add the the following functions to the class.</span></span>

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
    private void openCalendarFragment() {
        getSupportFragmentManager().beginTransaction()
                .replace(R.id.fragment_container, new CalendarFragment())
                .commit();
        mNavigationView.setCheckedItem(R.id.nav_calendar);
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

1. <span data-ttu-id="6ca88-168">Substitua a função `onNavigationItemSelected` existente pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="6ca88-168">Replace the existing `onNavigationItemSelected` function with the following.</span></span>

    ```java
    @Override
    public boolean onNavigationItemSelected(@NonNull MenuItem menuItem) {
        // Load the fragment that corresponds to the selected item
        switch (menuItem.getItemId()) {
            case R.id.nav_home:
                openHomeFragment(mUserName);
                break;
            case R.id.nav_calendar:
                openCalendarFragment();
                break;
            case R.id.nav_signin:
                signIn();
                break;
            case R.id.nav_signout:
                signOut();
                break;
        }

        mDrawer.closeDrawer(GravityCompat.START);

        return true;
    }
    ```

1. <span data-ttu-id="6ca88-169">Adicione o seguinte no final da `onCreate` função para carregar o fragmento inicial quando o aplicativo for iniciado.</span><span class="sxs-lookup"><span data-stu-id="6ca88-169">Add the following at the end of the `onCreate` function to load the home fragment when the app starts.</span></span>

    ```java
    // Load the home fragment by default on startup
    if (savedInstanceState == null) {
        openHomeFragment(mUserName);
    }
    ```

1. <span data-ttu-id="6ca88-170">Salve todas as suas alterações.</span><span class="sxs-lookup"><span data-stu-id="6ca88-170">Save all of your changes.</span></span>

1. <span data-ttu-id="6ca88-171">No menu **executar** , selecione **executar "aplicativo"**.</span><span class="sxs-lookup"><span data-stu-id="6ca88-171">On the **Run** menu, select **Run 'app'**.</span></span>

<span data-ttu-id="6ca88-172">O menu do aplicativo deve funcionar para navegar entre os dois fragmentos e alterar quando você toca nos botões **entrar** ou **sair.**</span><span class="sxs-lookup"><span data-stu-id="6ca88-172">The app's menu should work to navigate between the two fragments and change when you tap the **Sign in** or **Sign out** buttons.</span></span>

![Captura de tela do aplicativo](./images/app-screens.png)
