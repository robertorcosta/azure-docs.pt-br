---
title: Visão geral da tradução de fala – serviço de fala
titleSuffix: Azure Cognitive Services
description: A tradução de fala permite que você adicione tradução de fala de ponta a ponta, em tempo real e em vários idiomas, a seus aplicativos, ferramentas e dispositivos. A mesma API pode ser usada para a tradução com conversão de fala em fala e de fala em texto. Este artigo é uma visão geral dos benefícios e funcionalidades do serviço de tradução de fala.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: erhopf
ms.custom: devx-track-csharp, cog-serv-seo-aug-2020
keywords: Tradução de fala
ms.openlocfilehash: 94ddd06068513261b5b73b313877e273c7251d62
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954952"
---
# <a name="what-is-speech-translation"></a>O que é tradução de fala?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Nesta visão geral, você aprende sobre os benefícios e recursos do serviço de tradução de fala, que permite a tradução de fala em tempo real, de [voz a voz](language-support.md#speech-translation) e de fala a texto de fluxos de áudio. Com o SDK de Fala, os seus aplicativos, ferramentas e dispositivos têm acesso a transcrições de origem e a saídas de tradução para o áudio fornecido. Os resultados provisórios de transcrição e tradução são retornados conforme a fala é detectada e os resultados finais podem ser convertidos em fala sintetizada.

## <a name="core-features"></a>Principais recursos

* Tradução de conversão de fala em texto com resultados de reconhecimento.
* Conversão de fala em fala.
* Suporte para tradução para vários idiomas de destino.
* Resultados de tradução e de reconhecimento provisórios.

## <a name="get-started"></a>Introdução 

Consulte o guia de [início rápido](get-started-speech-translation.md) para começar a usar a tradução de fala. O serviço de tradução de fala está disponível por meio do [SDK de fala](speech-sdk.md) e da [CLI de fala](spx-overview.md).

## <a name="sample-code"></a>Código de exemplo

O código de exemplo para o SDK de fala está disponível no GitHub. Esses exemplos abrangem cenários comuns, como a leitura de áudio de um arquivo ou fluxo, um reconhecimento/conversão de captura única e contínua e o trabalho com modelos personalizados.

* [Exemplos de conversão de fala em texto e tradução (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Guias de migração

Se seus aplicativos, ferramentas ou produtos estiverem usando o [API de tradução de fala](./how-to-migrate-from-translator-speech-api.md), criamos guias para ajudá-lo a migrar para o serviço de fala.

* [Migrar do API de Tradução de Fala para o serviço de fala](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Documentos de Referência

* [SDK da fala](./speech-sdk.md)
* [SDK de Dispositivos de Fala](speech-devices-sdk.md)
* [API REST: conversão de fala em texto](rest-speech-to-text.md)
* [API REST: conversão de texto em fala](rest-text-to-speech.md)
* [API REST: transcrição e personalização em lote](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)

## <a name="next-steps"></a>Próximas etapas

* Concluir o [início rápido](get-started-speech-translation.md) da tradução de fala
* [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](overview.md#try-the-speech-service-for-free)
* [Obter o SDK de Fala](speech-sdk.md)