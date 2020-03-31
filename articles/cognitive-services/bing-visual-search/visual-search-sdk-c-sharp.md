---
title: 'Início Rápido: Obtenha insights de imagem usando o SDK para C# – Pesquisa Visual do Bing'
titleSuffix: Azure Cognitive Services
description: Aprenda a carregar uma imagem usando o SDK da Pesquisa Visual do Bing e obtenha insights sobre ela com este início rápido.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: b1f5274bcae1f6e59f6dea94beee810a4613d739
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446604"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-c"></a>Início Rápido: Obtenha insights de imagem usando o SDK de Pesquisa Visual do Bing para C#

Use este início rápido para começar obtendo insights de imagem do serviço de Pesquisa Visual do Bing, usando o SDK do C#. Embora a Pesquisa Visual do Bing tenha uma API REST compatível com a maioria das linguagens de programação, o SDK fornece uma maneira fácil de integrar o serviço aos seus aplicativos. O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVisualSearch).

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Se você estiver usando Linux/MacOS, este aplicativo poderá ser executado usando [Mono](https://www.mono-project.com/).
* O pacote de Pesquisa Visual do NuGet. 
    - No Gerenciador de Soluções no Visual Studio, clique com o botão direito do mouse no seu projeto e selecione `Manage NuGet Packages` no menu. Instale o pacote `Microsoft.Azure.CognitiveServices.Search.VisualSearch`. Instalar os pacotes do NuGet também instala o seguinte:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Criar e inicializar o aplicativo

1. No Visual Studio, crie um novo projeto. Em seguida, adicione as seguintes diretivas.
    
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
    ```

2. Crie uma instância do cliente com sua chave de assinatura.
    
    ```csharp
    var client = new VisualSearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```
    
## <a name="send-a-search-request"></a>Enviar uma solicitação de pesquisa 

1. Criar uma `FileStream` para suas imagens (neste caso, `TestImages/image.jpg`). Em seguida, use o cliente para enviar uma solicitação de pesquisa usando `client.Images.VisualSearchMethodAsync()`. 
    
    ```csharp
     System.IO.FileStream stream = new FileStream(Path.Combine("TestImages", "image.jpg"), FileMode.Open);
     // The knowledgeRequest parameter is not required if an image binary is passed in the request body
     var visualSearchResults = client.Images.VisualSearchMethodAsync(image: stream, knowledgeRequest: (string)null).Result;
    ```
    
2. Analise os resultados das consultas anteriores:

    ```csharp
    // Visual Search results
    if (visualSearchResults.Image?.ImageInsightsToken != null)
    {
        Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
    }
    else
    {
        Console.WriteLine("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.Tags.Count > 0)
    {
        var firstTagResult = visualSearchResults.Tags[0];
        Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");
    
        // List of actions in first tag
        if (firstTagResult.Actions.Count > 0)
        {
            var firstActionResult = firstTagResult.Actions[0];
            Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
            Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
        }
        else
        {
            Console.WriteLine("Couldn't find tag actions!");
        }
    }
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](tutorial-bing-visual-search-single-page-app.md)
