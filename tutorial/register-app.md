<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="164fa-101">Neste exercício, você criará um novo aplicativo nativo do Azure AD usando o centro de administração do Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="164fa-101">In this exercise you will create a new Azure AD native application using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="164fa-102">Abra um navegador, navegue até o [centro de administração do Azure Active Directory](https://aad.portal.azure.com) e faça logon usando uma **conta pessoal** (também conhecida como conta da Microsoft) ou **Conta Corporativa ou de Estudante**.</span><span class="sxs-lookup"><span data-stu-id="164fa-102">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com) and login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="164fa-103">Selecione **Azure Active Directory** na navegação à esquerda e, em seguida, selecione **registros de aplicativo** em **gerenciar**.</span><span class="sxs-lookup"><span data-stu-id="164fa-103">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="164fa-104">Uma captura de tela dos registros de aplicativo</span><span class="sxs-lookup"><span data-stu-id="164fa-104">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

1. <span data-ttu-id="164fa-105">Selecione **Novo registro**.</span><span class="sxs-lookup"><span data-stu-id="164fa-105">Select **New registration**.</span></span> <span data-ttu-id="164fa-106">Na página **Registrar um aplicativo**, defina os valores da seguinte forma.</span><span class="sxs-lookup"><span data-stu-id="164fa-106">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="164fa-107">Defina **Nome** para `Android Graph Tutorial`.</span><span class="sxs-lookup"><span data-stu-id="164fa-107">Set **Name** to `Android Graph Tutorial`.</span></span>
    - <span data-ttu-id="164fa-108">Defina **Tipos de conta com suporte** para **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.</span><span class="sxs-lookup"><span data-stu-id="164fa-108">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="164fa-109">Deixe o **URI de Redirecionamento** vazio.</span><span class="sxs-lookup"><span data-stu-id="164fa-109">Leave **Redirect URI** empty.</span></span>

    ![Uma captura de tela da página registrar um aplicativo](./images/aad-register-an-app.png)

1. <span data-ttu-id="164fa-111">Escolha **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="164fa-111">Choose **Register**.</span></span> <span data-ttu-id="164fa-112">Na página **tutorial do Android Graph** , copie o valor da **ID do aplicativo (cliente)** e salve-o, você precisará dele na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="164fa-112">On the **Android Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Uma captura de tela da ID do aplicativo do novo registro de aplicativo](./images/aad-application-id.png)

1. <span data-ttu-id="164fa-114">Selecione o link **Adicionar um URI** de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="164fa-114">Select the **Add a Redirect URI** link.</span></span> <span data-ttu-id="164fa-115">Na página **redirecionar URIs** , localize a seção redirecionar **URIs sugeridos para clientes públicos (móvel, área de trabalho)** .</span><span class="sxs-lookup"><span data-stu-id="164fa-115">On the **Redirect URIs** page, locate the **Suggested Redirect URIs for public clients (mobile, desktop)** section.</span></span> <span data-ttu-id="164fa-116">Selecione o URI que começa com `msal` e copie-o e, em seguida, escolha **salvar**.</span><span class="sxs-lookup"><span data-stu-id="164fa-116">Select the URI that begins with `msal` and copy it, then choose **Save**.</span></span> <span data-ttu-id="164fa-117">Salve o URI de redirecionamento copiado, será necessário na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="164fa-117">Save the copied redirect URI, you will need it in the next step.</span></span>

    ![Captura de tela da página URIs de redirecionamento](./images/aad-redirect-uris.png)