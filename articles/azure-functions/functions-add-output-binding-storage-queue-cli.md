---
title: Conectar o Azure Functions ao armazenamento do Azure usando ferramentas de linha de comando
description: Saiba como conectar o Azure Functions a uma fila de armazenamento do Azure adicionando uma associação de saída ao seu projeto de linha de comando.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 9181caf516d5c2003cfe99b125d2921732cbbb9d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "79473380"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Conectar o Azure Functions ao armazenamento do Azure usando ferramentas de linha de comando

Neste artigo, você integrará uma fila do Armazenamento do Azure à função e à conta de armazenamento criada [no início rápido anterior](functions-create-first-azure-function-azure-cli.md). Obtenha essa integração usando uma *associação de saída* que grava os dados de uma solicitação HTTP em uma mensagem na fila. A conclusão deste artigo não gera nenhum custo adicional além dos primeiros centavos em US$ do início rápido anterior. Para saber mais sobre associações, confira [Conceitos de gatilhos e de associações do Azure Functions](functions-triggers-bindings.md).

## <a name="configure-your-local-environment"></a>Configurar o ambiente local

Antes de começar, conclua o artigo [Início rápido: criar um projeto de Azure Functions da linha de comando](functions-create-first-azure-function-azure-cli.md). Se você já limpou os recursos ao final daquele artigo, percorra as etapas novamente para recriar o aplicativo de funções e recursos relacionados no Azure.

## <a name="retrieve-the-azure-storage-connection-string"></a>Recuperar a cadeia de conexão do Armazenamento do Azure

Quando você criou um aplicativo de funções no Azure no início rápido anterior, também criou uma conta de armazenamento. A cadeia de conexão dessa conta é armazenada com segurança nas configurações do aplicativo no Azure. Ao baixar a configuração para o arquivo *local.settings.json*, use essa conexão para gravar em uma fila do Armazenamento na mesma conta ao executar a função localmente. 

1. Na raiz do projeto, execute o comando a seguir, substituindo `<APP_NAME>` pelo nome do aplicativo de funções do início rápido anterior. Esse comando substituirá todos os valores existentes no arquivo.

    ```
    func azure functionapp fetch-app-settings <APP_NAME>
    ```
    
1. Abra *local.settings.json* e localize o valor chamado `AzureWebJobsStorage`, que é a cadeia de conexão da conta de armazenamento. Você usará o nome `AzureWebJobsStorage` e a cadeia de conexão em outras seções deste artigo.

> [!IMPORTANT]
> Como *local.settings.json* contém segredos baixados do Azure, sempre exclua esse arquivo do controle do código-fonte. O arquivo *.gitignore* criado com um projeto de funções locais exclui o arquivo por padrão.

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

## <a name="add-an-output-binding-definition-to-the-function"></a>Adicionar uma definição de associação de saída à função

Embora uma função possa ter apenas um gatilho, ela pode ter várias associações de entrada e saída, o que permite que você se conecte a outros serviços e recursos do Azure sem escrever um código de integração personalizado. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Você declara essas associações no arquivo *function.json* em sua pasta de funções. No início rápido anterior, o arquivo *function.json* na pasta *HttpExample* contém duas associações na coleção `bindings`:  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
Cada associação tem, pelo menos, um tipo, uma direção e um nome. No exemplo acima, a primeira associação é do tipo `httpTrigger` com a direção `in`. Para a direção de `in`, `name` especifica o nome de um parâmetro de entrada que é enviado para a função quando invocado pelo gatilho.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
A segunda associação na coleção é denominada `res`. Essa associação `http` é uma associação de saída (`out`) usada para gravar a resposta HTTP. 

Para fazer uma gravação em uma fila do Armazenamento do Azure por meio dessa função, adicione uma associação `out` do tipo `queue` com o nome `msg`, conforme mostrado no código abaixo:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
A segunda associação na coleção é do tipo `http` com a direção `out`; nesse caso, o `name` especial de `$return` indica que essa associação usa o valor retornado da função em vez de fornecer um parâmetro de entrada.

Para fazer uma gravação em uma fila do Armazenamento do Azure por meio dessa função, adicione uma associação `out` do tipo `queue` com o nome `msg`, conforme mostrado no código abaixo:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
A segunda associação na coleção é denominada `res`. Essa associação `http` é uma associação de saída (`out`) usada para gravar a resposta HTTP. 

Para fazer uma gravação em uma fila do Armazenamento do Azure por meio dessa função, adicione uma associação `out` do tipo `queue` com o nome `msg`, conforme mostrado no código abaixo:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Nesse caso, `msg` é fornecido à função como um argumento de saída. Para um tipo `queue`, também é necessário especificar o nome da fila em `queueName` e fornecer o *nome* da conexão do Armazenamento do Azure (por meio de *local.settings.json*) em `connection`. 
::: zone-end  

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  

