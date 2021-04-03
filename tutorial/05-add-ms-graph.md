<!-- markdownlint-disable MD002 MD041 -->

Neste exercício, você incorporará o Microsoft Graph ao aplicativo. Para este aplicativo, você usará o [SDK](https://github.com/microsoftgraph/msgraph-sdk-java) do Microsoft Graph para Java fazer chamadas para o Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Obtenha eventos de calendário do Outlook

Nesta seção, você estenderá a classe para adicionar uma função para obter os eventos do usuário para a semana atual e atualizar para `GraphHelper` `CalendarFragment` usar essas novas funções.

1. Abra **GraphHelper** e adicione as instruções `import` a seguir à parte superior do arquivo.

    ```java
    import com.microsoft.graph.options.Option;
    import com.microsoft.graph.options.HeaderOption;
    import com.microsoft.graph.options.QueryOption;
    import com.microsoft.graph.requests.EventCollectionPage;
    import com.microsoft.graph.requests.EventCollectionRequestBuilder;
    import java.time.ZonedDateTime;
    import java.time.format.DateTimeFormatter;
    import java.util.LinkedList;
    import java.util.List;
    import java.util.concurrent.CompletableFuture;
    ```

1. Adicione as seguintes funções à `GraphHelper` classe.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphHelper.java" id="GetEventsSnippet":::

    > [!NOTE]
    > Considere o que o código `getCalendarView` está fazendo.
    >
    > - O URL que será chamado é `/v1.0/me/calendarview`.
    >   - Os `startDateTime` `endDateTime` parâmetros e consulta definem o início e o fim do exibição de calendário.
    >   - o header faz com que o Microsoft Graph retorne os horários de início e término de cada evento `Prefer: outlook.timezone` no fuso horário do usuário.
    >   - A função `select` limita os campos retornados para cada evento apenas para aqueles que o modo de exibição realmente usará.
    >   - A `orderby` função classifica os resultados por hora de início.
    >   - A `top` função solicita 25 resultados por página.
    > - A função verifica se há mais resultados disponíveis e `processPage` solicita páginas adicionais, se necessário.

1. Clique com o botão direito do **mouse na pasta app/java/com.example.graphtutorial** e selecione **Novo**, depois **Java Classe**. Nomeia a `GraphToIana` classe e selecione **OK**.

1. Abra o novo arquivo e substitua seu conteúdo pelo seguinte.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphToIana.java" id="GraphToIanaSnippet":::

1. Adicione as instruções `import` a seguir à parte superior do arquivo **CalendarFragment.**

    ```java
    import android.util.Log;
    import android.widget.ListView;
    import com.google.android.material.snackbar.BaseTransientBottomBar;
    import com.google.android.material.snackbar.Snackbar;
    import com.microsoft.graph.core.ClientException;
    import com.microsoft.graph.models.Event;
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

1. Adicione o membro a seguir à `CalendarFragment` classe.

    ```java
    private List<Event> mEventList = null;
    ```

1. Adicione as seguintes funções à classe `CalendarFragment` para ocultar e mostrar a barra de progresso.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="ProgressBarSnippet":::

1. Adicione a função a seguir para saída da lista de eventos para fins de depuração.

    ```java
    private void addEventsToList() {
        // Temporary for debugging
        String jsonEvents = GraphHelper.getInstance().serializeObject(mEventList);
        Log.d("GRAPH", jsonEvents);
    }
    ```

1. Substitua a função `onCreateView` existente na classe pelo `CalendarFragment` seguinte.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="OnCreateViewSnippet":::

1. Execute o aplicativo, entre e toque **no** item de navegação Calendário no menu. Você deve ver um despejo JSON dos eventos no log de depuração no Android Studio.

## <a name="display-the-results"></a>Exibir os resultados

Agora você pode substituir o despejo JSON por algo para exibir os resultados de maneira amigável. Nesta seção, você adicionará um ao fragmento de calendário, criará um layout para cada item no , e criará um adaptador de lista personalizado para o que mapeia os campos de cada um para o apropriado na `ListView` `ListView` `ListView` `Event` `TextView` exibição.

1. Substitua o `TextView` **aplicativo/res/layout/fragment_calendar.xml** por `ListView` um .

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/fragment_calendar.xml" highlight="6-11":::

1. Clique com o botão direito do **mouse na pasta aplicativo/res/layout** e selecione **Novo**, em **seguida, Arquivo de recurso layout.**

1. Nomee o `event_list_item` arquivo, altere **o elemento Root** para e selecione `RelativeLayout` **OK**.

1. Abra o **arquivoevent_list_item.xml** e substitua seu conteúdo pelo seguinte.

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/event_list_item.xml":::

1. Clique com o botão direito do **mouse na pasta app/java/com.example.graphtutorial** e selecione **Novo**, depois **Java Classe**.

1. Nomeia a `EventListAdapter` classe e selecione **OK**.

1. Abra o **arquivo EventListAdapter** e substitua seu conteúdo pelo seguinte.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/EventListAdapter.java" id="EventListAdapterSnippet":::

1. Abra a **classe CalendarFragment** e substitua a função `addEventsToList` existente pelo seguinte.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="AddEventsToListSnippet":::

1. Execute o aplicativo, entre e toque no **item de** navegação Calendário. Você deve ver a lista de eventos.

    ![Uma captura de tela da tabela de eventos](./images/calendar-list.png)
