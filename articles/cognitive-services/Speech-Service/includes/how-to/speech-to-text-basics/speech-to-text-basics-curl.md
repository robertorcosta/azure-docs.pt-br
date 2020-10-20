---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 36d12b29054f736b65af5ac411adbc26d870b982
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014131"
---
Neste início rápido, você aprenderá a usar a conversão de fala em texto com o serviço de Fala e o cURL.

Para obter uma visão de alto nível dos conceitos de Conversão de Fala em Texto, confira o artigo de [visão geral](../../../speech-to-text.md).

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tem uma conta do Azure e uma assinatura do Serviço de Fala. Se você não tiver uma conta e uma assinatura, [experimente o serviço de Fala gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-speech-to-text"></a>Conversão de fala em texto

No prompt de comando, execute o comando a seguir. Será necessário inserir os valores a seguir no comando.
- Sua chave de assinatura do serviço de Fala.
- Sua região do serviço de Fala.
- O caminho do arquivo de áudio de entrada. É possível gerar arquivos de áudio usando a [conversão de texto em fala](../../../get-started-text-to-speech.md).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="request":::

Você deverá receber uma resposta semelhante à mostrada a seguir.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="response":::

Para obter mais informações, confira a [Referência da API REST de conversão de fala em texto](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text).
