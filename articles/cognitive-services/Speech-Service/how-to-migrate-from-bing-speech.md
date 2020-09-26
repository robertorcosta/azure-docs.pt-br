---
title: Migrar do Fala do Bing para o serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como migrar de uma assinatura existente do Fala do Bing para o serviço de fala dos serviços cognitivas do Azure.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: nitinme
ms.openlocfilehash: 43679c52727f8cc84c7292592b68dddae7f1ea68
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362071"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migrar do Fala do Bing para o serviço de fala

Use este artigo para migrar seus aplicativos do API de Fala do Bing para o serviço de fala.

Este artigo descreve as diferenças entre as APIs de Fala do Bing e o serviço de fala e sugere estratégias para migrar seus aplicativos. Sua chave de assinatura do API de Fala do Bing não funcionará com o serviço de fala; Você precisará de uma nova assinatura do serviço de fala.

Uma única chave de assinatura do serviço de fala concede acesso aos recursos a seguir. Cada um deles é medido separadamente e, portanto, você será cobrado somente pelos recursos que usar.

* [Conversão de fala em texto](speech-to-text.md)
* [Conversão de fala em texto personalizada](https://cris.ai)
* [Conversão de texto em fala](text-to-speech.md)
* [Conversão de texto em vozes de fala personalizada](how-to-customize-voice-font.md)
* [Tradução de fala](speech-translation.md) (não inclui [Tradução de texto](../translator/translator-info-overview.md))

O [SDK de Fala](speech-sdk.md) é um substituto funcional para as bibliotecas de cliente de Fala do Bing, mas usa uma API diferente.

## <a name="comparison-of-features"></a>Comparação de recursos

O serviço de fala é muito semelhante ao Fala do Bing, com as seguintes diferenças.

| Recurso | Fala do Bing | Serviço de Fala | Detalhes |
|--|--|--|--|
| SDK do C# | :heavy_check_mark: | :heavy_check_mark: | O serviço de fala dá suporte ao Windows 10, Plataforma Universal do Windows (UWP) e .NET Standard 2,0. |
| SDK do C++ | :heavy_minus_sign: | :heavy_check_mark: | O serviço de fala dá suporte ao Windows e ao Linux. |
| Java SDK | :heavy_check_mark: | :heavy_check_mark: | O serviço de fala dá suporte a dispositivos Android e de fala. |
| Reconhecimento de fala contínua | 10 minutos | Ilimitada (com o SDK) | Os protocolos de WebSockets do serviço de fala Fala do Bing e do Speech dão suporte a até 10 minutos por chamada. No entanto, o SDK de Fala se reconecta automaticamente no tempo limite ou se desconecta. |
| Resultados intermediários ou parciais | :heavy_check_mark: | :heavy_check_mark: | Com o protocolo WebSockets ou SDK. |
| Modelos de fala personalizados | :heavy_check_mark: | :heavy_check_mark: | A Fala do Bing requer uma assinatura separada da Fala Personalizada. |
| Fontes de voz personalizadas | :heavy_check_mark: | :heavy_check_mark: | A Fala do Bing requer uma assinatura separada da Voz Personalizada. |
| vozes de 24 kHz | :heavy_minus_sign: | :heavy_check_mark: |
| Reconhecimento de intenção de fala | Requer uma chamada separada à API LUIS | Integrada (com o SDK) | Você pode usar uma chave LUIS com o serviço de fala. |
| Reconhecimento simples de intenção | :heavy_minus_sign: | :heavy_check_mark: |
| Transcrição em lote de arquivos de áudio longos | :heavy_minus_sign: | :heavy_check_mark: |
| Modo de reconhecimento | Manual por meio do URI do ponto de extremidade | Automática | O modo de reconhecimento não está disponível no serviço de fala. |
| Localidade do ponto de extremidade | Global | Regional | Os pontos de extremidade regionais melhoram a latência. |
| APIs REST | :heavy_check_mark: | :heavy_check_mark: | As APIs REST do serviço de fala são compatíveis com Fala do Bing (ponto de extremidade diferente). As APIs REST são compatíveis com as funcionalidades de conversão de texto em fala e uma funcionalidade limitada de conversão de fala em texto. |
| Protocolos WebSockets | :heavy_check_mark: | :heavy_check_mark: | A API WebSockets do serviço de fala é compatível com Fala do Bing (ponto de extremidade diferente). Se possível, migre para o SDK de Fala para simplificar seu código. |
| Chamadas à API de serviço a serviço | :heavy_check_mark: | :heavy_minus_sign: | Fornecidas na Fala do Bing por meio da Biblioteca de Serviço do C#. |
| SDK do código-fonte aberto | :heavy_check_mark: | :heavy_minus_sign: |

O serviço de fala usa um modelo de preços baseado em tempo (em vez de um modelo baseado em transações). Consulte [preços do serviço de fala](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para obter detalhes.

## <a name="migration-strategies"></a>Estratégias de migração

Se você ou sua organização tiver aplicativos em desenvolvimento ou produção que usam um API de Fala do Bing, você deverá atualizá-los para usar o serviço de fala assim que possível. Consulte a [documentação do serviço de fala](index.yml) para ver SDKs disponíveis, exemplos de código e tutoriais.

As [APIs REST](rest-apis.md) do serviço de fala são compatíveis com as apis de fala do Bing. Se você estiver usando o Fala do Bing APIs REST, precisará apenas alterar o ponto de extremidade REST e alternar para uma chave de assinatura do serviço de fala.

Os protocolos do WebSocket do serviço de fala também são compatíveis com aqueles usados pelo Fala do Bing. É recomendável que você use o SDK do Speech em vez de WebSockets para o novo desenvolvimento. É uma boa ideia migrar o código existente para o SDK também. No entanto, da mesma forma que com as APIs REST, o código existente que usa a Fala do Bing por meio de WebSockets requer apenas uma alteração no ponto de extremidade e uma chave atualizada.

Se você estiver usando uma biblioteca de clientes de Fala do Bing para uma linguagem de programação específica, migrar para o [SDK de Fala](speech-sdk.md) exigirá alterações no seu aplicativo porque a API é diferente. O SDK de Fala pode tornar seu código mais simples e dar acesso a novos recursos. O SDK de fala está disponível em uma ampla variedade de linguagens de programação. As APIs em todas as plataformas são semelhantes, facilitando o desenvolvimento multiplataforma.

O serviço de fala não oferece um ponto de extremidade global. Determine se o aplicativo funcionará de maneira eficiente usando um ponto de extremidade regional único para todo o tráfego. Caso contrário, use a geolocalização para determinar o ponto de extremidade mais eficiente. Você precisa de uma assinatura de serviço de fala separada em cada região usada.

Se o aplicativo usar conexões de longa duração e não for possível usar os SDKs disponíveis, você poderá usar uma conexão WebSockets. Gerencie o limite de tempo limite de 10 minutos ao fazer a reconexão nos momentos apropriados.

Como começar a usar o SDK de Fala:

1. Faça o download do [SDK de Fala](speech-sdk.md).
1. Trabalhe nos [guias de início rápido](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet) e [tutoriais](how-to-recognize-intents-from-speech-csharp.md)do serviço de fala. Examine também os [exemplos de código](samples.md) para ter experiência com as novas APIs.
1. Atualize seu aplicativo para usar o serviço de fala.

## <a name="support"></a>Suporte

Os clientes de Fala do Bing devem entrar em contato com o atendimento ao cliente abrindo um [tíquete de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Você também pode entrar em contato conosco caso sua necessidade de suporte precise de um [plano de suporte técnico](https://azure.microsoft.com/support/plans/).

Para o suporte ao serviço de fala, ao SDK e à API, visite a [página de suporte](support.md)do serviço de fala.

## <a name="next-steps"></a>Próximas etapas

* [Experimente gratuitamente o serviço de fala](overview.md#try-the-speech-service-for-free)
* [Início rápido: Reconhecer fala em um aplicativo UWP usando SDK de Fala](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Confira também
* [Notas de versão do serviço de fala](releasenotes.md)
* [O que é o serviço de fala](overview.md)
* [Documentação do Speech Service e do Speech SDK](speech-sdk.md#get-the-speech-sdk)
