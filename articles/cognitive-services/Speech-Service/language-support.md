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
ms.openlocfilehash: a864717304dacaf6cf4c77c92050827af619ed39
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92736679"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Idiomas compatíveis e suporte de voz para o serviço de Fala

Os idiomas compatíveis variam de acordo com a funcionalidade do serviço de Fala. As tabelas a seguir resumem os idiomas compatíveis para as ofertas de serviço de [Conversão de fala em texto](#speech-to-text), [Conversão de texto em fala](#text-to-speech) e [Tradução de fala](#speech-translation).

## <a name="speech-to-text"></a>Conversão de fala em texto

O SDK do Microsoft Speech e a API REST são compatíveis com os idiomas (localidades) a seguir. 

Para melhorar a precisão, a personalização é oferecida para um subconjunto de idiomas por meio do upload de **Áudio + Transcrições rotuladas por humanos** ou **Texto relacionado: sentenças** . Para saber mais sobre a personalização, confira [Introdução à Fala Personalizada](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Linguagem                          | Localidade | Personalizações                                   |
|-----------------------------------|--------|--------------------------------------------------|
|Árabe (Bahrein), padrão moderno  |`ar-BH` | Modelo de linguagem                                   |
|Árabe (Egito)                     |`ar-EG` | Modelo de linguagem                                   |
|Árabe (Iraque)                      |`ar-IQ` | Modelo de linguagem                                   |
|Árabe (Jordânia)                    |`ar-JO` | Modelo de linguagem                                   |
|Árabe (Kuwait)                    |`ar-KW` | Modelo de linguagem                                   |
|Árabe (Líbano)                   |`ar-LB` | Modelo de linguagem                                   |
|Árabe (Omã)                      |`ar-OM` | Modelo de linguagem                                   |
|Árabe (Catar)                     |`ar-QA` | Modelo de linguagem                                   |
|Árabe (Arábia Saudita)              |`ar-SA` | Modelo de linguagem                                   |
|Árabe (Síria)                     |`ar-SY` | Modelo de linguagem                                   |
|Árabe (Emirados Árabes Unidos)      |`ar-AE` | Modelo de linguagem                                   |
|Búlgaro (Bulgária)               |`bg-BG` | Modelo de linguagem                                   |
|Catalão (Espanha)                    |`ca-ES` | Modelo de linguagem                                   |
|Chinês (Cantonês tradicional)   |`zh-HK` | Modelo de linguagem                                   |
|Chinês (mandarim, simplificado)     |`zh-CN` | Modelo acústico<br>Modelo de linguagem                 |
|Chinês (Mandarim Taiwanês)       |`zh-TW` | Modelo de linguagem                                   |
|Croata (Croácia)                 |`hr-HR` | Modelo de linguagem                                   |
|Tcheco (República Tcheca)             |`cs-CZ` | Modelo de Linguagem                                   |
|Dinamarquês (Dinamarca)                   |`da-DK` | Modelo de linguagem                                   |
|Holandês (Países Baixos)                |`nl-NL` | Modelo de linguagem                                   |
|Inglês (Austrália)                |`en-AU` | Modelo acústico<br>Modelo de linguagem                 |
|Inglês (Canadá)                   |`en-CA` | Modelo acústico<br>Modelo de linguagem                 |
|Inglês (Hong Kong)                |`en-HK` | Modelo de Linguagem                                   |
|Inglês (Índia)                    |`en-IN` | Modelo acústico<br>Modelo de linguagem                 |
|Inglês (Irlanda)                  |`en-IE` | Modelo de Linguagem                                   |
|Inglês (Nova Zelândia)              |`en-NZ` | Modelo acústico<br>Modelo de linguagem                 |
|Inglês (Filipinas)              |`en-PH` | Modelo de Linguagem                                   |
|Inglês (Singapura)                |`en-SG` | Modelo de Linguagem                                   |
|Inglês (África do Sul)             |`en-ZA` | Modelo de Linguagem                                   |
|Inglês (Reino Unido)           |`en-GB` | Modelo acústico<br>Modelo de linguagem<br>Pronúncia|
|Inglês (Estados Unidos)            |`en-US` | Modelo acústico<br>Modelo de linguagem<br>Pronúncia|
|Estoniano (Estônia)                  |`et-EE` | Modelo de Linguagem                                   |
|Finlandês (Finlândia)                  |`fi-FI` | Modelo de linguagem                                   |
|Francês (Canadá)                    |`fr-CA` | Modelo acústico<br>Modelo de linguagem                 |
|Francês (França)                    |`fr-FR` | Modelo acústico<br>Modelo de linguagem<br>Pronúncia|
|Alemão (Alemanha)                   |`de-DE` | Modelo acústico<br>Modelo de linguagem<br>Pronúncia|
|Grego (Grécia)                     |`el-GR` | Modelo de linguagem                                   |
|Guzerate (Índia)                  |`gu-IN` | Modelo de linguagem                                   |
|Híndi (Índia)                      |`hi-IN` | Modelo acústico<br>Modelo de linguagem                 |
|Húngaro (Hungria)                |`hu-HU` | Modelo de Linguagem                                   |
|Irlandês (Irlanda)                     |`ga-IE` | Modelo de linguagem                                   |
|Italiano (Itália)                    |`it-IT` | Modelo acústico<br>Modelo de linguagem<br>Pronúncia|
|Japonês (Japão)                   |`ja-JP` | Modelo de linguagem                                   |
|Coreano (Coreia do Sul)                     |`ko-KR` | Modelo de linguagem                                   |
|Letão (Letônia)                   |`lv-LV` | Modelo de linguagem                                   |
|Lituano (Lituânia)             |`lt-LT` | Modelo de linguagem                                   |
|Maltês (Malta)                     |`mt-MT` | Modelo de linguagem                                   |
|Marati (Índia)                    |`mr-IN` | Modelo de linguagem                                   |
|Norueguês (BokmÃ ¥ l) (Noruega)       |`nb-NO` | Modelo de linguagem                                   |
|Polonês (Polônia)                    |`pl-PL` | Modelo de linguagem                                   |
|Português (Brasil)                |`pt-BR` | Modelo acústico<br>Modelo de linguagem<br>Pronúncia|
|Português (Portugal)              |`pt-PT` | Modelo de linguagem                                   |
|Romeno (Romênia)                 |`ro-RO` | Modelo de linguagem                                   |
|Russo (Rússia)                   |`ru-RU` | Modelo acústico<br>Modelo de linguagem                 |
|Eslovaco (Eslováquia)                  |`sk-SK` | Modelo de linguagem                                   |
|Esloveno (Eslovênia)               |`sl-SI` | Modelo de linguagem                                   |
|Espanhol (Argentina)                |`es-AR` | Modelo de Linguagem                                   |
|Espanhol (Bolívia)                  |`es-BO` | Modelo de Linguagem                                   |
|Espanhol (Chile)                    |`es-CL` | Modelo de Linguagem                                   |
|Espanhol (Colômbia)                 |`es-CO` | Modelo de Linguagem                                   |
|Espanhol (Costa Rica)               |`es-CR` | Modelo de Linguagem                                   |
|Espanhol (Cuba)                     |`es-CU` | Modelo de Linguagem                                   |
|Espanhol (República Dominicana)       |`es-DO` | Modelo de Linguagem                                   |
|Espanhol (Equador)                  |`es-EC` | Modelo de Linguagem                                   |
|Espanhol (El Salvador)              |`es-SV` | Modelo de Linguagem                                   |
|Espanhol (Guatemala)                |`es-GT` | Modelo de Linguagem                                   |
|Espanhol (Honduras)                 |`es-HN` | Modelo de Linguagem                                   |
|Espanhol (México)                   |`es-MX` | Modelo acústico<br>Modelo de linguagem                 |
|Espanhol (Nicarágua)                |`es-NI` | Modelo de Linguagem                                   |
|Espanhol (Panamá)                   |`es-PA` | Modelo de Linguagem                                   |
|Espanhol (Paraguai)                 |`es-PY` | Modelo de Linguagem                                   |
|Espanhol (Peru)                     |`es-PE` | Modelo de Linguagem                                   |
|Espanhol (Porto Rico)              |`es-PR` | Modelo de Linguagem                                   |
|Espanhol (Espanha)                    |`es-ES` | Modelo acústico<br>Modelo de linguagem                 |
|Espanhol (Uruguai)                  |`es-UY` | Modelo de Linguagem                                   |
|Espanhol (EUA)                      |`es-US` | Modelo de Linguagem                                   |
|Espanhol (Venezuela)                |`es-VE` | Modelo de Linguagem                                   |
|Sueco (Suécia)                   |`sv-SE` | Modelo de linguagem                                   |
|Tâmil (Índia)                      |`ta-IN` | Modelo de linguagem                                   |
|Télugo (Índia)                     |`te-IN` | Modelo de linguagem                                   |
|Tailandês (Tailândia)                    |`th-TH` | Modelo de linguagem                                   |
|Turco (Turquia)                   |`tr-TR` | Modelo de linguagem                                   |

## <a name="text-to-speech"></a>Conversão de texto em fala

O SDK do Microsoft Speech e as APIs REST são compatíveis com essas vozes, cada uma delas compatível com um idioma e um dialeto específicos, identificados pela localidade. Você também pode obter uma lista completa de idiomas e vozes compatíveis para cada região/ponto de extremidade específico por meio da [API vozes/lista](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> Os preços variam para vozes padrão, personalizadas e neurais. Visite a página [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para obter mais informações.

### <a name="neural-voices"></a>Vozes neurais

A conversão de texto em fala neural é um novo tipo de sintetização de fala alimentado por redes neurais profundas. Ao usar uma voz neural, a fala sintetizada é quase indistinguível de gravações de humana.

Vozes neurais podem ser usadas para interagir com chatbots e assistentes de voz de maneira mais natural e participativa para converter textos digitais, como livros eletrônicos, em audiolivros e aprimorar sistemas de navegação veiculares. Com a prosódia natural semelhante à humana e a articulação clara das palavras, as vozes neurais reduzem significativamente a fadiga de escuta quando os usuários interagem com sistemas de inteligência artificial.

Para obter mais informações sobre a disponibilidade regional, consulte [regiões](regions.md#standard-and-neural-voices).

|Linguagem  | Localidade           | Gênero | Nome da voz | Suporte de estilo |
|--|--|--|--|--|
| Árabe (Egito) | `ar-EG` | Feminino | `ar-EG-SalmaNeural` | Geral |
| Árabe (Arábia Saudita) | `ar-SA` | Feminino | `ar-SA-ZariyahNeural` | Geral |
| Búlgaro (Bulgary) | `bg-BG` <sup>Novo</sup> | Feminino | `bg-BG-KalinaNeural` | Geral |
| Cantonês (chinês tradicional, Hong Kong) | `zh-HK` | Feminino | `zh-HK-HiuGaaiNeural` | Geral |
| Catalão (Espanha) | `ca-ES` | Feminino | `ca-ES-AlbaNeural` | Geral |
| Croata (Croácia) | `hr-HR` <sup>Novo</sup> | Feminino | `hr-HR-GabrijelaNeural` | Geral |
| Tcheco (tcheco) | `cs-CZ` <sup>Novo</sup> | Feminino | `cs-CZ-VlastaNeural` | Geral |
| Dinamarquês (Dinamarca) | `da-DK` | Feminino | `da-DK-ChristelNeural` | Geral |
| Holandês (Países Baixos) | `nl-NL` | Feminino | `nl-NL-ColetteNeural` | Geral |
| Inglês (Austrália) | `en-AU` | Feminino | `en-AU-NatashaNeural` | Geral |
| Inglês (Austrália) | `en-AU` <sup>Novo</sup> | Masculino | `en-AU-WilliamNeural` | Geral |
| Inglês (Canadá) | `en-CA` | Feminino | `en-CA-ClaraNeural` | Geral |
| Inglês (Índia) | `en-IN` | Feminino | `en-IN-NeerjaNeural` | Geral |
| Inglês (Irlanda) | `en-IE` <sup>Novo</sup> | Feminino | `en-IE-EmilyNeural` | Geral |
| Inglês (Reino Unido) | `en-GB` | Feminino | `en-GB-LibbyNeural` | Geral |
| Inglês (Reino Unido) | `en-GB` | Feminino | `en-GB-MiaNeural` | Geral |
| Inglês (Reino Unido) | `en-GB` <sup>Novo</sup> | Masculino | `en-GB-RyanNeural` | Geral |
| Inglês (Estados Unidos) | `en-US` | Feminino | `en-US-AriaNeural` | Geral, com vários estilos de voz disponíveis |
| Inglês (Estados Unidos) | `en-US` | Masculino | `en-US-GuyNeural` | Geral |
| Inglês (Estados Unidos) | `en-US` <sup>Novo</sup> | Feminino | `en-US-JennyNeural` | Geral, com vários estilos de voz disponíveis |
| Finlandês (Finlândia) | `fi-FI` | Feminino | `fi-FI-NooraNeural` | Geral |
| Francês (Canadá) | `fr-CA` | Feminino | `fr-CA-SylvieNeural` | Geral |
| Francês (Canadá) | `fr-CA` <sup>Novo</sup> | Masculino | `fr-CA-JeanNeural` | Geral |
| Francês (França) | `fr-FR` | Feminino | `fr-FR-DeniseNeural` | Geral |
| Francês (França) | `fr-FR` <sup>Novo</sup> | Masculino | `fr-FR-HenriNeural` | Geral |
| Francês (Suíça) | `fr-CH` <sup>Novo</sup> | Feminino | `fr-CH-ArianeNeural` | Geral |
| Alemão (Áustria) | `de-AT` <sup>Novo</sup> | Feminino | `de-AT-IngridNeural` | Geral |
| Alemão (Alemanha) | `de-DE` | Feminino | `de-DE-KatjaNeural` | Geral |
| Alemão (Alemanha) | `de-DE` <sup>Novo</sup> | Masculino | `de-DE-ConradNeural` | Geral |
| Alemão (Suíça) | `de-CH` <sup>Novo</sup> | Feminino | `de-CH-LeniNeural` | Geral |
| Grego (Grécia) | `el-GR` <sup>Novo</sup> | Feminino | `el-GR-AthinaNeural` | Geral |
| Hebraico (Israel) | `he-IL` <sup>Novo</sup> | Feminino | `he-IL-HilaNeural` | Geral |
| Híndi (Índia) | `hi-IN` | Feminino | `hi-IN-SwaraNeural` | Geral |
| Húngaro (Hungria) | `hu-HU` <sup>Novo</sup> | Feminino | `hu-HU-NoemiNeural` | Geral |
| Indonésio (Indonésia) | `id-ID` <sup>Novo</sup> | Masculino | `id-ID-ArdiNeural` | Geral |
| Italiano (Itália) | `it-IT` | Feminino | `it-IT-ElsaNeural` | Geral |
| Italiano (Itália) | `it-IT` <sup>Novo</sup> | Feminino | `it-IT-IsabellaNeural` | Geral |
| Italiano (Itália) | `it-IT` <sup>Novo</sup> | Masculino | `it-IT-DiegoNeural` | Geral |
| Japonês (Japão) | `ja-JP` | Feminino | `ja-JP-NanamiNeural` | Geral |
| Japonês (Japão) | `ja-JP` <sup>Novo</sup> | Masculino | `ja-JP-KeitaNeural` | Geral |
| Coreano (Coreia do Sul) | `ko-KR` | Feminino | `ko-KR-SunHiNeural` | Geral |
| Coreano (Coreia do Sul) | `ko-KR` <sup>Novo</sup> | Masculino | `ko-KR-InJoonNeural` | Geral |
| Malaio (Malásia) | `ms-MY` <sup>Novo</sup> | Feminino | `ms-MY-YasminNeural` | Geral |
| Mandarim (chinês simplificado, China) | `zh-CN` | Feminino | `zh-CN-XiaoxiaoNeural` | Geral, com vários estilos de voz disponíveis |
| Mandarim (chinês simplificado, China) | `zh-CN` | Feminino | `zh-CN-XiaoyouNeural` | Voz de criança, otimização para narração de histórias |
| Mandarim (chinês simplificado, China) | `zh-CN` | Masculino | `zh-CN-YunyangNeural` | Otimizado para leitura de notícias, vários estilos de voz disponíveis |
| Mandarim (chinês simplificado, China) | `zh-CN` | Masculino | `zh-CN-YunyeNeural` | Otimização para narração de histórias |
| Mandarim (chinês tradicional, Taiwan) | `zh-TW` | Feminino | `zh-TW-HsiaoYuNeural` | Geral |
| Norueguês, BokmÃ ¥ l (Noruega) | `nb-NO` | Feminino | `nb-NO-IselinNeural` | Geral |
| Polonês (Polônia) | `pl-PL` | Feminino | `pl-PL-ZofiaNeural` | Geral |
| Português (Brasil) | `pt-BR` | Feminino | `pt-BR-FranciscaNeural` | Geral, com vários estilos de voz disponíveis |
| Português (Brasil) | `pt-BR` <sup>Novo</sup> | Masculino | `pt-BR-AntonioNeural` | Geral |
| Português (Portugal) | `pt-PT` | Feminino | `pt-PT-FernandaNeural` | Geral |
| Romeno (Romênia) | `ro-RO` <sup>Novo</sup> | Feminino | `ro-RO-AlinaNeural` | Geral |
| Russo (Rússia) | `ru-RU` | Feminino | `ru-RU-DariyaNeural` | Geral |
| Eslovaco (Eslováquia) | `sk-SK` <sup>Novo</sup> | Feminino | `sk-SK-ViktoriaNeural` | Geral |
| Esloveno (Eslovênia) | `sl-SI` <sup>Novo</sup> | Feminino | `sl-SI-PetraNeural` | Geral |
| Espanhol (México) | `es-MX` | Feminino | `es-MX-DaliaNeural` | Geral |
| Espanhol (México) | `es-MX` <sup>Novo</sup> | Masculino | `es-MX-JorgeNeural` | Geral |
| Espanhol (Espanha) | `es-ES` | Feminino | `es-ES-ElviraNeural` | Geral |
| Espanhol (Espanha) | `es-ES` <sup>Novo</sup> | Masculino | `es-ES-AlvaroNeural` | Geral |
| Sueco (Suécia) | `sv-SE` | Feminino | `sv-SE-HilleviNeural` | Geral |
| Tâmil (Índia) | `ta-IN` <sup>Novo</sup> | Feminino | `ta-IN-PallaviNeural` | Geral |
| Télugo (Índia) | `te-IN` <sup>Novo</sup> | Feminino | `te-IN-ShrutiNeural` | Geral |
| Tailandês (Tailândia) | `th-TH` | Feminino | `th-TH-AcharaNeural` | Geral |
| Tailandês (Tailândia) | `th-TH` <sup>Novo</sup> | Feminino | `th-TH-PremwadeeNeural` | Geral |
| Turco (Turquia) | `tr-TR` | Feminino | `tr-TR-EmelNeural` | Geral |
| Vietnamita (Vietnã) | `vi-VN` <sup>Novo</sup> | Feminino | `vi-VN-HoaiMyNeural` | Geral|

> [!IMPORTANT]
> A voz `en-US-JessaNeural` foi alterada para `en-US-AriaNeural`. Se você usava a voz "Jessa", faça a conversão para "Aria".

Para saber como você pode configurar e ajustar as vozes neurais, confira [Linguagem de marcação para síntese de fala](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> Você poderá continuar a usar o mapeamento de nome de serviço completo como "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)" em suas solicitações de síntese de fala.

### <a name="standard-voices"></a>Vozes padrão

Mais de 75 vozes padrão estão disponíveis em mais de 45 idiomas e localidades, que permitem converter texto em fala sintetizada. Para obter mais informações sobre a disponibilidade regional, consulte [regiões](regions.md#standard-and-neural-voices).

| Linguagem | Localidade | Gênero | Nome da voz |
|--|--|--|--|
| Árabe (árabe)  |  `ar-EG`  |  Feminino  |  `ar-EG-Hoda`|
| Árabe (Arábia Saudita)  |  `ar-SA`  |  Masculino  |  `ar-SA-Naayf`|
| Búlgaro (Bulgária)  |  `bg-BG`  |  Masculino  |  `bg-BG-Ivan`|
| Cantonês (chinês tradicional, Hong Kong)  |  `zh-HK`  |  Masculino  |  `zh-HK-Danny`|
| Cantonês (chinês tradicional, Hong Kong)  |  `zh-HK`  |  Feminino  |  `zh-HK-TracyRUS`|
| Catalão (Espanha)  |  `ca-ES`  |  Feminino  |  `ca-ES-HerenaRUS`|
| Croata (Croácia)  |  `hr-HR`  |  Masculino  |  `hr-HR-Matej`|
| Tcheco (República Tcheca)  |  `cs-CZ`  |  Masculino  |  `cs-CZ-Jakub`|
| Dinamarquês (Dinamarca)  |  `da-DK`  |  Feminino  |  `da-DK-HelleRUS`|
| Holandês (Países Baixos)  |  `nl-NL`  |  Feminino  |  `nl-NL-HannaRUS`|
| Inglês (Austrália)  |  `en-AU`  |  Feminino  |  `en-AU-Catherine`|
| Inglês (Austrália)  |  `en-AU`  |  Feminino  |  `en-AU-HayleyRUS`|
| Inglês (Canadá)  |  `en-CA`  |  Feminino  |  `en-CA-HeatherRUS`|
| Inglês (Canadá)  |  `en-CA`  |  Feminino  |  `en-CA-Linda`|
| Inglês (Índia)  |  `en-IN`  |  Feminino  |  `en-IN-Heera`|
| Inglês (Índia)  |  `en-IN`  |  Feminino  |  `en-IN-PriyaRUS`|
| Inglês (Índia)  |  `en-IN`  |  Masculino  |  `en-IN-Ravi`|
| Inglês (Irlanda)  |  `en-IE`  |  Masculino  |  `en-IE-Sean`|
| Inglês (Reino Unido)  |  `en-GB`  |  Masculino  |  `en-GB-George`|
| Inglês (Reino Unido)  |  `en-GB`  |  Feminino  |  `en-GB-HazelRUS`|
| Inglês (Reino Unido)  |  `en-GB`  |  Feminino  |  `en-GB-Susan`|
| Inglês (Estados Unidos)  |  `en-US`  |  Masculino  |  `en-US-BenjaminRUS`|
| Inglês (Estados Unidos)  |  `en-US`  |  Masculino  |  `en-US-GuyRUS`|
| Inglês (Estados Unidos)  |  `en-US`  |  Feminino  |  `en-US-JessaRUS`|
| Inglês (Estados Unidos)  |  `en-US`  |  Feminino  |  `en-US-ZiraRUS`|
| Finlandês (Finlândia)  |  `fi-FI`  |  Feminino  |  `fi-FI-HeidiRUS`|
| Francês (Canadá)  |  `fr-CA`  |  Feminino  |  `fr-CA-Caroline`|
| Francês (Canadá)  |  `fr-CA`  |  Feminino  |  `fr-CA-HarmonieRUS`|
| Francês (França)  |  `fr-FR`  |  Feminino  |  `fr-FR-HortenseRUS`|
| Francês (França)  |  `fr-FR`  |  Feminino  |  `fr-FR-Julie`|
| Francês (França)  |  `fr-FR`  |  Masculino  |  `fr-FR-Paul`|
| Francês (Suíça)  |  `fr-CH`  |  Masculino  |  `fr-CH-Guillaume`|
| Alemão (Áustria)  |  `de-AT`  |  Masculino  |  `de-AT-Michael`|
| Alemão (Alemanha)  |  `de-DE`  |  Feminino  |  `de-DE-HeddaRUS`|
| Alemão (Alemanha)  |  `de-DE`  |  Masculino  |  `de-DE-Stefan`|
| Alemão (Suíça)  |  `de-CH`  |  Masculino  |  `de-CH-Karsten`|
| Grego (Grécia)  |  `el-GR`  |  Masculino  |  `el-GR-Stefanos`|
| Hebraico (Israel)  |  `he-IL`  |  Masculino  |  `he-IL-Asaf`|
| Híndi (Índia)  |  `hi-IN`  |  Masculino  |  `hi-IN-Hemant`|
| Híndi (Índia)  |  `hi-IN`  |  Feminino  |  `hi-IN-Kalpana`|
| Húngaro (Hungria)  |  `hu-HU`  |  Masculino  |  `hu-HU-Szabolcs`|
| Indonésio (Indonésia)  |  `id-ID`  |  Masculino  |  `id-ID-Andika`|
| Italiano (Itália)  |  `it-IT`  |  Masculino  |  `it-IT-Cosimo`|
| Italiano (Itália)  |  `it-IT`  |  Feminino  |  `it-IT-LuciaRUS`|
| Japonês (Japão)  |  `ja-JP`  |  Feminino  |  `ja-JP-Ayumi`|
| Japonês (Japão)  |  `ja-JP`  |  Feminino  |  `ja-JP-HarukaRUS`|
| Japonês (Japão)  |  `ja-JP`  |  Masculino  |  `ja-JP-Ichiro`|
| Coreano (Coreia do Sul)  |  `ko-KR`  |  Feminino  |  `ko-KR-HeamiRUS`|
| Malaio (Malásia)  |  `ms-MY`  |  Masculino  |  `ms-MY-Rizwan`|
| Mandarim (chinês simplificado, China)  |  `zh-CN`  |  Feminino  |  `zh-CN-HuihuiRUS`|
| Mandarim (chinês simplificado, China)  |  `zh-CN`  |  Masculino  |  `zh-CN-Kangkang`|
| Mandarim (chinês simplificado, China)  |  `zh-CN`  |  Feminino  |  `zh-CN-Yaoyao`|
| Mandarim (chinês tradicional, Taiwan)  |  `zh-TW`  |  Feminino  |  `zh-TW-HanHanRUS`|
| Mandarim (chinês tradicional, Taiwan)  |  `zh-TW`  |  Feminino  |  `zh-TW-Yating`|
| Mandarim (chinês tradicional, Taiwan)  |  `zh-TW`  |  Masculino  |  `zh-TW-Zhiwei`|
| Norueguês, BokmÃ ¥ l (Noruega)  |  `nb-NO`  |  Feminino  |  `nb-NO-HuldaRUS`|
| Polonês (Polônia)  |  `pl-PL`  |  Feminino  |  `pl-PL-PaulinaRUS`|
| Português (Brasil)  |  `pt-BR`  |  Masculino  |  `pt-BR-Daniel`|
| Português (Brasil)  |  `pt-BR`  |  Feminino  |  `pt-BR-HeloisaRUS`|
| Português (Portugal)  |  `pt-PT`  |  Feminino  |  `pt-PT-HeliaRUS`|
| Romeno (Romênia)  |  `ro-RO`  |  Masculino  |  `ro-RO-Andrei`|
| Russo (Rússia)  |  `ru-RU`  |  Feminino  |  `ru-RU-EkaterinaRUS`|
| Russo (Rússia)  |  `ru-RU`  |  Feminino  |  `ru-RU-Irina`|
| Russo (Rússia)  |  `ru-RU`  |  Masculino  |  `ru-RU-Pavel`|
| Eslovaco (Eslováquia)  |  `sk-SK`  |  Masculino  |  `sk-SK-Filip`|
| Esloveno (Eslovênia)  |  `sl-SI`  |  Masculino  |  `sl-SI-Lado`|
| Espanhol (México)  |  `es-MX`  |  Feminino  |  `es-MX-HildaRUS`|
| Espanhol (México)  |  `es-MX`  |  Masculino  |  `es-MX-Raul`|
| Espanhol (Espanha)  |  `es-ES`  |  Feminino  |  `es-ES-HelenaRUS`|
| Espanhol (Espanha)  |  `es-ES`  |  Feminino  |  `es-ES-Laura`|
| Espanhol (Espanha)  |  `es-ES`  |  Masculino  |  `es-ES-Pablo`|
| Sueco (Suécia)  |  `sv-SE`  |  Feminino  |  `sv-SE-HedvigRUS`|
| Tâmil (Índia)  |  `ta-IN`  |  Masculino  |  `ta-IN-Valluvar`|
| Télugo (Índia)  |  `te-IN`  |  Feminino  |  `te-IN-Chitra`|
| Tailandês (Tailândia)  |  `th-TH`  |  Masculino  |  `th-TH-Pattara`|
| Turco (Turquia)  |  `tr-TR`  |  Feminino  |  `tr-TR-SedaRUS`|
| Vietnamita (Vietnã)  |  `vi-VN`  |  Masculino  |  `vi-VN-An`  |


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
| Klingon                 | `tlh-Latn`    |
| Klingon (plqaD)         | `tlh-Piqd`    |
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

| Linguagem | Localidade | Verificação dependente do texto | Verificação independente do texto | Identificação independente do texto |
|----|----|----|----|----|
|Inglês (EUA)  |  pt-BR  |  sim  |  sim  |  sim |
|Chinês (mandarim, simplificado) | zh-CN     |     n/d |     sim |     sim|
|Inglês (Austrália)     | en-AU     | n/d     | sim     | sim|
|Inglês (Canadá)     | en-CA     | n/d |     sim |     sim|
|Inglês (Reino Unido)     | en-GB     | n/d     | sim     | sim|
|Francês (Canadá)     | fr-CA     | n/d     | sim |     sim|
|Francês (França)     | fr-FR     | n/d     | sim     | sim|
|Alemão (Alemanha)     | de-DE     | n/d     | sim     | sim|
|Italiano | it-IT     |     n/d     | sim |     sim|
|Japonês     | ja-JP | n/d     | sim     | sim|
|Português (Brasil) | pt-BR |     n/d |     sim |     sim|
|Espanhol (México)     | es-MX     | n/d |     sim |     sim|
|Espanhol (Espanha)     | es-ES | n/d     | sim |     sim|

## <a name="next-steps"></a>Próximas etapas

* [Criar uma conta gratuita do Azure](https://azure.microsoft.com/free/cognitive-services/)
* [Veja como reconhecer fala em C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