Para obter mais informações sobre os detalhes das associações, confira [Conceitos de associações e gatilhos do Azure Functions](functions-triggers-bindings.md) e [configuração de saída da fila](functions-bindings-storage-queue-output.md#configuration).

## <a name="add-code-to-use-the-output-binding"></a>Adicionar código para usar a associação de saída

Com a associação de fila especificada em *function.json*, agora você pode atualizar a função para receber o parâmetro de saída `msg` e gravar mensagens na fila.

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

Observe que você *não* precisa escrever nenhum código para autenticação, obtenção de uma referência de fila ou gravação de dados. Todas essas tarefas de integração são convenientemente processadas no Azure Functions Runtime e na associação de saída da fila.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Exibir a mensagem na fila do Armazenamento do Azure

Veja a fila no [portal do Azure](../storage/queues/storage-quickstart-queues-portal.md) ou no [Gerenciador de Armazenamento do Microsoft Azure](https://storageexplorer.com/). Veja também a fila na CLI do Azure, conforme descrito nas seguintes etapas:

1. Abra o arquivo *local.setting.json* do projeto de funções e copie o valor da cadeia de conexão. Em um terminal ou uma janela Comando, execute o comando a seguir para criar uma variável de ambiente chamada `AZURE_STORAGE_CONNECTION_STRING`, colando a cadeia de conexão específica no lugar de `<MY_CONNECTION_STRING>`. (Essa variável de ambiente significa que você não precisa fornecer a cadeia de conexão para cada comando posterior usando o argumento `--connection-string`.)

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="azure-cli"></a>[CLI do Azure](#tab/cmd)
    
    ```azurecli
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. (Opcional) Use o comando [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) para ver as filas de armazenamento em sua conta. A saída desse comando deve incluir uma fila chamada `outqueue`, que foi criada quando a função gravou a primeira mensagem nessa fila.
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. Use o comando [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) para ler as mensagens dessa fila, que deve ser o primeiro nome usado ao testar a função anteriormente. O comando lê e remove a primeira mensagem da fila. 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="azure-cli"></a>[CLI do Azure](#tab/cmd)
    
    ```azurecli
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    Esse script usa o Certutil para decodificar a coleção de mensagens codificadas em Base64 de um arquivo temporário local. Se não houver nenhuma saída, tente remover `> NUL` do script para deixar de suprimir a saída do Certutil, caso haja um erro. 
    
    ---
    
    Como o corpo da mensagem é armazenado [codificado em base64](functions-bindings-storage-queue-trigger.md#encoding), a mensagem deve ser decodificada antes de ser exibida. Depois de executar `az storage message get`, a mensagem é removida da fila. Se houver apenas uma mensagem em `outqueue`, você não recuperará uma mensagem quando executar esse comando pela segunda vez e receberá um erro.

## <a name="redeploy-the-project-to-azure"></a>Reimplantar o projeto no Azure

Agora que você verificou localmente que a função gravou uma mensagem na fila do Armazenamento do Azure, poderá reimplantar o projeto para atualizar o ponto de extremidade em execução no Azure.

1. Na pasta *LocalFunctionsProj*, use o comando [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) para reimplantar o projeto, substituindo`<APP_NAME>` pelo nome do aplicativo.

    ```
    func azure functionapp publish <APP_NAME>
    ```
    
1. Como no início rápido anterior, use um navegador ou um cURL para testar a função reimplantada.

    # <a name="browser"></a>[Navegador](#tab/browser)
    
    Copie a **URL de Invocação** completa mostrada na saída do comando de publicação na barra de endereços de um navegador, acrescentando o parâmetro de consulta `&name=Functions`. O navegador deverá exibir uma saída semelhante à que foi exibida quando você executou a função localmente.

    ![A saída da função executada no Azure em um navegador](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # <a name="curl"></a>[curl](#tab/curl)
    
    Execute [`curl`](https://curl.haxx.se/) com a **URL de Invocação**, acrescentando o parâmetro `&name=Functions`. A saída do comando deverá ser o texto, "Olá, Functions".
    
    ![A saída da função executada no Azure usando CURL](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-curl.png)

    --- 

1. Examine a fila do Armazenamento novamente, conforme descrito na seção anterior, para verificar se ela contém a nova mensagem gravada na fila.

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de terminar, use o comando a seguir para excluir o grupo de recursos e todos os recursos contidos nele para evitar custos adicionais.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Próximas etapas

Você atualizou sua função disparada por HTTP para gravar dados em uma Fila de armazenamento. Agora você pode aprender mais sobre o desenvolvimento de funções na linha de comando usando o Core Tools e a CLI do Azure:

+ [Trabalhar com o Azure Functions Core Tools](functions-run-local.md)  

::: zone pivot="programming-language-csharp"  
+ [Exemplos de projetos de função completos em C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Referência do desenvolvedor de C# do Azure Functions](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Exemplos de projetos de função completos em JavaScript](/samples/browse/?products=azure-functions&languages=javascript).

+ [Guia do desenvolvedor de JavaScript do Azure Functions](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Exemplos de projetos de função completos em TypeScript](/samples/browse/?products=azure-functions&languages=typescript).

+ [Guia do desenvolvedor de TypeScript do Azure Functions](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Exemplos de projetos de função completos no Python](/samples/browse/?products=azure-functions&languages=python).

+ [Guia do desenvolvedor do Python para o Azure Functions](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Exemplos de projetos de função completos no PowerShell](/samples/browse/?products=azure-functions&languages=azurepowershell).

+ [Guia do desenvolvedor do PowerShell do Azure Functions](functions-reference-powershell.md) 
::: zone-end
+ [Gatilhos e associações de Azure Functions](functions-triggers-bindings.md)

+ [Página de preços do Functions](https://azure.microsoft.com/pricing/details/functions/)

+ [Como estimar os custos do plano de Consumo](functions-consumption-costs.md) 
