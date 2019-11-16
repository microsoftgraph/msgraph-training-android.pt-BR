# <a name="how-to-run-the-completed-project"></a>Como executar o projeto concluído

## <a name="prerequisites"></a>Pré-requisitos

Para executar o projeto concluído nessa pasta, você precisará do seguinte:

- [Android Studio](https://developer.android.com/studio/) instalado em sua máquina de desenvolvimento. (**Observação:** este tutorial foi escrito com o Android Studio versão 3.5.1 e o SDK 10,0 do Android. As etapas deste guia podem funcionar com outras versões, mas que não foram testadas.
- Uma conta pessoal da Microsoft com uma caixa de correio no Outlook.com ou uma conta corporativa ou de estudante da Microsoft.

Se você não tem uma conta da Microsoft, há algumas opções para obter uma conta gratuita:

- Você pode [se inscrever para uma nova conta pessoal da Microsoft](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).
- Você pode [se inscrever no programa para desenvolvedores do office 365](https://developer.microsoft.com/office/dev-program) para obter uma assinatura gratuita do Office 365.

## <a name="register-an-application-with-the-azure-portal"></a>Registrar um aplicativo com o portal do Azure

1. Abra um navegador, navegue até o [centro de administração do Azure Active Directory](https://aad.portal.azure.com) e faça logon usando uma **conta pessoal** (também conhecida como conta da Microsoft) ou **Conta Corporativa ou de Estudante**.

1. Selecione **Azure Active Directory** na navegação esquerda e selecione **Registros de aplicativos** em **Gerenciar**.

    ![Uma captura de tela dos registros de aplicativo ](../../tutorial/images/aad-portal-app-registrations.png)

1. Selecione **Novo registro**. Na página **Registrar um aplicativo**, defina os valores da seguinte forma.

    - Defina **Nome** para `Android Graph Tutorial`.
    - Defina **Tipos de conta com suporte** para **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
    - Em **URI de redirecionamento**, defina o menu suspenso como **cliente público/nativo (celular & Desktop)** e `msauth://YOUR_PACKAGE_NAME/callback`defina o `YOUR_PACKAGE_NAME` valor como, substituindo com o nome do pacote do seu projeto.

    ![Uma captura de tela da página registrar um aplicativo](../../tutorial/images/aad-register-an-app.png)

1. Selecione **registrar**. Na página **tutorial do Android Graph** , copie o valor da **ID do aplicativo (cliente)** e salve-o, você precisará dele na próxima etapa.

    ![Uma captura de tela da ID do aplicativo do novo registro de aplicativo](../../tutorial/images/aad-application-id.png)

## <a name="configure-the-sample"></a>Configurar o exemplo

1. Renomeie `msal_config.json.example` o `msal_config.json` arquivo e mova o arquivo para `GraphTutorial/app/src/main/res/raw` o diretório.
1. Edite `msal_config.json` o arquivo e faça as seguintes alterações.
    1. Substitua `YOUR_APP_ID_HERE` pela **ID do aplicativo** obtida do portal do Azure.

## <a name="run-the-sample"></a>Executar o exemplo

No Android Studio, selecione **executar "aplicativo"** no menu **executar** .
