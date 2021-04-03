<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="c8f94-101">Neste exercício, você incorporará o Microsoft Graph ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c8f94-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="c8f94-102">Para este aplicativo, você usará o [SDK](https://github.com/microsoftgraph/msgraph-sdk-java) do Microsoft Graph para Java fazer chamadas para o Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="c8f94-102">For this application, you will use the [Microsoft Graph SDK for Java](https://github.com/microsoftgraph/msgraph-sdk-java) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="c8f94-103">Obtenha eventos de calendário do Outlook</span><span class="sxs-lookup"><span data-stu-id="c8f94-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="c8f94-104">Nesta seção, você estenderá a classe para adicionar uma função para obter os eventos do usuário para a semana atual e atualizar para `GraphHelper` `CalendarFragment` usar essas novas funções.</span><span class="sxs-lookup"><span data-stu-id="c8f94-104">In this section you will extend the `GraphHelper` class to add a function to get the user's events for the current week and update `CalendarFragment` to use these new functions.</span></span>

1. <span data-ttu-id="c8f94-105">Abra **GraphHelper** e adicione as instruções `import` a seguir à parte superior do arquivo.</span><span class="sxs-lookup"><span data-stu-id="c8f94-105">Open **GraphHelper** and add the following `import` statements to the top of the file.</span></span>

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

1. <span data-ttu-id="c8f94-106">Adicione as seguintes funções à `GraphHelper` classe.</span><span class="sxs-lookup"><span data-stu-id="c8f94-106">Add the following functions to the `GraphHelper` class.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphHelper.java" id="GetEventsSnippet":::

    > [!NOTE]
    > <span data-ttu-id="c8f94-107">Considere o que o código `getCalendarView` está fazendo.</span><span class="sxs-lookup"><span data-stu-id="c8f94-107">Consider what the code in `getCalendarView` is doing.</span></span>
    >
    > - <span data-ttu-id="c8f94-108">O URL que será chamado é `/v1.0/me/calendarview`.</span><span class="sxs-lookup"><span data-stu-id="c8f94-108">The URL that will be called is `/v1.0/me/calendarview`.</span></span>
    >   - <span data-ttu-id="c8f94-109">Os `startDateTime` `endDateTime` parâmetros e consulta definem o início e o fim do exibição de calendário.</span><span class="sxs-lookup"><span data-stu-id="c8f94-109">The `startDateTime` and `endDateTime` query parameters define the start and end of the calendar view.</span></span>
    >   - <span data-ttu-id="c8f94-110">o header faz com que o Microsoft Graph retorne os horários de início e término de cada evento `Prefer: outlook.timezone` no fuso horário do usuário.</span><span class="sxs-lookup"><span data-stu-id="c8f94-110">the `Prefer: outlook.timezone` header causes the Microsoft Graph to return the start and end times of each event in the user's time zone.</span></span>
    >   - <span data-ttu-id="c8f94-111">A função `select` limita os campos retornados para cada evento apenas para aqueles que o modo de exibição realmente usará.</span><span class="sxs-lookup"><span data-stu-id="c8f94-111">The `select` function limits the fields returned for each events to just those the view will actually use.</span></span>
    >   - <span data-ttu-id="c8f94-112">A `orderby` função classifica os resultados por hora de início.</span><span class="sxs-lookup"><span data-stu-id="c8f94-112">The `orderby` function sorts the results by start time.</span></span>
    >   - <span data-ttu-id="c8f94-113">A `top` função solicita 25 resultados por página.</span><span class="sxs-lookup"><span data-stu-id="c8f94-113">The `top` function requests 25 results per page.</span></span>
    > - <span data-ttu-id="c8f94-114">A função verifica se há mais resultados disponíveis e `processPage` solicita páginas adicionais, se necessário.</span><span class="sxs-lookup"><span data-stu-id="c8f94-114">The `processPage` function checks if there are more results available and requests additional pages if needed.</span></span>

1. <span data-ttu-id="c8f94-115">Clique com o botão direito do **mouse na pasta app/java/com.example.graphtutorial** e selecione **Novo**, depois **Java Classe**.</span><span class="sxs-lookup"><span data-stu-id="c8f94-115">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="c8f94-116">Nomeia a `GraphToIana` classe e selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="c8f94-116">Name the class `GraphToIana` and select **OK**.</span></span>

1. <span data-ttu-id="c8f94-117">Abra o novo arquivo e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="c8f94-117">Open the new file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphToIana.java" id="GraphToIanaSnippet":::

1. <span data-ttu-id="c8f94-118">Adicione as instruções `import` a seguir à parte superior do arquivo **CalendarFragment.**</span><span class="sxs-lookup"><span data-stu-id="c8f94-118">Add the following `import` statements to the top of the **CalendarFragment** file.</span></span>

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

1. <span data-ttu-id="c8f94-119">Adicione o membro a seguir à `CalendarFragment` classe.</span><span class="sxs-lookup"><span data-stu-id="c8f94-119">Add the following member to the `CalendarFragment` class.</span></span>

    ```java
    private List<Event> mEventList = null;
    ```

1. <span data-ttu-id="c8f94-120">Adicione as seguintes funções à classe `CalendarFragment` para ocultar e mostrar a barra de progresso.</span><span class="sxs-lookup"><span data-stu-id="c8f94-120">Add the following functions to the `CalendarFragment` class to hide and show the progress bar.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="ProgressBarSnippet":::

1. <span data-ttu-id="c8f94-121">Adicione a função a seguir para saída da lista de eventos para fins de depuração.</span><span class="sxs-lookup"><span data-stu-id="c8f94-121">Add the following function to output the event list for debugging purposes.</span></span>

    ```java
    private void addEventsToList() {
        // Temporary for debugging
        String jsonEvents = GraphHelper.getInstance().serializeObject(mEventList);
        Log.d("GRAPH", jsonEvents);
    }
    ```

1. <span data-ttu-id="c8f94-122">Substitua a função `onCreateView` existente na classe pelo `CalendarFragment` seguinte.</span><span class="sxs-lookup"><span data-stu-id="c8f94-122">Replace the existing `onCreateView` function in the `CalendarFragment` class with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="OnCreateViewSnippet":::

1. <span data-ttu-id="c8f94-123">Execute o aplicativo, entre e toque **no** item de navegação Calendário no menu.</span><span class="sxs-lookup"><span data-stu-id="c8f94-123">Run the app, sign in, and tap the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="c8f94-124">Você deve ver um despejo JSON dos eventos no log de depuração no Android Studio.</span><span class="sxs-lookup"><span data-stu-id="c8f94-124">You should see a JSON dump of the events in the debug log in Android Studio.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="c8f94-125">Exibir os resultados</span><span class="sxs-lookup"><span data-stu-id="c8f94-125">Display the results</span></span>

<span data-ttu-id="c8f94-126">Agora você pode substituir o despejo JSON por algo para exibir os resultados de maneira amigável.</span><span class="sxs-lookup"><span data-stu-id="c8f94-126">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span> <span data-ttu-id="c8f94-127">Nesta seção, você adicionará um ao fragmento de calendário, criará um layout para cada item no , e criará um adaptador de lista personalizado para o que mapeia os campos de cada um para o apropriado na `ListView` `ListView` `ListView` `Event` `TextView` exibição.</span><span class="sxs-lookup"><span data-stu-id="c8f94-127">In this section, you will add a `ListView` to the calendar fragment, create a layout for each item in the `ListView`, and create a custom list adapter for the `ListView` that maps the fields of each `Event` to the appropriate `TextView` in the view.</span></span>

1. <span data-ttu-id="c8f94-128">Substitua o `TextView` **aplicativo/res/layout/fragment_calendar.xml** por `ListView` um .</span><span class="sxs-lookup"><span data-stu-id="c8f94-128">Replace the `TextView` in **app/res/layout/fragment_calendar.xml** with a `ListView`.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/fragment_calendar.xml" highlight="6-11":::

1. <span data-ttu-id="c8f94-129">Clique com o botão direito do **mouse na pasta aplicativo/res/layout** e selecione **Novo**, em **seguida, Arquivo de recurso layout.**</span><span class="sxs-lookup"><span data-stu-id="c8f94-129">Right-click the **app/res/layout** folder and select **New**, then **Layout resource file**.</span></span>

1. <span data-ttu-id="c8f94-130">Nomee o `event_list_item` arquivo, altere **o elemento Root** para e selecione `RelativeLayout` **OK**.</span><span class="sxs-lookup"><span data-stu-id="c8f94-130">Name the file `event_list_item`, change the **Root element** to `RelativeLayout`, and select **OK**.</span></span>

1. <span data-ttu-id="c8f94-131">Abra o **arquivoevent_list_item.xml** e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="c8f94-131">Open the **event_list_item.xml** file and replace its contents with the following.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/event_list_item.xml":::

1. <span data-ttu-id="c8f94-132">Clique com o botão direito do **mouse na pasta app/java/com.example.graphtutorial** e selecione **Novo**, depois **Java Classe**.</span><span class="sxs-lookup"><span data-stu-id="c8f94-132">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span>

1. <span data-ttu-id="c8f94-133">Nomeia a `EventListAdapter` classe e selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="c8f94-133">Name the class `EventListAdapter` and select **OK**.</span></span>

1. <span data-ttu-id="c8f94-134">Abra o **arquivo EventListAdapter** e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="c8f94-134">Open the **EventListAdapter** file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/EventListAdapter.java" id="EventListAdapterSnippet":::

1. <span data-ttu-id="c8f94-135">Abra a **classe CalendarFragment** e substitua a função `addEventsToList` existente pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="c8f94-135">Open the **CalendarFragment** class and replace the existing `addEventsToList` function with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="AddEventsToListSnippet":::

1. <span data-ttu-id="c8f94-136">Execute o aplicativo, entre e toque no **item de** navegação Calendário.</span><span class="sxs-lookup"><span data-stu-id="c8f94-136">Run the app, sign in, and tap the **Calendar** navigation item.</span></span> <span data-ttu-id="c8f94-137">Você deve ver a lista de eventos.</span><span class="sxs-lookup"><span data-stu-id="c8f94-137">You should see the list of events.</span></span>

    ![Uma captura de tela da tabela de eventos](./images/calendar-list.png)
