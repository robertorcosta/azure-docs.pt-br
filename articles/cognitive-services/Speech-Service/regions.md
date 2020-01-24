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
ms.openlocfilehash: 409ce8b904997f2ab75f70b2138ec5b1e70a0e69
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816654"
---
# <a name="speech-service-supported-regions"></a>Regiões com suporte do serviço de fala

O Serviço de Fala permite que seu aplicativo converta áudio em texto, realize a tradução de fala e converta texto em Fala. O serviço está disponível em várias regiões com pontos de extremidade exclusivos para o SDK de Fala e APIs de REST.

Certifique-se de que você use o ponto de extremidade que corresponde à região de sua assinatura.

## <a name="speech-sdk"></a>SDK de fala

No [SDK de Fala](speech-sdk.md), regiões são especificadas como uma cadeia de caracteres (por exemplo, como um parâmetro para `SpeechConfig.FromSubscription` no SDK de Fala para C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Conversão de fala em texto, texto em fala e tradução

O SDK de fala está disponível nessas regiões para **reconhecimento de fala**, conversão de **texto em fala**e **tradução**:

| Região           | Parâmetro do SDK de Fala | Portal de personalização de Fala    |
| ---------------- | -------------------- | ------------------------------ |
| Oeste dos EUA          | `westus`             | https://westus.cris.ai         |
| Oeste dos EUA 2        | `westus2`            | https://westus2.cris.ai        |
| Leste dos EUA          | `eastus`             | https://eastus.cris.ai         |
| Leste dos EUA 2        | `eastus2`            | https://eastus2.cris.ai        |
| EUA Central       | `centralus`          | https://centralus.cris.ai      |
| Centro-Norte dos EUA | `northcentralus`     | https://northcentralus.cris.ai |
| Centro-Sul dos EUA | `southcentralus`     | https://southcentralus.cris.ai |
| Índia Central    | `centralindia`       | https://centralindia.cris.ai   |
| Leste da Ásia        | `eastasia`           | https://eastasia.cris.ai       |
| Sudeste Asiático   | `southeastasia`      | https://southeastasia.cris.ai  |
| Leste do Japão       | `japaneast`          | https://japaneast.cris.ai      |
| Coreia Central    | `koreacentral`       | https://koreacentral.cris.ai   |
| Austrália Oriental   | `australiaeast`      | https://australiaeast.cris.ai  |
| Canadá Central   | `canadacentral`      | https://canadacentral.cris.ai  |
| Europa Setentrional     | `northeurope`        | https://northeurope.cris.ai    |
| Oeste da Europa      | `westeurope`         | https://westeurope.cris.ai     |
| Sul do Reino Unido         | `uksouth`            | https://uksouth.cris.ai        |
| França Central   | `francecentral`      | https://francecentral.cris.ai  |

### <a name="intent-recognition"></a>Reconhecimento de intenção

As regiões disponíveis para **reconhecimento de intenção** por meio do SDK de Fala são os seguintes:

| Região global | Região           | Parâmetro do SDK de Fala |
| ------------- | ---------------- | -------------------- |
| Ásia          | Leste da Ásia        | `eastasia`           |
| Ásia          | Sudeste Asiático   | `southeastasia`      |
| Austrália     | Austrália Oriental   | `australiaeast`      |
| Europa        | Europa Setentrional     | `northeurope`        |
| Europa        | Oeste da Europa      | `westeurope`         |
| América do Norte | Leste dos EUA          | `eastus`             |
| América do Norte | Leste dos EUA 2        | `eastus2`            |
| América do Norte | Centro-Sul dos EUA | `southcentralus`     |
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
| Oeste da Europa    | `westeurope`         |
| Europa Setentrional   | `northeurope`        |
| Sudeste Asiático | `southeastasia`      |

## <a name="rest-apis"></a>APIs REST

O serviço de fala também expõe pontos de extremidade REST para solicitações de Fala em texto e texto em Fala.

### <a name="speech-to-text"></a>Conversão de fala em texto

Para obter a documentação de referência de fala para texto, consulte [API REST de fala em texto](rest-speech-to-text.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Conversão de texto em fala

Para obter a documentação de referência de conversão de texto em fala, consulte [API REST de conversão de texto em fala](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
