---
title: Início rápido da biblioteca de clientes Java do Content Moderator
titleSuffix: Azure Cognitive Services
description: Neste início rápido, saiba como começar a usar a biblioteca de clientes do Content Moderator dos Serviços Cognitivos do Azure para Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 01/27/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 4d4e09bed1350a59848ef6853efe2a301d7357e9
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87375619"
---
Introdução à biblioteca de clientes do Content Moderator para Java. Siga essas etapas para instalar o pacote e testar o código de exemplo para tarefas básicas. O Content Moderator é um serviço cognitivo que verifica conteúdo de textos, imagens e vídeos para verificar material que possa ser ofensivo, de risco ou, de alguma maneira, indesejável. Quando tal material é encontrado, o serviço aplica rótulos (sinalizadores) apropriados ao conteúdo. Seu aplicativo pode tratar de conteúdo sinalizado para cumprir as normas ou manter o ambiente desejado para os usuários.

Use a biblioteca de clientes do Content Moderator para Java para:

* Imagens moderadas para conteúdo para adulto ou erótico, texto ou rostos humanos.

[Documentação de referência](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable) | [Artefato (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) | [Amostras](https://docs.microsoft.com/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* A versão atual do [JDK (Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A [ferramenta de build Gradle](https://gradle.org/install/) ou outro gerenciador de dependência.

## <a name="setting-up"></a>Configurando

### <a name="create-a-content-moderator-azure-resource"></a>Criar um recurso do Azure no Content Moderator

Os Serviços Cognitivos do Azure são representados por recursos do Azure que você assina. Crie um recurso para o Content Moderator usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou a [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) em seu computador local. Também é possível:

* Exibir esse recurso no [portal do Azure](https://portal.azure.com/).

Depois de obter uma chave de seu recurso, [crie uma variável de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave, denominada `AZURE_CONTENTMODERATOR_KEY`.

### <a name="create-a-new-gradle-project"></a>Criar um novo projeto Gradle

Em uma janela de console (como cmd, PowerShell ou Bash), crie um novo diretório para seu aplicativo e navegue até ele. 

```console
mkdir myapp && cd myapp
```
Execute `gradle init`. Esse comando criará arquivos de build essenciais para o Gradle, incluindo o *build.gradle.kts*, que é usado no runtime para criar e configurar seu aplicativo. Execute este comando no diretório de trabalho:

```console
gradle init --type basic
```

Quando solicitado a escolher uma DSL de script de build, selecione **Kotlin**.

Localize o *build.gradle.kts* e abra-o com seu IDE ou editor de texto preferencial. Depois copie nessa configuração de build a seguir. Essa configuração define o projeto como um aplicativo Java cujo ponto de entrada é a classe **ContentModeratorQuickstart**. Ele importa a biblioteca de clientes do Content Moderator, bem como o SDK do Gson para a serialização JSON.

```kotlin
plugins {
    java
    application
}

application{ 
    mainClassName = "ContentModeratorQuickstart"
}

repositories{
    mavenCentral()
}

dependencies{
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-contentmoderator", version = "1.0.2-beta")
    compile(group = "com.google.code.gson", name = "gson", version = "2.8.5")
}
```

Em seu diretório de trabalho, execute o comando a seguir para criar uma pasta de origem do projeto.

```console
mkdir -p src/main/java
```

Em seguida, crie um arquivo chamado *ContentModeratorQuickstart.java* na nova pasta. Abra o arquivo em seu editor ou IDE preferencial e importe as seguintes bibliotecas na parte superior:

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

## <a name="object-model"></a>Modelo de objeto

As classes a seguir tratam de alguns dos principais recursos da biblioteca de clientes do Java do Content Moderator.

|Nome|Descrição|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|Essa classe é necessária para toda a funcionalidade do Content Moderator. Você a instancia com suas informações de assinatura e a usa para produzir instâncias de outras classes.|
|[ImageModeration](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|Essa classe fornece a funcionalidade para analisar imagens de conteúdo para adulto, informações pessoais ou rostos humanos.|
|[TextModerations](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|Essa classe fornece a funcionalidade para analisar o texto de idioma, profanação, erros e informações pessoais.|
|[Análises](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|Essa classe fornece a funcionalidade das APIs de Revisão, incluindo os métodos para criar trabalhos, fluxos de trabalho personalizados e revisões humanas.|


## <a name="code-examples"></a>Exemplos de código

Estes snippets de códigos mostram como realizar as seguintes tarefas com a biblioteca de clientes do Content Moderator para Java:

* [Autenticar o cliente](#authenticate-the-client)
* [Moderar imagens](#moderate-images)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Esta etapa pressupõe que você já [criou uma variável de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave do Content Moderator, denominada `AZURE_CONTENTMODERATOR_KEY`.

No método do `main` aplicativo, crie um objeto [ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable) usando o valor do ponto de extremidade da assinatura e a variável de ambiente da chave de assinatura. 

> [!NOTE]
> Se você criou a variável de ambiente depois de iniciar o aplicativo, será necessário fechar e recarregar o editor, o IDE ou o shell em execução para acessar a variável.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]

## <a name="moderate-images"></a>Moderar imagens

### <a name="get-images"></a>Obter imagens

Na pasta **src/main/** do seu projeto, crie uma pasta **recursos** e navegue até ela. Em seguida, crie um novo arquivo de texto, *ImageFiles.txt*. Nesse arquivo, você adiciona as URLs de imagens para analisar&mdash;uma URL em cada linha. Use as seguintes imagens de exemplo:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Definir a classe auxiliar

Em seguida, no seu arquivo *ContentModeratorQuickstart.java*, adicione a seguinte definição de classe dentro da classe **ContentModeratorQuickstart**. Essa classe interna será usada posteriormente no processo de moderação da imagem.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]

### <a name="iterate-through-images"></a>Iterar as imagens

Em seguida, adicione o código a seguir à parte inferior do método `main`. Ou você pode adicioná-lo a um método separado que é chamado de `main`. Esse código percorre cada linha do arquivo _ImageFiles.txt_.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Verificar se há conteúdo adulto ou erótico
Essa linha de código verifica a imagem na URL fornecida quanto a conteúdo para adulto ou erótico. Consulte o guia conceitual de moderação de imagem para obter informações sobre esses termos.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Verificar se há texto
Essa linha de código verifica a imagem quanto a texto visível.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Verificar se há rostos
Essa linha de código verifica a imagem quanto a rostos humanos.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Por fim, armazene as informações retornadas na lista `EvaluationData`.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Imprimir resultados

Após o loop `while`, adicione o código a seguir, que imprime os resultados no console e em um arquivo de saída, *src/main/resources/ModerationOutput.json*.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Feche a instrução `try` e adicione uma instrução `catch` para concluir o método.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>Executar o aplicativo

É possível criar o aplicativo com:

```console
gradle build
```

Execute o aplicativo com o comando `gradle run`:

```console
gradle run
```

Em seguida, navegue até o arquivo *src/main/resources/ModerationOutput.json* e exiba os resultados da sua moderação de conteúdo.

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a usar a biblioteca Java do Content Moderator para realizar tarefas de moderação. Em seguida, saiba mais sobre a moderação de imagens ou outra mídia lendo um guia conceitual.

> [!div class="nextstepaction"]
>[Conceitos de moderação de imagem](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [O que é o Azure Content Moderator?](../../overview.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java).