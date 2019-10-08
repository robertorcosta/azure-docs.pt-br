---
title: 'Início Rápido: Biblioteca de clientes da Pesquisa Visual do Bing para Java | Microsoft Docs'
description: Carregue uma imagem usando o SDK da Pesquisa Visual do Bing e obtenha insights sobre ela.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 09/30/2019
ms.author: aahi
ms.openlocfilehash: 7fb00fd3ce588aeeba4f315f191f6b82d6b75715
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695721"
---
# <a name="quickstart-bing-visual-search-client-library-for-java"></a>Início Rápido: Biblioteca de clientes da Pesquisa Visual do Bing para Java

Introdução à biblioteca de clientes da Pesquisa Visual do Bing para Java. Siga essas etapas para instalar o pacote e testar o código de exemplo para tarefas básicas. 

Use a biblioteca de clientes da Pesquisa Visual do Bing para Java para:

* Fazer upload de uma imagem para enviar uma solicitação de pesquisa visual.
* Obter o token do insight da imagem e as marcas da pesquisa visual.

[Documentação de referência](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingvisualsearch?view=azure-java-stable) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingVisualSearch) | [Artefato (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-visualsearch/) | [Exemplos](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [JDK (Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A [ferramenta de build do Gradle](https://gradle.org/install/) ou outro gerenciador de dependência

## <a name="setting-up"></a>Configurando

### <a name="create-a-bing-visual-search-azure-resource"></a>Criar um recurso do Azure da Pesquisa Visual do Bing

Os Serviços Cognitivos do Azure são representados por recursos do Azure que você assina. Crie um recurso da Pesquisa Visual do Bing usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou a [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) no computador local. Você também pode:

* Obter uma [chave de avaliação](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias gratuitamente. Depois de se inscrever, ela estará disponível no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Exibir esse recurso no [portal do Azure](https://portal.azure.com/).

Depois de obter uma chave do recurso ou da assinatura de avaliação, [crie uma variável de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave, chamada `BING_SEARCH_V7_SUBSCRIPTION_KEY`.

### <a name="create-a-new-gradle-project"></a>Criar um novo projeto Gradle

Em uma janela de console (como cmd, PowerShell ou Bash), crie um novo diretório para seu aplicativo e navegue até ele. 

```console
mkdir myapp && cd myapp
```

Execute o comando `gradle init` em seu diretório de trabalho. Esse comando criará arquivos de build essenciais para o Gradle, incluindo *build.gradle.kts*, que é usado em runtime para criar e configurar seu aplicativo.

```console
gradle init --type basic
```

Quando solicitado a escolher uma **DSL**, escolha **Kotlin**.

Localize o *build.gradle.kts* e abra-o com seu IDE ou editor de texto preferencial. Depois copie nessa configuração de build:

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingVisualSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Crie uma pasta para seu aplicativo de exemplo. Do diretório de trabalho, execute o seguinte comando:

```console
mkdir -p src/main/java
```

Crie uma pasta para a imagem da qual deseja fazer upload na API. Coloque a imagem dentro da pasta **resources**.

```console
mkdir -p src/main/resources
``` 

Navegue até a nova pasta e crie um arquivo chamado *BingVisualSearchSample.java*. Abra-a no editor ou IDE de sua preferência e adicione as seguintes instruções `import`:

[!code-java[Import statements](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=imports)]

Em seguida, crie uma classe.

```java
public class BingVisualSearchSample {
}
```

No método `main` do aplicativo, crie variáveis para a chave e o ponto de extremidade do Azure do recurso. Se você criou a variável de ambiente depois de iniciar o aplicativo, será necessário fechar e recarregar o editor, o IDE ou o shell em execução para acessar a variável. Em seguida, crie um `byte[]` para a imagem que você carregará. Crie um bloco `try` para os métodos que você definirá posteriormente e carregue a imagem e converta-a em bytes usando `toByteArray()`.

[!code-java[Main method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=main)]


### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Este início rápido usa o gerenciador de dependência do Gradle. Você pode encontrar a biblioteca de clientes e informações para outros gerenciadores de dependência no [Repositório Central do Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

No arquivo *build.gradle.kts* do projeto, lembre-se de incluir a biblioteca de clientes como uma instrução `implementation`. 

```kotlin
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

## <a name="code-examples"></a>Exemplos de código

Esses snippets de código mostram como realizar as seguintes tarefas com a biblioteca de clientes da Pesquisa Visual do Bing e o Java:

* [Autenticar o cliente](#authenticate-the-client)
* [Enviar uma solicitação de pesquisa visual](#send-a-visual-search-request)
* [Imprimir o token do insight da imagem e as marcas da pesquisa visual](#print-the-image-insight-token-and-visual-search-tags)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este início rápido pressupõe que você tenha [criado uma variável de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave da Pesquisa Visual do Bing, chamada `BING_SEARCH_V7_SUBSCRIPTION_KEY`.


No método principal, lembre-se de usar sua chave de assinatura para criar uma instância de um objeto [BingVisualSearchAPI](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingvisualsearchapi?view=azure-java-stable).

```csharp
BingVisualSearchAPI client = BingVisualSearchManager.authenticate(subscriptionKey);
```

## <a name="send-a-visual-search-request"></a>Enviar uma solicitação de pesquisa visual

Em um novo método, envie a matriz de bytes da imagem (que foi criada no método `main()`) usando o método [bingImages().visualSearch()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingimages.visualsearch?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_visualsearch_BingImages_visualSearch__) do cliente. 

[!code-java[visualSearch() method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=visualSearch)]

## <a name="print-the-image-insight-token-and-visual-search-tags"></a>Imprimir o token do insight da imagem e as marcas da pesquisa visual

Verifique se o objeto [ImageKnowledge](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.models.imageknowledge?view=azure-java-stable) é nulo. Se ele não for, imprima o token de insights da imagem, o número de marcas, o número de ações e o primeiro tipo de ação.

[!code-java[Print token and tags](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=printVisualSearchResults)]

## <a name="run-the-application"></a>Executar o aplicativo

É possível criar o aplicativo com:

```console
gradle build
```

Execute o aplicativo com a meta `run`:

```console
gradle run
```

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](tutorial-bing-visual-search-single-page-app.md)
