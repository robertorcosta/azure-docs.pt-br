---
title: Adicionar uma associação de fila do Armazenamento do Azure à sua função do Python
description: Integre uma fila do Armazenamento do Azure a uma função do Python usando uma associação de saída.
ms.date: 01/15/2020
ms.topic: quickstart
ms.openlocfilehash: 6cea44dca666bbf002de6e2b7dd283f49ac7bd5a
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485158"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Adicionar uma associação de fila do Armazenamento do Azure à sua função do Python

Neste artigo, você integrará uma fila do Armazenamento do Azure à função e à conta de armazenamento criada em [Criar uma função do Python disparada por HTTP](functions-create-first-function-python.md). Obtenha essa integração usando uma *associação de saída* que grava os dados de uma solicitação HTTP em uma mensagem na fila. A conclusão deste artigo não gera nenhum custo adicional além dos primeiros centavos em US$ do início rápido anterior.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua o início rápido [Criar uma função do Python disparada por HTTP](functions-create-first-function-python.md). Se você já limpou os recursos ao final daquele artigo, percorra as etapas novamente para recriar o aplicativo Functions no Azure, mas mantenha os recursos em vigor.

## <a name="retrieve-the-azure-storage-connection-string"></a>Recuperar a cadeia de conexão do Armazenamento do Azure

Quando você criou um aplicativo de funções no Azure no início rápido anterior, também criou uma conta de armazenamento. A cadeia de conexão dessa conta é armazenada com segurança nas configurações do aplicativo no Azure. Ao baixar a configuração para o arquivo *local.settings.json*, use essa conexão para gravar em uma fila do Armazenamento na mesma conta ao executar a função localmente. 

1. Na raiz do projeto, execute o comando a seguir, substituindo `<app_name>` pelo nome do aplicativo de funções do início rápido anterior. Esse comando substituirá todos os valores existentes no arquivo.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Abra *local.settings.json* e localize o valor chamado `AzureWebJobsStorage`, que é a cadeia de conexão da conta de armazenamento. Você usará o nome `AzureWebJobsStorage` e a cadeia de conexão em outras seções deste artigo.

> [!IMPORTANT]
> Como *local.settings.json* contém segredos baixados do Azure, sempre exclua esse arquivo do controle do código-fonte. O arquivo *.gitignore* criado com um projeto de funções locais exclui o arquivo por padrão.

## <a name="add-an-output-binding-to-functionjson"></a>Adicionar uma associação de saída a function.json

Embora uma função possa ter apenas um gatilho, ela pode ter várias associações de entrada e saída, o que permite que você se conecte a outros serviços e recursos do Azure sem escrever um código de integração personalizado. Declare essas associações no arquivo *function.json* na pasta de funções, conforme apropriado para a linguagem que você está usando para a função.

No início rápido anterior, o arquivo *function.json* na pasta *HttpExample* contém duas associações na coleção `bindings`:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Cada associação tem, pelo menos, um tipo, uma direção e um nome. No exemplo acima, a primeira associação é do tipo `httpTrigger` com a direção `in`. Para a direção de `in`, `name` especifica o nome de um parâmetro de entrada que é enviado para a função quando invocado pelo gatilho.

A segunda associação é do tipo `http` com a direção `out`; nesse caso, o `name` especial de `$return` indica que essa associação usa o valor retornado da função em vez de fornecer um parâmetro de entrada.

Para fazer uma gravação em uma fila do Armazenamento do Azure por meio dessa função, adicione uma associação `out` do tipo `queue` com o nome `msg`, conforme mostrado no código abaixo:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

Nesse caso, `msg` é fornecido à função como um argumento de saída. Para um tipo `queue`, também é necessário especificar o nome da fila em `queueName` e fornecer o *nome* da conexão do Armazenamento do Azure (por meio de *local.settings.json*) em `connection`.

