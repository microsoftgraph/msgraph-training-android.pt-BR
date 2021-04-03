<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="e3280-101">Este tutorial ensina como criar um aplicativo Android que usa a API do Microsoft Graph para recuperar informações de calendário para um usuário.</span><span class="sxs-lookup"><span data-stu-id="e3280-101">This tutorial teaches you how to build an Android app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="e3280-102">Se você preferir apenas baixar o tutorial concluído, poderá baixar ou clonar o [repositório do GitHub.](https://github.com/microsoftgraph/msgraph-training-android)</span><span class="sxs-lookup"><span data-stu-id="e3280-102">If you prefer to just download the completed tutorial, you can download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-android).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e3280-103">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="e3280-103">Prerequisites</span></span>

<span data-ttu-id="e3280-104">Antes de iniciar este tutorial, você deve ter [o Android Studio](https://developer.android.com/studio/) instalado em sua máquina de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="e3280-104">Before you start this tutorial, you should have [Android Studio](https://developer.android.com/studio/) installed on your development machine.</span></span>

<span data-ttu-id="e3280-105">Você também deve ter uma conta pessoal da Microsoft com uma caixa de correio no Outlook.com, ou uma conta de estudante ou de trabalho da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="e3280-105">You should also have either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span> <span data-ttu-id="e3280-106">Se você não tiver uma conta da Microsoft, há algumas opções para obter uma conta gratuita:</span><span class="sxs-lookup"><span data-stu-id="e3280-106">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="e3280-107">Você pode [se inscrever em uma nova conta pessoal da Microsoft.](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)</span><span class="sxs-lookup"><span data-stu-id="e3280-107">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="e3280-108">Você pode se inscrever no Programa de Desenvolvedores do [Office 365](https://developer.microsoft.com/office/dev-program) para obter uma assinatura gratuita do Office 365.</span><span class="sxs-lookup"><span data-stu-id="e3280-108">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

> [!NOTE]
> <span data-ttu-id="e3280-109">Este tutorial foi escrito com o Android Studio versão 4.1.3 e o SDK do Android 10.0.</span><span class="sxs-lookup"><span data-stu-id="e3280-109">This tutorial was written with Android Studio version 4.1.3 and the Android 10.0 SDK.</span></span> <span data-ttu-id="e3280-110">As etapas neste guia podem funcionar com outras versões, mas que não foram testadas.</span><span class="sxs-lookup"><span data-stu-id="e3280-110">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="feedback"></a><span data-ttu-id="e3280-111">Comentários</span><span class="sxs-lookup"><span data-stu-id="e3280-111">Feedback</span></span>

<span data-ttu-id="e3280-112">Forneça qualquer comentário sobre este tutorial no repositório [do GitHub.](https://github.com/microsoftgraph/msgraph-training-android)</span><span class="sxs-lookup"><span data-stu-id="e3280-112">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-android).</span></span>
