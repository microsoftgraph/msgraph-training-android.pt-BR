<!-- markdownlint-disable MD002 MD041 -->

Neste exercício, você incorporará o Microsoft Graph ao aplicativo. Para esse aplicativo, você usará o [SDK](https://github.com/microsoftgraph/msgraph-sdk-java) do Microsoft Graph para Java para fazer chamadas para o Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Obter eventos de calendário do Outlook

Nesta seção, você estenderá a classe para adicionar uma função para obter os eventos do usuário para a semana atual e atualizar para `GraphHelper` `CalendarFragment` usar essas novas funções.

1. Abra **GraphHelper** e adicione as instruções `import` a seguir na parte superior do arquivo.

    ```java
    import com.microsoft.graph.options.Option;
    import com.microsoft.graph.options.HeaderOption;
    import com.microsoft.graph.options.QueryOption;
    import com.microsoft.graph.requests.extensions.IEventCollectionPage;
    import com.microsoft.graph.requests.extensions.IEventCollectionRequestBuilder;
    import java.time.ZonedDateTime;
    import java.time.format.DateTimeFormatter;
    import java.util.LinkedList;
    import java.util.List;
    ```

1. Adicione as funções a seguir à `GraphHelper` classe.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphHelper.java" id="GetEventsSnippet":::

    > [!NOTE]
    > Considere o que o código `getCalendarView` está fazendo.
    >
    > - A URL que será chamada é `/v1.0/me/calendarview` .
    >   - Os `startDateTime` `endDateTime` parâmetros e consulta definem o início e o fim da exibição de calendário.
    >   - o header faz com que o Microsoft Graph retorne as horas de início e término de cada evento `Prefer: outlook.timezone` no fuso horário do usuário.
    >   - A função limita os campos retornados para cada evento a apenas aqueles que o ponto de exibição `select` realmente usará.
    >   - A `orderby` função classifica os resultados por hora de início.
    >   - A `top` função solicita 25 resultados por página.
    > - Um retorno de chamada é definido ( ) para verificar se há mais resultados disponíveis e `pagingCallback` para solicitar páginas adicionais, se necessário.

1. Clique com o botão direito do **mouse na pasta app/java/com.example.graphtutorial** e selecione **New**, em seguida, **Classe Java.** Nome da classe `GraphToIana` e selecione **OK**.

1. Abra o novo arquivo e substitua seu conteúdo pelo seguinte.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphToIana.java" id="GraphToIanaSnippet":::

1. Adicione as instruções `import` a seguir na parte superior do arquivo **CalendarFragment.**

    ```java
    import android.util.Log;
    import android.widget.ListView;
    import com.google.android.material.snackbar.BaseTransientBottomBar;
    import com.google.android.material.snackbar.Snackbar;
    import com.microsoft.graph.concurrency.ICallback;
    import com.microsoft.graph.core.ClientException;
    import com.microsoft.graph.models.extensions.Event;
    import com.microsoft.identity.client.AuthenticationCallback;
    import com.microsoft.identity.client.IAuthenticationResult;
    import com.microsoft.identity.client.exception.MsalException;
    import java.time.DayOfWeek;
    import java.time.ZoneId;
    import java.time.ZonedDateTime;
    import java.time.temporal.ChronoUnit;
    import java.time.temporal.TemporalAdjusters;
    import java.util.List;
    ```

1. Adicione o seguinte membro à `CalendarFragment` classe.

    ```java
    private List<Event> mEventList = null;
    ```

1. Adicione as seguintes funções à classe `CalendarFragment` para ocultar e mostrar a barra de progresso.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="ProgressBarSnippet":::

1. Adicione a função a seguir para fornecer um retorno de chamada para `getCalendarView` a função em `GraphHelper` .

    ```java
    private ICallback<List<Event>> getCalendarViewCallback() {
        return new ICallback<List<Event>>() {
            @Override
            public void success(List<Event> eventList) {
                mEventList = eventList;

                // Temporary for debugging
                String jsonEvents = GraphHelper.getInstance().serializeObject(mEventList);
                Log.d("GRAPH", jsonEvents);

                hideProgressBar();
            }

            @Override
            public void failure(ClientException ex) {
                hideProgressBar();
                Log.e("GRAPH", "Error getting events", ex);
                Snackbar.make(getView(),
                    ex.getMessage(),
                    BaseTransientBottomBar.LENGTH_LONG).show();
            }
        };
    }
    ```

1. Substitua a função `onCreateView` existente na classe pelo `CalendarFragment` seguinte.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="OnCreateViewSnippet":::

    Observe o que esse código faz. Primeiro, ele chama `acquireTokenSilently` para obter o token de acesso. Chamar esse método sempre que um token de acesso for necessário é uma prática melhor, pois ele aproveita as capacidades de atualização de token e cache da MSAL. Internamente, a MSAL verifica se há um token armazenado em cache e verifica se ele expirou. Se o token estiver presente e não tiver expirado, ele apenas retornará o token armazenado em cache. Se ele tiver expirado, ele tentará atualizar o token antes de revolvê-lo.

    Depois que o token é recuperado, o código chama o `getCalendarView` método para obter os eventos do usuário.

1. Execute o aplicativo, entre e toque no item **de** navegação calendário no menu. Você deverá ver um despejo JSON dos eventos no log de depuração no Android Studio.

## <a name="display-the-results"></a>Exibir os resultados

Agora você pode substituir o despejo JSON por algo para exibir os resultados de maneira amigável. Nesta seção, você adicionará um ao fragmento de calendário, criará um layout para cada item no e criará um adaptador de lista personalizado para o que mapeia os campos de cada um para o apropriado na `ListView` `ListView` `ListView` `Event` `TextView` exibição.

1. Substitua o `TextView` **aplicativo/res/layout/fragment_calendar.xml** por `ListView` um .

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/fragment_calendar.xml" highlight="6-11":::

1. Clique com o botão direito do **mouse na pasta app/res/layout** e selecione **Novo** e, em seguida, arquivo de **recurso layout.**

1. Nome do `event_list_item` arquivo, altere **o elemento Root** para e selecione `RelativeLayout` **OK**.

1. Abra o **event_list_item.xml** arquivo e substitua seu conteúdo pelo seguinte.

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/event_list_item.xml":::

1. Clique com o botão direito do **mouse na pasta app/java/com.example.graphtutorial** e selecione **New**, em seguida, **Classe Java.**

1. Nome da classe `EventListAdapter` e selecione **OK**.

1. Abra o **arquivo EventListAdapter** e substitua seu conteúdo pelo seguinte.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/EventListAdapter.java" id="EventListAdapterSnippet":::

1. Abra a **classe CalendarFragment** e adicione a seguinte função à classe.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="AddEventsToListSnippet":::

1. Substitua o código de depuração temporário da `success` substituição por `addEventsToList();` .

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="SuccessSnippet" highlight="5":::

1. Execute o aplicativo, entre e toque no item **de navegação** calendário. Você deverá ver a lista de eventos.

    ![Uma captura de tela da tabela de eventos](./images/calendar-list.png)
