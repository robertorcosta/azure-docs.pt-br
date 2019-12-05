---
title: Suporte a idiomas-serviço de fala
titleSuffix: Azure Cognitive Services
description: O serviço de fala dá suporte a vários idiomas para conversão de fala em texto e de texto em fala, juntamente com a tradução de fala. Este artigo fornece uma lista abrangente de suporte de idioma por recurso de serviço.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 8db70ccbabfc165dbe3f7b9a61dbe5023a87f708
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815374"
---
# <a name="language-and-region-support-for-the-speech-service"></a>Suporte a idiomas e regiões para o serviço de fala

O suporte a idiomas varia de acordo com a funcionalidade do serviço de fala. As tabelas a seguir resumem o suporte a idiomas para ofertas de [voz para texto](#speech-to-text), conversão de [texto em fala](#text-to-speech)e serviço de [tradução de fala](#speech-translation) .

## <a name="speech-to-text"></a>Conversão de fala em texto

O SDK do Microsoft Speech e a API REST dão suporte aos seguintes idiomas (localidades). Para melhorar a precisão, a personalização é oferecida para um subconjunto das linguagens por meio do carregamento de áudio + transcrições com rótulo humano ou texto relacionado: frases.  Atualmente, a personalização de pronúncia está disponível apenas para `en-US` e `de-DE`. Saiba mais sobre a personalização [aqui](how-to-custom-speech.md).

 Localidade | Idioma | Com suporte | Personalizável
------|------------|-----------|-------------
`ar-EG` | Árabe (Egito), padrão moderno | SIM | SIM
`ar-SA` | Árabe (Arábia Saudita) | SIM | SIM
`ar-AE` | Árabe (dos EAU) | SIM | SIM
`ar-KW` | Árabe (Kuwait) | SIM | SIM
`ar-QA` | Árabe (Catar) | SIM | SIM
`ca-ES` | Catalão | SIM | Não
`da-DK` | Dinamarquês (Dinamarca) | SIM | Não
`de-DE` | Alemão (Alemanha) | SIM | SIM
`en-AU` | Inglês (Austrália) | SIM | SIM
`en-CA` | Inglês (Canadá) | SIM | SIM
`en-GB` | Inglês (Reino Unido) | SIM | SIM
`en-IN` | English (India) | SIM | SIM
`en-NZ` | Inglês (Nova Zelândia) | SIM | SIM
`en-US` | Inglês (Estados Unidos) | SIM | SIM
`es-ES` | Espanhol (Espanha) | SIM | SIM
`es-MX` | Espanhol (México) | SIM | SIM
`fi-FI` | Finlandês (Finlândia) | SIM | Não
`fr-CA` | Francês (Canadá) | SIM | SIM
`fr-FR` | Francês (França) | SIM | SIM
`gu-IN` | Guzerate (indiano) | SIM | SIM
`hi-IN` | Hindi (Índia) | SIM | SIM
`it-IT` | Italiano (Itália) | SIM | SIM
`ja-JP` | Japonês (Japão) | SIM | SIM
`ko-KR` | Coreano (Coreia) | SIM | SIM
`mr-IN` | Marati (Índia) | SIM | SIM
`nb-NO` | Norueguês (Bokmål) (Noruega) | SIM | Não
`nl-NL` | Holandês (Países Baixos) | SIM | SIM
`pl-PL` | Polonês (Polônia) | SIM | Não
`pt-BR` | Português (Brasil) | SIM | SIM
`pt-PT` | Português (Portugal) | SIM | SIM
`ru-RU` | Russo (Rússia) | SIM | SIM
`sv-SE` | Sueco (Suécia) | SIM | Não
`ta-IN` | Tâmil (Índia) | SIM | SIM
`te-IN` | Télugo (Índia) | SIM | SIM
`zh-CN` | Chinês (mandarim, simplificado) | SIM | SIM
`zh-HK` | Chinês (Cantonês, tradicional) | SIM | SIM
`zh-TW` | Chinês (Mandarim Taiwanês) | SIM | SIM
`th-TH` | Tailandês (Tailândia) | SIM | Não
`tr-TR` | Turquia | SIM | SIM

## <a name="text-to-speech"></a>Conversão de texto em fala

O SDK do Microsoft Speech e as APIs REST dão suporte a essas vozes, cada uma delas com suporte a um idioma e dialeto específicos, identificados pela localidade.

> [!IMPORTANT]
> Os preços variam para vozes padrão, personalizadas e neurais. Visite a página [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para obter mais informações.

### <a name="neural-voices"></a>Vozes neurais

A conversão de texto em fala neural é um novo tipo de sintetização de fala alimentado por redes neurais profundas. Ao usar uma voz neural, a fala sintetizada é quase indistinguível de gravações de humana.

As vozes neurais podem ser usadas para fazer interações com os assistentes de voz e chatbots mais naturais e envolventes, converter textos digitais, como livros eletrônicos, em Audiobooks e aprimorar os sistemas de navegação no carro. Com a prosódia natural semelhante à humana e a articulação clara das palavras, as vozes neurais reduzem significativamente a fadiga de escuta quando os usuários interagem com sistemas de inteligência artificial.

Para obter uma lista completa de vozes neurais e a disponibilidade regional, consulte [regiões](regions.md#standard-and-neural-voices).

Localidade | Idioma | Sexo | Mapeamento de nome de serviço completo | Nome curto da voz
--------|----------|--------|---------|------------
`de-DE` | Alemão (Alemanha) | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (de-DE, KatjaNeural)" | "de-DE-KatjaNeural"
`en-US` | English (US) | Masculino | "Conversão de Texto em Fala do Microsoft Server (en-US, GuyNeural)" | "en-US-GuyNeural"
`en-US` | English (US) | Feminino | "Conversão de Texto em Fala do Microsoft Server (en-US, JessaNeural)" | "en-US-JessaNeural"
`it-IT` | Italiano (Itália) | Feminino |"Microsoft Server Speech Conversão de Texto em Fala Voice (IT-IT, ElsaNeural)" | "IT-IT-ElsaNeural"
`zh-CN` | Chinês (continente) | Feminino | "Voz para Conversão de Texto em Fala de Fala do Microsoft Server (zh-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

> [!NOTE]
> Você pode usar o mapeamento de nome de serviço completo ou o nome de voz curto em suas solicitações de síntese de fala.

### <a name="standard-voices"></a>Vozes padrão

Mais de 75 vozes padrão estão disponíveis em mais de 45 idiomas e localidades, que permitem converter texto em fala sintetizada. Para obter mais informações sobre a disponibilidade regional, consulte [regiões](regions.md#standard-and-neural-voices).

Localidade | Idioma | Sexo | Mapeamento de nome de serviço completo | Nome curto
-------|----------|---------|----------|----------
<sup>&dagger;</sup>`ar-EG` | Árabe (Egito) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ar-EG, Hoda)” | "ar-ex-Hoda"
`ar-SA` | Árabe (Arábia Saudita) | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ar-SA, Naayf)” | "ar-SA-Naayf"
`bg-BG` | Búlgaro | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (bg-BG, Ivan)” | "bg-BG-Ivan"
`ca-ES` | Catalão (Espanha) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ca-ES, HerenaRUS)” | "ca-ES-HerenaRUS"
`cs-CZ` | Tcheco | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (cs-CZ, Jakub)” | "CS-CZ-Jakub"
`da-DK` | Dinamarquês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (da-DK, HelleRUS)” | "da-DK-HelleRUS"
`de-AT` | Alemão (Áustria) | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-AT, Michael)” | "de a Michael"
`de-CH` | Alemão (Suíça) | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-CH, Karsten)” | "de-CH-Karsten"
`de-DE` | Alemão (Alemanha) | Feminino | "Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-DE, Hedda)" | "de-DE-Hedda"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-DE, HeddaRUS)” | "de-DE-HeddaRUS"
| | | Masculino | "Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-DE, Stefan, Apollo)" | "de-DE-Rodrigo-Apollo"
`el-GR` | Grego | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (el-GR, Stefanos)” | "El-GR-Stefanos"
`en-AU` | Inglês (Austrália) | Feminino | "Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-AU, Catherine)" | "en-AU-Catherine"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-AU, HayleyRUS)” | "en-AU-HayleyRUS"
`en-CA` | Inglês (Canadá) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-CA, Linda)” | "en-CA-linda"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-CA, HeatherRUS)” | "en-CA-HeatherRUS"
`en-GB` | English (UK) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-GB, Susan, Apollo)” | "en-GB-Susan-Apollo"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-GB, HazelRUS)” | "en-GB-HazelRUS"
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-GB, George, Apollo)” | "en-GB-George-Apollo"
`en-IE` | Inglês (Irlanda) | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-IE, Sean)” | "en-IE-Sílvio"
`en-IN` | English (India) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-IN, Heera, Apollo)” | "en-IN-Heera-Apollo"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-IN, PriyaRUS)” | "en-IN-PriyaRUS"
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-IN, Ravi, Apollo)” | "en-IN-Ravi-Apollo"
`en-US` | English (US) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-US, ZiraRUS)” | "en-US-ZiraRUS"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-US, JessaRUS)” | "en-US-JessaRUS"
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-US, BenjaminRUS)” | "en-US-BenjaminRUS"
| | | Feminino | "Conversão de Texto em Fala do Microsoft Server (en-US, Jessa24kRUS)" | "en-US-Jessa24kRUS"
| | | Masculino | "Conversão de Texto em Fala do Microsoft Server (en-US, Guy24kRUS)" | "en-US-Guy24kRUS"
`es-ES` | Espanhol (Espanha) |Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-ES, Laura, Apollo)” | "es-ES-Laura-Apollo"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-ES, HelenaRUS)” | "es-ES-HelenaRUS"
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-ES, Pablo, Apollo)” | "es-ES-Pablo-Apollo"
`es-MX` | Espanhol (México) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-MX, HildaRUS)” | "es-MX-HildaRUS"
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-MX, Raul, Apollo)” | "es-MX-Ricardo-Apollo"
`fi-FI` | Finlandês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fi-FI, HeidiRUS)” | "fi-FI-HeidiRUS"
`fr-CA` | Francês (Canadá) |Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-CA, Caroline)” | "fr-CA-Carolina"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-CA, HarmonieRUS)” | "fr-CA-HarmonieRUS"
`fr-CH` | Francês (Suíça)| Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-CH, Guillaume)” | "fr-CH-Guillaume"
`fr-FR` | Francês (França)| Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-FR, Julie, Apollo)” | "fr-FR-Julie-Apollo"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-FR, HortenseRUS)” | "fr-FR-HortenseRUS"
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-FR, Paul, Apollo)” | "fr-FR-Paul-Apollo"
`he-IL` | Hebraico (Israel) | Masculino| “Voz da Conversão de Texto em Fala do Microsoft Server Speech (he-IL, Asaf)” | "he-IL-Asaf"
`hi-IN` | Hindi (Índia) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hi-IN, Kalpana, Apollo)” | "hi-IN-Kalpana-Apollo"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hi-IN, Kalpana)” | "hi-IN-Kalpana"
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hi-IN, Hemant)” | "hi-IN-Hemant"
`hr-HR` | Croata | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hr-HR, Matej)” | "HR-HR-Matej"
`hu-HU` | Húngaro | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hu-HU, Szabolcs)” | "hu-HU-Szabolcs"
`id-ID` | Indonésio| Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (id-ID, Andika)” | "ID-ID-andika"
`it-IT` | Italiano | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (it-IT, Cosimo, Apollo)” | "IT-IT-Cosimo-Apollo"
| | | Feminino | "Conversão de Texto em Fala do Microsoft Server (it-IT, LuciaRUS)" | "IT-IT-LuciaRUS"
`ja-JP` | Japonês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ja-JP, Ayumi, Apollo)” | "ja-JP-Ayumi-Apollo"
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ja-JP, Ichiro, Apollo)” | "ja-JP-Ichiro-Apollo"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ja-JP, HarukaRUS)” | "ja-JP-HarukaRUS"
`ko-KR` | Coreano | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ko-KR, HeamiRUS)” | "ko-KR-HeamiRUS"
`ms-MY` | Malaio | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ms-MY, Rizwan)” | "MS-MY-Rizwan"
`nb-NO` | Norueguês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (nb-NO, HuldaRUS)” | "NB-NO-HuldaRUS"
`nl-NL` | Holandês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (nl-NL, HannaRUS)” | "nl-NL-HannaRUS"
`pl-PL` | Polonês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (pl-PL, PaulinaRUS)” | "pl-PL-PaulinaRUS"
`pt-BR` | Português (Brasil) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (pt-BR, HeloisaRUS)” | "pt-BR-HeloisaRUS"
| | | Masculino |“Voz da Conversão de Texto em Fala do Microsoft Server Speech (pt-BR, Daniel, Apollo)” | "pt-BR-Daniel-Apollo"
`pt-PT` | Português (Portugal) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (pt-PT, HeliaRUS)” | "pt-PT-HeliaRUS"
`ro-RO` | Romeno | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ro-RO, Andrei)” | "RO-RO-Andrei"
`ru-RU` |Russo| Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ru-RU, Irina, Apollo)” | "ru-RU-Irina-Apollo"
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ru-RU, Pavel, Apollo)” | "ru-RU-Pavel-Apollo"
| | | Feminino | "Conversão de Texto em Fala do Microsoft Server (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS
`sk-SK` | Eslovaco | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (sk-SK, Filip)” | "SK-SK-Filip"
`sl-SI` | Esloveno | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (sl-SI, Lado)” | "sl-SI-Lado"
`sv-SE` | Sueco | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (sv-SE, HedvigRUS)” | "sv-SE-HedvigRUS"
`ta-IN` | Tâmil (Índia) | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ta-IN, Valluvar)” | "ta-IN-Valluvar"
`te-IN` | Télugo (Índia) | Feminino | "Voz da Conversão de Texto em Fala do Microsoft Server Speech (te-IN, Chitra)" | "Chitra"
`th-TH` | Tailandês | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (th-TH, Pattara)” | "th-TH-Pattara"
`tr-TR` | Turco | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (tr-TR, SedaRUS)” | "tr-TR-SedaRUS"
`vi-VN` | Vietnamita | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (vi-VN, An)” | "vi-VN-um"
`zh-CN` | Chinês (continente) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-CN, HuihuiRUS)” | "zh-CN-HuihuiRUS"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-CN, Yaoyao, Apollo)” | "zh-CN-Yaoyao-Apollo"
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-CN, Kangkang, Apollo)” | "zh-CN-Kangkang-Apollo"
`zh-HK` | Chinês (Hong Kong) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-HK, Tracy, Apollo)” | "ZH-HK-Tracy-Apollo"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-HK, TracyRUS)” | "ZH-HK-TracyRUS"
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-HK, Danny, Apollo)” | "ZH-HK-Danny-Apollo"
`zh-TW` | Chinês (Taiwan) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-TW, Yating, Apollo)” | "zh-TW-Yating-Apollo"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-TW, HanHanRUS)” | "zh-TW-HanHanRUS"
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-TW, Zhiwei, Apollo)” | "zh-TW-Zhiwei-Apollo"

