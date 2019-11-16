# <a name="completed-module-add-azure-ad-authentication"></a>Módulo concluído: Adicionar autenticação do Azure AD

A versão do projeto nesse diretório reflete a conclusão do tutorial para a [adição da autenticação do Azure ad](https://docs.microsoft.com/graph/tutorials/android?tutorial-step=3). Se você usar esta versão do projeto, precisará concluir o restante do tutorial em [obter dados do calendário](https://docs.microsoft.com/graph/tutorials/android?tutorial-step=4).

> **Observação:** Presume-se que você já tenha registrado um aplicativo no portal do Azure conforme especificado em [registrar o aplicativo no portal](https://docs.microsoft.com/graph/tutorials/android?tutorial-step=2). Você precisa configurar esta versão do exemplo da seguinte maneira:
>
> 1. Renomear `./GraphTutorial/msal_config.json.example` o `msal_config.json`arquivo para.
> 1. Mova o `msal_config.json` arquivo para o `./GraphTutorial/app/src/main/res/raw` diretório.
> 1. Edite `msal_config.json` o arquivo e faça as seguintes alterações.
>     1. Substitua `YOUR_APP_ID_HERE` pela **ID do aplicativo** obtida do portal do Azure.
