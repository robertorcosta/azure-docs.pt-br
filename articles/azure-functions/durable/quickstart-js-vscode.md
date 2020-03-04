---
title: Criar sua primeira função durável no Azure usando JavaScript
description: Criar e publicar uma Durable Function do Azure usando o Visual Studio Code.
author: ColbyTresness
ms.topic: quickstart
ms.date: 11/07/2018
ms.reviewer: azfuncdf, cotresne
ms.openlocfilehash: 431bd45763cbe24e44d47342b32c5c452a27b0f6
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210286"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Criar sua primeira função durável em JavaScript

*Durable Functions* são uma extensão do [Azure Functions](../functions-overview.md) que permitem que você escreva funções com estado em um ambiente sem servidor. A extensão gerencia estado, pontos de verificação e reinicializações para você.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

Neste artigo, você aprenderá a usar a extensão do Azure Functions do Visual Studio Code para criar e testar localmente uma função durável "hello world".  Essa função orquestrará e encadeará chamadas para outras funções. Em seguida, você publicará o código de função no Azure.

![Como executar funções duráveis no Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Prerequisites

Para concluir este tutorial:

* Instale o [Visual Studio Code](https://code.visualstudio.com/download).

* Verifique se tem a versão mais recente do [Azure Functions Core Tools](../functions-run-local.md).

* Em um computador Windows, verifique se você tem o [Emulador de Armazenamento do Azure](../../storage/common/storage-use-emulator.md) instalado e em execução. Em um computador Mac ou Linux, você deve usar uma conta de armazenamento real do Azure.

* Verifique se você tem a versão 8.0 ou posterior do [Node.js](https://nodejs.org/) instalada.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Criar seu projeto local 

Nesta seção, você usará o Visual Studio Code para criar um projeto local do Azure Functions. 

1. No Visual Studio Code, pressione F1 para abrir a paleta de comandos. Na paleta de comandos, pesquise e selecione `Azure Functions: Create new project...`.

1. Escolha um local de diretório para o workspace do projeto e escolha **Selecionar**.

    > [!NOTE]
    > Estas etapas foram projetadas para serem concluídas fora de um workspace. Nesse caso, não selecione uma pasta de projeto que faz parte de um workspace.

1. Seguindo os prompts, forneça as seguintes informações referentes à linguagem desejada:

    | Prompt | Valor | Descrição |
    | ------ | ----- | ----------- |
    | Selecione uma linguagem de programação para o seu projeto de aplicativo de função. | JavaScript | Criar um projeto local do Functions em Node.js. |
    | Selecione uma versão | Azure Functions v2 | Você verá essa opção somente quando o Core Tools ainda não estiver instalado. Nesse caso, o Core Tools é instalado na primeira vez em que você executa o aplicativo. |
    | Selecione um modelo para a primeira função do projeto | Gatilho HTTP | Crie uma função disparada por HTTP no novo aplicativo de funções. |
    | Forneça um nome de função | HttpTrigger | Pressione Enter para usar o nome padrão. |
    | Nível de autorização | Função | O nível de autorização `function` exige que você forneça uma chave de acesso ao chamar o ponto de extremidade HTTP da função. Isso dificulta o acesso a um ponto de extremidade desprotegido. Para saber mais, confira [Chaves de autorização](../functions-bindings-http-webhook-trigger.md#authorization-keys).  |
    | Selecione como você gostaria de abrir seu projeto | Adicionar ao workspace | Cria o aplicativo de funções no workspace atual. |

O Visual Studio Code instalará o Azure Functions Core Tools se necessário. Ele também cria o projeto de aplicativo de funções em um novo workspace. Este projeto contém os arquivos de configuração [host.json](../functions-host-json.md) e [local.settings.json](../functions-run-local.md#local-settings-file). Ele também cria uma pasta HttpExample que contém o [arquivo de definição function.json](../functions-reference-node.md#folder-structure) e o [arquivo index.js](../functions-reference-node.md#exporting-a-function), um arquivo Node.js que contém o código de função.

Um arquivo package.json também é criado na pasta raiz.

## <a name="install-the-durable-functions-npm-package"></a>Instalar o pacote npm de Durable Functions

1. Instale o pacote npm `durable-functions` executando `npm install durable-functions` no diretório raiz do aplicativo de funções.

## <a name="creating-your-functions"></a>Criando suas funções

Agora, vamos criar as três funções necessárias para começar a usar o Durable Functions: um iniciador de HTTP, um orquestrador e uma função de atividade. O iniciador de HTTP estabelecerá toda a solução e o orquestrador expedirá trabalho para várias funções de atividade.

### <a name="http-starter"></a>Iniciador de HTTP

Primeiro, crie uma função disparada por HTTP que inicia uma orquestração de função durável.

1. Do *Azure: Functions*, escolha o ícone **Criar Função**.

    ![Criar uma função](./media/quickstart-js-vscode/create-function.png)

2. Selecione a pasta com seu projeto de aplicativo de funções e selecione o modelo de função **Iniciador de HTTP do Durable Functions**.

    ![Escolher o modelo de iniciador de HTTP](./media/quickstart-js-vscode/create-function-choose-template.png)

3. Deixe o nome padrão como `DurableFunctionsHttpStart` e pressione ** **Enter**; em seguida, selecione a autenticação **Anônima**.

    ![Escolher autenticação anônima](./media/quickstart-js-vscode/create-function-anonymous-auth.png)

Agora, criamos um ponto de entrada em nossa Durable Function. Vamos adicionar um orquestrador.

### <a name="orchestrator"></a>Orchestrator

Adicionamos um orquestrador para coordenar as funções de atividade.

1. Do *Azure: Functions*, escolha o ícone **Criar Função**.

    ![Criar uma função](./media/quickstart-js-vscode/create-function.png)

2. Selecione a pasta com seu projeto de aplicativo de funções e selecione o modelo de função **Orquestrador do Durable Functions**. Deixe o nome como o padrão "DurableFunctionsOrchestrator"

    ![Escolher o modelo de orquestrador](./media/quickstart-js-vscode/create-function-choose-template.png)

Adicionamos um orquestrador para coordenar as funções de atividade. Agora, vamos adicionar a função de atividade referenciada.

### <a name="activity"></a>Atividade

Agora, vamos criar uma função de atividade para executar efetivamente o trabalho da solução.

1. Do *Azure: Functions*, escolha o ícone **Criar Função**.

    ![Criar uma função](./media/quickstart-js-vscode/create-function.png)

2. Selecione a pasta com seu projeto de aplicativo de funções e selecione o modelo de função **Atividade do Durable Functions**. Deixe o nome como o padrão "Hello".

    ![Escolher o modelo de atividade](./media/quickstart-js-vscode/create-function-choose-template.png)

Agora adicionamos todos os componentes necessários para iniciar uma orquestração e unir as funções de atividade.

## <a name="test-the-function-locally"></a>Testar a função localmente

As Ferramentas Principais do Azure Functions permitem executar um projeto do Azure Functions no seu computador de desenvolvimento local. É solicitado que você instale essas ferramentas na primeira vez em que inicia uma função no Visual Studio Code.

1. Em um computador Windows, inicie o Emulador de Armazenamento do Azure e verifique se a propriedade **AzureWebJobsStorage** de *local.settings.json* está definida como `UseDevelopmentStorage=true`.

    Para o Emulador de Armazenamento 5.8, verifique se a propriedade **AzureWebJobsSecretStorageType** de local.settings.json está definida como `files`. Em um computador Mac ou Linux, defina a propriedade **AzureWebJobsStorage** na cadeia de conexão de uma conta de armazenamento do Azure existente. Você pode criar a conta de armazenamento mais tarde neste artigo.

2. Para testar sua função, defina um ponto de interrupção no código da função e pressione F5 para iniciar o projeto de aplicativo de funções. A saída do Core Tools é exibida no painel **Terminal**. Se essa for a primeira vez que você usa Durable Functions, a extensão Durable Functions será instalada e a compilação poderá levar alguns segundos.

    > [!NOTE]
    > Durable Functions em JavaScript exigem a versão **1.7.0** ou superior da extensão **Microsoft.Azure.WebJobs.Extensions.DurableTask**. Execute o seguinte comando na pasta raiz do seu aplicativo do Azure Functions para instalar a extensão Durable Functions `func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.7.0`

3. No painel **Terminal**, copie o ponto de extremidade de URL da sua função disparada por HTTP.

    ![Saída local do Azure](../media/functions-create-first-function-vs-code/functions-vscode-f5.png)

4. Substitua `{functionName}` por `DurableFunctionsOrchestrator`.

5. Usando uma ferramenta como [Postman](https://www.getpostman.com/) ou [cURL](https://curl.haxx.se/), envie uma solicitação HTTP POST para o ponto de extremidade de URL.

   A resposta é o resultado inicial da função HTTP informando que a orquestração durável foi iniciada com êxito. A resposta ainda não é o resultado final da orquestração. A resposta inclui algumas URLs úteis. Por enquanto, vamos consultar o status da orquestração.

6. Copie o valor da URL para `statusQueryGetUri` e cole-o na barra de endereços do navegador e execute a solicitação. Como alternativa, você também pode continuar usando o Postman para emitir a solicitação GET.

   A solicitação consultará a instância de orquestração do status. Você deve obter uma resposta eventual, que nos mostra que a instância foi concluída e inclui as saídas ou os resultados da função durável. Ele tem esta aparência: 

    ```json
    {
        "instanceId": "d495cb0ac10d4e13b22729c37e335190",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-11-08T07:07:40Z",
        "lastUpdatedTime": "2018-11-08T07:07:52Z"
    }
    ```

7. Para interromper a depuração, pressione **Shift+F5** no VS Code.

Após verificar se a função foi executada corretamente no computador local, é hora de publicar o projeto no Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Testar sua função no Azure

1. Copie a URL do gatilho de HTTP do painel **Saída**. A URL que chama a função HTTP disparada deve estar no seguinte formato:

        http://<functionappname>.azurewebsites.net/orchestrators/<functionname>

2. Cole essa nova URL para a solicitação HTTP na barra de endereços do navegador. Ao usar o aplicativo publicado, você deve obter a mesma resposta de status como antes.

## <a name="next-steps"></a>Próximas etapas

Você usou o Visual Studio Code para criar e publicar um aplicativo de funções duráveis de JavaScript.

> [!div class="nextstepaction"]
> [Saiba mais sobre os padrões comuns de função durável](durable-functions-overview.md#application-patterns)