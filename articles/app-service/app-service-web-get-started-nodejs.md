---
title: 'Início Rápido: Criar um aplicativo Web Node.js'
description: Implante seu primeiro Olá, Mundo em Node.js no Serviço de Aplicativo do Azure em minutos. Você fará a implantação usando o Visual Studio Code, que é um dos vários modos de implantação no Serviço de Aplicativo.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 03/04/2020
ms.custom: mvc, devcenter, seodec18
ms.openlocfilehash: 4f08068e0920eb8a9e93fbbf91cb352bb8860fe3
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2020
ms.locfileid: "84905911"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Criar um aplicativo Web do Node.js no Azure 

Introdução ao Serviço de Aplicativo do Azure com a criação de um aplicativo Node.js/Express localmente usando o Visual Studio Code e, em seguida, a implantação do aplicativo na nuvem. Como você usa uma Camada gratuita do Serviço de Aplicativo, não é gerado nenhum custo para a conclusão deste início rápido.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- [Node.js e npm](https://nodejs.org). Execute o comando `node --version` para verificar se o Node.js está instalado.
- [Visual Studio Code](https://code.visualstudio.com/).
- A [extensão do Serviço de Aplicativo do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) para Visual Studio Code.

## <a name="clone-and-run-a-local-nodejs-application"></a>Clonar e executar um aplicativo Node.js local

1. No computador local, abra um terminal e clone o repositório de exemplo:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Navegue até a nova pasta do aplicativo:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Inicie o aplicativo para testá-lo localmente:

    ```bash
    npm start
    ```
    
1. Abra o navegador e navegue até `http://localhost:1337`. O navegador deverá exibir "Olá, Mundo!".

1. Selecione **CTRL**+**C** no terminal para interromper o servidor.

> [!div class="nextstepaction"]
> [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>Implantar o aplicativo no Azure

Nesta seção, você implantará o aplicativo Node.js no Azure usando o VS Code e a extensão do Serviço de Aplicativo do Azure.

1. No terminal, verifique se você está na pasta *nodejs-docs-hello-world* e, em seguida, inicie o Visual Studio Code com o seguinte comando:

    ```bash
    code .
    ```

1. Na barra de atividade do VS Code, selecione o logotipo do Azure para mostrar o gerenciador do **SERVIÇO DE APLICATIVO DO AZURE**. Selecione **Entrar no Azure...** e siga as instruções. (Confira [Solução de problemas de entrada no Azure](#troubleshooting-azure-sign-in) abaixo em caso de erros.) Após a conexão, o gerenciador deverá mostrar o nome da sua assinatura do Azure.

    ![Entrar no Azure](containers/media/quickstart-nodejs/sign-in.png)

1. No gerenciador do **SERVIÇO DE APLICATIVO DO AZURE** do VS Code, selecione o ícone de seta azul para cima para implantar o aplicativo no Azure. (Invoque também o mesmo comando na **paleta de comandos** (**CTRL**+**Shift**+**P**) digitando 'implantar no aplicativo Web' e escolhendo **Serviço de Aplicativo do Azure: Implantar no Aplicativo Web**).

    ![Implantar no aplicativo Web](containers/media/quickstart-nodejs/deploy.png)
        
1. Escolha a pasta *nodejs-docs-hello-world*.

1. Escolha uma opção de criação com base no sistema operacional no qual você deseja implantar:

    - Linux: Escolha **Criar Aplicativo Web**
    - Windows: Escolha **Criar Aplicativo Web... Avançado**

1. Digite um nome global exclusivo para o aplicativo Web e selecione **Enter**. O nome precisa ser exclusivo em todo o Azure e usar somente caracteres alfanuméricos ('A-Z', 'a-z' e '0-9') e hifens ('-').

1. Se estiver direcionando para o Linux, selecione uma versão do Node.js quando solicitado. Uma versão **LTS** é recomendada.

1. Se o destino for o Windows, siga os avisos adicionais:
    1. Selecione **Criar um grupo de recursos** e, em seguida, insira um nome para o grupo de recursos, como `AppServiceQS-rg`.
    1. Para o sistema operacional, selecione **Windows**.
    1. Selecione **Criar Plano do Serviço de Aplicativo**, insira um nome para o plano (como `AppServiceQS-plan`) e, em seguida, selecione **F1 Gratuito** como o tipo de preço.
    1. Escolha **Ignorar por enquanto** o Application Insights for solicitado.
    1. Escolha uma região perto de você ou perto dos recursos que deseja acessar.

1. Depois de responder a todos os avisos, o VS Code mostrará os recursos do Azure que estão sendo criados para o aplicativo no pop-up de notificação.

    Na implantação no Linux, selecione **Sim** quando precisar atualizar a configuração para executar `npm install` no servidor Linux de destino.

    ![Aviso para atualizar a configuração no servidor Linux de destino](containers/media/quickstart-nodejs/server-build.png)

1. Selecione **Sim** quando for exibido o aviso **Sempre implantar o workspace "nodejs-docs-hello-world" em (nome do aplicativo)"** . A seleção da opção **Sim** instrui o VS Code a ter automaticamente como destino o mesmo aplicativo Web do Serviço de Aplicativo com as implantações seguintes.

1. Na implantação no Linux, selecione **Procurar no Site** no aviso para ver o aplicativo Web recém-implantado após a conclusão da implantação. O navegador deverá exibir "Olá, Mundo!"

1. Se estiver fazendo a implantação no Windows, primeiro, você precisará definir o número de versão do Node.js para o aplicativo Web:

    1. No VS Code, expanda o nó do novo serviço de aplicativo, clique com o botão direito do mouse em **Configurações de Aplicativo** e selecione **Adicionar Nova Configuração...** :

        ![Comando Adicionar configuração de aplicativo](containers/media/quickstart-nodejs/add-setting.png)

    1. Insira `WEBSITE_NODE_DEFAULT_VERSION` para a chave de configuração.
    1. Insira `10.15.2` para o valor da configuração.
    1. Clique com o botão direito do mouse no nó do serviço de aplicativo e selecione **Reiniciar**

        ![Comando Reiniciar serviço de aplicativo](containers/media/quickstart-nodejs/restart.png)

    1. Clique com o botão direito do mouse no nó do serviço de aplicativo mais uma vez e selecione **Procurar no Site**.

> [!div class="nextstepaction"]
> [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Solução de problemas de entrada no Azure

Se for exibido o erro **"Não é possível encontrar a assinatura com o nome [ID da assinatura]"** quando você entrar no Azure, isso poderá ocorrer porque talvez você esteja atrás de um proxy e não consiga acessar a API do Azure. Configure as variáveis de ambiente `HTTP_PROXY` e `HTTPS_PROXY` com suas informações de proxy no seu terminal usando o `export`.

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Se a configuração das variáveis de ambiente não corrigir o problema, entre em contato conosco selecionando o botão **Encontrei um problema** acima.

### <a name="update-the-app"></a>Atualizar o aplicativo

Você pode implantar alterações nesse aplicativo fazendo edições no VS Code, salvando os arquivos e, em seguida, usando o mesmo processo que antes, escolhendo apenas o aplicativo existente, em vez de criar outro.

## <a name="viewing-logs"></a>Exibir logs

Veja a saída de log (chamadas a `console.log`) no aplicativo diretamente na Janela de Saída do VS Code.

1. No gerenciador do **SERVIÇO DE APLICATIVO DO AZURE**, clique com o botão direito do mouse no nó do aplicativo e escolha **Iniciar Streaming de Logs**.

    ![Iniciar Streaming de Logs](containers/media/quickstart-nodejs/view-logs.png)

1. Quando solicitado, escolha habilitar o registro em log e reinicie o aplicativo. Depois que o aplicativo for reiniciado, a janela de saída do VS Code será aberta com uma conexão com o fluxo de log. 

    ![Habilitar registro em log e reiniciar](containers/media/quickstart-nodejs/enable-restart.png)

1. Após alguns segundos, a Janela de Saída mostrará uma mensagem indicando que você está conectado ao serviço de streaming de log. Gere mais atividades de saída atualizando a página no navegador.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).    
    </pre>

> [!div class="nextstepaction"]
> [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Próximas etapas

Parabéns, você concluiu com êxito este início rápido!

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo Node.js com o MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Configurar o aplicativo Node.js](configure-language-nodejs.md)

Confira as outras extensões do Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Ferramentas do Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Ferramentas da CLI do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Ferramentas do Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Ou obtenha todas elas instalando o pacote de extensão [Pacote de Nós para Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack).

