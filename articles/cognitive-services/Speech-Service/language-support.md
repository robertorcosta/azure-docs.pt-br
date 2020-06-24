---
title: Idiomas compatíveis – Serviço de Fala
titleSuffix: Azure Cognitive Services
description: O Serviço de Fala é compatível com vários idiomas para conversão de fala em texto e vice-versa, bem como com a tradução de fala. Este artigo fornece uma lista abrangente de idiomas compatíveis por recurso de serviço.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: trbye
ms.custom: seodec18
ms.openlocfilehash: 89b2b7b993ef9613740dd777546cc15a12203e62
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85210721"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Idiomas compatíveis e suporte de voz para o serviço de Fala

Os idiomas compatíveis variam de acordo com a funcionalidade do serviço de Fala. As tabelas a seguir resumem os idiomas compatíveis para as ofertas de serviço de [Conversão de fala em texto](#speech-to-text), [Conversão de texto em fala](#text-to-speech) e [Tradução de fala](#speech-translation).

## <a name="speech-to-text"></a>Conversão de fala em texto

O SDK do Microsoft Speech e a API REST são compatíveis com os idiomas (localidades) a seguir. 

Para melhorar a precisão, a personalização é oferecida para um subconjunto de idiomas por meio do upload de **Áudio + Transcrições rotuladas por humanos** ou **Texto relacionado: sentenças**. Para saber mais sobre a personalização, confira [Introdução à Fala Personalizada](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Local  | Linguagem                          | Com suporte | Personalizações                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | Árabe (EAU)                      | Sim       | Não                                                |
| `ar-BH` | Árabe (Bahrein), padrão moderno | Sim       | Modelo de linguagem                                    |
| `ar-EG` | Árabe (Egito)                    | Sim       | Modelo de linguagem                                    |
| `ar-IL` | Árabe (Israel)                   | Sim       | Não                                                |
| `ar-JO` | Árabe (Jordânia)                   | Sim       | Não                                                |
| `ar-KW` | Árabe (Kuwait)                   | Sim       | Não                                                |
| `ar-LB` | Árabe (Líbano)                  | Sim       | Não                                                |
| `ar-PS` | Árabe (Palestina)                | Sim       | Não                                                |
| `ar-QA` | Árabe (Catar)                    | Sim       | Não                                                |
| `ar-SA` | Árabe (Arábia Saudita)             | Sim       | Não                                                |
| `ar-SY` | Árabe (Síria)                    | Sim       | Modelo de linguagem                                    |
| `ca-ES` | Catalão                           | Sim       | Modelo de linguagem                                    |
| `da-DK` | Dinamarquês (Dinamarca)                  | Sim       | Modelo de linguagem                                    |
| `de-DE` | Alemão (Alemanha)                  | Sim       | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `en-AU` | Inglês (Austrália)               | Sim       | Modelo acústico<br>Modelo de linguagem                  |
| `en-CA` | Inglês (Canadá)                  | Sim       | Modelo acústico<br>Modelo de linguagem                  |
| `en-GB` | Inglês (Reino Unido)          | Sim       | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `en-IN` | Inglês (Índia)                   | Sim       | Modelo acústico<br>Modelo de linguagem                  |
| `en-NZ` | Inglês (Nova Zelândia)             | Sim       | Modelo acústico<br>Modelo de linguagem                  |
| `en-US` | Inglês (Estados Unidos)           | Sim       | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `es-ES` | Espanhol (Espanha)                   | Sim       | Modelo acústico<br>Modelo de linguagem                  |
| `es-MX` | Espanhol (México)                  | Sim       | Modelo acústico<br>Modelo de linguagem                  |
| `fi-FI` | Finlandês (Finlândia)                 | Sim       | Modelo de linguagem                                    |
| `fr-CA` | Francês (Canadá)                   | Sim       | Modelo acústico<br>Modelo de linguagem                  |
| `fr-FR` | Francês (França)                   | Sim       | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `gu-IN` | Guzerate (Índia)                 | Sim       | Modelo de linguagem                                    |
| `hi-IN` | Híndi (Índia)                     | Sim       | Modelo acústico<br>Modelo de linguagem                  |
| `it-IT` | Italiano (Itália)                   | Sim       | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `ja-JP` | Japonês (Japão)                  | Sim       | Modelo de linguagem                                    |
| `ko-KR` | Coreano (Coreia do Sul)                    | Sim       | Modelo de linguagem                                    |
| `mr-IN` | Marati (Índia)                   | Sim       | Modelo de linguagem                                    |
| `nb-NO` | Norueguês (Bokmål) (Noruega)       | Sim       | Modelo de linguagem                                    |
| `nl-NL` | Holandês (Países Baixos)               | Sim       | Modelo de linguagem                                    |
| `pl-PL` | Polonês (Polônia)                   | Sim       | Modelo de linguagem                                    |
| `pt-BR` | Português (Brasil)               | Sim       | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `pt-PT` | Português (Portugal)             | Sim       | Modelo de linguagem                                    |
| `ru-RU` | Russo (Rússia)                  | Sim       | Modelo acústico<br>Modelo de linguagem                  |
| `sv-SE` | Sueco (Suécia)                  | Sim       | Modelo de linguagem                                    |
| `ta-IN` | Tâmil (Índia)                     | Sim       | Modelo de linguagem                                    |
| `te-IN` | Télugo (Índia)                    | Sim       | Modelo de linguagem                                    |
| `th-TH` | Tailandês (Tailândia)                   | Sim       | Não                                                |
| `tr-TR` | Turco (Turquia)                  | Sim       | Modelo de linguagem                                    |
| `zh-CN` | Chinês (mandarim, simplificado)    | Sim       | Modelo acústico<br>Modelo de linguagem                  |
| `zh-HK` | Chinês (Cantonês tradicional)  | Sim       | Modelo de linguagem                                    |
| `zh-TW` | Chinês (Mandarim Taiwanês)      | Sim       | Modelo de linguagem                                    |

## <a name="text-to-speech"></a>Conversão de texto em fala

O SDK do Microsoft Speech e as APIs REST são compatíveis com essas vozes, cada uma delas compatível com um idioma e um dialeto específicos, identificados pela localidade. Você também pode obter uma lista completa de idiomas e vozes compatíveis para cada região/ponto de extremidade específico por meio da [API vozes/lista](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> Os preços variam para vozes padrão, personalizadas e neurais. Visite a página [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para obter mais informações.

### <a name="neural-voices"></a>Vozes neurais

A conversão de texto em fala neural é um novo tipo de sintetização de fala alimentado por redes neurais profundas. Ao usar uma voz neural, a fala sintetizada é quase indistinguível de gravações de humana.

Vozes neurais podem ser usadas para interagir com chatbots e assistentes de voz de maneira mais natural e participativa para converter textos digitais, como livros eletrônicos, em audiolivros e aprimorar sistemas de navegação veiculares. Com a prosódia natural semelhante à humana e a articulação clara das palavras, as vozes neurais reduzem significativamente a fadiga de escuta quando os usuários interagem com sistemas de inteligência artificial.

Para obter mais informações sobre a disponibilidade regional, consulte [regiões](regions.md#standard-and-neural-voices).

|Local  | Linguagem            | Gênero | Nome da voz | Suporte de estilo |
|--|--|--|--|--|
| `de-DE` | Alemão (Alemanha)                | Feminino | "de-DE-KatjaNeural"      | Geral |
| `en-AU` | Inglês (Austrália)             | Feminino | "en-AU-NatashaNeural"    | Geral |
| `en-CA` | Inglês (Canadá)                | Feminino | "en-CA-ClaraNeural"      | Geral |
| `en-GB` | Inglês (Reino Unido)                    | Feminino | "en-GB-LibbyNeural"      | Geral |
|         |                                 | Feminino | "en-GB-MiaNeural"        | Geral |
| `en-US` | Inglês (EUA)                    | Feminino | "en-US-AriaNeural"       | Geral, com vários estilos de voz disponíveis |
|         |                                 | Masculino   | "en-US-GuyNeural"        | Geral |
| `es-ES` | Espanhol (Espanha)                 | Feminino | "es-ES-ElviraNeural"     | Geral |
| `es-MX` | Espanhol (México)                | Feminino | "es-MX-DaliaNeural"      | Geral |
| `fr-CA` | Francês (Canadá)                 | Feminino | "fr-CA-SylvieNeural"     | Geral |
| `fr-FR` | Francês (França)                 | Feminino | "fr-FR-DeniseNeural"     | Geral |
| `it-IT` | Italiano (Itália)                 | Feminino | "it-IT-ElsaNeural"       | Geral |
| `ja-JP` | Japonês                        | Feminino | "ja-JP-NanamiNeural"     | Geral |
| `ko-KR` | Coreano                          | Feminino | "ko-KR-SunHiNeural"      | Geral |
| `nb-NO` | Norueguês                       | Feminino | "nb-NO-IselinNeural"     | Geral |
| `pt-BR` | Português (Brasil)             | Feminino | "pt-BR-FranciscaNeural"  | Geral |
| `tr-TR` | Turco                         | Feminino | "tr-TR-EmelNeural"       | Geral |
| `zh-CN` | Chinês (mandarim, simplificado)  | Feminino | "zh-CN-XiaoxiaoNeural"   | Geral, com vários estilos de voz disponíveis |
|         |                                 | Feminino | "zh-CN-XiaoyouNeural"    | Voz de criança, otimização para narração de histórias |
|         |                                 | Masculino   | "zh-CN-YunyangNeural"    | Otimização para leitura de notícias, com vários estilos de voz disponíveis |
|         |                                 | Masculino   | "zh-CN-YunyeNeural"      | Otimização para narração de histórias |

> [!IMPORTANT]
> A voz `en-US-JessaNeural` foi alterada para `en-US-AriaNeural`. Se você usava a voz "Jessa", faça a conversão para "Aria".

Para saber como você pode configurar e ajustar as vozes neurais, confira [Linguagem de marcação para síntese de fala](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> Você poderá continuar a usar o mapeamento de nome de serviço completo como "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)" em suas solicitações de síntese de fala.

### <a name="standard-voices"></a>Vozes padrão

Mais de 75 vozes padrão estão disponíveis em mais de 45 idiomas e localidades, que permitem converter texto em fala sintetizada. Para obter mais informações sobre a disponibilidade regional, consulte [regiões](regions.md#standard-and-neural-voices).

| Local | Linguagem | Gênero | Nome da voz |
|--|--|--|--|
| <sup>1</sup>`ar-EG` | Árabe (Egito) | Feminino | "ar-EG-Hoda" |
| `ar-SA` | Árabe (Arábia Saudita) | Masculino | "ar-SA-Naayf" |
| `bg-BG` | Búlgaro | Masculino |  "bg-BG-Ivan" |
| `ca-ES` | Catalão | Feminino |  "ca-ES-HerenaRUS" |
| `cs-CZ` | Tcheco | Masculino | "cs-CZ-Jakub" |
| `da-DK` | Dinamarquês | Feminino |  "da-DK-HelleRUS" |
| `de-AT` | Alemão (Áustria) | Masculino | "de-AT-Michael" |
| `de-CH` | Alemão (Suíça) | Masculino |  "de-CH-Karsten" |
| `de-DE` | Alemão (Alemanha) | Feminino |  "de-DE-Hedda" |
|  |  | Feminino | "de-DE-HeddaRUS" |
|  |  | Masculino |  "de-DE-Stefan-Apollo" |
| `el-GR` | Grego | Masculino | "el-GR-Stefanos" |
| `en-AU` | Inglês (Austrália) | Feminino |  "en-AU-Catherine" |
|  |  | Feminino |  "en-AU-HayleyRUS" |
| `en-CA` | Inglês (Canadá) | Feminino |  "en-CA-Linda" |
|  |  | Feminino |  "en-CA-HeatherRUS" |
| `en-GB` | Inglês (Reino Unido) | Feminino |  "en-GB-Susan-Apollo" |
|  |  | Feminino |  "en-GB-HazelRUS" |
|  |  | Masculino |  "en-GB-George-Apollo" |
| `en-IE` | Inglês (Irlanda) | Masculino | "en-IE-Sean" |
| `en-IN` | Inglês (Índia) | Feminino | "en-IN-Heera-Apollo" |
|  |  | Feminino |  "en-IN-PriyaRUS" |
|  |  | Masculino |  "en-IN-Ravi-Apollo" |
| `en-US` | Inglês (EUA) | Feminino |  "en-US-ZiraRUS" |
|  |  | Feminino | "en-US-AriaRUS" |
|  |  | Masculino | "en-US-BenjaminRUS" |
|  |  | Masculino |  "en-US-Guy24kRUS" |
| `es-ES` | Espanhol (Espanha) | Feminino |  "es-ES-Laura-Apollo" |
|  |  | Feminino | "es-ES-HelenaRUS" |
|  |  | Masculino | "es-ES-Pablo-Apollo" |
| `es-MX` | Espanhol (México) | Feminino |  "es-MX-HildaRUS" |
|  |  | Masculino | "es-MX-Raul-Apollo" |
| `fi-FI` | Finlandês | Feminino | "fi-FI-HeidiRUS" |
| `fr-CA` | Francês (Canadá) | Feminino | "fr-CA-Caroline" |
|  |  | Feminino | "fr-CA-HarmonieRUS" |
| `fr-CH` | Francês (Suíça) | Masculino | "fr-CH-Guillaume" |
| `fr-FR` | Francês (França) | Feminino |  "fr-FR-Julie-Apollo" |
|  |  | Feminino |"fr-FR-HortenseRUS" |
|  |  | Masculino |  "fr-FR-Paul-Apollo" |
| `he-IL` | Hebraico (Israel) | Masculino |  "he-IL-Asaf" |
| `hi-IN` | Híndi (Índia) | Feminino | "hi-IN-Kalpana-Apollo" |
|  |  | Feminino |  "hi-IN-Kalpana" |
|  |  | Masculino |  "hi-IN-Hemant" |
| `hr-HR` | Croata | Masculino | "hr-HR-Matej" |
| `hu-HU` | Húngaro | Masculino |  "hu-HU-Szabolcs" |
| `id-ID` | Indonésio | Masculino | "id-ID-Andika" |
| `it-IT` | Italiano | Masculino |  "it-IT-Cosimo-Apollo" |
|  |  | Feminino |  "it-IT-LuciaRUS" |
| `ja-JP` | Japonês | Feminino |  "ja-JP-Ayumi-Apollo" |
|  |  | Masculino | "ja-JP-Ichiro-Apollo" |
|  |  | Feminino |  "ja-JP-HarukaRUS" |
| `ko-KR` | Coreano | Feminino | "ko-KR-HeamiRUS" |
| `ms-MY` | Malaio | Masculino |  "ms-MY-Rizwan" |
| `nb-NO` | Norueguês | Feminino |  "nb-NO-HuldaRUS" |
| `nl-NL` | Holandês | Feminino |  "nl-NL-HannaRUS" |
| `pl-PL` | Polonês | Feminino |  "pl-PL-PaulinaRUS" |
| `pt-BR` | Português (Brasil) | Feminino | "pt-BR-HeloisaRUS" |
|  |  | Masculino |  "pt-BR-Daniel-Apollo" |
| `pt-PT` | Português (Portugal) | Feminino | "pt-PT-HeliaRUS" |
| `ro-RO` | Romeno | Masculino | "ro-RO-Andrei" |
| `ru-RU` | Russo | Feminino |  "ru-RU-Irina-Apollo" |
|  |  | Masculino | "ru-RU-Pavel-Apollo" |
|  |  | Feminino |  ru-RU-EkaterinaRUS |
| `sk-SK` | Eslovaco | Masculino | "sk-SK-Filip" |
| `sl-SI` | Esloveno | Masculino |  "sl-SI-Lado" |
| `sv-SE` | Sueco | Feminino | "sv-SE-HedvigRUS" |
| `ta-IN` | Tâmil (Índia) | Masculino |  "ta-IN-Valluvar" |
| `te-IN` | Télugo (Índia) | Feminino |  "te-IN-Chitra" |
| `th-TH` | Tailandês | Masculino |  "th-TH-Pattara" |
| `tr-TR` | Turco (Turquia) | Feminino | "tr-TR-SedaRUS" |
| `vi-VN` | Vietnamita | Masculino |  "vi-VN-An" |
| `zh-CN` | Chinês (mandarim, simplificado) | Feminino |  "zh-CN-HuihuiRUS" |
|  |  | Feminino | "zh-CN-Yaoyao-Apollo" |
|  |  | Masculino | "zh-CN-Kangkang-Apollo" |
| `zh-HK` | Chinês (Cantonês tradicional) | Feminino |  "zh-HK-Tracy-Apollo" |
|  |  | Feminino | "zh-HK-TracyRUS" |
|  |  | Masculino |  "zh-HK-Danny-Apollo" |
| `zh-TW` | Chinês (Mandarim Taiwanês) | Feminino |  "zh-TW-Yating-Apollo" |
|  |  | Feminino | "zh-TW-HanHanRUS" |
|  |  | Masculino |  "zh-TW-Zhiwei-Apollo" |

**1** *ar-EG é compatível com MSA (árabe padrão moderno).*

> [!IMPORTANT]
> A voz `en-US-Jessa` foi alterada para `en-US-Aria`. Se você usava a voz "Jessa", faça a conversão para "Aria".

> [!TIP]
> Você poderá continuar a usar o mapeamento de nome de serviço completo como "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)" em suas solicitações de síntese de fala.

### <a name="customization"></a>Personalização

A personalização de voz está disponível para `de-DE`, `en-GB`, `en-IN`, `en-US`, `es-MX`, `fr-FR`, `it-IT`, `pt-BR` e `zh-CN`. Selecione a localidade correta que corresponde aos dados de treinamento que você tem para treinar um modelo de voz personalizada. Por exemplo, se os dados de gravação que você tem são falados em inglês com sotaque britânico, selecione `en-GB`.

> [!NOTE]
> Não damos suporte ao treinamento de modelo bilíngue em Voz Personalizada, exceto para o modelo bilíngue chinês-inglês. Selecione "Modelo bilíngue chinês/inglês" se você deseja treinar uma voz chinesa que também pode falar em inglês. O treinamento de voz em todas as localidades começa com um conjunto de dados de pelo menos 2.000 enunciados – exceto para o `en-US` e o `zh-CN`, nos quais você pode começar com qualquer volume de dados de treinamento.

## <a name="speech-translation"></a>Tradução de fala

A API de **Tradução de Fala** é compatível com diferentes idiomas para conversão de fala em fala e de fala em texto. O idioma de origem deve ser sempre da tabela de idiomas de conversão de fala em texto. Os idiomas de destino disponíveis dependem de se o destino de tradução é fala ou texto. Você pode traduzir a entrada de fala em mais de [60 idiomas](https://www.microsoft.com/translator/business/languages/). Um subconjunto de idiomas está disponível para [síntese de fala](language-support.md#text-languages).

### <a name="text-languages"></a>Idiomas de texto

| Idioma de texto           | Código de idioma |
|:------------------------|:-------------:|
| Africâner               | `af`          |
| Árabe                  | `ar`          |
| Bangla                  | `bn`          |
| Bósnio (latim)         | `bs`          |
| Búlgaro               | `bg`          |
| Cantonês (tradicional) | `yue`         |
| Catalão                 | `ca`          |
| Chinês simplificado      | `zh-Hans`     |
| Chinês (tradicional)     | `zh-Hant`     |
| Croata                | `hr`          |
| Tcheco                   | `cs`          |
| Dinamarquês                  | `da`          |
| Holandês                   | `nl`          |
| Inglês                 | `en`          |
| Estoniano                | `et`          |
| Fijiano                  | `fj`          |
| Filipino                | `fil`         |
| Finlandês                 | `fi`          |
| Francês                  | `fr`          |
| Alemão                  | `de`          |
| Grego                   | `el`          |
| Guzerate                | `gu`          |
| Crioulo haitiano          | `ht`          |
| Hebraico                  | `he`          |
| Híndi                   | `hi`          |
| Hmong Daw               | `mww`         |
| Húngaro               | `hu`          |
| Indonésio              | `id`          |
| Irlandês                   | `ga`          |
| Italiano                 | `it`          |
| Japonês                | `ja`          |
| canarim                 | `kn`          |
| Suaíli               | `sw`          |
| Klingon                 | `tlh`         |
| Klingon (plqaD)         | `tlh-Qaak`    |
| Coreano                  | `ko`          |
| Letão                 | `lv`          |
| Lituano              | `lt`          |
| Malgaxe                | `mg`          |
| Malaio                   | `ms`          |
| Malaiala               | `ml`          |
| Maltês                 | `mt`          |
| Maori                   | `mi`          |
| Marati                 | `mr`          |
| Norueguês               | `nb`          |
| Persa                 | `fa`          |
| Polonês                  | `pl`          |
| Português (Brasil)     | `pt-br`       |
| Português (Portugal)   | `pt-pt`       |
| Panjabi                 | `pa`          |
| Queretaro Otomi         | `otq`         |
| Romeno                | `ro`          |
| Russo                 | `ru`          |
| Samoano                  | `sm`          |
| Sérvio (cirílico)      | `sr-Cyrl`     |
| Sérvio (latino)         | `sr-Latn`     |
| Eslovaco                  | `sk`          |
| Esloveno               | `sl`          |
| Espanhol                 | `es`          |
| Sueco                 | `sv`          |
| Taitiano                | `ty`          |
| Tâmil                   | `ta`          |
| Télugo                  | `te`          |
| Tailandês                    | `th`          |
| Tongan                  | `to`          |
| Turco                 | `tr`          |
| Ucraniano               | `uk`          |
| Urdu                    | `ur`          |
| Vietnamita              | `vi`          |
| Galês                   | `cy`          |
| Yucatec Maya            | `yua`         |

## <a name="speaker-recognition"></a>Reconhecimento do Locutor

Veja na tabela a seguir os idiomas com suporte para as várias APIs de Reconhecimento do Locutor. Confira a [visão geral](speaker-recognition-overview.md) para obter informações adicionais sobre o Reconhecimento do Locutor.

| Local | Linguagem | Verificação dependente do texto | Verificação independente do texto | Identificação independente do texto |
|----|----|----|----|----|
| pt-BR | Inglês (EUA) | sim | sim | sim |
|zh-CN  |Chinês (mandarim, simplificado)|    n/d|    sim|    sim|
|de-DE  |Alemão (Alemanha)   |n/d    |sim    |sim|
|en-GB  |Inglês (Reino Unido)   |n/d    |sim    |sim|
|fr-FR  |Francês (França)    |n/d    |sim    |sim|
|en-AU  |Inglês (Austrália)    |n/d    |sim    |sim|
|en-CA  |Inglês (Canadá)   |n/d|   sim|    sim|
|fr-CA  |Francês (Canadá)    |n/d    |sim|   sim|
|it-IT  |Italiano|   n/d |sim|   sim|
|es-ES| Espanhol (Espanha) |n/d    |sim|   sim|
|es-MX  |Espanhol (México)   |n/d|   sim|    sim|
|ja-JP| Japonês    |n/d    |sim    |sim|
|pt-BR| Português (Brasil)|    n/d|    sim|    sim|

## <a name="next-steps"></a>Próximas etapas

* [Obter sua assinatura de avaliação do serviço de Fala](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como reconhecer fala em C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
