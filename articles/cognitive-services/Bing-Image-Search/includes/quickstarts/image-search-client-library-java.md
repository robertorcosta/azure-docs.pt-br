---
title: Início rápido da biblioteca de clientes Java da Pesquisa de Imagem do Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: 4656d8116ddd2e618adad97aeea576e88bc8785f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "78899383"
---
Use este início rápido para fazer sua primeira pesquisa de imagem usando a biblioteca de clientes da Pesquisa de Imagem do Bing, que é um wrapper para a API e contém os mesmos recursos. Esse aplicativo simples do Java envia uma consulta de pesquisa de imagem, analisa a resposta JSON e exibe a URL da primeira imagem retornada.

O código-fonte para esse exemplo está disponível [no GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingImageSearch/Quickstart) com anotações e tratamentos de erro adicionais.

## <a name="prerequisites"></a>Prerequisites

A versão mais recente do JDK [(Java Development Kit)](https://aka.ms/azure-jdks)

Instale as dependências da biblioteca de clientes da Pesquisa de Imagem do Bing usando Maven, Gradle ou outro sistema de gerenciamento de dependência. O arquivo POM Maven requer a seguinte declaração:

```xml
 <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-imagesearch</artifactId>
      <version>1.0.1</version>
    </dependency>
 </dependencies>
```

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar o aplicativo

1. Crie um novo projeto Java em seu IDE ou editor favorito e adicione as seguintes importações à sua implementação de classe:

    ```java
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchAPI;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchManager;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImageObject;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImagesModel;
    ```

2. No seu método principal, crie variáveis para o termo de pesquisa e a chave de assinatura. Em seguida, instancie o cliente da Pesquisa de Imagem do Bing.

    ```java
    final String subscriptionKey = "COPY_YOUR_KEY_HERE";
    String searchTerm = "canadian rockies";
    //Image search client
    BingImageSearchAPI client = BingImageSearchManager.authenticate(subscriptionKey);
    ```

## <a name="send-a-search-request-to-the-api"></a>Enviar uma solicitação de pesquisa para a API

1. Usando `bingImages().search()`, envie a solicitação HTTP que contém a consulta de pesquisa. Salve a resposta como um `ImagesModel`.

   ```java
    ImagesModel imageResults = client.bingImages().search()
                .withQuery(searchTerm)
                .withMarket("en-us")
                .execute();
    ```

## <a name="parse-and-view-the-result"></a>Analisar e exibir o resultado

Analise os resultados da imagem retornados na resposta.
Se a resposta contiver os resultados da pesquisa, armazene o primeiro resultado e imprima os detalhes, como a URL da miniatura, a URL original, juntamente com o número total de imagens retornadas.  

```java
if (imageResults != null && imageResults.value().size() > 0) {
    // Image results
    ImageObject firstImageResult = imageResults.value().get(0);

    System.out.println(String.format("Total number of images found: %d", imageResults.value().size()));
    System.out.println(String.format("First image thumbnail url: %s", firstImageResult.thumbnailUrl()));
    System.out.println(String.format("First image content url: %s", firstImageResult.contentUrl()));
}
else {
        System.out.println("Couldn't find image results!");
     }

```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial de aplicativo de página única da Pesquisa de Imagem do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Confira também

* [O que é a Pesquisa de Imagem do Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Experimente uma demonstração interativa online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Obtenha uma chave de acesso de Serviços Cognitivos grátis](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Exemplos do Java para o SDK de Serviços Cognitivos do Azure](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
* [Documentação dos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services)
* [Referência da API de Pesquisa de Imagem do Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
