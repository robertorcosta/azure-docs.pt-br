---
title: Migrar do bing speech para o serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como migrar de uma assinatura existente do Bing Speech para o serviço Speech da Azure Cognitive Services.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: nitinme
ms.openlocfilehash: 7b78bdb070cdf1364fe7fbdc75f175be7ce145ff
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656462"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migrar do Bing Speech para o serviço de fala

Use este artigo para migrar seus aplicativos da API bing speech para o serviço Speech.

Este artigo descreve as diferenças entre as APIs do Bing Speech e o serviço speech, e sugere estratégias para migrar suas aplicações. Sua chave de assinatura da API bing speech não funcionará com o serviço Speech; você precisará de uma nova assinatura do serviço Speech.

Uma única chave de assinatura do serviço Speech concede acesso aos seguintes recursos. Cada um deles é medido separadamente e, portanto, você será cobrado somente pelos recursos que usar.

* [Fala-a-texto](speech-to-text.md)
* [Conversão de fala em texto personalizada](https://cris.ai)
* [Texto-para-fala](text-to-speech.md)
* [Conversão de texto em vozes de fala personalizada](how-to-customize-voice-font.md)
* [Tradução de fala](speech-translation.md) (não inclui [Tradução de texto](../translator/translator-info-overview.md))

O [SDK de Fala](speech-sdk.md) é um substituto funcional para as bibliotecas de cliente de Fala do Bing, mas usa uma API diferente.

## <a name="comparison-of-features"></a>Comparação de recursos

O serviço de fala é em grande parte semelhante ao Bing Speech, com as seguintes diferenças.

| Recurso | Fala do Bing | Serviço de Fala | Detalhes |
|--|--|--|--|
| SDK do C# | :heavy_check_mark: | :heavy_check_mark: | O serviço de fala suporta o Windows 10, a Universal Windows Platform (UWP) e o .NET Standard 2.0. |
| SDK do C++ | :heavy_minus_sign: | :heavy_check_mark: | O serviço de fala suporta Windows e Linux. |
| Java SDK | :heavy_check_mark: | :heavy_check_mark: | O serviço de fala suporta dispositivos Android e speech. |
| Reconhecimento de fala contínua | 10 minutos | Ilimitada (com o SDK) | Os protocolos websockets do serviço bing speech e speech suportam até 10 minutos por chamada. No entanto, o SDK de Fala se reconecta automaticamente no tempo limite ou se desconecta. |
| Resultados intermediários ou parciais | :heavy_check_mark: | :heavy_check_mark: | Com o protocolo WebSockets ou SDK. |
| Modelos de fala personalizados | :heavy_check_mark: | :heavy_check_mark: | A Fala do Bing requer uma assinatura separada da Fala Personalizada. |
| Fontes de voz personalizadas | :heavy_check_mark: | :heavy_check_mark: | A Fala do Bing requer uma assinatura separada da Voz Personalizada. |
| Vozes de 24 kHz | :heavy_minus_sign: | :heavy_check_mark: |
| Reconhecimento de intenção de fala | Requer uma chamada separada à API LUIS | Integrada (com o SDK) | Você pode usar uma chave LUIS com o serviço Speech. |
| Reconhecimento simples de intenção | :heavy_minus_sign: | :heavy_check_mark: |
| Transcrição em lote de arquivos de áudio longos | :heavy_minus_sign: | :heavy_check_mark: |
| Modo de reconhecimento | Manual por meio do URI do ponto de extremidade | Automático | O modo de reconhecimento não está disponível no serviço Speech. |
| Localidade do ponto de extremidade | Global | Regional | Os pontos de extremidade regionais melhoram a latência. |
| APIs REST | :heavy_check_mark: | :heavy_check_mark: | As APIs de serviço de fala rest são compatíveis com bing speech (ponto final diferente). As APIs REST são compatíveis com as funcionalidades de conversão de texto em fala e uma funcionalidade limitada de conversão de fala em texto. |
| Protocolos WebSockets | :heavy_check_mark: | :heavy_check_mark: | A API do serviço de fala WebSockets é compatível com o Bing Speech (ponto final diferente). Se possível, migre para o SDK de Fala para simplificar seu código. |
| Chamadas à API de serviço a serviço | :heavy_check_mark: | :heavy_minus_sign: | Fornecidas na Fala do Bing por meio da Biblioteca de Serviço do C#. |
| SDK do código-fonte aberto | :heavy_check_mark: | :heavy_minus_sign: |

O serviço Speech usa um modelo de preços baseado em tempo (em vez de um modelo baseado em transações). Consulte [os preços dos serviços de fala](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para obter detalhes.

## <a name="migration-strategies"></a>Estratégias de migração

Se você ou sua organização tiver aplicativos em desenvolvimento ou produção que usem uma API de Bing Speech, você deve atualizá-los para usar o serviço Speech o mais rápido possível. Consulte a [documentação](index.yml) do serviço Speech para sdks disponíveis, amostras de código e tutoriais.

As [APIs de](rest-apis.md) serviço de fala REST são compatíveis com as APIs de discurso de bing. Se você estiver usando as APIs Bing Speech REST no momento, você só precisa alterar o ponto final REST e mudar para uma tecla de assinatura do serviço Speech.

Os protocolos WebSockets do serviço de fala também são compatíveis com os usados pelo Bing Speech. É recomendável que você use o SDK do Speech em vez de WebSockets para o novo desenvolvimento. É uma boa ideia migrar o código existente para o SDK também. No entanto, da mesma forma que com as APIs REST, o código existente que usa a Fala do Bing por meio de WebSockets requer apenas uma alteração no ponto de extremidade e uma chave atualizada.

Se você estiver usando uma biblioteca de clientes de Fala do Bing para uma linguagem de programação específica, migrar para o [SDK de Fala](speech-sdk.md) exigirá alterações no seu aplicativo porque a API é diferente. O SDK de Fala pode tornar seu código mais simples e dar acesso a novos recursos. O Speech SDK está disponível em uma ampla variedade de linguagens de programação. As APIs em todas as plataformas são semelhantes, facilitando o desenvolvimento multiplataforma.

O serviço de discurso não oferece um ponto final global. Determine se o aplicativo funcionará de maneira eficiente usando um ponto de extremidade regional único para todo o tráfego. Caso contrário, use a geolocalização para determinar o ponto de extremidade mais eficiente. Você precisa de uma assinatura de serviço de fala separada em cada região que você usa.

Se o aplicativo usar conexões de longa duração e não for possível usar os SDKs disponíveis, você poderá usar uma conexão WebSockets. Gerencie o limite de tempo limite de 10 minutos ao fazer a reconexão nos momentos apropriados.

Como começar a usar o SDK de Fala:

1. Faça o download do [SDK de Fala](speech-sdk.md).
1. Trabalhe através do serviço de fala [guias](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet) e [tutoriais de início rápido](how-to-recognize-intents-from-speech-csharp.md). Examine também os [exemplos de código](samples.md) para ter experiência com as novas APIs.
1. Atualize seu aplicativo para usar o serviço Speech.

## <a name="support"></a>Suporte

Os clientes de Fala do Bing devem entrar em contato com o atendimento ao cliente abrindo um [tíquete de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Você também pode entrar em contato conosco caso sua necessidade de suporte precise de um [plano de suporte técnico](https://azure.microsoft.com/support/plans/).

Para o serviço de fala, suporte a SDK e API, visite a [página de suporte ao](support.md)serviço speech .

## <a name="next-steps"></a>Próximas etapas

* [Experimente o serviço de fala gratuitamente](get-started.md)
* [Início rápido: Reconhecer fala em um aplicativo UWP usando SDK de Fala](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Confira também
* [Notas de liberação do serviço de fala](releasenotes.md)
* [O que é o serviço de fala](overview.md)
* [Serviço de fala e documentação do Speech SDK](speech-sdk.md#get-the-speech-sdk)
