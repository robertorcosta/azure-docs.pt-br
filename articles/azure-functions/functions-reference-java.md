---
title: Referência de desenvolvedor Java para funções do Azure
description: Entenda como desenvolver funções usando Java.
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 4b1f39ff4fd48a3ed99b34391e9cc6efdad86a5d
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672995"
---
# <a name="azure-functions-java-developer-guide"></a>Guia do desenvolvedor de Java do Azure Functions

O tempo de execução do Azure Functions suporta [Java SE 8 LTS (zulu8.31.0.2-jre8.0.181-win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/). Este guia contém informações sobre os meandros de escrever Funções Azure com Java.

Como acontece com outros idiomas, um Aplicativo de Função pode ter uma ou mais funções. Uma função Java `public` é um método, decorado `@FunctionName`com a anotação. Este método define a entrada para uma função Java, e deve ser único em um pacote específico. Um aplicativo de função escrito em Java pode ter várias `@FunctionName`classes com vários métodos públicos anotados com .

Este artigo pressupõe que você já tenha lido a [Referência do desenvolvedor do Azure Functions](functions-reference.md). Você também deve completar as funções de início rápido para criar sua primeira função, usando [Visual Studio Code](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java) ou [Maven](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java).

## <a name="programming-model"></a>Modelo de programação 

Os conceitos de [gatilhos e ligações](functions-triggers-bindings.md) são fundamentais para as Funções do Azure. Os gatilhos iniciam a execução do seu código. As ligações fornecem uma maneira de transmitir dados e retornar dados de uma função, sem precisar escrever código de acesso a dados personalizado.

## <a name="create-java-functions"></a>Criar funções Java

Para facilitar a criação de funções Java, existem ferramentas e arquétipos baseados em Maven que usam modelos Java predefinidos para ajudá-lo a criar projetos com um gatilho de função específico.    

### <a name="maven-based-tooling"></a>Ferramentas baseadas em Maven

Os seguintes ambientes de desenvolvedor têm ferramentas de funções do Azure que permitem criar projetos de função Java: 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

Os links do artigo acima mostram como criar suas primeiras funções usando seu IDE de escolha. 

### <a name="project-scaffolding"></a>Andaimes do Projeto

Se você preferir o desenvolvimento da linha de comando a partir do Terminal, `Apache Maven` a maneira mais simples de andaimes projetos de funções baseados em Java é usar arquétipos. O arquétipo Java Maven for Azure Functions é publicado sob o seguinte _groupId_:_artifactId_: [com.microsoft.azure:azure-functions-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/). 

O comando a seguir gera um novo projeto de função Java usando este arquétipo:

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

Para começar a usar este arquétipo, consulte o [Java quickstart](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java). 

## <a name="create-kotlin-functions-preview"></a>Criar funções Kotlin (visualização)

Há também um arquétipo Maven para gerar funções Kotlin. Este arquétipo, atualmente em pré-visualização, é publicado sob o seguinte _groupId_:_artifactId_: [com.microsoft.azure:azure-functions-kotlin-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-kotlin-archetype/). 

O comando a seguir gera um novo projeto de função Java usando este arquétipo:

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```

Para começar a usar este arquétipo, consulte o [Kotlin quickstart](functions-create-first-kotlin-maven.md).

## <a name="folder-structure"></a>Estrutura de pastas

Aqui está a estrutura da pasta de um projeto Java funções do Azure:

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

_* O projeto Kotlin parece muito semelhante, já que ainda é Maven_

Você pode usar um arquivo [host.json](functions-host-json.md) compartilhado para configurar o aplicativo de função. Cada função possui seu próprio arquivo de código (.java) e arquivo de configuração de associação (function.json).

Você pode colocar mais de uma função em um projeto. Evite colocar suas funções no jars separados. O `FunctionApp` diretório no alvo é o que é implantado no seu aplicativo de função no Azure.

## <a name="triggers-and-annotations"></a>Gatilhos e anotações

 As funções são invocadas por um gatilho, como uma solicitação HTTP, um temporizador ou uma atualização de dados. Sua função precisa processar esse gatilho, e quaisquer outras entradas, para produzir uma ou mais saídas.

Use as anotações Java incluídas no pacote [com.microsoft.azure.functions.annotation. *](/java/api/com.microsoft.azure.functions.annotation) para ligar entradas e saídas a seus métodos. Para obter mais informações, consulte os [docs de referência Java](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> Você deve configurar uma conta de armazenamento Azure em seu [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) para executar o armazenamento Azure Blob, o armazenamento Azure Queue ou os acionados de armazenamento azure Table localmente.

Exemplo:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

Aqui está o `function.json` correspondente gerado pelo [azure-functions-maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.Function.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="jdk-runtime-availability-and-support"></a>Suporte e disponibilidade de runtime do JDK 

Para o desenvolvimento local de aplicativos de função Java, baixe e use o [Azul Zulu Enterprise para Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java 8 JDKs da [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/). O Azure Functions usa o runtime do JDK Java 8 da Azul quando você implanta seus aplicativos de funções na nuvem.

[O suporte ao Azure](https://azure.microsoft.com/support/) para problemas com os JDKs e aplicativos de função está disponível com um [plano de suporte qualificado.](https://azure.microsoft.com/support/plans/)

## <a name="customize-jvm"></a>Personalizar JVM

As funções permitem personalizar a máquina virtual Java (JVM) usada para executar suas funções Java. As [seguintes opções de JVM](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) são usadas por padrão:

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

Você pode fornecer argumentos adicionais `JAVA_OPTS`em uma configuração de aplicativo chamada . Você pode adicionar configurações de aplicativo ao seu aplicativo de função implantado no Azure no portal Azure ou no Azure CLI.

### <a name="azure-portal"></a>Portal do Azure

No [portal Azure,](https://portal.azure.com)use a [guia Configurações](functions-how-to-use-azure-function-app-settings.md#settings) do aplicativo para adicionar a `JAVA_OPTS` configuração.

### <a name="azure-cli"></a>CLI do Azure

Você pode usar o [comando az functionapp config set](/cli/azure/functionapp/config/appsettings) set para definir, `JAVA_OPTS`como no exemplo a seguir:

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
Este exemplo permite o modo sem cabeça. Substitua pelo `<APP_NAME>` nome do seu `<RESOURCE_GROUP>` aplicativo de função e pelo grupo de recursos.

> [!WARNING]  
> No [plano de consumo,](functions-scale.md#consumption-plan) `WEBSITE_USE_PLACEHOLDER` você deve adicionar `0`a configuração com um valor de .  
Esta configuração aumenta os tempos de início frio para funções Java.

## <a name="third-party-libraries"></a>Bibliotecas de terceiros 

O Azure Functions oferece suporte ao uso de bibliotecas de terceiros. Por padrão, todas as dependências `pom.xml` especificadas no arquivo [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) do projeto são automaticamente empacotadas durante o objetivo. Para bibliotecas não especificadas como dependências no `pom.xml`arquivo, coloque-as em um diretório`lib` no diretório-raiz da função. As dependências `lib` colocadas no diretório são adicionadas ao carregador de classe do sistema em tempo de execução.

A `com.microsoft.azure.functions:azure-functions-java-library` dependência é fornecida no classpath por padrão e não precisa `lib` ser incluída no diretório. Além disso, [azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) adiciona dependências listadas [aqui](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) ao classpath.

## <a name="data-type-support"></a>Suporte do tipo de dados

Você pode usar objetos Java simples e `azure-functions-java-library`antigos (POJOs), tipos definidos em , ou tipos de dados primitivos, como String e Integer, para vincular-se a ligações de entrada ou saída.

### <a name="pojos"></a>POJOs

Para converter dados de entrada para POJO, [azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) usa a biblioteca [gson.](https://github.com/google/gson) Tipos de POJO usados como entradas para funções devem ser `public`.

### <a name="binary-data"></a>Dados binários

Vincule entradas binárias `byte[]`ou saídas a , definindo `dataType` `binary`o campo em sua função.json a :

```java
   @FunctionName("BlobTrigger")
    @StorageAccount("AzureWebJobsStorage")
     public void blobTrigger(
        @BlobTrigger(name = "content", path = "myblob/{fileName}", dataType = "binary") byte[] content,
        @BindingName("fileName") String fileName,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Blob trigger function processed a blob.\n Name: " + fileName + "\n Size: " + content.length + " Bytes");
    }
```

Se você espera valores nulos, use `Optional<T>`.

## <a name="bindings"></a>Associações

As ligações de entrada e saída fornecem uma maneira declarativa de se conectar aos dados de dentro do seu código. Uma função pode ter várias ligações de entrada e saída.

### <a name="input-binding-example"></a>Exemplo de vinculação de entrada

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData,
    ) {
        testOutputData.setValue(new Person(httpbody + "Partition", httpbody + "Row", httpbody + "Name"));
        return "Hello, " + inputReq + " and " + inputData.getKey() + ".";
    }

    public static class TestInputData {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
    public static class Person {
        public String PartitionKey;
        public String RowKey;
        public String Name;

        public Person(String p, String r, String n) {
            this.PartitionKey = p;
            this.RowKey = r;
            this.Name = n;
        }
    }
}
```

Você invoca esta função com uma solicitação HTTP. 
- A carga útil da `String` solicitação HTTP `inputReq`é passada como um para o argumento .
- Uma entrada é recuperada do armazenamento de `TestInputData` tabela, `inputData`e é passada quanto ao argumento .

Para receber um lote de entradas, `List<String>`você `List<POJO>`pode vincular a `String[]`, `POJO[]`, ou .

```java
@FunctionName("ProcessIotMessages")
    public void processIotMessages(
        @EventHubTrigger(name = "message", eventHubName = "%AzureWebJobsEventHubPath%", connection = "AzureWebJobsEventHubSender", cardinality = Cardinality.MANY) List<TestEventData> messages,
        final ExecutionContext context)
    {
        context.getLogger().info("Java Event Hub trigger received messages. Batch size: " + messages.size());
    }
    
    public class TestEventData {
    public String id;
}

```

Essa função é acionada sempre que houver novos dados no hub de eventos configurado. Como `cardinality` o conjunto `MANY`está definido para , a função recebe um lote de mensagens do hub de eventos. `EventData`do hub de eventos `TestEventData` é convertido para a execução da função.

### <a name="output-binding-example"></a>Exemplo de associação de saída

Você pode vincular uma vinculação de `$return`saída ao valor de retorno usando . 

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

Se houver várias associações de saída, use o valor de retorno de apenas um deles.

Para enviar vários valores de saída, use `OutputBinding<T>` definido no pacote `azure-functions-java-library`. 

```java
@FunctionName("QueueOutputPOJOList")
    public HttpResponseMessage QueueOutputPOJOList(@HttpTrigger(name = "req", methods = { HttpMethod.GET,
            HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "itemsOut", queueName = "test-output-java-pojo", connection = "AzureWebJobsStorage") OutputBinding<List<TestData>> itemsOut, 
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");
       
        String query = request.getQueryParameters().get("queueMessageId");
        String queueMessageId = request.getBody().orElse(query);
        itemsOut.setValue(new ArrayList<TestData>());
        if (queueMessageId != null) {
            TestData testData1 = new TestData();
            testData1.id = "msg1"+queueMessageId;
            TestData testData2 = new TestData();
            testData2.id = "msg2"+queueMessageId;

            itemsOut.getValue().add(testData1);
            itemsOut.getValue().add(testData2);

            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + queueMessageId).build();
        } else {
            return request.createResponseBuilder(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Did not find expected items in CosmosDB input list").build();
        }
    }

     public static class TestData {
        public String id;
    }
```

Você invoca esta função em um HttpRequest. Ele grava vários valores no armazenamento de fila.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage e HttpResponseMessage

 Estes são `azure-functions-java-library`definidos em . Eles são tipos de ajudantes para trabalhar com funções HttpTrigger.

| Tipo especializado      |       Destino        | Uso típico                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Gatilho de HTTP     | Obtém método, cabeçalhos ou consultas |
| `HttpResponseMessage` | Associação de saída HTTP | Status de retorno diferente de 200   |

## <a name="metadata"></a>Metadados

Poucos gatilhos enviam [gatilhos de metadados](/azure/azure-functions/functions-triggers-bindings) juntamente com dados de entrada. Você pode usar `@BindingName` anotação para vincular a metadados de gatilho.


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
No exemplo anterior, `queryValue` o está vinculado ao `name` parâmetro de seqüência de consulta na URL de solicitação HTTP, `http://{example.host}/api/metadata?name=test`. Aqui está outro exemplo, mostrando `Id` como se vincular a metadados de gatilho de fila.

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " with metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> O nome fornecido na anotação precisa corresponder à propriedade de metadados.

## <a name="execution-context"></a>Contexto de execução

`ExecutionContext`, definido `azure-functions-java-library`no , contém métodos auxiliares para se comunicar com as funções tempo de execução.

### <a name="logger"></a>Agente

Use `getLogger`, `ExecutionContext`definido em , para escrever logs a partir do código de função.

Exemplo:

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>Exibir logs e rastreamento

Você pode usar o Azure CLI para transmitir o registro de Java stdout e stderr, bem como outros registros de aplicativos. 

Veja como configurar seu aplicativo de função para gravar o registro de aplicativos usando o Cli do Azure:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Para transmitir a saída de registro para o aplicativo de função usando o Azure CLI, abra uma nova sessão de comando, Bash ou Terminal e digite o seguinte comando:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
O comando [az webapp log tail](/cli/azure/webapp/log) tem `--provider` opções para filtrar a saída usando a opção. 

Para baixar os arquivos de log como um único arquivo ZIP usando o Azure CLI, abra uma nova sessão de prompt de comando, Bash ou Terminal e digite o seguinte comando:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Você deve ter ativado o registro do sistema de arquivos no portal Azure ou no Azure CLI antes de executar este comando.

## <a name="environment-variables"></a>Variáveis de ambiente

Em funções, [configurações do aplicativo](functions-app-settings.md), como conexão de serviço cadeias de caracteres, são expostas como variáveis de ambiente durante a execução. Você pode acessar essas configurações usando, `System.getenv("AzureWebJobsStorage")`.

O exemplo a seguir obtém a `myAppSetting`configuração do [aplicativo,](functions-how-to-use-azure-function-app-settings.md#settings)com a chave nomeada :

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o desenvolvimento do Java de funções do Azure, consulte os seguintes recursos:

* [Práticas recomendadas do Azure Functions](functions-best-practices.md)
* [Referência do desenvolvedor do Azure Functions](functions-reference.md)
* [Azure Funções gatilhos e vinculações](functions-triggers-bindings.md)
* Desenvolvimento local e depuração com [Visual Studio Code,](https://code.visualstudio.com/docs/java/java-azurefunctions) [IntelliJ](functions-create-maven-intellij.md)e [Eclipse](functions-create-maven-eclipse.md)
* [Azure Functions de Java de Depuração Remota com Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Plugin Maven para Funções Azure](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Simplifique `azure-functions:add` a criação de funções através do objetivo e prepare um diretório de preparação para [implantação de arquivo ZIP](deployment-zip-push.md).
