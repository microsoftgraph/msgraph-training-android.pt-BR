<!-- markdownlint-disable MD002 MD041 -->

Comece criando um novo projeto do Android Studio.

1. Abra o Android Studio e selecione **Iniciar um novo projeto do Android Studio** na tela de boas-vindas.

1. Na caixa de diálogo Criar **Novo Projeto,** selecione **Atividade Vazia** e, em seguida, selecione **Próximo**.

    ![Uma captura de tela da caixa de diálogo Criar Novo Projeto no Android Studio](./images/choose-project.png)

1. Na caixa **de diálogo Configurar seu** projeto, de configure o **nome** como , verifique se o campo Idioma está definido como , e verifique se o nível `Graph Tutorial` mínimo da API está definido como  `Java`  `API 29: Android 10.0 (Q)` . Modifique **o nome do pacote e** salve o local **conforme** necessário. Selecione **Concluir**.

    ![Uma captura de tela da caixa de diálogo Configurar seu projeto](./images/configure-project.png)

> [!IMPORTANT]
> O código e as instruções neste tutorial usam o nome do pacote **com.example.graphtutorial**. Se você usar um nome de pacote diferente ao criar o projeto, certifique-se de usar o nome do pacote onde quer que você veja esse valor.

## <a name="install-dependencies"></a>Instalar dependências

Antes de continuar, instale algumas dependências adicionais que você usará mais tarde.

