<!-- markdownlint-disable MD002 MD041 -->

Neste exercício, você criará um novo aplicativo nativo do Azure AD usando o centro de administração do Azure Active Directory.

1. Abra um navegador, navegue até o [centro de administração do Azure Active Directory](https://aad.portal.azure.com) e faça logon usando uma **conta pessoal** (também conhecida como conta da Microsoft) ou **Conta Corporativa ou de Estudante**.

1. Selecione **Azure Active Directory** na navegação à esquerda e, em seguida, selecione **registros de aplicativo** em **gerenciar**.

    ![Uma captura de tela dos registros de aplicativo ](./images/aad-portal-app-registrations.png)

1. Selecione **Novo registro**. Na página **Registrar um aplicativo**, defina os valores da seguinte forma.

    - Defina **Nome** para `Android Graph Tutorial`.
    - Defina **Tipos de conta com suporte** para **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
    - Deixe o **URI de Redirecionamento** vazio.

    ![Uma captura de tela da página registrar um aplicativo](./images/aad-register-an-app.png)

1. Escolha **Registrar**. Na página **tutorial do Android Graph** , copie o valor da **ID do aplicativo (cliente)** e salve-o, você precisará dele na próxima etapa.

    ![Uma captura de tela da ID do aplicativo do novo registro de aplicativo](./images/aad-application-id.png)

1. Selecione o link **Adicionar um URI** de redirecionamento. Na página **redirecionar URIs** , localize a seção redirecionar **URIs sugeridos para clientes públicos (móvel, área de trabalho)** . Selecione o URI que começa com `msal` e copie-o e, em seguida, escolha **salvar**. Salve o URI de redirecionamento copiado, será necessário na próxima etapa.

    ![Captura de tela da página URIs de redirecionamento](./images/aad-redirect-uris.png)