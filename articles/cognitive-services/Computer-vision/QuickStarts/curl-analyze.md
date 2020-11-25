---
title: 'Início Rápido: Analisar uma imagem remota – REST, cURL'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você analisará uma imagem remota usando a API da Pesquisa Visual Computacional com cURL.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 60b0ab502f98868cc5b3ffb254aaf1798ea1d227
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95999953"
---
# <a name="quickstart-analyze-a-remote-image-using-the-computer-vision-rest-api-and-curl"></a>Início Rápido: Analisar uma imagem remota usando o cURL e a API REST da Pesquisa Visual Computacional

Neste início rápido, você analisará uma imagem armazenada remotamente para extrair características visuais usando a API REST da Pesquisa Visual Computacional. Com o método [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b), é possível extrair recursos visuais com base no conteúdo de imagem.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/) 
* [Curl](https://curl.haxx.se/)
* Depois de obter sua assinatura do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Crie um recurso da Pesquisa Visual Computacional"  target="_blank">crie um recurso da Pesquisa Visual Computacional <span class="docon docon-navigate-external x-hidden-focus"></span></a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
  * Você precisará da chave e do ponto de extremidade do recurso criado para conectar seu aplicativo ao serviço de Pesquisa Visual Computacional. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
  * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

## <a name="create-and-run-the-sample-command"></a>Criar e executar o comando de exemplo

Para criar e executar o exemplo, siga estas etapas:

1. Copie o seguinte comando em um editor de texto.
1. Faça as alterações a seguir no comando quando necessário:
    1. Substitua o valor de `<subscriptionKey>` pela sua chave de assinatura.
    1. Substitua a primeira parte da URL de solicitação (`westcentralus`) pelo texto em sua URL de ponto de extremidade.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Outra opção é alterar a URL da imagem no corpo da solicitação (`http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\`) para uma URL de uma imagem diferente a ser analisada.
1. Abra una janela de prompt de comando.
1. Cole o comando do editor de texto na janela do prompt de comando e, em seguida, execute-o.

```bash
curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v3.1/analyze?visualFeatures=Categories,Description&details=Landmarks" -d "{\"url\":\"http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\"}"
```

## <a name="examine-the-response"></a>Examinar a resposta

Uma resposta com êxito é retornada em JSON. O aplicativo de exemplo analisa e exibe uma resposta bem-sucedida na janela do prompt de comando, semelhante ao exemplo a seguir:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "b6e33879-abb2-43a0-a96e-02cb5ae0b795",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>Próximas etapas

Explore a API da Pesquisa Visual Computacional usada para analisar uma imagem, detectar celebridades e pontos de referência, criar uma miniatura e extrair textos manuscritos e impressos. Para testar rapidamente a API da Pesquisa Visual Computacional, experimente o [Abrir o console de teste de API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b/console).

> [!div class="nextstepaction"]
> [Explorar a API da Pesquisa Visual Computacional](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b)
