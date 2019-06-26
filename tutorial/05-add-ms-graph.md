<!-- markdownlint-disable MD002 MD041 -->

Neste exercício, você incorporará o Microsoft Graph no aplicativo. Para este aplicativo, você usará o [SDK do Microsoft Graph para Java](https://github.com/microsoftgraph/msgraph-sdk-java) para fazer chamadas para o Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Obter eventos de calendário do Outlook

Nesta seção, você estenderá a `GraphHelper` classe para adicionar uma função para obter os eventos e a atualização `CalendarFragment` do usuário para usar essas novas funções.

1. Abra o arquivo **GraphHelper** e adicione as seguintes `import` instruções à parte superior do arquivo.

    ```java
    import com.microsoft.graph.options.Option;
    import com.microsoft.graph.options.QueryOption;
    import com.microsoft.graph.requests.extensions.IEventCollectionPage;
    import java.util.LinkedList;
    import java.util.List;
    ```

1. Adicione as seguintes funções à `GraphHelper` classe.

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
    > Considere o que o código `getEvents` está fazendo.
    >
    > - A URL que será chamada é `/v1.0/me/events`.
    > - A `select` função limita os campos retornados para cada evento para apenas > aqueles que o modo de exibição realmente usará.
    > - O `QueryOption` nome `orderby` é usado para classificar os resultados pela data e hora em que foram criados, com o item mais recente em primeiro lugar.

1. Adicione as seguintes `import` instruções à parte superior do arquivo **CalendarFragment** .

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

1. Adicione os membros a seguir à `CalendarFragment` classe.

    ```java
    private List<Event> mEventList = null;
    private ProgressBar mProgress = null;
    ```

1. Adicione as seguintes funções à `CalendarFragment` classe para ocultar e mostrar a barra de progresso e fornecer um retorno de chamada para a `getEvents` função no `GraphHelper`.

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

1. Substitua a `onCreate` função na `GraphHelper` classe para obter os eventos do usuário do Microsoft Graph.

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

Observe o que esse código faz. Primeiro, ele chama `acquireTokenSilently` para obter o token de acesso. Chamar esse método sempre que um token de acesso é necessário é uma prática recomendada, pois aproveita as capacidades de cache e atualização de token do MSAL. Internamente, o MSAL verifica se há um token armazenado em cache e, em seguida, verifica se ele expirou. Se o token estiver presente e não tiver expirado, ele apenas retornará o token armazenado em cache. Se ele tiver expirado, ele tentará atualizar o token antes de devolvê-lo.

Depois que o token for recuperado, o código chamará `getEvents` o método para obter os eventos do usuário.

Agora você pode executar o aplicativo, entrar e tocar no item de navegação de **calendário** no menu. Você verá um despejo JSON dos eventos no log de depuração no Android Studio.

## <a name="display-the-results"></a>Exibir os resultados

Agora você pode substituir o despejo JSON por algo para exibir os resultados de forma amigável. Nesta seção, você adicionará `ListView` um ao fragmento do calendário, criará um layout para cada item no `ListView`e criará um adaptador de lista personalizado para o `ListView` que mapeia os campos de cada `Event` um para o `TextView` apropriado no modo de exibição.

1. Substitua o `TextView` em **app/res/layout/fragment_calendar. xml** por um `ListView`.

    ```xml
    <ListView
        android:id="@+id/eventlist"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:divider="@color/colorPrimary"
        android:dividerHeight="1dp" />
    ```

1. Clique com o botão direito do mouse na pasta **app/res/layout** e selecione **novo**e, em seguida, **arquivo de recurso de layout**.

1. Nomeie o arquivo `event_list_item`, altere o **elemento raiz** para `RelativeLayout`e selecione **OK**.

1. Abra o arquivo **event_list_item. xml** e substitua seu conteúdo pelo seguinte.

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

1. Clique com o botão direito do mouse na pasta **app/Java/com. example. graphtutorial** e selecione **nova**e, em seguida, **classe Java**.

1. Nomeie a classe `EventListAdapter` e selecione **OK**.

1. Abra o arquivo **EventListAdapter** e substitua seu conteúdo pelo seguinte.

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

1. Abra a classe **CalendarFragment** e adicione a função a seguir à classe.

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

1. Adicione a seguinte linha de código na `success` substituição após a `mEventList = iEventCollectionPage.getCurrentPage();` linha.

    ```java
    addEventsToList();
    ```

1. Execute o aplicativo, entre e toque no item de navegação **calendário** . Você deve ver a lista de eventos.

    ![Uma captura de tela da tabela de eventos](./images/calendar-list.png)
