---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 029cb1e59876eb01c609ef8b7ba0412ed35a2a1b
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940729"
---
Neste início rápido, você aprenderá converter texto em fala usando o serviço de Fala e o cURL.

Para obter uma visão de alto nível dos conceitos de Conversão de Texto em Fala, confira o artigo de [visão geral](../../../text-to-speech.md).

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tem uma conta do Azure e uma assinatura do Serviço de Fala. Se você não tiver uma conta e uma assinatura, [experimente o serviço de Fala gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-text-to-speech"></a>Converter texto em fala

No prompt de comando, execute o comando a seguir. Será necessário inserir os valores a seguir no comando.
- Sua chave de assinatura do serviço de Fala.
- Sua região do serviço de Fala.

Talvez também seja interessante alterar os valores a seguir.
- O valor do cabeçalho `X-Microsoft-OutputFormat`, que controla o formato de saída de áudio. Você pode encontrar uma lista de formatos de saída de áudio com suporte na [referência da API REST de conversão de texto em fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech#audio-outputs).
- A voz de saída. Para obter uma lista de vozes disponíveis para seu ponto de extremidade de Fala, confira a próxima seção.
- O arquivo de saída. Neste exemplo, direcionamos a resposta do servidor para um arquivo chamado `output.wav`.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/text-to-speech.sh":::

## <a name="list-available-voices-for-your-speech-endpoint"></a>Listar as vozes disponíveis para seu ponto de extremidade de Fala

Para listar as vozes disponíveis para seu ponto de extremidade de Fala, execute o comando a seguir.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="request":::

Você deverá receber uma resposta semelhante à mostrada a seguir.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="response":::