- `com.google.android.material:material` para disponibilizar o [modo de](https://material.io/develop/android/components/navigation-view/) exibição de navegação para o aplicativo.
- [Biblioteca de Autenticação da Microsoft (MSAL) para Android para](https://github.com/AzureAD/microsoft-authentication-library-for-android) lidar com a autenticação do Azure AD e o gerenciamento de tokens.
- [SDK do Microsoft Graph para Java](https://github.com/microsoftgraph/msgraph-sdk-java) para fazer chamadas para o Microsoft Graph.

1. Expanda **Scripts de Gradle** e abra **build.gradle (Módulo: Graph_Tutorial.app)**.

1. Adicione as seguintes linhas dentro do `dependencies` valor.

    :::code language="gradle" source="../demo/GraphTutorial/app/build.gradle" id="DependenciesSnippet":::

1. Adicione um `packagingOptions` valor dentro do valor em `android` **build.gradle (Módulo: Graph_Tutorial.app)**.

    ```Gradle
    packagingOptions {
        pickFirst 'META-INF/*'
    }
    ```

1. Adicione o repositório do Azure Maven para a biblioteca MicrosoftDeviceSDK, uma dependência do MSAL. Abra **build.gradle (Project: Graph_Tutorial)**. Adicione o seguinte ao `repositories` valor dentro do `allprojects` valor.

    ```Gradle
    maven {
        url 'https://pkgs.dev.azure.com/MicrosoftDeviceSDK/DuoSDK-Public/_packaging/Duo-SDK-Feed/maven/v1'
    }
    ```

1. Salve suas alterações. No menu **Arquivo,** selecione **Sincronizar Projeto com Arquivos gradle**.

## <a name="design-the-app"></a>Design do aplicativo

O aplicativo usará uma gaveta de navegação para navegar entre diferentes exibições. Nesta etapa, você atualizará a atividade para usar um layout da gaveta de navegação e adicionará fragmentos para os exibições.

### <a name="create-a-navigation-drawer"></a>Criar uma gaveta de navegação

Nesta seção, você criará ícones para o menu de navegação do aplicativo, criará um menu para o aplicativo e atualizará o tema e o layout do aplicativo para ser compatível com uma gaveta de navegação.

#### <a name="create-icons"></a>Criar ícones

1. Clique com o botão direito do **mouse na pasta app/res/drawable** e selecione **Novo**, em seguida, **Vector Asset**.

1. Clique no botão ícone ao lado de **Clip Art**.

1. Na janela **Selecionar Ícone,** digite na barra de pesquisa e selecione o `home` **ícone** Home e selecione **OK**.

1. Altere **o nome** para `ic_menu_home` .

    ![Uma captura de tela da janela Configurar Ativo Vetor](./images/create-icon.png)

1. Selecione **Próximo** **e,** em seguida, Concluir .

1. Repita a etapa anterior para criar mais quatro ícones.

    - Nome: `ic_menu_calendar` , Ícone: `event`
    - Nome: `ic_menu_add_event` , Ícone: `add box`
    - Nome: `ic_menu_signout` , Ícone: `exit to app`
    - Nome: `ic_menu_signin` , Ícone: `person add`

#### <a name="create-the-menu"></a>Criar o menu

1. Clique com o botão direito do mouse **na pasta res** e selecione **Novo**, em seguida, Diretório de Recursos **do Android**.

1. Altere **o tipo de recurso** para e selecione `menu` **OK**.

1. Clique com o botão direito do mouse na nova **pasta de menu** e selecione **Novo**, em **seguida, menu arquivo de recurso**.

1. Nomeia o `drawer_menu` arquivo e selecione **OK**.

1. Quando o arquivo for aberto, selecione a guia **Código** para exibir o XML e substitua todo o conteúdo pelo seguinte.

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/menu/drawer_menu.xml":::

#### <a name="update-application-theme-and-layout"></a>Atualizar o tema e o layout do aplicativo

1. Abra o **arquivo app/res/values/themes.xml** e adicione as seguintes linhas dentro do `style` elemento.

    ```xml
    <item name="windowActionBar">false</item>
    <item name="windowNoTitle">true</item>
    ```

1. Abra o **arquivo app/res/values-night/themes.xml** e adicione as seguintes linhas dentro do `style` elemento.

    ```xml
    <item name="windowActionBar">false</item>
    <item name="windowNoTitle">true</item>
    ```

1. Clique com o botão direito **do mouse na pasta aplicativo/res/layout.**

1. Selecione **Novo** e, em seguida, **arquivo de recurso layout.**

1. Nomee o `nav_header` arquivo e altere **o elemento Root** para , `LinearLayout` em seguida, selecione **OK**.

1. Abra o **nav_header.xml** e selecione a **guia** Código. Substitua todo o conteúdo pelo seguinte.

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/nav_header.xml":::

1. Abra o **arquivo app/res/layout/activity_main.xml** e atualize o layout para um substituindo o `DrawerLayout` XML existente pelo seguinte.

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/activity_main.xml":::

1. Abra **app/res/values/strings.xml** e adicione os seguintes elementos dentro do `resources` elemento.

    ```xml
    <string name="navigation_drawer_open">Open navigation drawer</string>
    <string name="navigation_drawer_close">Close navigation drawer</string>
    ```

1. Abra o **arquivo app/java/com.example/graphtutorial/MainActivity** e substitua todo o conteúdo pelo seguinte.

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

### <a name="add-fragments"></a>Adicionar fragmentos

Nesta seção, você criará fragmentos para as exibições de início e calendário.

1. Clique com o botão direito do **mouse na pasta aplicativo/res/layout** e selecione **Novo**, em **seguida, Arquivo de recurso layout.**

1. Nomee o `fragment_home` arquivo e altere **o elemento Root** para , `RelativeLayout` em seguida, selecione **OK**.

1. Abra o **arquivofragment_home.xml** e substitua seu conteúdo pelo seguinte.

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/fragment_home.xml":::

1. Clique com o botão direito do **mouse na pasta aplicativo/res/layout** e selecione **Novo**, em **seguida, Arquivo de recurso layout.**

1. Nomee o `fragment_calendar` arquivo e altere **o elemento Root** para , `RelativeLayout` em seguida, selecione **OK**.

1. Abra o **arquivofragment_calendar.xml** e substitua seu conteúdo pelo seguinte.

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

1. Clique com o botão direito do **mouse na pasta aplicativo/res/layout** e selecione **Novo**, em **seguida, Arquivo de recurso layout.**

1. Nomee o `fragment_new_event` arquivo e altere **o elemento Root** para , `RelativeLayout` em seguida, selecione **OK**.

1. Abra o **arquivofragment_new_event.xml** e substitua seu conteúdo pelo seguinte.

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

1. Clique com o botão direito do **mouse na pasta app/java/com.example.graphtutorial** e selecione **Novo**, depois **Java Classe**.

1. Nomeia a `HomeFragment` classe e selecione **OK**.

1. Abra o **arquivo HomeFragment** e substitua seu conteúdo pelo seguinte.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/HomeFragment.java" id="HomeSnippet":::

1. Clique com o botão direito do **mouse na pasta app/java/com.example.graphtutorial** e selecione **Novo**, depois **Java Classe**.

1. Nomeia a `CalendarFragment` classe e selecione **OK**.

1. Abra o **arquivo CalendarFragment** e substitua seu conteúdo pelo seguinte.

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

1. Clique com o botão direito do **mouse na pasta app/java/com.example.graphtutorial** e selecione **Novo**, depois **Java Classe**.

1. Nomeia a `NewEventFragment` classe e selecione **OK**.

1. Abra o **arquivo NewEventFragment** e substitua seu conteúdo pelo seguinte.

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

1. Abra o **arquivo MainActivity.java** e adicione as seguintes funções à classe.

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

1. Substitua a função `onNavigationItemSelected` existente pela seguinte.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="OnNavItemSelectedSnippet":::

1. Salve todas as alterações.

1. No menu **Executar,** selecione **Executar 'app'**.

O menu do aplicativo deve funcionar para navegar entre os  dois fragmentos e alterar quando você tocar nos botões Entrar ou **Sair.**

![Captura de tela do aplicativo](./images/app-screens.png)