Para obter mais informações sobre os detalhes das associações, confira [Conceitos de associações e gatilhos do Azure Functions](functions-triggers-bindings.md) e [configuração de saída da fila](functions-bindings-storage-queue-output.md#configuration).

## <a name="add-code-to-use-the-output-binding"></a>Adicionar código para usar a associação de saída

Com a associação de fila especificada em *function.json*, agora você pode atualizar a função para receber o parâmetro de saída `msg` e gravar mensagens na fila.

Atualize *HttpExample\\\_\_init\_\_.py* para que ele corresponda ao código a seguir, adicionando o parâmetro `msg` à definição da função e a `msg.set(name)` na instrução `if name:`.

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

O parâmetro `msg` é uma instância do [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest). O método `set` grava uma mensagem de cadeia de caracteres na fila, nesse caso, o nome transmitido para a função na cadeia de consulta de URL.

Observe que você *não* precisa escrever nenhum código para autenticação, obtenção de uma referência de fila ou gravação de dados. Todas essas tarefas de integração são convenientemente processadas no Azure Functions Runtime e na associação de saída da fila.

## <a name="run-and-test-the-function-locally"></a>Executar e testar a função localmente

1. Em um terminal ou um prompt de comando, navegue até a pasta do projeto de função, *LocalFunctionProj*.

1. Inicie o host de runtime do Azure Functions usando o comando a seguir.

    ```
    func host start
    ```

1. Depois que a inicialização for concluída e a URL para o ponto de extremidade `HttpExample` for exibida, copie a URL para um navegador e acrescente a cadeia de consulta `?name=<your-name>`, tornando a URL completa, como `http://localhost:7071/api/HttpExample?name=Guido`. O navegador deverá exibir uma mensagem como `Hello Guido`, conforme mostrado no artigo anterior.

    Se o ponto de extremidade `HttpExample` não for exibido, interrompa o host com **CTRL**+**C** e verifique se há erros na saída. Por exemplo, o host não ativará o ponto de extremidade se houver um erro em *function.json*. Verifique também se você está executando `func host start` na pasta do projeto de funções e não na pasta *HttpExample*.

1. Quando terminar, interrompa o host com **CTRL**+**C**.

> [!TIP]
> Durante a inicialização, o host baixa e instala a [Extensão de associação de armazenamento](functions-bindings-storage-blob.md#add-to-your-functions-app) e outras extensões de associação da Microsoft. Essa instalação ocorre porque as extensões de associação estão habilitadas por padrão no arquivo *host.json* com as seguintes propriedades:
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> Se você receber erros relacionados às extensões de associação, verifique se as propriedades acima estão presentes em *host.json*.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Exibir a mensagem na fila do Armazenamento do Azure

Quando a função gera uma resposta HTTP para o navegador da Web, ela também chama `msg.set(name)`, que grava uma mensagem em uma fila do Armazenamento do Azure chamada `outqueue`, conforme especificado na associação de fila. Veja a fila no [portal do Azure](../storage/queues/storage-quickstart-queues-portal.md) ou no [Gerenciador de Armazenamento do Microsoft Azure](https://storageexplorer.com/). Veja também a fila na CLI do Azure, conforme descrito nas seguintes etapas:

1. Abra o arquivo *local.setting.json* do projeto de funções e copie o valor da cadeia de conexão. Em um terminal ou uma janela Comando, execute o comando a seguir para criar uma variável de ambiente chamada `AZURE_STORAGE_CONNECTION_STRING`, colando a cadeia de conexão específica no lugar de `<connection_string>`. (Essa variável de ambiente significa que você não precisa fornecer a cadeia de conexão para cada comando posterior usando o argumento `--connection-string`.)

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<connection_string>"
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    ---
    
1. (Opcional) Use o comando [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) para ver as filas de armazenamento em sua conta. A saída desse comando deve incluir uma fila chamada `outqueue`, que foi criada quando a função gravou a primeira mensagem nessa fila.
    
    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    az storage queue list --output tsv
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    az storage queue list --output tsv
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    az storage queue list --output tsv
    ```
    
    ---


1. Use o comando [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) para exibir as mensagens nessa fila, que deve ser o primeiro nome usado ao testar a função anteriormente. O comando recupera a primeira mensagem na fila na [codificação de Base64](functions-bindings-storage-queue-trigger.md#encoding), de modo que você também precisará decodificar a mensagem para exibi-la como texto.

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    Como você precisa desreferenciar a coleção de mensagens e decodificar da Base64, execute o PowerShell e use o comando do PowerShell.

    ---
    
## <a name="redeploy-the-project-to-azure"></a>Reimplantar o projeto no Azure

Agora que você testou a função localmente e verificou se ela gravou uma mensagem na fila do Armazenamento do Azure, poderá reimplantar o projeto para atualizar o ponto de extremidade em execução no Azure.

1. Na pasta *LocalFunctionsProj*, use o comando [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) para reimplantar o projeto, substituindo`<app_name>` pelo nome do aplicativo.

    ```
    func azure functionapp publish <app_name>
    ```
    
1. Como no início rápido anterior, use um navegador ou um cURL para testar a função reimplantada.

    # <a name="browser"></a>[Navegador](#tab/browser)
    
    Copie a **URL de invocação** completa mostrada na saída do comando de publicação para a barra de endereços de um navegador, acrescentando o parâmetro de consulta `&name=Azure`. O navegador deverá exibir uma saída semelhante à que foi exibida quando você executou a função localmente.

    ![A saída da função executada no Azure em um navegador](./media/functions-create-first-function-python/function-test-cloud-browser.png)

    # <a name="curl"></a>[curl](#tab/curl)
    
    Execute [curl](https://curl.haxx.se/) com a **URL de invocação**, acrescentando o parâmetro `&name=Azure`. A saída do comando deverá ser o texto "Olá, Azure".
    
    ![A saída da função executada no Azure usando curl](./media/functions-create-first-function-python/function-test-cloud-curl.png)

    --- 

1. Examine a fila do Armazenamento novamente, conforme descrito na seção anterior, para verificar se ela contém a nova mensagem gravada na fila.

    Se você estiver usando a CLI do Azure para examinar a fila, o comando `az storage message peek` mostrará apenas a primeira mensagem na fila. Para simular o processamento das mensagens, use `az storage message get` com todos os mesmos argumentos. O comando `get` retorna a mensagem e a remove da fila. Em seguida, você poderá repetir o mesmo comando até que a fila esteja vazia (e o comando fornecer um erro).

## <a name="clean-up-resources"></a>Limpar os recursos

Se você prosseguir para a próxima etapa, [Habilitar a integração do Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource), mantenha todos os recursos em vigor conforme você desenvolve o que já fez.

Caso contrário, use o comando a seguir para excluir o grupo de recursos e todos os recursos contidos nele para evitar custos adicionais.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Habilitar a integração do Application Insights com um aplicativo do Azure Functions](functions-monitoring.md#manually-connect-an-app-insights-resource)

Outros recursos:

- [Exemplos de projetos de função completos no Python](/samples/browse/?products=azure-functions&languages=python).
- [Guia do desenvolvedor do Python para o Azure Functions](functions-reference-python.md)
- [Gatilhos e associações do Azure Functions](functions-triggers-bindings.md).
- [Página de preços do Functions](https://azure.microsoft.com/pricing/details/functions/)
- Artigo [Como estimar os custos do plano de Consumo](functions-consumption-costs.md).
