---
title: Detecção de objetos – Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Aprenda conceitos relacionados ao recurso de detecção de objetos da API de visão computacional - uso e limites.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 3957e15a09bd7e7ecd814d169451af3241108b64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131668"
---
# <a name="detect-common-objects-in-images"></a>Detectar objetos comuns em imagens

A detecção de objetos é semelhante à [marcação](concept-tagging-images.md), mas a API retorna as coordenadas da caixa delimitadora (em pixels) para cada objeto encontrado. Por exemplo, se uma imagem contiver um cachorro, um gato e uma pessoa, a operação Detect listará esses objetos junto com as coordenadas na imagem. Você pode usar essa funcionalidade para processar as relações entre os objetos em uma imagem. Ele também permite determinar se há várias instâncias da mesma tag em uma imagem.

A API de Detecção se aplica a tags com base em objetos ou seres vivos identificados na imagem. Atualmente, não há relação formal entre a taxonomia de marcação e a taxonomia de detecção de objetos. Em um nível conceitual, a API Detect só encontra objetos e seres vivos, enquanto a API tag também pode incluir termos contextuais como "interior", que não podem ser localizados com caixas delimitadoras.

## <a name="object-detection-example"></a>Exemplo de detecção de objetos

A resposta JSON a seguir ilustra o que a Pesquisa Visual Computacional retorna ao detectar objetos na imagem de exemplo.

![Uma mulher usando um dispositivo Microsoft Surface em uma cozinha](./Images/windows-kitchen.jpg)

```json
{
   "objects":[
      {
         "rectangle":{
            "x":730,
            "y":66,
            "w":135,
            "h":85
         },
         "object":"kitchen appliance",
         "confidence":0.501
      },
      {
         "rectangle":{
            "x":523,
            "y":377,
            "w":185,
            "h":46
         },
         "object":"computer keyboard",
         "confidence":0.51
      },
      {
         "rectangle":{
            "x":471,
            "y":218,
            "w":289,
            "h":226
         },
         "object":"Laptop",
         "confidence":0.85,
         "parent":{
            "object":"computer",
            "confidence":0.851
         }
      },
      {
         "rectangle":{
            "x":654,
            "y":0,
            "w":584,
            "h":473
         },
         "object":"person",
         "confidence":0.855
      }
   ],
   "requestId":"a7fde8fd-cc18-4f5f-99d3-897dcd07b308",
   "metadata":{
      "width":1260,
      "height":473,
      "format":"Jpeg"
   }
}
```

## <a name="limitations"></a>Limitações

É importante observar as limitações da detecção de objetos para que você possa evitar ou mitigar os efeitos de falsos negativos (objetos perdidos) e detalhes limitados.

* Os objetos geralmente não são detectados se forem pequenos (menos de 5% da imagem).
* Os objetos geralmente não são detectados se eles estão dispostos de perto (uma pilha de placas, por exemplo).
* Os objetos não são diferenciados por marca ou nome do produto (tipos diferentes de refrigerante na prateleira de uma loja, por exemplo). No entanto, você pode obter informações sobre a marca de uma imagem usando o recurso [Detecção de marca](concept-brand-detection.md).

## <a name="use-the-api"></a>Usar a API

O recurso de detecção de objeto faz parte da API [Analisar Imagem](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa). Você pode chamar essa API por meio de um SDK nativo ou por meio de chamadas REST. Inclua `Objects` no parâmetro de consulta **visualFeatures.** Em seguida, quando você receber a resposta JSON completa, `"objects"` basta analisar a seqüência para o conteúdo da seção.

* [Partida rápida: Visão computacional .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Quickstart: Analise uma imagem (Rest API)](./quickstarts/csharp-analyze.md)