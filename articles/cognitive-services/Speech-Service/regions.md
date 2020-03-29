---
title: Regiões - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Uma lista de regiões disponíveis e pontos finais para o serviço de fala, incluindo fala-para-texto, texto-para-fala e tradução de fala.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 560575ca7f51218e472abecb4319f4a3db69b1ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220463"
---
# <a name="speech-service-supported-regions"></a>Serviço de fala apoiado regiões

O Serviço de Fala permite que seu aplicativo converta áudio em texto, realize a tradução de fala e converta texto em Fala. O serviço está disponível em várias regiões com pontos de extremidade exclusivos para o SDK de Fala e APIs de REST.

O portal Speech para executar configurações personalizadas para sua experiência de fala para todas as regiões está disponível aqui:https://speech.microsoft.com

Para invocações do seu serviço de fala, certifique-se de que a chamada corresponda à região para sua assinatura.

## <a name="speech-sdk"></a>SDK de fala

No [SDK de Fala](speech-sdk.md), regiões são especificadas como uma cadeia de caracteres (por exemplo, como um parâmetro para `SpeechConfig.FromSubscription` no SDK de Fala para C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Fala-para-texto, texto-para-fala e tradução

O portal de personalização de falas está disponível aqui:https://speech.microsoft.com

O serviço de fala está disponível nessas regiões para reconhecimento de **fala,** **texto-para-fala**e **tradução:**

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

Se você usar o [Speech SDK,](speech-sdk.md)as regiões serão especificadas pelo **identificador Região** (por exemplo, como parâmetro para `SpeechConfig.FromSubscription`). Certifique-se de que a região está combinando com a região de sua assinatura.

### <a name="intent-recognition"></a>Reconhecimento de intenção

As regiões disponíveis para **reconhecimento de intenção** por meio do SDK de Fala são os seguintes:

| Região global | Região           | Identificador de região |
| ------------- | ---------------- | -------------------- |
| Ásia          | Leste da Ásia        | `eastasia`           |
| Ásia          | Sudeste Asiático   | `southeastasia`      |
| Austrália     | Leste da Austrália   | `australiaeast`      |
| Europa        | Norte da Europa     | `northeurope`        |
| Europa        | Europa Ocidental      | `westeurope`         |
| América do Norte | Leste dos EUA          | `eastus`             |
| América do Norte | Leste dos EUA 2        | `eastus2`            |
| América do Norte | Centro-Sul dos Estados Unidos | `southcentralus`     |
| América do Norte | Centro-Oeste dos EUA  | `westcentralus`      |
| América do Norte | Oeste dos EUA          | `westus`             |
| América do Norte | Oeste dos EUA 2        | `westus2`            |
| América do Sul | Sul do Brasil     | `brazilsouth`        |

Este é um subconjunto das regiões de publicação compatíveis com o [LUIS (Serviço Inteligente de Reconhecimento Vocal)](/azure/cognitive-services/luis/luis-reference-regions).

### <a name="voice-assistants"></a>Assistentes de voz

O [Speech SDK](speech-sdk.md) suporta recursos **de assistente de voz** nessas regiões:

| Região         | Identificador de região |
| -------------- | -------------------- |
| Oeste dos EUA        | `westus`             |
| Oeste dos EUA 2      | `westus2`            |
| Leste dos EUA        | `eastus`             |
| Leste dos EUA 2      | `eastus2`            |
| Europa Ocidental    | `westeurope`         |
| Norte da Europa   | `northeurope`        |
| Sudeste Asiático | `southeastasia`      |

## <a name="rest-apis"></a>APIs REST

O serviço de fala também expõe pontos de extremidade REST para solicitações de Fala em texto e texto em Fala.

### <a name="speech-to-text"></a>Conversão de fala em texto

Para obter a documentação de referência de fala a texto, consulte [API REST de fala para texto](rest-speech-to-text.md).

O ponto final para a API REST tem esse formato:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Substitua pelo `<REGION_IDENTIFIER>` identificador que corresponde à região da sua assinatura a partir desta tabela:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> O parâmetro de linguagem deve ser anexado à URL para evitar o recebimento de um erro HTTP 4xx. Por exemplo, o idioma definido para inglês dos EUA usando o ponto de extremidade West US é: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

### <a name="text-to-speech"></a>Conversão de texto em fala

Para obter a documentação de referência texto-a-fala, consulte [API REST](rest-text-to-speech.md)de texto para fala .

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
