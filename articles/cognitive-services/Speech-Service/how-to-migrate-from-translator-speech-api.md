---
title: Migre da API de fala tradutora para o serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como migrar seus aplicativos da API de fala do tradutor para o serviço Speech.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: aahi
ms.openlocfilehash: 75a456c4a297b0465c34b8e0af2e87056ad565b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77560891"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Migre da API de fala tradutora para o serviço de fala

Use este artigo para migrar seus aplicativos da API de fala do Microsoft Translator para o [serviço Speech](index.yml). Este guia descreve as diferenças entre a API de Fala tradutora e o serviço de fala, e sugere estratégias para migrar suas aplicações.

> [!NOTE]
> A tecla de assinatura da API do Tradutor Speech não será aceita pelo serviço Speech. Você precisará criar uma nova assinatura do serviço Speech.

## <a name="comparison-of-features"></a>Comparação de recursos

| Recurso                                           | API de Tradução de Fala                                  | Serviço de Fala | Detalhes                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tradução para texto                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Tradução para fala                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Ponto de extremidade global                                   | :heavy_check_mark:                                              | :heavy_minus_sign:                 | O serviço de discurso não oferece um ponto final global. Um ponto de extremidade global pode direcionar automaticamente o tráfego para o ponto de extremidade regional mais próximo, diminuindo a latência no aplicativo.                                                    |
| Pontos de extremidade regionais                                | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Limite do tempo de conexão                             | 90 minutos                                               | Ilimitado com o SDK. 10 minutos com uma conexão WebSockets.                                                                                                                                                                                                                                                                                   |
| Chave de autenticação no cabeçalho                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Traduzido para vários idiomas em uma única solicitação | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| SDKs disponíveis                                    | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Consulte a [documentação](index.yml) do serviço Speech para sdks disponíveis.                                                                                                                                                    |
| Conexões WebSockets                            | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| API de Idiomas                                     | :heavy_check_mark:                                              | :heavy_minus_sign:                 | O serviço Speech suporta a mesma gama de idiomas descritos no artigo [de referência de línguas API](../translator-speech/languages-reference.md) do Tradutor. |
| Marcador e filtro de conteúdo ofensivo                       | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| .WAV/PCM como entrada                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Outros tipos de arquivo como entrada                         | :heavy_minus_sign:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Resultados parciais                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Informações de tempo                                       | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                 |
| ID de Correlação                                    | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Modelos de fala personalizados                              | :heavy_minus_sign:                                              | :heavy_check_mark:                 | O serviço Speech oferece modelos de fala personalizados que permitem personalizar o reconhecimento de voz ao vocabulário exclusivo da sua organização.                                                                                                                                           |
| Modelos de tradução personalizados                         | :heavy_minus_sign:                                              | :heavy_check_mark:                 | A assinatura da API de Tradução de Texto da Microsoft permite que você use o [Tradutor Personalizado](https://www.microsoft.com/translator/business/customization/) para usar seus próprios dados para traduções mais precisas.                                                 |

## <a name="migration-strategies"></a>Estratégias de migração

Se você ou sua organização tiver aplicativos em desenvolvimento ou produção que usem a API de fala do tradutor, você deve atualizá-los para usar o serviço Speech. Consulte a documentação do [serviço Speech](index.yml) para sdks disponíveis, amostras de código e tutoriais. Quando você estiver migrando, considere o seguinte:

* O serviço de discurso não oferece um ponto final global. Determine se o aplicativo funcionará de maneira eficiente usando um ponto de extremidade regional único para todo o tráfego. Caso contrário, use a geolocalização para determinar o ponto de extremidade mais eficiente.

* Se seu aplicativo usar conexões de longa duração e não for possível usar os SDKs disponíveis, você poderá usar uma conexão WebSockets. Gerencie o limite de tempo limite de 10 minutos ao fazer a reconexão nos momentos apropriados.

* Se o aplicativo usar a API de texto tradutor e a API de fala do tradutor para habilitar modelos de tradução personalizados, você pode adicionar IDs de categoria diretamente usando o serviço Speech.

* Ao contrário da API de fala do tradutor, o serviço Speech pode concluir traduções em vários idiomas em uma única solicitação.

## <a name="next-steps"></a>Próximas etapas

* [Experimente o serviço de fala gratuitamente](get-started.md)
* [Início rápido: Reconhecer fala em um aplicativo UWP usando SDK de Fala](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Confira também

* [O que é o Serviço de Fala](overview.md)
* [Serviço de fala e documentação do Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
