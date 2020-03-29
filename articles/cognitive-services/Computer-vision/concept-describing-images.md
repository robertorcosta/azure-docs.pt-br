---
title: Descrições de imagem - Visão computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados ao recurso de descrição de imagem da API da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 7bf95a2b49608ef1f031a3b443db92b42cdae624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244692"
---
# <a name="describe-images-with-human-readable-language"></a>Descrever imagens com linguagem legível por humanos

A Pesquisa Visual Computacional pode analisar uma imagem e gerar uma sentença legível por humanos que descreve seu conteúdo. O algoritmo realmente retorna várias descrições com base em diferentes características visuais, e cada descrição recebe uma pontuação de confiança. A saída final é uma lista de descrições ordenadas do maior para o menor nível de confiança.

## <a name="image-description-example"></a>Exemplo de descrição de imagem

A resposta JSON a seguir ilustra o que o Computer Vision retorna ao descrever a imagem de exemplo com base em seus recursos visuais.

![Uma imagem em preto e branco dos edifícios em Manhattan](./Images/bw_buildings.png)

```json
{
    "description": {
        "tags": ["outdoor", "building", "photo", "city", "white", "black", "large", "sitting", "old", "water", "skyscraper", "many", "boat", "river", "group", "street", "people", "field", "tall", "bird", "standing"],
        "captions": [
            {
                "text": "a black and white photo of a city",
                "confidence": 0.95301952483304808
            },
            {
                "text": "a black and white photo of a large city",
                "confidence": 0.94085190563213816
            },
            {
                "text": "a large white building in a city",
                "confidence": 0.93108362931954824
            }
        ]
    },
    "requestId": "b20bfc83-fb25-4b8d-a3f8-b2a1f084b159",
    "metadata": {
        "height": 300,
        "width": 239,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>Usar a API

O recurso de descrição da imagem faz parte da API [Analisar imagem.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Você pode chamar essa API por meio de um SDK nativo ou por meio de chamadas REST. Inclua `Description` no parâmetro de consulta **visualFeatures.** Em seguida, quando você receber a resposta JSON completa, `"description"` basta analisar a seqüência para o conteúdo da seção.

* [Partida rápida: Visão computacional .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Quickstart: Analise uma imagem (Rest API)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>Próximas etapas

Aprenda os conceitos relacionados de [marcar imagens](concept-tagging-images.md) e [categorizar imagens](concept-categorizing-images.md).
