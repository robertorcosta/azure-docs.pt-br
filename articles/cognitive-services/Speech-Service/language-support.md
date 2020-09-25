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
ms.openlocfilehash: b50f6c4b2ed86b950e68cf8c55b63af687ff9ead
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91298042"
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

| Local  | Linguagem                          | Personalizações                                    |
|---------|-----------------------------------|---------------------------------------------------|
| `ar-AE` | Árabe (Emirados Árabes Unidos)     | Modelo de linguagem                                    |
| `ar-BH` | Árabe (Bahrein), padrão moderno | Modelo de linguagem                                    |
| `ar-EG` | Árabe (Egito)                    | Modelo de linguagem                                    |
| `ar-IL` | Árabe (Israel)                   | Não                                                |
| `ar-IQ` | Árabe (Iraque)                     | Modelo de linguagem                                    |
| `ar-JO` | Árabe (Jordânia)                   | Modelo de linguagem                                    |
| `ar-KW` | Árabe (Kuwait)                   | Modelo de linguagem                                    |
| `ar-LB` | Árabe (Líbano)                  | Modelo de linguagem                                    |
| `ar-OM` | Árabe (Omã)                     | Modelo de linguagem                                    |
| `ar-PS` | Árabe (estado de Palestina)       | Não                                                |
| `ar-QA` | Árabe (Catar)                    | Modelo de linguagem                                    |
| `ar-SA` | Árabe (Arábia Saudita)             | Modelo de linguagem                                    |
| `ar-SY` | Árabe (Síria)                    | Modelo de linguagem                                    |
| `bg-BG` | Búlgaro (Bulgária)              | Modelo de linguagem                                    |
| `ca-ES` | Catalão (Espanha)                   | Modelo de linguagem                                    |
| `cs-CZ` | Tcheco (República Tcheca)            | Modelo de Linguagem                                    | 
| `da-DK` | Dinamarquês (Dinamarca)                  | Modelo de linguagem                                    |
| `de-DE` | Alemão (Alemanha)                  | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `el-GR` | Grego (Grécia)                    | Modelo de linguagem                                    |
| `en-AU` | Inglês (Austrália)               | Modelo acústico<br>Modelo de linguagem                  |
| `en-CA` | Inglês (Canadá)                  | Modelo acústico<br>Modelo de linguagem                  |
| `en-GB` | Inglês (Reino Unido)          | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `en-HK` | Inglês (Hong Kong)               | Modelo de Linguagem                                    | 
| `en-IE` | Inglês (Irlanda)                 | Modelo de Linguagem                                    | 
| `en-IN` | Inglês (Índia)                   | Modelo acústico<br>Modelo de linguagem                  |
| `en-NZ` | Inglês (Nova Zelândia)             | Modelo acústico<br>Modelo de linguagem                  |
| `en-PH` | Inglês (Filipinas)             | Modelo de Linguagem                                    | 
| `en-SG` | Inglês (Singapura)               | Modelo de Linguagem                                    | 
| `en-US` | Inglês (Estados Unidos)           | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `en-ZA` | Inglês (África do Sul)            | Modelo de Linguagem                                    | 
| `es-AR` | Espanhol (Argentina)               | Modelo de Linguagem                                    | 
| `es-BO` | Espanhol (Bolívia)                 | Modelo de Linguagem                                    | 
| `es-CL` | Espanhol (Chile)                   | Modelo de Linguagem                                    | 
| `es-CO` | Espanhol (Colômbia)                | Modelo de Linguagem                                    | 
| `es-CR` | Espanhol (Costa Rica)              | Modelo de Linguagem                                    | 
| `es-CU` | Espanhol (Cuba)                    | Modelo de Linguagem                                    | 
| `es-DO` | Espanhol (República Dominicana)      | Modelo de Linguagem                                    | 
| `es-EC` | Espanhol (Equador)                 | Modelo de Linguagem                                    | 
| `es-ES` | Espanhol (Espanha)                   | Modelo acústico<br>Modelo de linguagem                  |
| `es-GT` | Espanhol (Guatemala)               | Modelo de Linguagem                                    | 
| `es-HN` | Espanhol (Honduras)                | Modelo de Linguagem                                    | 
| `es-MX` | Espanhol (México)                  | Modelo acústico<br>Modelo de linguagem                  |
| `es-NI` | Espanhol (Nicarágua)               | Modelo de Linguagem                                    | 
| `es-PA` | Espanhol (Panamá)                  | Modelo de Linguagem                                    | 
| `es-PE` | Espanhol (Peru)                    | Modelo de Linguagem                                    | 
| `es-PR` | Espanhol (Porto Rico)             | Modelo de Linguagem                                    | 
| `es-PY` | Espanhol (Paraguai)                | Modelo de Linguagem                                    | 
| `es-SV` | Espanhol (El Salvador)             | Modelo de Linguagem                                    | 
| `es-US` | Espanhol (EUA)                     | Modelo de Linguagem                                    | 
| `es-UY` | Espanhol (Uruguai)                 | Modelo de Linguagem                                    | 
| `es-VE` | Espanhol (Venezuela)               | Modelo de Linguagem                                    |
| `et-EE` | Estoniano (Estônia)                 | Modelo de Linguagem                                    | 
| `fi-FI` | Finlandês (Finlândia)                 | Modelo de linguagem                                    |
| `fr-CA` | Francês (Canadá)                   | Modelo acústico<br>Modelo de linguagem                  |
| `fr-FR` | Francês (França)                   | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `ga-IE` | Irlandês (Irlanda)                    | Modelo de linguagem                                    |
| `gu-IN` | Guzerate (Índia)                 | Modelo de linguagem                                    |
| `hi-IN` | Híndi (Índia)                     | Modelo acústico<br>Modelo de linguagem                  |
| `hr-HR` | Croata (Croácia)                | Modelo de linguagem                                    |
| `hu-HU` | Húngaro (Hungria)               | Modelo de Linguagem                                    | 
| `it-IT` | Italiano (Itália)                   | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `ja-JP` | Japonês (Japão)                  | Modelo de linguagem                                    |
| `ko-KR` | Coreano (Coreia do Sul)                    | Modelo de linguagem                                    |
| `lt-LT` | Lituano (Lituânia)            | Modelo de linguagem                                    |
| `lv-LV` | Letão (Letônia)                  | Modelo de linguagem                                    |
| `mr-IN` | Marati (Índia)                   | Modelo de linguagem                                    |
| `mt-MT` | Maltês (Malta)                    | Modelo de linguagem                                    |
| `nb-NO` | Norueguês (Bokmål) (Noruega)       | Modelo de linguagem                                    |
| `nl-NL` | Holandês (Países Baixos)               | Modelo de linguagem                                    |
| `pl-PL` | Polonês (Polônia)                   | Modelo de linguagem                                    |
| `pt-BR` | Português (Brasil)               | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `pt-PT` | Português (Portugal)             | Modelo de linguagem                                    |
| `ro-RO` | Romeno (Romênia)                | Modelo de linguagem                                    |
| `ru-RU` | Russo (Rússia)                  | Modelo acústico<br>Modelo de linguagem                  |
| `sk-SK` | Eslovaco (Eslováquia)                 | Modelo de linguagem                                    |
| `sl-SI` | Esloveno (Eslovênia)              | Modelo de linguagem                                    |
| `sv-SE` | Sueco (Suécia)                  | Modelo de linguagem                                    |
| `ta-IN` | Tâmil (Índia)                     | Modelo de linguagem                                    |
| `te-IN` | Télugo (Índia)                    | Modelo de linguagem                                    |
| `th-TH` | Tailandês (Tailândia)                   | Modelo de linguagem                                    |
| `tr-TR` | Turco (Turquia)                  | Modelo de linguagem                                    |
| `zh-CN` | Chinês (mandarim, simplificado)    | Modelo acústico<br>Modelo de linguagem                  |
| `zh-HK` | Chinês (Cantonês tradicional)  | Modelo de linguagem                                    |
| `zh-TW` | Chinês (Mandarim Taiwanês)      | Modelo de linguagem                                    |

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
| `ar-EG` | Árabe (Egito) | Feminino | `ar-EG-SalmaNeural` | Geral |
| `ar-SA` | Árabe (Arábia Saudita) | Feminino | `ar-SA-ZariyahNeural` | Geral |
| `bg-BG`<sup>Novo</sup> | Búlgaro (Bulgary) | Feminino | `bg-BG-KalinaNeural` | Geral |
| `ca-ES` | Catalão (Espanha) | Feminino | `ca-ES-AlbaNeural` | Geral |
| `cs-CZ`<sup>Novo</sup> | Tcheco (tcheco)  | Feminino | `cs-CZ-VlastaNeural` | Geral |
| `da-DK` | Dinamarquês (Dinamarca) | Feminino | `da-DK-ChristelNeural` | Geral |
| `de-AT`<sup>Novo</sup> | Alemão (Áustria) | Feminino | `de-AT-IngridNeural` | Geral |
| `de-CH`<sup>Novo</sup> | Alemão (Suíça) | Feminino | `de-CH-LeniNeural` | Geral |
| `de-DE` | Alemão (Alemanha) | Feminino | `de-DE-KatjaNeural` | Geral |
| `de-DE`<sup>Novo</sup> | Alemão (Alemanha) | Masculino | `de-DE-ConradNeural` | Geral |
| `el-GR`<sup>Novo</sup> | Grego (Grécia) | Feminino | `el-GR-AthinaNeural` | Geral |
| `en-AU` | Inglês (Austrália) | Feminino | `en-AU-NatashaNeural` | Geral |
| `en-AU`<sup>Novo</sup> | Australiano (Austrália) | Masculino | `en-AU-WilliamNeural` | Geral |
| `en-CA` | Inglês (Canadá) | Feminino | `en-CA-ClaraNeural` | Geral |
| `en-GB` | Inglês (Reino Unido) | Feminino | `en-GB-LibbyNeural` | Geral |
| `en-GB` | Inglês (Reino Unido) | Feminino | `en-GB-MiaNeural` | Geral |
| `en-GB`<sup>Novo</sup> | Inglês (Reino Unido) | Masculino | `En-GB-RyanNeural` | Geral |
| `en-IE`<sup>Novo</sup> | Inglês-Irlandês (Irlanda) | Feminino | `en-IE-EmilyNeural` | Geral |
| `en-IN` | Inglês (Índia) | Feminino | `en-IN-NeerjaNeural` | Geral |
| `en-US` | Inglês (Estados Unidos) | Feminino | `en-US-AriaNeural` | Geral, com vários estilos de voz disponíveis |
| `en-US` | Inglês (Estados Unidos) | Masculino | `en-US-GuyNeural` | Geral |
| `en-US`<sup>Novo</sup> | Inglês (Estados Unidos) | Feminino | `en-US-JennyNeural` | Geral, com vários estilos de voz disponíveis |
| `es-ES` | Espanhol (Espanha) | Feminino | `es-ES-ElviraNeural` | Geral |
| `es-ES`<sup>Novo</sup> | Espanhol (Espanha) | Masculino | `es-ES-AlvaroNeural` | Geral |
| `es-MX` | Espanhol (México) | Feminino | `es-MX-DaliaNeural` | Geral |
| `es-MX`<sup>Novo</sup> | Espanhol (México) | Masculino | `es-MX-JorgeNeural` | Geral |
| `fi-FI` | Finlandês (Finlândia) | Feminino | `fi-FI-NooraNeural` | Geral |
| `fr-CA` | Francês (Canadá) | Feminino | `fr-CA-SylvieNeural` | Geral |
| `fr-CA`<sup>Novo</sup> | Francês (Canadá) | Masculino | `fr-CA-JeanNeural` | Geral |
| `fr-CH`<sup>Novo</sup> | Francês (Suíça) | Feminino | `fr-CH-ArianeNeural` | Geral |
| `fr-FR` | Francês (França) | Feminino | `fr-FR-DeniseNeural` | Geral |
| `fr-FR`<sup>Novo</sup> | Francês (França) | Masculino | `fr-FR-HenriNeural` | Geral |
| `he-IL`<sup>Novo</sup> | Hebraico (isareal) | Feminino | `he-IL-HilaNeural` | Geral |
| `hi-IN` | Híndi (Índia) | Feminino | `hi-IN-SwaraNeural` | Geral |
| `hr-HR`<sup>Novo</sup> | Croata (Croácia) | Feminino | `hr-HR-GabrijelaNeural` | Geral |
| `hu-HU`<sup>Novo</sup> | Húngaro (Hungria) | Feminino | `hu-HU-NoemiNeural` | Geral |
| `id-ID`<sup>Novo</sup> | Bahasa Indonésio (Indonésia) | Masculino | `id-ID-ArdiNeural` | Geral |
| `it-IT` | Italiano (Itália) | Feminino | `it-IT-ElsaNeural` | Geral |
| `it-IT`<sup>Novo</sup> | Italiano (Itália) | Feminino | `it-IT-IsabellaNeural` | Geral |
| `it-IT`<sup>Novo</sup> | Italiano (Itália) | Masculino | `it-IT-DiegoNeural` | Geral |
| `ja-JP` | Japonês (Japão) | Feminino | `ja-JP-NanamiNeural` | Geral |
| `ja-JP`<sup>Novo</sup> | Japonês (Japão) | Masculino | `ja-JP-KeitaNeural` | Geral |
| `ko-KR` | Coreano (Coreia do Sul) | Feminino | `ko-KR-SunHiNeural` | Geral |
| `ko-KR`<sup>Novo</sup> | Coreano (Coreia do Sul) | Masculino | `ko-KR-InJoonNeural` | Geral |
| `ms-MY`<sup>Novo</sup> | Malaio (Malásia) | Feminino | `ms-MY-YasminNeural` | Geral |
| `nb-NO` | Norueguês, Bokmål (Noruega) | Feminino | `nb-NO-IselinNeural` | Geral |
| `nl-NL` | Holandês (Países Baixos) | Feminino | `nl-NL-ColetteNeural` | Geral |
| `pl-PL` | Polonês (Polônia) | Feminino | `pl-PL-ZofiaNeural` | Geral |
| `pt-BR` | Português (Brasil) | Feminino | `pt-BR-FranciscaNeural` | Geral, com vários estilos de voz disponíveis |
| `pt-BR`<sup>Novo</sup> | Português do Brasil (Brasil) | Masculino | `pt-BR-AntonioNeural` | Geral |
| `pt-PT` | Português (Portugal) | Feminino | `pt-PT-FernandaNeural` | Geral |
| `ro-RO`<sup>Novo</sup> | Romeno (Romênia) | Feminino | `ro-RO-AlinaNeural` | Geral |
| `ru-RU` | Russo (Rússia) | Feminino | `ru-RU-DariyaNeural` | Geral |
| `sk-SK`<sup>Novo</sup> | Eslovaco (Eslováquia) | Feminino | `sk-SK-ViktoriaNeural` | Geral |
| `sl-SI`<sup>Novo</sup> | Esloveno (Eslovênia) | Feminino | `sl-SI-PetraNeural` | Geral |
| `sv-SE` | Sueco (Suécia) | Feminino | `sv-SE-HilleviNeural` | Geral |
| `ta-IN`<sup>Novo</sup> | Tâmil (Índia) | Feminino | `ta-IN-PallaviNeural` | Geral |
| `te-IN`<sup>Novo</sup> | Télugo (Índia) | Feminino | `te-IN-ShrutiNeural` | Geral |
| `th-TH` | Tailandês (Tailândia) | Feminino | `th-TH-AcharaNeural` | Geral |
| `th-TH`<sup>Novo</sup> | Tailandês (Tailândia) | Feminino | `th-TH-PremwadeeNeural` | Geral |
| `tr-TR` | Turco (Turquia) | Feminino | `tr-TR-EmelNeural` | Geral |
| `vi-VN`<sup>Novo</sup> | Vietnamita (Vietnã) | Feminino | `vi-VN-HoaiMyNeural` | Geral |
| `zh-CN` | Mandarim (chinês simplificado, China) | Feminino | `zh-CN-XiaoxiaoNeural` | Geral, com vários estilos de voz disponíveis |
| `zh-CN` | Mandarim (chinês simplificado, China) | Feminino | `zh-CN-XiaoyouNeural` | Voz de criança, otimização para narração de histórias |
| `zh-CN` | Mandarim (chinês simplificado, China) | Masculino | `zh-CN-YunyangNeural` | Otimizado para leitura de notícias, vários estilos de voz disponíveis |
| `zh-CN` | Mandarim (chinês simplificado, China) | Masculino | `zh-CN-YunyeNeural` | Otimização para narração de histórias |
| `zh-HK` | Cantonês (chinês tradicional, Hong Kong) | Feminino | `zh-HK-HiuGaaiNeural` | Geral |
| `zh-TW` | Mandarim (chinês tradicional, Taiwan) | Feminino | `zh-TW-HsiaoYuNeural` | Geral |


