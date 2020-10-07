---
title: 'Início Rápido: Biblioteca de clientes da Pesquisa Visual Computacional para Java'
description: Neste início rápido, comece a usar a biblioteca de clientes da Pesquisa Visual Computacional para Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/19/2019
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 2b305b1ffc5c72780f903c7798fbce24c630baba
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "89321815"
---
<a name="HOLTop"></a>

[Documentação de referência](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable) | [Artefato (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision) | [Amostras](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* A versão atual do [JDK (Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A [ferramenta de build Gradle](https://gradle.org/install/) ou outro gerenciador de dependência.
* Depois de obter sua assinatura do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Crie um recurso da Pesquisa Visual Computacional"  target="_blank">crie um recurso da Pesquisa Visual Computacional <span class="docon docon-navigate-external x-hidden-focus"></span></a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso criado para conectar seu aplicativo ao serviço de Pesquisa Visual Computacional. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.
* [Crie variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave e a URL do ponto de extremidade, chamados `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT`, respectivamente.

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-gradle-project"></a>Criar um novo projeto Gradle

Em uma janela de console (como cmd, PowerShell ou Bash), crie um novo diretório para seu aplicativo e navegue até ele. 

```console
mkdir myapp && cd myapp
```

Execute o comando `gradle init` em seu diretório de trabalho. Esse comando criará arquivos de build essenciais para o Gradle, incluindo o *build.gradle.kts*, que é usado no runtime para criar e configurar seu aplicativo.

```console
gradle init --type basic
```

Quando solicitado a escolher uma **DSL**, escolha **Kotlin**.

Localize o *build.gradle.kts* e abra-o com seu IDE ou editor de texto preferencial. Depois copie nessa configuração de build a seguir. Essa configuração define o projeto como um aplicativo Java cujo ponto de entrada é a classe **ComputerVisionQuickstarts**. Ela importa a biblioteca da Pesquisa Visual Computacional.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "ComputerVisionQuickstarts"
}
repositories {
    mavenCentral()
}
```

Em seu diretório de trabalho, execute o comando a seguir para criar uma pasta de origem do projeto:

```console
mkdir -p src/main/java
```

Navegue até a nova pasta e crie um arquivo chamado *ComputerVisionQuickstarts.java*. Abra-a no editor ou IDE de sua preferência e adicione as seguintes instruções `import`:

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imports)]

Em seguida, adicione uma definição de classe para **ComputerVisionQuickstarts**.

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Este início rápido usa o gerenciador de dependência do Gradle. Você pode encontrar a biblioteca de clientes e informações para outros gerenciadores de dependência no [Repositório Central do Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision).

No arquivo *build.gradle.kts* do seu projeto, inclua a biblioteca de clientes da Pesquisa Visual Computacional como uma dependência.

```kotlin
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.4-beta")
}
```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com alguns dos principais recursos do Java SDK da Pesquisa Visual Computacional.

|Nome|Descrição|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) | Essa classe é necessária para toda a funcionalidade da Pesquisa Visual Computacional. Você a instancia com suas informações de assinatura e a usa para produzir instâncias de outras classes.|
|[ComputerVision](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable)| Essa classe é proveniente do objeto de cliente e manipula diretamente todas as operações de imagem, como análise de imagem, detecção de texto e geração de miniaturas.|
|[VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable)| Essa enumeração define os diferentes tipos de análise de imagem que podem ser feitos em uma operação de análise padrão. Especifique um conjunto de valores VisualFeatureTypes dependendo de suas necessidades. |

## <a name="code-examples"></a>Exemplos de código

Esses snippets de códigos mostram como realizar as seguintes tarefas com a biblioteca de clientes da Pesquisa Visual Computacional para Java:

* [Autenticar o cliente](#authenticate-the-client)
* [Analisar uma imagem](#analyze-an-image)
* [Ler texto impresso e manuscrito](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este guia de início rápido pressupõe que você já [criou uma variável de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave da Pesquisa Visual Computacional, denominada `COMPUTER_VISION_SUBSCRIPTION_KEY`.

O código a seguir adiciona um método `main` à sua classe e cria variáveis para o ponto de extremidade e a chave do Azure do recurso. Será necessário inserir sua própria cadeia de caracteres de ponto de extremidade, que pode ser encontrada verificando a seção **Visão geral** do portal do Azure. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_mainvars)]

Em seguida, adicione o seguinte código para criar um objeto [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) e passe-o para outros métodos, que você definirá mais tarde.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_client)]

> [!NOTE]
> Se você tiver criado a variável de ambiente depois de iniciar o aplicativo, será necessário fechar e recarregar o editor, o IDE ou o shell em execução para acessar a variável.

## <a name="analyze-an-image"></a>Analisar uma imagem

O código a seguir define um método `AnalyzeLocalImage`, que usa o objeto de cliente para analisar uma imagem local e imprimir os resultados. O método retorna uma descrição de texto, categorização, lista de marcas, rostos detectados, sinalizadores de conteúdo para adulto, cores principais e tipo de imagem.

### <a name="set-up-test-image"></a>Configurar imagem de teste

Primeiro, crie uma pasta **resources/** na pasta **src/main/** do seu projeto e adicione uma imagem que você gostaria de analisar. Em seguida, adicione a seguinte definição de método à sua classe **ComputerVisionQuickstarts**. Se necessário, altere o valor de `pathToLocalImage` para corresponder ao arquivo de imagem. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

> [!NOTE]
> Você também pode analisar uma imagem remota usando sua URL. Confira o código de exemplo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) para ver cenários que envolvem imagens remotas.

### <a name="specify-visual-features"></a>Especificar recursos visuais

Em seguida, especifique quais recursos visuais você gostaria de extrair em sua análise. Confira a enumeração [VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable) para obter uma lista completa.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>Analisar
Esse método imprime resultados detalhados no console para cada escopo de análise de imagem. Recomendamos que você envolva essa chamada de método em um bloco Try/Catch. O método **analyzeImageInStream** retorna um objeto **ImageAnalysis** que contém todas as informações extraídas.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_analyze)]

As seções a seguir mostram como analisar essas informações detalhadamente.

### <a name="get-image-description"></a>Obter a descrição da imagem

O código a seguir obtém a lista de legendas geradas para a imagem. Para obter mais informações, confira [Descrever imagens](../../concept-describing-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_captions)]

### <a name="get-image-category"></a>Obter categoria da imagem

O código a seguir obtém a categoria detectada da imagem. Para obter mais informações, consulte [Categorizar imagens](../../concept-categorizing-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_category)]

### <a name="get-image-tags"></a>Obter marcas da imagem

O código a seguir obtém o conjunto de marcas detectadas na imagem. Para obter mais informações, consulte [Marcas de conteúdo](../../concept-tagging-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_tags)]

### <a name="detect-faces"></a>Detectar faces

O código a seguir retorna os rostos detectados na imagem com suas coordenadas de retângulo e atributos de rosto selecionados. Para obter mais informações, consulte [Detecção facial](../../concept-detecting-faces.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Detectar conteúdo para adulto, erótico ou violento

O código a seguir imprime a presença detectada de conteúdo para adulto na imagem. Para obter mais informações, confira [Conteúdo para adulto, erótico e violento](../../concept-detecting-adult-content.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_adult)]

### <a name="get-image-color-scheme"></a>Obter esquema de cores da imagem

O código a seguir imprime os atributos de cor detectados na imagem, como as cores dominantes e a cor de destaque. Para obter mais informações, consulte [Esquemas de cores](../../concept-detecting-color-schemes.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_colors)]

### <a name="get-domain-specific-content"></a>Obter conteúdo específico do domínio

A Pesquisa Visual Computacional pode usar o modelo especializado para realizar análise adicional em imagens. Para obter mais informações, confira [Conteúdo específico do domínio](../../concept-detecting-domain-content.md). 

O código a seguir analisa dados sobre celebridades detectados na imagem.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_celebrities)]

O código a seguir analisa dados sobre pontos de referência detectados na imagem.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_landmarks)]

### <a name="get-the-image-type"></a>Obter o tipo de imagem

O código a seguir imprime informações sobre o tipo de imagem – seja clip-art ou desenho de linha.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imagetype)]

## <a name="read-printed-and-handwritten-text"></a>Ler texto impresso e manuscrito

A Pesquisa Visual Computacional pode ler um texto visível em uma imagem e convertê-lo em um fluxo de caracteres. Esta seção define um método, `ReadFromFile`, que usa um caminho de arquivo local e imprime o texto da imagem no console.

> [!NOTE]
> Também é possível ler texto em uma imagem remota usando sua URL. Confira o código de exemplo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) para ver cenários que envolvem imagens remotas.

### <a name="set-up-test-image"></a>Configurar imagem de teste

Crie uma pasta **resources/** na pasta **src/main/** do seu projeto e adicione uma imagem com base na qual você gostaria de ler o texto. Você pode baixar uma [imagem de amostra](https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/cognitive-services/Computer-vision/Images/readsample.jpg) para usar aqui.

Em seguida, adicione a seguinte definição de método à sua classe **ComputerVisionQuickstarts**. Se necessário, altere o valor de `localFilePath` para corresponder ao arquivo de imagem. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_setup)]

### <a name="call-the-read-api"></a>Chamar a API de Leitura

Adicione o código a seguir para chamar o método **readInStreamWithServiceResponseAsync** para a imagem fornecida.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_call)]


O bloco de código a seguir extrai a ID da operação da resposta da chamada de leitura. Ele usa essa ID com um método auxiliar para imprimir os resultados de leitura de texto no console. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_response)]

Feche o bloco try/catch e a definição de método.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_catch)]

### <a name="get-read-results"></a>Obter os resultados da leitura

Em seguida, adicione uma definição para o método auxiliar. Esse método usa a ID da operação da etapa anterior para consultar a operação de leitura e obter os resultados do OCR quando eles estiverem disponíveis.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_call)]

O restante do método analisa os resultados do OCR e os imprime no console.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_print)]

Por fim, adicione o outro método auxiliar usado acima, que extrai a ID da operação da resposta inicial.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_opid_extract)]

## <a name="run-the-application"></a>Executar o aplicativo

É possível criar o aplicativo com:

```console
gradle build
```

Execute o aplicativo com o comando `gradle run`:

```console
gradle run
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a usar a biblioteca Java de Pesquisa Visual Computacional para realizar tarefas básicas. Em seguida, explore a documentação de referência para saber mais sobre a biblioteca.

> [!div class="nextstepaction"]
>[Referência à Pesquisa Visual Computacional (Java)](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable)

* [O que é a Pesquisa Visual Computacional?](../../overview.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java).
