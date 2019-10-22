---
title: Conectar a função Java ao Armazenamento do Azure
description: Saiba como conectar uma função Java disparada por HTTP ao Armazenamento do Azure usando uma associação de saída do Armazenamento de Filas.
author: ggailey777
ms.author: glenga
ms.date: 10/14/2019
ms.topic: quickstart
ms.service: azure-functions
manager: gwallace
ms.openlocfilehash: c78630af7d09cc911862c8e823c5dfeee9cabbd9
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333398"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Conectar a função Java ao Armazenamento do Azure

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Este artigo mostra como integrar a função criada no [artigo de início rápido anterior](functions-create-first-java-maven.md) com uma fila de Armazenamento do Azure. A associação de saída que você adiciona a essa função escreve dados de uma solicitação HTTP em uma mensagem na fila.

A maioria das associações requer uma cadeia de conexão armazenada que o Functions usa para acessar o serviço vinculado. Para facilitar essa conexão, use a conta de armazenamento que você criou com o seu aplicativo de funções. A conexão com essa conta já está armazenada em uma configuração de aplicativo chamada `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este artigo, conclua as etapas na [parte 1 do início rápido do Java](functions-create-first-java-maven.md).

## <a name="download-the-function-app-settings"></a>Baixar as configurações do aplicativo de funções

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Habilitar pacotes de extensão

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Agora, você pode adicionar a associação de saída do Armazenamento ao seu projeto.

## <a name="add-an-output-binding"></a>Adicionar uma associação de saída

Em um projeto Java, as associações são definidas como anotações de associação no método de função. O arquivo *function.json* é então gerado automaticamente com base nessas anotações.

Procure a localização do código de função em _src/main/java_, abra o arquivo de projeto *Function.java* e adicione o seguinte parâmetro à definição do método `run`:

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

O parâmetro `msg` é um tipo [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding), que representa uma coleção de cadeias de caracteres que são gravadas como mensagens em uma associação de saída quando a função é concluída. Nesse caso, a saída é uma fila de armazenamento denominada `outqueue`. A cadeia de conexão para a conta de armazenamento é definida pelo método `connection`. Em vez da própria cadeia de conexão, passe a configuração de aplicativo que contém a cadeia de conexão da conta de armazenamento.

A definição do método `run` agora deverá ser semelhante ao seguinte exemplo:  

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION)  
        HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    ...
}
```

## <a name="add-code-that-uses-the-output-binding"></a>Adicionar o código que usa a associação de saída

Agora, você pode usar o novo parâmetro `msg` para fazer a gravação na associação de saída por meio do código de função. Adicione a linha de código a seguir antes da resposta de êxito para adicionar o valor de `name` à associação de saída `msg`.

```java
msg.setValue(name);
```

Ao usar uma associação de saída, não é necessário usar o código do SDK do Armazenamento do Azure para se autenticar, para obter uma referência de fila ou para escrever dados. O tempo de execução do Functions e a associação de saída da fila fazem essas tarefas para você.

O método `run` agora deverá ser semelhante ao seguinte exemplo:

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    context.getLogger().info("Java HTTP trigger processed a request.");

    // Parse query parameter
    String query = request.getQueryParameters().get("name");
    String name = request.getBody().orElse(query);

    if (name == null) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST).body("Please pass a name on the query string or in the request body").build();
    } else {
        // Write the name to the message queue. 
        msg.setValue(name);

        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
    }
}
```

## <a name="update-the-tests"></a>Atualizar os testes

Como o arquétipo também cria um conjunto de testes, você precisa atualizar esses testes para manipular o novo parâmetro `msg` na assinatura do método `run`.  

Procure a localização do código de teste em _src/test/java_, abra o arquivo de projeto *Function.java* e substitua a linha de código em `//Invoke` pelo código a seguir.

```java
@SuppressWarnings("unchecked")
final OutputBinding<String> msg = (OutputBinding<String>)mock(OutputBinding.class);

// Invoke
final HttpResponseMessage ret = new Function().run(req, msg, context);
``` 

Agora você está pronto para experimentar a nova associação de saída localmente.

## <a name="run-the-function-locally"></a>Executar a função localmente

Como anteriormente, use o seguinte comando para compilar o projeto e iniciar o Functions Runtime localmente:

```bash
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]  
> Como você habilitou os pacotes de extensões no host.json, a [extensão de associação do armazenamento](functions-bindings-storage-blob.md#packages---functions-2x) foi baixada e instalada para você durante a inicialização, juntamente com outras extensões de associação da Microsoft.

Como anteriormente, dispare a função na linha de comando usando o cURL em uma nova janela de terminal:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

Dessa vez, a associação de saída também cria uma fila denominada `outqueue` em sua Conta de armazenamento e adiciona uma mensagem com essa mesma cadeia de caracteres.

Em seguida, use a CLI do Azure para exibir a nova fila e verifique se uma mensagem foi adicionada. Também é possível exibir sua fila usando o [Gerenciador de Armazenamento do Microsoft Azure][Azure Storage Explorer] ou no [portal do Azure](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Reimplantar o projeto 

Para atualizar o aplicativo publicado, execute o seguinte comando novamente:  

```azurecli
mvn azure-functions:deploy
```

Novamente, use o cURL para testar a função implantada. Como anteriormente, passe o valor `AzureFunctions` no corpo da solicitação POST para a URL, como neste exemplo:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

[Examine a mensagem da Fila de armazenamento](#query-the-storage-queue) novamente para verificar se a associação de saída gera uma nova mensagem na fila, conforme esperado.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Você atualizou a função disparada por HTTP para gravar dados em uma fila de armazenamento. Para saber mais sobre como desenvolver o Azure Functions com o Java, confira o [Guia do desenvolvedor de Java para o Azure Functions](functions-reference-java.md) e [Gatilhos e associações do Azure Functions](functions-triggers-bindings.md). Para obter exemplos de projetos completos de funções em Java, confira as [Amostras de funções Java](/samples/browse/?products=azure-functions&languages=Java). 

Em seguida, você deve habilitar o monitoramento do Application Insights para seu aplicativo de funções:

> [!div class="nextstepaction"]
> [Habilitar a integração do Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)


[Azure Storage Explorer]: https://storageexplorer.com/