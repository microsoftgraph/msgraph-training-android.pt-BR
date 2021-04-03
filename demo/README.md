# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="06752-101">Como executar o projeto concluído</span><span class="sxs-lookup"><span data-stu-id="06752-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="06752-102">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="06752-102">Prerequisites</span></span>

<span data-ttu-id="06752-103">Para executar o projeto concluído nesta pasta, você precisa do seguinte:</span><span class="sxs-lookup"><span data-stu-id="06752-103">To run the completed project in this folder, you need the following:</span></span>

- <span data-ttu-id="06752-104">[Android Studio](https://developer.android.com/studio/) instalado em sua máquina de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="06752-104">[Android Studio](https://developer.android.com/studio/) installed on your development machine.</span></span> <span data-ttu-id="06752-105">(**Observação:** este tutorial foi escrito com o Android Studio versão 4.1.3 e o SDK do Android 10.0.</span><span class="sxs-lookup"><span data-stu-id="06752-105">(**Note:** This tutorial was written with Android Studio version 4.1.3 and the Android 10.0 SDK.</span></span> <span data-ttu-id="06752-106">As etapas neste guia podem funcionar com outras versões, mas que não foram testadas.)</span><span class="sxs-lookup"><span data-stu-id="06752-106">The steps in this guide may work with other versions, but that has not been tested.)</span></span>
- <span data-ttu-id="06752-107">Uma conta pessoal da Microsoft com uma caixa de correio Outlook.com ou uma conta de estudante ou de trabalho da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="06752-107">Either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span>

<span data-ttu-id="06752-108">Se você não tiver uma conta da Microsoft, há algumas opções para obter uma conta gratuita:</span><span class="sxs-lookup"><span data-stu-id="06752-108">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="06752-109">Você pode [se inscrever em uma nova conta pessoal da Microsoft.](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)</span><span class="sxs-lookup"><span data-stu-id="06752-109">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="06752-110">Você pode se inscrever no Programa de Desenvolvedores do [Office 365](https://developer.microsoft.com/office/dev-program) para obter uma assinatura gratuita do Office 365.</span><span class="sxs-lookup"><span data-stu-id="06752-110">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-an-application-with-the-azure-portal"></a><span data-ttu-id="06752-111">Registrar um aplicativo no Portal do Azure</span><span class="sxs-lookup"><span data-stu-id="06752-111">Register an application with the Azure Portal</span></span>

1. <span data-ttu-id="06752-112">Abra um navegador, navegue até o [centro de administração do Azure Active Directory](https://aad.portal.azure.com) e faça logon usando uma **conta pessoal** (também conhecida como conta da Microsoft) ou **Conta Corporativa ou de Estudante**.</span><span class="sxs-lookup"><span data-stu-id="06752-112">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com) and login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="06752-113">Selecione **Azure Active Directory** na navegação esquerda e selecione **Registros de aplicativos** em **Gerenciar**.</span><span class="sxs-lookup"><span data-stu-id="06752-113">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="06752-114">Uma captura de tela dos registros do aplicativo</span><span class="sxs-lookup"><span data-stu-id="06752-114">A screenshot of the App registrations</span></span> ](../tutorial/images/aad-portal-app-registrations.png)

1. <span data-ttu-id="06752-115">Selecione **Novo registro**.</span><span class="sxs-lookup"><span data-stu-id="06752-115">Select **New registration**.</span></span> <span data-ttu-id="06752-116">Na página **Registrar um aplicativo**, defina os valores da seguinte forma.</span><span class="sxs-lookup"><span data-stu-id="06752-116">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="06752-117">Defina **Nome** para `Android Graph Tutorial`.</span><span class="sxs-lookup"><span data-stu-id="06752-117">Set **Name** to `Android Graph Tutorial`.</span></span>
    - <span data-ttu-id="06752-118">Defina **Tipos de conta com suporte** para **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.</span><span class="sxs-lookup"><span data-stu-id="06752-118">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="06752-119">Em **URI** de redirecionamento, de definir o menu suspenso como Cliente **público/nativo (área** de trabalho móvel &) e de definir o valor como , substituindo pelo nome do pacote `msauth://YOUR_PACKAGE_NAME/callback` do seu `YOUR_PACKAGE_NAME` projeto.</span><span class="sxs-lookup"><span data-stu-id="06752-119">Under **Redirect URI**, set the dropdown to **Public client/native (mobile & desktop)** and set the value to `msauth://YOUR_PACKAGE_NAME/callback`, replacing `YOUR_PACKAGE_NAME` with your project's package name.</span></span>

    ![Captura de tela da página Registrar um aplicativo](../tutorial/images/aad-register-an-app.png)

1. <span data-ttu-id="06752-121">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="06752-121">Select **Register**.</span></span> <span data-ttu-id="06752-122">Na página **Tutorial do Android Graph,** copie o valor da ID do Aplicativo **(cliente)** e salve-a, você precisará dele na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="06752-122">On the **Android Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Captura de tela da ID do aplicativo do novo registro do aplicativo](../tutorial/images/aad-application-id.png)

## <a name="configure-the-sample"></a><span data-ttu-id="06752-124">Configurar o exemplo</span><span class="sxs-lookup"><span data-stu-id="06752-124">Configure the sample</span></span>

1. <span data-ttu-id="06752-125">Renomeie `msal_config.json.example` o arquivo para `msal_config.json` e mova o arquivo para o `GraphTutorial/app/src/main/res/raw` diretório.</span><span class="sxs-lookup"><span data-stu-id="06752-125">Rename the `msal_config.json.example` file to `msal_config.json` and move the file into the `GraphTutorial/app/src/main/res/raw` directory.</span></span>
1. <span data-ttu-id="06752-126">Edite `msal_config.json` o arquivo e faça as seguintes alterações.</span><span class="sxs-lookup"><span data-stu-id="06752-126">Edit the `msal_config.json` file and make the following changes.</span></span>
    1. <span data-ttu-id="06752-127">Substitua `YOUR_APP_ID_HERE` pela **ID do Aplicativo que** você recebeu do portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="06752-127">Replace `YOUR_APP_ID_HERE` with the **Application Id** you got from the Azure portal.</span></span>
    1. <span data-ttu-id="06752-128">Substitua `com.example.graphtutorial` pelo nome do pacote.</span><span class="sxs-lookup"><span data-stu-id="06752-128">Replace `com.example.graphtutorial` with your package name.</span></span>

## <a name="run-the-sample"></a><span data-ttu-id="06752-129">Executar o exemplo</span><span class="sxs-lookup"><span data-stu-id="06752-129">Run the sample</span></span>

<span data-ttu-id="06752-130">No Android Studio, selecione **Executar 'aplicativo'** **no** menu Executar.</span><span class="sxs-lookup"><span data-stu-id="06752-130">In Android Studio, select **Run 'app'** on the **Run** menu.</span></span>
