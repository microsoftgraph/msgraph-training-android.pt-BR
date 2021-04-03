<!-- markdownlint-disable MD002 MD041 -->

Nesta seção, você adicionará a capacidade de criar eventos no calendário do usuário.

1. Abra **GraphHelper** e adicione as instruções `import` a seguir à parte superior do arquivo.

    ```java
    import com.microsoft.graph.models.Attendee;
    import com.microsoft.graph.models.DateTimeTimeZone;
    import com.microsoft.graph.models.EmailAddress;
    import com.microsoft.graph.models.ItemBody;
    import com.microsoft.graph.models.AttendeeType;
    import com.microsoft.graph.models.BodyType;
    ```

1. Adicione a seguinte função à `GraphHelper` classe para criar um novo evento.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphHelper.java" id="CreateEventSnippet":::

## <a name="update-new-event-fragment"></a>Atualizar novo fragmento de evento

1. Clique com o botão direito do **mouse na pasta app/java/com.example.graphtutorial** e selecione **Novo**, depois **Java Classe**. Nomeia a `EditTextDateTimePicker` classe e selecione **OK**.

1. Abra o novo arquivo e substitua seu conteúdo pelo seguinte.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/EditTextDateTimePicker.java" id="DateTimePickerSnippet":::

    Essa classe quebra um controle, mostrando um selador de data e hora quando o usuário toca nele e atualizando o valor com a data e a `EditText` hora escolhida.

1. Abra **aplicativo/res/layout/fragment_new_event.xml** e substitua seu conteúdo pelo seguinte.

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/fragment_new_event.xml":::

1. Abra **NewEventFragment** e adicione as instruções a seguir `import` na parte superior do arquivo.

    ```java
    import android.util.Log;
    import android.widget.Button;
    import com.google.android.material.snackbar.BaseTransientBottomBar;
    import com.google.android.material.snackbar.Snackbar;
    import com.google.android.material.textfield.TextInputLayout;
    import java.time.ZoneId;
    import java.time.ZonedDateTime;
    ```

1. Adicione os membros a seguir à `NewEventFragment` classe.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="InputsSnippet":::

1. Adicione as seguintes funções para mostrar e ocultar uma barra de progresso.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="ProgressBarSnippet":::

1. Adicione as seguintes funções para obter os valores dos controles de entrada e chamar a `GraphHelper.createEvent` função.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="CreateEventSnippet":::

1. Substitua o existente `onCreateView` pelo seguinte.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="OnCreateViewSnippet":::

1. Salve suas alterações e reinicie o aplicativo. Selecione o **item de** menu Novo Evento, preencha o formulário e selecione **CRIAR**.

    ![Uma captura de tela do formulário de evento create no aplicativo](images/create-event.png)
