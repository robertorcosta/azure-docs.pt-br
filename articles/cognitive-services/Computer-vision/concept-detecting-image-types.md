---
title: Detecção de tipo de imagem-Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados adetecção do tipo de imagem da API da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4e6c2db5333962d7ae43534998ffc1c48b0dba45
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80244555"
---
# <a name="detecting-image-types-with-computer-vision"></a>Detectar tipos de imagem com a Pesquisa Visual Computacional

Com a API de [análise de imagem](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) , pesquisa Visual computacional pode analisar o tipo de conteúdo de imagens, indicando se uma imagem é uma clip-art ou um desenho de linha.

## <a name="detecting-clip-art"></a>Detectar clip-art

A Pesquisa Visual Computacional analisa uma imagem e classifica a probabilidade da imagem ser clip-art em uma escala de 0 a 3, conforme descrito na tabela a seguir.

| Valor | Significado |
|-------|---------|
| 0 | Non-clip-art |
| 1 | Ambíguo |
| 2 | Normal-clip-art |
| 3 | Good-clip-art |

### <a name="clip-art-detection-examples"></a>Exemplos de detecção de clip-art

As respostas JSON a seguir ilustram o que a Pesquisa Visual Computacional retorna ao avaliar a probabilidade das imagens de exemplo serem clip-art.

![Imagem clip-art de uma fatia de queijo](./Images/cheese_clipart.png)

```json
{
    "imageType": {
        "clipArtType": 3,
        "lineDrawingType": 0
    },
    "requestId": "88c48d8c-80f3-449f-878f-6947f3b35a27",
    "metadata": {
        "height": 225,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Uma casa azul e o jardim da frente](./Images/house_yard.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "a9c8490a-2740-4e04-923b-e8f4830d0e47",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="detecting-line-drawings"></a>Detectar desenhos de linha

A Pesquisa Visual Computacional analisa uma imagem e retorna um valor booleano indicando se a imagem é um desenho de linha.

### <a name="line-drawing-detection-examples"></a>Exemplos de detecção de desenho de linha

As respostas JSON a seguir ilustram o que a Pesquisa Visual Computacional retorna ao indicar se as imagens de exemplo são desenhos de linha.

![Um desenho em linhas de um leão](./Images/lion_drawing.png)

```json
{
    "imageType": {
        "clipArtType": 2,
        "lineDrawingType": 1
    },
    "requestId": "6442dc22-476a-41c4-aa3d-9ceb15172f01",
    "metadata": {
        "height": 268,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Uma flor branca com um fundo verde](./Images/flower.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "98437d65-1b05-4ab7-b439-7098b5dfdcbf",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>Usar a API

O recurso de detecção de tipo de imagem faz parte da API de [análise de imagem](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) . Você pode chamar essa API por meio de um SDK nativo ou por meio de chamadas REST. Inclua `ImageType` no parâmetro de consulta **visualFeatures** . Em seguida, quando você obtém a resposta JSON completa, simplesmente analise a cadeia de caracteres para o `"imageType"` conteúdo da seção.

* [Início rápido: SDK do .NET Pesquisa Visual Computacional](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Início rápido: analisar uma imagem (API REST)](./quickstarts/csharp-analyze.md)
