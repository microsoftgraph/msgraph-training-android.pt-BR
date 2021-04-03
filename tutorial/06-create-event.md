<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="cfe95-101">Nesta seção, você adicionará a capacidade de criar eventos no calendário do usuário.</span><span class="sxs-lookup"><span data-stu-id="cfe95-101">In this section you will add the ability to create events on the user's calendar.</span></span>

1. <span data-ttu-id="cfe95-102">Abra **GraphHelper** e adicione as instruções `import` a seguir à parte superior do arquivo.</span><span class="sxs-lookup"><span data-stu-id="cfe95-102">Open **GraphHelper** and add the following `import` statements to the top of the file.</span></span>

    ```java
    import com.microsoft.graph.models.Attendee;
    import com.microsoft.graph.models.DateTimeTimeZone;
    import com.microsoft.graph.models.EmailAddress;
    import com.microsoft.graph.models.ItemBody;
    import com.microsoft.graph.models.AttendeeType;
    import com.microsoft.graph.models.BodyType;
    ```

1. <span data-ttu-id="cfe95-103">Adicione a seguinte função à `GraphHelper` classe para criar um novo evento.</span><span class="sxs-lookup"><span data-stu-id="cfe95-103">Add the following function to the `GraphHelper` class to create a new event.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphHelper.java" id="CreateEventSnippet":::

## <a name="update-new-event-fragment"></a><span data-ttu-id="cfe95-104">Atualizar novo fragmento de evento</span><span class="sxs-lookup"><span data-stu-id="cfe95-104">Update new event fragment</span></span>

1. <span data-ttu-id="cfe95-105">Clique com o botão direito do **mouse na pasta app/java/com.example.graphtutorial** e selecione **Novo**, depois **Java Classe**.</span><span class="sxs-lookup"><span data-stu-id="cfe95-105">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="cfe95-106">Nomeia a `EditTextDateTimePicker` classe e selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="cfe95-106">Name the class `EditTextDateTimePicker` and select **OK**.</span></span>

1. <span data-ttu-id="cfe95-107">Abra o novo arquivo e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="cfe95-107">Open the new file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/EditTextDateTimePicker.java" id="DateTimePickerSnippet":::

    <span data-ttu-id="cfe95-108">Essa classe quebra um controle, mostrando um selador de data e hora quando o usuário toca nele e atualizando o valor com a data e a `EditText` hora escolhida.</span><span class="sxs-lookup"><span data-stu-id="cfe95-108">This class wraps an `EditText` control, showing a date and time picker when the user taps it, and updating the value with the date and time picked.</span></span>

1. <span data-ttu-id="cfe95-109">Abra **aplicativo/res/layout/fragment_new_event.xml** e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="cfe95-109">Open **app/res/layout/fragment_new_event.xml** and replace its contents with the following.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/fragment_new_event.xml":::

1. <span data-ttu-id="cfe95-110">Abra **NewEventFragment** e adicione as instruções a seguir `import` na parte superior do arquivo.</span><span class="sxs-lookup"><span data-stu-id="cfe95-110">Open **NewEventFragment** and add the following `import` statements at the top of the file.</span></span>

    ```java
    import android.util.Log;
    import android.widget.Button;
    import com.google.android.material.snackbar.BaseTransientBottomBar;
    import com.google.android.material.snackbar.Snackbar;
    import com.google.android.material.textfield.TextInputLayout;
    import java.time.ZoneId;
    import java.time.ZonedDateTime;
    ```

1. <span data-ttu-id="cfe95-111">Adicione os membros a seguir à `NewEventFragment` classe.</span><span class="sxs-lookup"><span data-stu-id="cfe95-111">Add the following members to the `NewEventFragment` class.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="InputsSnippet":::

1. <span data-ttu-id="cfe95-112">Adicione as seguintes funções para mostrar e ocultar uma barra de progresso.</span><span class="sxs-lookup"><span data-stu-id="cfe95-112">Add the following functions to show and hide a progress bar.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="ProgressBarSnippet":::

1. <span data-ttu-id="cfe95-113">Adicione as seguintes funções para obter os valores dos controles de entrada e chamar a `GraphHelper.createEvent` função.</span><span class="sxs-lookup"><span data-stu-id="cfe95-113">Add the following functions to get the values from the input controls and call the `GraphHelper.createEvent` function.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="CreateEventSnippet":::

1. <span data-ttu-id="cfe95-114">Substitua o existente `onCreateView` pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="cfe95-114">Replace the existing `onCreateView` with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="OnCreateViewSnippet":::

1. <span data-ttu-id="cfe95-115">Salve suas alterações e reinicie o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cfe95-115">Save your changes and restart the app.</span></span> <span data-ttu-id="cfe95-116">Selecione o **item de** menu Novo Evento, preencha o formulário e selecione **CRIAR**.</span><span class="sxs-lookup"><span data-stu-id="cfe95-116">Select the **New Event** menu item, fill in the form, and select **CREATE**.</span></span>

    ![Uma captura de tela do formulário de evento create no aplicativo](images/create-event.png)
