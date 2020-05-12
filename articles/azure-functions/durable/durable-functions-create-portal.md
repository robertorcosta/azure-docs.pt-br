---
title: Criar Durable Functions usando o portal do Azure
description: Aprenda a instalar a extensão Durable Functions doa Azure Functions para o desenvolvimento do portal.
ms.topic: conceptual
ms.date: 04/10/2020
ms.reviewer: azfuncdf
ms.openlocfilehash: 6416ae4aba8b045c6c4fb0fe6557bdcd1efb3a9b
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83120125"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Criar Durable Functions usando o portal do Azure

A extensão [Funções Duráveis](durable-functions-overview.md) do Azure Functions é fornecida no pacote de NuGet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Esta extensão deve ser instalada em seu aplicativo de função. Este artigo mostra como instalar esse pacote para que você possa desenvolver funções duráveis no portal do Azure.

> [!NOTE]
> 
> * Se você estiver desenvolvendo funções duráveis em C#, considere o desenvolvimento do [Visual Studio 2019](durable-functions-create-first-csharp.md).
> * Se você estiver desenvolvendo funções duráveis em JavaScript, você deve considerar o [Desenvolvimento de código do Visual Studio Code](./quickstart-js-vscode.md).

## <a name="create-a-function-app"></a>Criar um aplicativo de funções

Você deve ter um aplicativo de função para hospedar a execução de qualquer função. Um aplicativo de funções permite agrupar suas funções como uma unidade lógica para facilitar o gerenciamento, a implantação, o dimensionamento e o compartilhamento de recursos. Você pode criar um aplicativo .NET ou JavaScript.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Por padrão, o aplicativo de função criado usa a versão 2.x do Azure Functions Runtime. A extensão Durable Functions funciona nas duas versões 1.x e 2.x do Azure Functions Runtime em C# e versão 2.x no JavaScript. No entanto, os modelos só estão disponíveis ao segmentar a versão 2.x do runtime independentemente da linguagem escolhida.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>Instalar o pacote npm de Durable Functions (apenas JavaScript)

Se estiver criando Durable Functions de JavaScript, você precisará instalar o [ `durable-functions` pacote NPM](https://www.npmjs.com/package/durable-functions):

1. Na página do aplicativo de funções, selecione **ferramentas avançadas** em **ferramentas de desenvolvimento** no painel esquerdo.

   :::image type="content" source="./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png" alt-text="Recursos da plataforma funções, escolha Kudu":::

2. Na página **ferramentas avançadas** , selecione **ir**.

3. No console do kudu, selecione **console de depuração**e, em seguida, **cmd**.

   :::image type="content" source="./media/durable-functions-create-portal/kudu-choose-debug-console.png" alt-text="Console de depuração do Kudu":::

3. Sua estrutura de diretório do arquivo do aplicativo de funções deve ser exibida. Navegue até a pasta `site/wwwroot`. Dela, você pode carregar um arquivo `package.json` arrastando e soltando-o na janela do diretório do arquivo. Há um exemplo `package.json` abaixo:

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.3.1"
      }
    }
    ```

   :::image type="content" source="./media/durable-functions-create-portal/kudu-choose-debug-console.png" alt-text="Carregar o pacote do package.json do Kudu":::

4. Após seu `package.json` ser carregado, execute o comando `npm install` do Console de Execução Remota do Kudu.

   ![Executar a instalação do npm do Kudu](./media/durable-functions-create-portal/kudu-npm-install.png)

## <a name="create-an-orchestrator-function"></a>Crie uma função de orquestrador

1. Em seu aplicativo de funções, selecione **funções** no painel esquerdo e, em seguida, selecione **Adicionar** no menu superior. 

1. No campo de pesquisa da página **nova função** , digite `durable` e escolha o modelo de **início de http Durable Functions** .

   :::image type="content" source="./media/durable-functions-create-portal/durable-functions-http-starter-template.png" alt-text="Selecione Durable Functions iniciador HTTP":::

1. Para o **novo** nome da função, digite `HttpStart` e, em seguida, selecione **criar função**.

   A função criada é usada para iniciar a orquestração.

1. Crie outra função no aplicativo de funções, desta vez usando o modelo **Durable Functions Orchestrator** . Nomeie sua nova função de orquestração `HelloSequence`.

1. Crie uma terceira função chamada `Hello` usando o modelo de **atividade de durable Functions** .

## <a name="test-the-durable-function-orchestration"></a>Teste a orquestração de função durável

1. Volte para a função **HttpStart** , escolha **obter URL de função**e selecione o ícone **copiar para área de transferência** para copiar a URL. Use essa URL para iniciar o **HelloSequence** função.

1. Use uma ferramenta HTTP, como Postman ou cURL, para enviar uma solicitação POST ao URL que você copiou. O exemplo a seguir é um comando cURL que envia uma solicitação POST para a função durável:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

    Neste exemplo, `{your-function-app-name}` é o domínio que é o nome do seu aplicativo de função. A mensagem de resposta contém um conjunto de nós de extremidade do URI que você pode usar para monitorar e gerenciar a execução, que se parece com o exemplo a seguir:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Chame o `statusQueryGetUri` endpoint URI e você verá o status atual da função durável, que pode ser semelhante a este exemplo:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Continue chamando o endpoint `statusQueryGetUri` até que o status seja alterado para **Completed** e você veja uma resposta como o exemplo a seguir:

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

A primeira função durável está agora em execução no Azure.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre os padrões comuns de função durável](durable-functions-overview.md#application-patterns)
