---
title: Suporte a idiomas-serviço de fala
titleSuffix: Azure Cognitive Services
description: O serviço de fala dá suporte a vários idiomas para conversão de fala em texto e de texto em fala, juntamente com a tradução de fala. Este artigo fornece uma lista abrangente de suporte de idioma por recurso de serviço.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: trbye
ms.custom: seodec18
ms.openlocfilehash: 437dc18dc16e879e95ff4ec7c1a9ab7ec3f17bef
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84265991"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Suporte de idioma e voz para o serviço de fala

O suporte a idiomas varia de acordo com a funcionalidade do serviço de fala. As tabelas a seguir resumem o suporte a idiomas para ofertas de [voz para texto](#speech-to-text), conversão de [texto em fala](#text-to-speech)e serviço de [tradução de fala](#speech-translation) .

## <a name="speech-to-text"></a>Conversão de fala em texto

O SDK do Microsoft Speech e a API REST dão suporte aos seguintes idiomas (localidades). 

Para melhorar a precisão, a personalização é oferecida para um subconjunto das linguagens por meio do carregamento de **áudio + transcrições com rótulo humano** ou **texto relacionado: frases**. Para saber mais sobre a personalização, consulte [introdução ao fala personalizada](how-to-custom-speech.md).

Para obter mais informações sobre como você pode melhorar a pronúncia, consulte [melhorar um modelo para fala personalizada](how-to-custom-speech-improve-accuracy.md#add-new-words-with-pronunciation).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Localidade  | Linguagem                          | Com suporte | Personalizações                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | Árabe (dos EAU)                      | Sim       | Não                                                |
| `ar-BH` | Árabe (Bahrein), padrão moderno | Sim       | Modelo de linguagem                                    |
| `ar-EG` | Árabe (Egito)                    | Sim       | Modelo de linguagem                                    |
| `ar-IL` | Árabe (Israel)                   | Sim       | Não                                                |
| `ar-KW` | Árabe (Kuwait)                   | Sim       | Não                                                |
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
| `gu-IN` | Guzerate (indiano)                 | Sim       | Modelo de linguagem                                    |
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
| `te-IN` | Télugo (Índia)                    | Sim       | Não                                                |
| `th-TH` | Tailandês (Tailândia)                   | Sim       | Não                                                |
| `tr-TR` | Turco (Turquia)                  | Sim       | Modelo de linguagem                                    |
| `zh-CN` | Chinês (mandarim, simplificado)    | Sim       | Modelo acústico<br>Modelo de linguagem                  |
| `zh-HK` | Chinês (Cantonês, tradicional)  | Sim       | Modelo de linguagem                                    |
| `zh-TW` | Chinês (Mandarim Taiwanês)      | Sim       | Modelo de linguagem                                    |

## <a name="text-to-speech"></a>Conversão de texto em fala

O SDK do Microsoft Speech e as APIs REST dão suporte a essas vozes, cada uma delas com suporte a um idioma e dialeto específicos, identificados pela localidade. Você também pode obter uma lista completa de idiomas e vozes com suporte para cada região/ponto de extremidade específico por meio da [API de vozes/lista](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> Os preços variam para vozes padrão, personalizadas e neurais. Visite a página [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para obter mais informações.

### <a name="neural-voices"></a>Vozes neurais

A conversão de texto em fala neural é um novo tipo de sintetização de fala alimentado por redes neurais profundas. Ao usar uma voz neural, a fala sintetizada é quase indistinguível de gravações de humana.

As vozes neurais podem ser usadas para fazer interações com os assistentes de voz e chatbots mais naturais e envolventes, converter textos digitais, como livros eletrônicos, em Audiobooks e aprimorar os sistemas de navegação no carro. Com a prosódia natural semelhante à humana e a articulação clara das palavras, as vozes neurais reduzem significativamente a fadiga de escuta quando os usuários interagem com sistemas de inteligência artificial.

Para obter mais informações sobre a disponibilidade regional, consulte [regiões](regions.md#standard-and-neural-voices).

|Localidade  | Linguagem            | Gênero | Nome da voz | Suporte de estilo |
|--|--|--|--|--|
| `de-DE` | Alemão (Alemanha)                | `Female` | "de-DE-KatjaNeural"      | Geral |
| `en-AU` | Inglês (Austrália)             | `Female` | "en-AU-NatashaNeural"    | Geral |
| `en-CA` | Inglês (Canadá)                | `Female` | "en-CA-ClaraNeural"      | Geral |
| `en-GB` | Inglês (Reino Unido)                    | `Female` | "en-GB-LibbyNeural"      | Geral |
|         |                                 | `Female` | "en-GB-MiaNeural"        | Geral |
| `en-US` | Inglês (EUA)                    | `Female` | "en-US-AriaNeural"       | Geral, vários estilos de voz disponíveis |
|         |                                 | `Male`   | "en-US-GuyNeural"        | Geral |
| `es-ES` | Espanhol (Espanha)                 | `Female` | "es-ES-ElviraNeural"     | Geral |
| `es-MX` | Espanhol (México)                | `Female` | "es-MX-DaliaNeural"      | Geral |
| `fr-CA` | Francês (Canadá)                 | `Female` | "fr-CA-SylvieNeural"     | Geral |
| `fr-FR` | Francês (França)                 | `Female` | "fr-FR-DeniseNeural"     | Geral |
| `it-IT` | Italiano (Itália)                 | `Female` | "IT-IT-ElsaNeural"       | Geral |
| `ja-JP` | Japonês                        | `Female` | "ja-JP-NanamiNeural"     | Geral |
| `ko-KR` | Coreano                          | `Female` | "ko-KR-SunHiNeural"      | Geral |
| `nb-NO` | Norueguês                       | `Female` | "NB-NO-IselinNeural"     | Geral |
| `pt-BR` | Português (Brasil)             | `Female` | "pt-BR-FranciscaNeural"  | Geral |
| `tr-TR` | Turco                         | `Female` | "tr-TR-EmelNeural"       | Geral |
| `zh-CN` | Chinês (mandarim, simplificado)  | `Female` | "zh-CN-XiaoxiaoNeural"   | Geral, vários estilos de voz disponíveis |
|         |                                 | `Female` | "zh-CN-XiaoyouNeural"    | Infantil Voice, otimizado para narração da história |
|         |                                 | `Male`   | "zh-CN-YunyangNeural"    | Otimizado para leitura de notícias, vários estilos de voz disponíveis |
|         |                                 | `Male`   | "zh-CN-YunyeNeural"      | Otimizado para narração da história |

> [!IMPORTANT]
> A `en-US-JessaNeural` voz foi alterada para `en-US-AriaNeural` . Se você estava usando "Jessa" antes, converta para "Aria".

Para saber como você pode configurar e ajustar as vozes neurais, consulte [linguagem de marcação de síntese de fala](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> Você pode continuar a usar o mapeamento de nome de serviço completo como "Microsoft Server Speech Conversão de Texto em Fala Voice (en-US, AriaNeural)" em suas solicitações de síntese de fala.

### <a name="standard-voices"></a>Vozes padrão

Mais de 75 vozes padrão estão disponíveis em mais de 45 idiomas e localidades, que permitem converter texto em fala sintetizada. Para obter mais informações sobre a disponibilidade regional, consulte [regiões](regions.md#standard-and-neural-voices).

| Localidade | Linguagem | Gênero | Nome da voz |
|--|--|--|--|
| <sup>uma</sup>`ar-EG` | Árabe (Egito) | `Female` | "ar-ex-Hoda" |
| `ar-SA` | Árabe (Arábia Saudita) | `Male` | "ar-SA-Naayf" |
| `bg-BG` | Búlgaro | `Male` |  "bg-BG-Ivan" |
| `ca-ES` | Catalão | `Female` |  "CA-ES-HerenaRUS" |
| `cs-CZ` | Tcheco | `Male` | "CS-CZ-Jakub" |
| `da-DK` | Dinamarquês | `Female` |  "da-DK-HelleRUS" |
| `de-AT` | Alemão (Áustria) | `Male` | "de a Michael" |
| `de-CH` | Alemão (Suíça) | `Male` |  "de-CH-Karsten" |
| `de-DE` | Alemão (Alemanha) | `Female` |  "de-DE-Hedda" |
|  |  | `Female` | "de-DE-HeddaRUS" |
|  |  | `Male` |  "de-DE-Rodrigo-Apollo" |
| `el-GR` | Grego | `Male` | "El-GR-Stefanos" |
| `en-AU` | Inglês (Austrália) | `Female` |  "en-AU-Catherine" |
|  |  | `Female` |  "en-AU-HayleyRUS" |
| `en-CA` | Inglês (Canadá) | `Female` |  "en-CA-linda" |
|  |  | `Female` |  "en-CA-HeatherRUS" |
| `en-GB` | Inglês (Reino Unido) | `Female` |  "en-GB-Susan-Apollo" |
|  |  | `Female` |  "en-GB-HazelRUS" |
|  |  | `Male` |  "en-GB-George-Apollo" |
| `en-IE` | Inglês (Irlanda) | `Male` | "en-IE-Sílvio" |
| `en-IN` | Inglês (Índia) | `Female` | "en-IN-Heera-Apollo" |
|  |  | `Female` |  "en-IN-PriyaRUS" |
|  |  | `Male` |  "en-IN-Ravi-Apollo" |
| `en-US` | Inglês (EUA) | `Female` |  "en-US-ZiraRUS" |
|  |  | `Female` | "en-US-AriaRUS" |
|  |  | `Male` | "en-US-BenjaminRUS" |
|  |  | `Male` |  "en-US-Guy24kRUS" |
| `es-ES` | Espanhol (Espanha) | `Female` |  "es-ES-Laura-Apollo" |
|  |  | `Female` | "es-ES-HelenaRUS" |
|  |  | `Male` | "es-ES-Pablo-Apollo" |
| `es-MX` | Espanhol (México) | `Female` |  "es-MX-HildaRUS" |
|  |  | `Male` | "es-MX-Ricardo-Apollo" |
| `fi-FI` | Finlandês | `Female` | "fi-FI-HeidiRUS" |
| `fr-CA` | Francês (Canadá) | `Female` | "fr-CA-Carolina" |
|  |  | `Female` | "fr-CA-HarmonieRUS" |
| `fr-CH` | Francês (Suíça) | `Male` | "fr-CH-Guillaume" |
| `fr-FR` | Francês (França) | `Female` |  "fr-FR-Julie-Apollo" |
|  |  | `Female` |"fr-FR-HortenseRUS" |
|  |  | `Male` |  "fr-FR-Paul-Apollo" |
| `he-IL` | Hebraico (Israel) | `Male` |  "he-IL-Asaf" |
| `hi-IN` | Híndi (Índia) | `Female` | "hi-IN-Kalpana-Apollo" |
|  |  | `Female` |  "hi-IN-Kalpana" |
|  |  | `Male` |  "hi-IN-Hemant" |
| `hr-HR` | Croata | `Male` | "HR-HR-Matej" |
| `hu-HU` | Húngaro | `Male` |  "hu-HU-Szabolcs" |
| `id-ID` | Indonésio | `Male` | "ID-ID-andika" |
| `it-IT` | Italiano | `Male` |  "IT-IT-Cosimo-Apollo" |
|  |  | `Female` |  "IT-IT-LuciaRUS" |
| `ja-JP` | Japonês | `Female` |  "ja-JP-Ayumi-Apollo" |
|  |  | `Male` | "ja-JP-Ichiro-Apollo" |
|  |  | `Female` |  "ja-JP-HarukaRUS" |
| `ko-KR` | Coreano | `Female` | "ko-KR-HeamiRUS" |
| `ms-MY` | Malaio | `Male` |  "MS-MY-Rizwan" |
| `nb-NO` | Norueguês | `Female` |  "NB-NO-HuldaRUS" |
| `nl-NL` | Holandês | `Female` |  "nl-NL-HannaRUS" |
| `pl-PL` | Polonês | `Female` |  "pl-PL-PaulinaRUS" |
| `pt-BR` | Português (Brasil) | `Female` | "pt-BR-HeloisaRUS" |
|  |  | `Male` |  "pt-BR-Daniel-Apollo" |
| `pt-PT` | Português (Portugal) | `Female` | "pt-PT-HeliaRUS" |
| `ro-RO` | Romeno | `Male` | "RO-RO-Andrei" |
| `ru-RU` | Russo | `Female` |  "ru-RU-Irina-Apollo" |
|  |  | `Male` | "ru-RU-Pavel-Apollo" |
|  |  | `Female` |  RU-RU-EkaterinaRUS |
| `sk-SK` | Eslovaco | `Male` | "SK-SK-Filip" |
| `sl-SI` | Esloveno | `Male` |  "SL-SI-lado" |
| `sv-SE` | Sueco | `Female` | "SV-SE-HedvigRUS" |
| `ta-IN` | Tâmil (Índia) | `Male` |  "ta-IN-Valluvar" |
| `te-IN` | Télugo (Índia) | `Female` |  "Chitra" |
| `th-TH` | Tailandês | `Male` |  "th-TH-Pattara" |
| `tr-TR` | Turco (Turquia) | `Female` | "tr-TR-SedaRUS" |
| `vi-VN` | Vietnamita | `Male` |  "vi-VN-um" |
| `zh-CN` | Chinês (mandarim, simplificado) | `Female` |  "zh-CN-HuihuiRUS" |
|  |  | `Female` | "zh-CN-Yaoyao-Apollo" |
|  |  | `Male` | "zh-CN-Kangkang-Apollo" |
| `zh-HK` | Chinês (Cantonês, tradicional) | `Female` |  "ZH-HK-Tracy-Apollo" |
|  |  | `Female` | "ZH-HK-TracyRUS" |
|  |  | `Male` |  "ZH-HK-Danny-Apollo" |
| `zh-TW` | Chinês (Mandarim Taiwanês) | `Female` |  "zh-TW-Yating-Apollo" |
|  |  | `Female` | "zh-TW-HanHanRUS" |
|  |  | `Male` |  "zh-TW-Zhiwei-Apollo" |

**1** *ar-ex dá suporte a Modern padrão árabe (MSA).*

> [!IMPORTANT]
> A `en-US-Jessa` voz foi alterada para `en-US-Aria` . Se você estava usando "Jessa" antes, converta para "Aria".

> [!TIP]
> Você pode continuar a usar o mapeamento de nome de serviço completo como "Microsoft Server Speech Conversão de Texto em Fala Voice (en-US, AriaRUS)" em suas solicitações de síntese de fala.

### <a name="customization"></a>Personalização

A personalização de voz está disponível para o,,,,,, `de-DE` `en-GB` `en-IN` `en-US` `es-MX` `fr-FR` `it-IT` , `pt-BR` e `zh-CN` . Selecione a localidade correta que corresponde aos dados de treinamento que você tem para treinar um modelo de voz personalizado. Por exemplo, se os dados de gravação que você tem são falados em inglês com um sotaque britânico, selecione `en-GB` .

> [!NOTE]
> Não damos suporte ao treinamento de modelo de bi-idiomas em voz personalizada, exceto para o Português do idioma chinês-inglês. Selecione "chinês-inglês bilíngüe" se você quiser treinar uma voz chinesa que também pode falar em inglês. O treinamento de voz em todas as localidades começa com um conjunto de dados de 2.000 + declarações, exceto para o `en-US` e `zh-CN` onde você pode começar com qualquer tamanho de dados de treinamento.

## <a name="speech-translation"></a>Tradução de fala

A API de **Tradução de Fala** é compatível com diferentes idiomas para conversão de fala em fala e de fala em texto. O idioma de origem sempre deve ser da tabela de linguagem de fala para texto. Os idiomas de destino disponíveis dependem de se o destino de tradução é fala ou texto. Você pode traduzir a entrada de fala em mais de [60 idiomas](https://www.microsoft.com/translator/business/languages/). Um subconjunto de idiomas está disponível para [síntese de fala](language-support.md#text-languages).

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

## <a name="next-steps"></a>Próximas etapas

* [Obtenha sua assinatura de avaliação do serviço de fala](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como reconhecer fala em C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