&dagger; *ar-EG é compatível com MSA (árabe padrão moderno).*

> [!NOTE]
> Você pode usar o mapeamento de nome de serviço completo ou o nome de voz curto em suas solicitações de síntese de fala.

### <a name="customization"></a>Personalização

A personalização de voz está disponível para `de-DE`, `en-GB`, `en-IN`, `en-US`, `es-MX`, `fr-FR`, `it-IT`, `pt-BR`e `zh-CN`. Selecione a localidade correta que corresponde aos dados de treinamento que você tem para treinar um modelo de voz personalizado. Por exemplo, se os dados de gravação que você tem são falados em inglês com um sotaque britânico, selecione `en-GB`.

> [!NOTE]
> Não damos suporte ao treinamento de modelo de bi-idiomas em voz personalizada, exceto para o Português do idioma chinês-inglês. Selecione "chinês-inglês bilíngüe" se você quiser treinar uma voz chinesa que também pode falar em inglês. O treinamento de voz em todas as localidades começa com um conjunto de dados de 2.000 + declarações, exceto pelo `en-US` e `zh-CN` em que você pode começar com qualquer tamanho de dados de treinamento.

## <a name="speech-translation"></a>Tradução de fala

A API de **Tradução de Fala** é compatível com diferentes idiomas para conversão de fala em fala e de fala em texto. O idioma de origem sempre deve ser da tabela de linguagem de fala para texto. Os idiomas de destino disponíveis dependem de se o destino de tradução é fala ou texto. Você pode traduzir a entrada de fala em mais de [60 idiomas](https://www.microsoft.com/translator/business/languages/). Um subconjunto de idiomas está disponível para [síntese de fala](language-support.md#text-languages).

### <a name="text-languages"></a>Idiomas de texto

| Idioma de texto    | Código de idioma |
|:----------- |:-------------:|
| Africâner      | `af`          |
| Árabe       | `ar`          |
| Bangla      | `bn`          |
| Bósnio (latim)      | `bs`          |
| Búlgaro      | `bg`          |
| Cantonês (tradicional)      | `yue`          |
| Catalão      | `ca`          |
| Chinês (simplificado)      | `zh-Hans`          |
| Chinês (tradicional)      | `zh-Hant`          |
| Croata      | `hr`          |
| Tcheco      | `cs`          |
| Dinamarquês      | `da`          |
| Holandês      | `nl`          |
| Inglês      | `en`          |
| Estoniano      | `et`          |
| Fijiano      | `fj`          |
| Filipino      | `fil`          |
| Finlandês      | `fi`          |
| Francês      | `fr`          |
| Alemão      | `de`          |
| Grego      | `el`          |
| Crioulo haitiano      | `ht`          |
| Hebraico      | `he`          |
| Híndi      | `hi`          |
| Hmong Daw      | `mww`          |
| Húngaro      | `hu`          |
| Indonésio      | `id`          |
| Italiano      | `it`          |
| Japonês      | `ja`          |
| Suaíli      | `sw`          |
| Klingon      | `tlh`          |
| Klingon (plqaD)      | `tlh-Qaak`          |
| Coreano      | `ko`          |
| Letão      | `lv`          |
| Lituano      | `lt`          |
| Malgaxe      | `mg`          |
| Malaio      | `ms`          |
| Maltês      | `mt`          |
| Norueguês      | `nb`          |
| Persa      | `fa`          |
| Polonês      | `pl`          |
| Português      | `pt`          |
| Queretaro Otomi      | `otq`          |
| Romeno      | `ro`          |
| Russo      | `ru`          |
| Samoano      | `sm`          |
| Sérvio (cirílico)      | `sr-Cyrl`          |
| Sérvio (latino)      | `sr-Latn`          |
| Eslovaco     | `sk`          |
| Esloveno      | `sl`          |
| Espanhol      | `es`          |
| Sueco      | `sv`          |
| Taitiano      | `ty`          |
| Tâmil      | `ta`          |
| Télugo      | `te`          |
| Tailandês      | `th`          |
| Tongan      | `to`          |
| Turco      | `tr`          |
| Ucraniano      | `uk`          |
| Urdu      | `ur`          |
| Vietnamita      | `vi`          |
| Galês      | `cy`          |
| Yucatec Maya      | `yua`          |


## <a name="next-steps"></a>Próximos passos

* [Obtenha sua assinatura de avaliação do serviço de fala](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como reconhecer fala em C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