> [!IMPORTANT]
> A voz `en-US-JessaNeural` foi alterada para `en-US-AriaNeural`. Se você usava a voz "Jessa", faça a conversão para "Aria".

Para saber como você pode configurar e ajustar as vozes neurais, confira [Linguagem de marcação para síntese de fala](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> Você poderá continuar a usar o mapeamento de nome de serviço completo como "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)" em suas solicitações de síntese de fala.

### <a name="standard-voices"></a>Vozes padrão

Mais de 75 vozes padrão estão disponíveis em mais de 45 idiomas e localidades, que permitem converter texto em fala sintetizada. Para obter mais informações sobre a disponibilidade regional, consulte [regiões](regions.md#standard-and-neural-voices).

| Local | Linguagem | Gênero | Nome da voz |
|--|--|--|--|
| `ar-EG` | Árabe (árabe) | Feminino | `ar-EG-Hoda` |
| `ar-SA` | Árabe (Arábia Saudita) | Masculino | `ar-SA-Naayf` |
| `bg-BG` | Búlgaro (Bulgária) | Masculino | `bg-BG-Ivan` |
| `ca-ES` | Catalão (Espanha) | Feminino | `ca-ES-HerenaRUS` |
| `cs-CZ` | Tcheco (República Tcheca) | Masculino | `cs-CZ-Jakub` |
| `da-DK` | Dinamarquês (Dinamarca) | Feminino | `da-DK-HelleRUS` |
| `de-AT` | Alemão (Áustria) | Masculino | `de-AT-Michael` |
| `de-CH` | Alemão (Suíça) | Masculino | `de-CH-Karsten` |
| `de-DE` | Alemão (Alemanha) | Feminino | `de-DE-HeddaRUS` |
| `de-DE` | Alemão (Alemanha) | Masculino | `de-DE-Stefan` |
| `el-GR` | Grego (Grécia) | Masculino | `el-GR-Stefanos` |
| `en-AU` | Inglês (Austrália) | Feminino | `en-AU-Catherine` |
| `en-AU` | Inglês (Austrália) | Feminino | `en-AU-HayleyRUS` |
| `en-CA` | Inglês (Canadá) | Feminino | `en-CA-HeatherRUS` |
| `en-CA` | Inglês (Canadá) | Feminino | `en-CA-Linda` |
| `en-GB` | Inglês (Reino Unido) | Masculino | `en-GB-George` |
| `en-GB` | Inglês (Reino Unido) | Feminino | `en-GB-HazelRUS` |
| `en-GB` | Inglês (Reino Unido) | Feminino | `en-GB-Susan` |
| `en-IE` | Inglês (Irlanda) | Masculino | `en-IE-Sean` |
| `en-IN` | Inglês (Índia) | Feminino | `en-IN-Heera` |
| `en-IN` | Inglês (Índia) | Feminino | `en-IN-PriyaRUS` |
| `en-IN` | Inglês (Índia) | Masculino | `en-IN-Ravi` |
| `en-US` | Inglês (Estados Unidos) | Masculino | `en-US-BenjaminRUS` |
| `en-US` | Inglês (Estados Unidos) | Masculino | `en-US-GuyRUS` |
| `en-US` | Inglês (Estados Unidos) | Feminino | `en-US-JessaRUS` |
| `en-US` | Inglês (Estados Unidos) | Feminino | `en-US-ZiraRUS` |
| `es-ES` | Espanhol (Espanha) | Feminino | `es-ES-HelenaRUS` |
| `es-ES` | Espanhol (Espanha) | Feminino | `es-ES-Laura` |
| `es-ES` | Espanhol (Espanha) | Masculino | `es-ES-Pablo` |
| `es-MX` | Espanhol (México) | Feminino | `es-MX-HildaRUS` |
| `es-MX` | Espanhol (México) | Masculino | `es-MX-Raul` |
| `fi-FI` | Finlandês (Finlândia) | Feminino | `fi-FI-HeidiRUS` |
| `fr-CA` | Francês (Canadá) | Feminino | `fr-CA-Caroline` |
| `fr-CA` | Francês (Canadá) | Feminino | `fr-CA-HarmonieRUS` |
| `fr-CH` | Francês (Suíça) | Masculino | `fr-CH-Guillaume` |
| `fr-FR` | Francês (França) | Feminino | `fr-FR-HortenseRUS` |
| `fr-FR` | Francês (França) | Feminino | `fr-FR-Julie` |
| `fr-FR` | Francês (França) | Masculino | `fr-FR-Paul` |
| `he-IL` | Hebraico (Israel) | Masculino | `he-IL-Asaf` |
| `hi-IN` | Híndi (Índia) | Masculino | `hi-IN-Hemant` |
| `hi-IN` | Híndi (Índia) | Feminino | `hi-IN-Kalpana` |
| `hr-HR` | Croata (Croácia) | Masculino | `hr-HR-Matej` |
| `hu-HU` | Húngaro (Hungria) | Masculino | `hu-HU-Szabolcs` |
| `id-ID` | Indonésio (Indonésia) | Masculino | `id-ID-Andika` |
| `it-IT` | Italiano (Itália) | Masculino | `it-IT-Cosimo` |
| `it-IT` | Italiano (Itália) | Feminino | `it-IT-LuciaRUS` |
| `ja-JP` | Japonês (Japão) | Feminino | `ja-JP-Ayumi` |
| `ja-JP` | Japonês (Japão) | Feminino | `ja-JP-HarukaRUS` |
| `ja-JP` | Japonês (Japão) | Masculino | `ja-JP-Ichiro` |
| `ko-KR` | Coreano (Coreia do Sul) | Feminino | `ko-KR-HeamiRUS` |
| `ms-MY` | Malaio (Malásia) | Masculino | `ms-MY-Rizwan` |
| `nb-NO` | Norueguês, Bokmål (Noruega) | Feminino | `nb-NO-HuldaRUS` |
| `nl-NL` | Holandês (Países Baixos) | Feminino | `nl-NL-HannaRUS` |
| `pl-PL` | Polonês (Polônia) | Feminino | `pl-PL-PaulinaRUS` |
| `pt-BR` | Português (Brasil) | Masculino | `pt-BR-Daniel` |
| `pt-BR` | Português (Brasil) | Feminino | `pt-BR-HeloisaRUS` |
| `pt-PT` | Português (Portugal) | Feminino | `pt-PT-HeliaRUS` |
| `ro-RO` | Romeno (Romênia) | Masculino | `ro-RO-Andrei` |
| `ru-RU` | Russo (Rússia) | Feminino | `ru-RU-EkaterinaRUS` |
| `ru-RU` | Russo (Rússia) | Feminino | `ru-RU-Irina` |
| `ru-RU` | Russo (Rússia) | Masculino | `ru-RU-Pavel` |
| `sk-SK` | Eslovaco (Eslováquia) | Masculino | `sk-SK-Filip` |
| `sl-SI` | Esloveno (Eslovênia) | Masculino | `sl-SI-Lado` |
| `sv-SE` | Sueco (Suécia) | Feminino | `sv-SE-HedvigRUS` |
| `ta-IN` | Tâmil (Índia) | Masculino | `ta-IN-Valluvar` |
| `te-IN` | Télugo (Índia) | Feminino | `te-IN-Chitra` |
| `th-TH` | Tailandês (Tailândia) | Masculino | `th-TH-Pattara` |
| `tr-TR` | Turco (Turquia) | Feminino | `tr-TR-SedaRUS` |
| `vi-VN` | Vietnamita (Vietnã) | Masculino | `vi-VN-An` |
| `zh-CN` | Mandarim (chinês simplificado, China) | Feminino | `zh-CN-HuihuiRUS` |
| `zh-CN` | Mandarim (chinês simplificado, China) | Masculino | `zh-CN-Kangkang` |
| `zh-CN` | Mandarim (chinês simplificado, China) | Feminino | `zh-CN-Yaoyao` |
| `zh-HK` | Cantonês (chinês tradicional, Hong Kong) | Masculino | `zh-HK-Danny` |
| `zh-HK` | Cantonês (chinês tradicional, Hong Kong) | Feminino | `zh-HK-TracyRUS` |
| `zh-TW` | Mandarim (chinês tradicional, Taiwan) | Feminino | `zh-TW-HanHanRUS` |
| `zh-TW` | Mandarim (chinês tradicional, Taiwan) | Feminino | `zh-TW-Yating` |
| `zh-TW` | Mandarim (chinês tradicional, Taiwan) | Masculino | `zh-TW-Zhiwei` |

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

| Local | Linguagem | Verificação dependente do texto | Verificação independente do texto | Identificação independente do texto |
|----|----|----|----|----|
| pt-BR | Inglês (EUA) | sim | sim | sim |
|zh-CN    |Chinês (mandarim, simplificado)|    n/d|    sim|    sim|
|de-DE    |Alemão (Alemanha)    |n/d    |sim    |sim|
|en-GB    |Inglês (Reino Unido)    |n/d    |sim    |sim|
|fr-FR    |Francês (França)    |n/d    |sim    |sim|
|en-AU    |Inglês (Austrália)    |n/d    |sim    |sim|
|en-CA    |Inglês (Canadá)    |n/d|    sim|    sim|
|fr-CA    |Francês (Canadá)    |n/d    |sim|    sim|
|it-IT    |Italiano|    n/d    |sim|    sim|
|es-ES|    Espanhol (Espanha)    |n/d    |sim|    sim|
|es-MX    |Espanhol (México)    |n/d|    sim|    sim|
|ja-JP|    Japonês    |n/d    |sim    |sim|
|pt-BR|    Português (Brasil)|    n/d|    sim|    sim|

## <a name="next-steps"></a>Próximas etapas

* [Criar uma conta gratuita do Azure](https://azure.microsoft.com/free/cognitive-services/)
* [Veja como reconhecer fala em C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
