<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="8429d-101">Neste exercício, você incorporará o Microsoft Graph no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8429d-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="8429d-102">Para este aplicativo, você usará o [SDK do Microsoft Graph para Java](https://github.com/microsoftgraph/msgraph-sdk-java) para fazer chamadas para o Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="8429d-102">For this application, you will use the [Microsoft Graph SDK for Java](https://github.com/microsoftgraph/msgraph-sdk-java) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="8429d-103">Obter eventos de calendário do Outlook</span><span class="sxs-lookup"><span data-stu-id="8429d-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="8429d-104">Nesta seção, você estenderá a `GraphHelper` classe para adicionar uma função para obter os eventos e a atualização `CalendarFragment` do usuário para usar essas novas funções.</span><span class="sxs-lookup"><span data-stu-id="8429d-104">In this section you will extend the `GraphHelper` class to add a function to get the user's events and update `CalendarFragment` to use these new functions.</span></span>

1. <span data-ttu-id="8429d-105">Abra o arquivo **GraphHelper** e adicione as seguintes `import` instruções à parte superior do arquivo.</span><span class="sxs-lookup"><span data-stu-id="8429d-105">Open the **GraphHelper** file and add the following `import` statements to the top of the file.</span></span>

    ```java
    import com.microsoft.graph.options.Option;
    import com.microsoft.graph.options.QueryOption;
    import com.microsoft.graph.requests.extensions.IEventCollectionPage;
    import java.util.LinkedList;
    import java.util.List;
    ```

1. <span data-ttu-id="8429d-106">Adicione as seguintes funções à `GraphHelper` classe.</span><span class="sxs-lookup"><span data-stu-id="8429d-106">Add the following functions to the `GraphHelper` class.</span></span>

    ```java
    public void getEvents(String accessToken, ICallback<IEventCollectionPage> callback) {
        mAccessToken = accessToken;

        // Use query options to sort by created time
        final List<Option> options = new LinkedList<Option>();
        options.add(new QueryOption("orderby", "createdDateTime DESC"));


        // GET /me/events
        mClient.me().events().buildRequest(options)
                .select("subject,organizer,start,end")
                .get(callback);

    }

    // Debug function to get the JSON representation of a Graph
    // object
    public String serializeObject(Object object) {
        return mClient.getSerializer().serializeObject(object);
    }
    ```

    > [!NOTE]
    > <span data-ttu-id="8429d-107">Considere o que o código `getEvents` está fazendo.</span><span class="sxs-lookup"><span data-stu-id="8429d-107">Consider what the code in `getEvents` is doing.</span></span>
    >
    > - <span data-ttu-id="8429d-108">A URL que será chamada é `/v1.0/me/events`.</span><span class="sxs-lookup"><span data-stu-id="8429d-108">The URL that will be called is `/v1.0/me/events`.</span></span>
    > - <span data-ttu-id="8429d-109">A `select` função limita os campos retornados para cada evento para apenas > aqueles que o modo de exibição realmente usará.</span><span class="sxs-lookup"><span data-stu-id="8429d-109">The `select` function limits the fields returned for each events to just > those the view will actually use.</span></span>
    > - <span data-ttu-id="8429d-110">O `QueryOption` nome `orderby` é usado para classificar os resultados pela data e hora em que foram criados, com o item mais recente em primeiro lugar.</span><span class="sxs-lookup"><span data-stu-id="8429d-110">The `QueryOption` named `orderby` is used to sort the results by the date and time they were created, with the most recent item being first.</span></span>

1. <span data-ttu-id="8429d-111">Adicione as seguintes `import` instruções à parte superior do arquivo **CalendarFragment** .</span><span class="sxs-lookup"><span data-stu-id="8429d-111">Add the following `import` statements to the top of the **CalendarFragment** file.</span></span>

    ```java
    import android.util.Log;
    import android.widget.ListView;
    import android.widget.ProgressBar;
    import com.microsoft.graph.concurrency.ICallback;
    import com.microsoft.graph.core.ClientException;
    import com.microsoft.graph.models.extensions.Event;
    import com.microsoft.graph.requests.extensions.IEventCollectionPage;
    import com.microsoft.identity.client.AuthenticationCallback;
    import com.microsoft.identity.client.AuthenticationResult;
    import com.microsoft.identity.client.exception.MsalException;
    import java.util.List;
    ```

1. <span data-ttu-id="8429d-112">Adicione os membros a seguir à `CalendarFragment` classe.</span><span class="sxs-lookup"><span data-stu-id="8429d-112">Add the following members to the `CalendarFragment` class.</span></span>

    ```java
    private List<Event> mEventList = null;
    private ProgressBar mProgress = null;
    ```

1. <span data-ttu-id="8429d-113">Adicione as seguintes funções à `CalendarFragment` classe para ocultar e mostrar a barra de progresso e fornecer um retorno de chamada para a `getEvents` função no `GraphHelper`.</span><span class="sxs-lookup"><span data-stu-id="8429d-113">Add the following functions to the `CalendarFragment` class to hide and show the progress bar, and to provide a callback for the `getEvents` function in `GraphHelper`.</span></span>

    ```java
    private void showProgressBar() {
        getActivity().runOnUiThread(new Runnable() {
            @Override
            public void run() {
                mProgress.setVisibility(View.VISIBLE);
            }
        });
    }

    private void hideProgressBar() {
        getActivity().runOnUiThread(new Runnable() {
            @Override
            public void run() {
                mProgress.setVisibility(View.GONE);
            }
        });
    }

    private ICallback<IEventCollectionPage> getEventsCallback() {
        return new ICallback<IEventCollectionPage>() {
            @Override
            public void success(IEventCollectionPage iEventCollectionPage) {
                mEventList = iEventCollectionPage.getCurrentPage();

                // Temporary for debugging
                String jsonEvents = GraphHelper.getInstance().serializeObject(mEventList);
                Log.d("GRAPH", jsonEvents);

                hideProgressBar();
            }

            @Override
            public void failure(ClientException ex) {
                Log.e("GRAPH", "Error getting events", ex);
                hideProgressBar();
            }
        };
    }
    ```

1. <span data-ttu-id="8429d-114">Substitua a `onCreate` função na `GraphHelper` classe para obter os eventos do usuário do Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="8429d-114">Override the `onCreate` function in the `GraphHelper` class to get the user's events from Microsoft Graph.</span></span>

    ```java
    @Override
    public void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        mProgress = getActivity().findViewById(R.id.progressbar);
        showProgressBar();

        // Get a current access token
        AuthenticationHelper.getInstance()
                .acquireTokenSilently(new AuthenticationCallback() {
                    @Override
                    public void onSuccess(AuthenticationResult authenticationResult) {
                        final GraphHelper graphHelper = GraphHelper.getInstance();

                        // Get the user's events
                        graphHelper.getEvents(authenticationResult.getAccessToken(),
                                getEventsCallback());
                    }

                    @Override
                    public void onError(MsalException exception) {
                        Log.e("AUTH", "Could not get token silently", exception);
                        hideProgressBar();
                    }

                    @Override
                    public void onCancel() {
                        hideProgressBar();
                    }
                });
    }
    ```

<span data-ttu-id="8429d-115">Observe o que esse código faz.</span><span class="sxs-lookup"><span data-stu-id="8429d-115">Notice what this code does.</span></span> <span data-ttu-id="8429d-116">Primeiro, ele chama `acquireTokenSilently` para obter o token de acesso.</span><span class="sxs-lookup"><span data-stu-id="8429d-116">First, it calls `acquireTokenSilently` to get the access token.</span></span> <span data-ttu-id="8429d-117">Chamar esse método sempre que um token de acesso é necessário é uma prática recomendada, pois aproveita as capacidades de cache e atualização de token do MSAL.</span><span class="sxs-lookup"><span data-stu-id="8429d-117">Calling this method every time an access token is needed is a best practice because it takes advantage of MSAL's caching and token refresh abilities.</span></span> <span data-ttu-id="8429d-118">Internamente, o MSAL verifica se há um token armazenado em cache e, em seguida, verifica se ele expirou.</span><span class="sxs-lookup"><span data-stu-id="8429d-118">Internally, MSAL checks for a cached token, then checks if it is expired.</span></span> <span data-ttu-id="8429d-119">Se o token estiver presente e não tiver expirado, ele apenas retornará o token armazenado em cache.</span><span class="sxs-lookup"><span data-stu-id="8429d-119">If the token is present and not expired, it just returns the cached token.</span></span> <span data-ttu-id="8429d-120">Se ele tiver expirado, ele tentará atualizar o token antes de devolvê-lo.</span><span class="sxs-lookup"><span data-stu-id="8429d-120">If it is expired, it attempts to refresh the token before returning it.</span></span>

<span data-ttu-id="8429d-121">Depois que o token for recuperado, o código chamará `getEvents` o método para obter os eventos do usuário.</span><span class="sxs-lookup"><span data-stu-id="8429d-121">Once the token is retrieved, the code then calls the `getEvents` method to get the user's events.</span></span>

<span data-ttu-id="8429d-122">Agora você pode executar o aplicativo, entrar e tocar no item de navegação de **calendário** no menu.</span><span class="sxs-lookup"><span data-stu-id="8429d-122">You can now run the app, sign in, and tap the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="8429d-123">Você verá um despejo JSON dos eventos no log de depuração no Android Studio.</span><span class="sxs-lookup"><span data-stu-id="8429d-123">You should see a JSON dump of the events in the debug log in Android Studio.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="8429d-124">Exibir os resultados</span><span class="sxs-lookup"><span data-stu-id="8429d-124">Display the results</span></span>

<span data-ttu-id="8429d-125">Agora você pode substituir o despejo JSON por algo para exibir os resultados de forma amigável.</span><span class="sxs-lookup"><span data-stu-id="8429d-125">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span> <span data-ttu-id="8429d-126">Nesta seção, você adicionará `ListView` um ao fragmento do calendário, criará um layout para cada item no `ListView`e criará um adaptador de lista personalizado para o `ListView` que mapeia os campos de cada `Event` um para o `TextView` apropriado no modo de exibição.</span><span class="sxs-lookup"><span data-stu-id="8429d-126">In this section, you will add a `ListView` to the calendar fragment, create a layout for each item in the `ListView`, and create a custom list adapter for the `ListView` that maps the fields of each `Event` to the appropriate `TextView` in the view.</span></span>

1. <span data-ttu-id="8429d-127">Substitua o `TextView` em **app/res/layout/fragment_calendar. xml** por um `ListView`.</span><span class="sxs-lookup"><span data-stu-id="8429d-127">Replace the `TextView` in **app/res/layout/fragment_calendar.xml** with a `ListView`.</span></span>

    ```xml
    <ListView
        android:id="@+id/eventlist"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:divider="@color/colorPrimary"
        android:dividerHeight="1dp" />
    ```

1. <span data-ttu-id="8429d-128">Clique com o botão direito do mouse na pasta **app/res/layout** e selecione **novo**e, em seguida, **arquivo de recurso de layout**.</span><span class="sxs-lookup"><span data-stu-id="8429d-128">Right-click the **app/res/layout** folder and select **New**, then **Layout resource file**.</span></span>

1. <span data-ttu-id="8429d-129">Nomeie o arquivo `event_list_item`, altere o **elemento raiz** para `RelativeLayout`e selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="8429d-129">Name the file `event_list_item`, change the **Root element** to `RelativeLayout`, and select **OK**.</span></span>

1. <span data-ttu-id="8429d-130">Abra o arquivo **event_list_item. xml** e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="8429d-130">Open the **event_list_item.xml** file and replace its contents with the following.</span></span>

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:padding="10dp">

        <TextView
            android:id="@+id/eventsubject"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Subject"
            android:textSize="20sp" />

        <TextView
            android:id="@+id/eventorganizer"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_below="@id/eventsubject"
            android:text="Adele Vance"
            android:textSize="15sp" />

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_below="@id/eventorganizer"
            android:orientation="horizontal">

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:paddingEnd="2sp"
                android:text="Start:"
                android:textSize="15sp"
                android:textStyle="bold" />

            <TextView
                android:id="@+id/eventstart"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="1:30 PM 2/19/2019"
                android:textSize="15sp" />

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:paddingStart="5sp"
                android:paddingEnd="2sp"
                android:text="End:"
                android:textSize="15sp"
                android:textStyle="bold" />

            <TextView
                android:id="@+id/eventend"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="1:30 PM 2/19/2019"
                android:textSize="15sp" />
        </LinearLayout>
    </RelativeLayout>
    ```

1. <span data-ttu-id="8429d-131">Clique com o botão direito do mouse na pasta **app/Java/com. example. graphtutorial** e selecione **nova**e, em seguida, **classe Java**.</span><span class="sxs-lookup"><span data-stu-id="8429d-131">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span>

1. <span data-ttu-id="8429d-132">Nomeie a classe `EventListAdapter` e selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="8429d-132">Name the class `EventListAdapter` and select **OK**.</span></span>

1. <span data-ttu-id="8429d-133">Abra o arquivo **EventListAdapter** e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="8429d-133">Open the **EventListAdapter** file and replace its contents with the following.</span></span>

    ```java
    package com.example.graphtutorial;

    import android.content.Context;
    import android.support.annotation.NonNull;
    import android.view.LayoutInflater;
    import android.view.View;
    import android.view.ViewGroup;
    import android.widget.ArrayAdapter;
    import android.widget.TextView;

    import com.microsoft.graph.models.extensions.DateTimeTimeZone;
    import com.microsoft.graph.models.extensions.Event;

    import java.time.LocalDateTime;
    import java.time.ZoneId;
    import java.time.ZonedDateTime;
    import java.time.format.DateTimeFormatter;
    import java.time.format.FormatStyle;
    import java.util.List;
    import java.util.TimeZone;

    public class EventListAdapter extends ArrayAdapter<Event> {
        private Context mContext;
        private int mResource;
        private ZoneId mLocalTimeZoneId;

        // Used for the ViewHolder pattern
        // https://developer.android.com/training/improving-layouts/smooth-scrolling
        static class ViewHolder {
            TextView subject;
            TextView organizer;
            TextView start;
            TextView end;
        }

        public EventListAdapter(Context context, int resource, List<Event> events) {
            super(context, resource, events);
            mContext = context;
            mResource = resource;
            mLocalTimeZoneId = TimeZone.getDefault().toZoneId();
        }

        @NonNull
        @Override
        public View getView(int position, View convertView, ViewGroup parent) {
            Event event = getItem(position);

            ViewHolder holder;

            if (convertView == null) {
                LayoutInflater inflater = LayoutInflater.from(mContext);
                convertView = inflater.inflate(mResource, parent, false);

                holder = new ViewHolder();
                holder.subject = convertView.findViewById(R.id.eventsubject);
                holder.organizer = convertView.findViewById(R.id.eventorganizer);
                holder.start = convertView.findViewById(R.id.eventstart);
                holder.end = convertView.findViewById(R.id.eventend);

                convertView.setTag(holder);
            } else {
                holder = (ViewHolder) convertView.getTag();
            }

            holder.subject.setText(event.subject);
            holder.organizer.setText(event.organizer.emailAddress.name);
            holder.start.setText(getLocalDateTimeString(event.start));
            holder.end.setText(getLocalDateTimeString(event.end));

            return convertView;
        }

        // Convert Graph's DateTimeTimeZone format to
        // a LocalDateTime, then return a formatted string
        private String getLocalDateTimeString(DateTimeTimeZone dateTime) {
            ZonedDateTime localDateTime = LocalDateTime.parse(dateTime.dateTime)
                    .atZone(ZoneId.of(dateTime.timeZone))
                    .withZoneSameInstant(mLocalTimeZoneId);

            return String.format("%s %s",
                    localDateTime.format(DateTimeFormatter.ofLocalizedDate(FormatStyle.MEDIUM)),
                    localDateTime.format(DateTimeFormatter.ofLocalizedTime(FormatStyle.SHORT)));
        }
    }
    ```

1. <span data-ttu-id="8429d-134">Abra a classe **CalendarFragment** e adicione a função a seguir à classe.</span><span class="sxs-lookup"><span data-stu-id="8429d-134">Open the **CalendarFragment** class and add the following function to the class.</span></span>

    ```java
    private void addEventsToList() {
        getActivity().runOnUiThread(new Runnable() {
            @Override
            public void run() {
                ListView eventListView = getView().findViewById(R.id.eventlist);

                EventListAdapter listAdapter = new EventListAdapter(getActivity(),
                        R.layout.event_list_item, mEventList);

                eventListView.setAdapter(listAdapter);
            }
        });
    }
    ```

1. <span data-ttu-id="8429d-135">Adicione a seguinte linha de código na `success` substituição após a `mEventList = iEventCollectionPage.getCurrentPage();` linha.</span><span class="sxs-lookup"><span data-stu-id="8429d-135">Add the following line of code in the `success` override after the `mEventList = iEventCollectionPage.getCurrentPage();` line.</span></span>

    ```java
    addEventsToList();
    ```

1. <span data-ttu-id="8429d-136">Execute o aplicativo, entre e toque no item de navegação **calendário** .</span><span class="sxs-lookup"><span data-stu-id="8429d-136">Run the app, sign in, and tap the **Calendar** navigation item.</span></span> <span data-ttu-id="8429d-137">Você deve ver a lista de eventos.</span><span class="sxs-lookup"><span data-stu-id="8429d-137">You should see the list of events.</span></span>

    ![Uma captura de tela da tabela de eventos](./images/calendar-list.png)