---
title: Regiões-serviço de fala
titleSuffix: Azure Cognitive Services
description: Uma lista de regiões e pontos de extremidade disponíveis para o serviço de fala, incluindo conversão de fala em texto, texto em fala e tradução de fala.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: f1f425a6e3c66a72c7632cfea16d78ea6cc0d319
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201145"
---
# <a name="speech-service-supported-regions"></a>Regiões com suporte do serviço de fala

O Serviço de Fala permite que seu aplicativo converta áudio em texto, realize a tradução de fala e converta texto em Fala. O serviço está disponível em várias regiões com pontos de extremidade exclusivos para o SDK de Fala e APIs de REST.

O portal de fala para executar configurações personalizadas para sua experiência de fala para todas as regiões está disponível aqui: https://speech.microsoft.com

Para invocações de seu serviço de fala, verifique se a chamada corresponde à região da sua assinatura.

## <a name="speech-sdk"></a>SDK de fala

No [SDK de Fala](speech-sdk.md), regiões são especificadas como uma cadeia de caracteres (por exemplo, como um parâmetro para `SpeechConfig.FromSubscription` no SDK de Fala para C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Conversão de fala em texto, texto em fala e tradução

O portal de personalização de fala está disponível aqui: https://speech.microsoft.com

O serviço de fala está disponível nessas regiões para **reconhecimento de fala**, conversão de **texto em fala**e **tradução**:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

Se você usar o [SDK de fala](speech-sdk.md), as regiões serão especificadas pelo **identificador de região** (por exemplo, como um parâmetro para `SpeechConfig.FromSubscription`). Verifique se a região corresponde à região da sua assinatura.

### <a name="intent-recognition"></a>Reconhecimento de intenção

As regiões disponíveis para **reconhecimento de intenção** por meio do SDK de Fala são os seguintes:

| Região global | Região           | Parâmetro do SDK de Fala |
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

O [SDK de fala](speech-sdk.md) dá suporte a recursos do **Assistente de voz** nessas regiões:

| Região         | Parâmetro do SDK de Fala |
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

Para obter a documentação de referência de fala para texto, consulte [API REST de fala em texto](rest-speech-to-text.md).

O ponto de extremidade para a API REST tem este formato:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Substitua `<REGION_IDENTIFIER>` pelo identificador correspondente à região da sua assinatura desta tabela:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> O parâmetro de linguagem deve ser anexado à URL para evitar o recebimento de um erro HTTP 4xx. Por exemplo, o idioma definido para inglês dos EUA usando o ponto de extremidade West US é: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

### <a name="text-to-speech"></a>Conversão de texto em fala

Para obter a documentação de referência de conversão de texto em fala, consulte [API REST de conversão de texto em fala](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
