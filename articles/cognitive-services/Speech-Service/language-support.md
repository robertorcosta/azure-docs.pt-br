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
ms.date: 11/04/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 26cab7ba3ed864382ae5511755fee09c3826702c
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580173"
---
# <a name="language-and-region-support-for-the-speech-services"></a>Suporte a idiomas e regiões para os serviços de fala

Há suporte para diferentes idiomas para diferentes funções dos Serviços de Fala. As tabelas a seguir resumem o suporte ao idioma.

## <a name="speech-to-text"></a>Conversão de fala em texto

O SDK do Microsoft Speech e a API REST dão suporte aos seguintes idiomas (localidades). Para melhorar a precisão, a personalização é oferecida para um subconjunto das linguagens por meio do carregamento de áudio + transcrições com rótulo humano ou texto relacionado: frases.  Atualmente, a personalização de pronúncia está disponível apenas para en-US e de-DE. Saiba mais sobre a personalização [aqui](how-to-custom-speech.md).

  Localidade | idioma | Suportado | Personalizável | Suporte a contêiner
 ------|------------|-----------|--------------|--------------
 ar-EG | Árabe (Egito), padrão moderno | ✔️ | ✔️ | ✔️
 ar-SA | Árabe (Arábia Saudita) | ✔️ | ✔️ | ❌
 ar-AE | Árabe (dos EAU) | ✔️ | ✔️ | ❌
 ar-KW | Árabe (Kuwait) | ✔️ | ✔️ | ❌
 ar-QA | Árabe (catar) | ✔️ | ✔️ | ❌
 ca-ES | Catalão | ✔️ | ❌ | ✔️
 da-DK | Dinamarquês (Dinamarca) | ✔️ | ❌ | ✔️
 de-DE | Alemão (Alemanha) | ✔️ | ✔️ | ✔️
 en-AU | Inglês (Austrália) | ✔️ | ✔️ | ✔️
 en-CA | Inglês (Canadá) | ✔️ | ✔️ | ✔️
 en-GB | Inglês (Reino Unido) | ✔️ | ✔️ | ✔️
 en-IN | Inglês (Índia) | ✔️ | ✔️ | ✔️
 en-NZ | Inglês (Nova Zelândia) | ✔️ | ✔️ | ✔️
 en-US | Inglês (Estados Unidos) | ✔️ | ✔️ | ✔️
 es-ES | Espanhol (Espanha) | ✔️ | ✔️ | ✔️
 es-MX | Espanhol (México) | ✔️ | ✔️ | ✔️
 fi-FI | Finlandês (Finlândia) | ✔️ | ❌ | ✔️
 fr-CA | Francês (Canadá) | ✔️ | ✔️ | ✔️
 fr-FR | Francês (França) | ✔️ | ✔️ | ✔️
 gu-IN | Guzerate (indiano) | ✔️ | ✔️ | ❌
 hi-IN | Hindi (Índia) | ✔️ | ✔️ | ✔️
 it-IT | Italiano (Itália) | ✔️ | ✔️ | ✔️
 ja-JP | Japonês (Japão) | ✔️ | ✔️ | ✔️
 ko-KR | Coreano (Coreia) | ✔️ | ✔️ | ✔️
 Mr-IN | Marati (Índia) | ✔️ | ✔️ | ❌
 nb-NO | Norueguês (Bokmål) (Noruega) | ✔️ | ❌ | ✔️
 nl-NL | Holandês (Países Baixos) | ✔️ | ✔️ | ✔️
 pl-PL | Polonês (Polônia) | ✔️ | ❌ | ✔️
 pt-BR | Português (Brasil) | ✔️ | ✔️ | ✔️
 pt-PT | Português (Portugal) | ✔️ | ✔️ | ✔️
 ru-RU | Russo (Rússia) | ✔️ | ✔️ | ✔️
 sv-SE | Sueco (Suécia) | ✔️ | ❌ | ✔️
 ta-IN | Tâmil (Índia) | ✔️ | ✔️ | ❌
 te-IN | Télugo (Índia) | ✔️ | ✔️ | ❌
 zh-CN | Chinês (mandarim, simplificado) | ✔️ | ✔️ | ✔️
 zh-HK | Chinês (Cantonês, tradicional) | ✔️ | ✔️ | ✔️
 zh-TW | Chinês (Mandarim Taiwanês) | ✔️ | ✔️ | ✔️
 th-TH | Tailandês (Tailândia) | ✔️ | ❌ | ✔️
 tr-TR | Turquia | ✔️ | ✔️ | ❌


## <a name="text-to-speech"></a>Conversão de texto em fala

O Microsoft Speech SDK e a API REST dão suporte a essas vozes, cada uma delas com suporte a um idioma e dialeto específicos, identificados pela localidade.

> [!IMPORTANT]
> Os preços variam para vozes padrão, personalizadas e neurais. Visite a página [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para obter mais informações.

### <a name="neural-voices"></a>Vozes neurais

A conversão de texto em fala neural é um novo tipo de sintetização de fala alimentado por redes neurais profundas. Ao usar uma voz neural, a fala sintetizada é quase indistinguível de gravações de humana.

As vozes neurais podem ser usadas para fazer interações com os assistentes de voz e chatbots mais naturais e envolventes, converter textos digitais, como livros eletrônicos, em Audiobooks e aprimorar os sistemas de navegação no carro. Com a prosódia natural semelhante à humana e a articulação clara das palavras, as vozes neurais reduzem significativamente a fadiga de escuta quando os usuários interagem com sistemas de inteligência artificial.

Para obter uma lista completa de vozes neurais e a disponibilidade regional, consulte [regiões](regions.md#standard-and-neural-voices).

Localidade | idioma | Gênero | Mapeamento de nome de serviço completo | Nome curto da voz
--------|----------|--------|---------|------------
de-DE | Alemão (Alemanha) | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (de-DE, KatjaNeural)" | "de-DE-KatjaNeural"
en-US | Inglês (EUA) | Masculino | "Conversão de Texto em Fala do Microsoft Server (en-US, GuyNeural)" | "en-US-GuyNeural"
en-US | Inglês (EUA) | Feminino | "Conversão de Texto em Fala do Microsoft Server (en-US, JessaNeural)" | "en-US-JessaNeural"
it-IT | Italiano (Itália) | Feminino |"Microsoft Server Speech Conversão de Texto em Fala Voice (IT-IT, ElsaNeural)" | "IT-IT-ElsaNeural"
zh-CN | Chinês (continente) | Feminino | "Voz para Conversão de Texto em Fala de Fala do Microsoft Server (zh-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

> [!NOTE]
> Você pode usar o mapeamento de nome de serviço completo ou o nome de voz curto em suas solicitações de síntese de fala.

### <a name="standard-voices"></a>Vozes padrão

Mais de 75 vozes padrão estão disponíveis em mais de 45 idiomas e localidades, que permitem converter texto em fala sintetizada. Para obter mais informações sobre a disponibilidade regional, consulte [regiões](regions.md#standard-and-neural-voices).

Localidade | idioma | Gênero | Mapeamento de nome de serviço completo | Nome curto | Suporte a contêiner
-------|----------|---------|----------|----------|------
ar-EG\* | Árabe (Egito) | Feminino | "Conversão de Texto em Fala do Microsoft Server (ar-EG, Hoda)" | "ar-ex-Hoda" | ✔️
ar-SA | Árabe (Arábia Saudita) | Masculino | "Conversão de Texto em Fala do Microsoft Server (ar-SA, Naayf)" | "ar-SA-Naayf" | ✔️
bg-BG | Búlgaro | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (bg-BG, Ivan)” | "bg-BG-Ivan" | ✔️
ca-ES | Catalão (Espanha) | Feminino | "Conversão de Texto em Fala do Microsoft Server (ca-ES, HerenaRUS)" | "CA-ES-HerenaRUS" | ✔️
cs-CZ | Tcheco | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (cs-CZ, Jakub)” | "CS-CZ-Jakub" | ✔️
da-DK | Dinamarquês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (da-DK, HelleRUS)” | "da-DK-HelleRUS" | ✔️
de-AT | Alemão (Áustria) | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-AT, Michael)” | "de a Michael" | ✔️
de-CH | Alemão (Suíça) | Masculino | "Conversão de Texto em Fala do Microsoft Server (de-CH, Karsten)" | "de-CH-Karsten" | ✔️
de-DE | Alemão (Alemanha) | Feminino | "Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-DE, Hedda)" | "de-DE-Hedda" | ❌
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-DE, HeddaRUS)” | "de-DE-HeddaRUS" | ✔️
| | | Masculino | "Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-DE, Stefan, Apollo)" | "de-DE-Rodrigo-Apollo" | ✔️
el-GR | Grego | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (el-GR, Stefanos)” | "El-GR-Stefanos" | ✔️
en-AU | Inglês (Austrália) | Feminino | "Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-AU, Catherine)" | "en-AU-Catherine" | ✔️
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-AU, HayleyRUS)” | "en-AU-HayleyRUS" | ✔️
en-CA | Inglês (Canadá) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-CA, Linda)” | "en-CA-linda" | ✔️
| | | Feminino | "Conversão de Texto em Fala do Microsoft Server (en-CA, HeatherRUS)" | "en-CA-HeatherRUS" | ✔️
en-GB | Inglês (Reino Unido) | Feminino | "Conversão de Texto em Fala do Microsoft Server (en-GB, Susan, Apollo)" | "en-GB-Susan-Apollo" | ✔️
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-GB, HazelRUS)” | "en-GB-HazelRUS" | ✔️
| | | Masculino | "Conversão de Texto em Fala do Microsoft Server (en-GB, George, Apollo)" | "en-GB-George-Apollo" | ✔️
en-IE | Inglês (Irlanda) | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-IE, Sean)” | "en-IE-Sílvio" | ✔️
en-IN | Inglês (Índia) | Feminino | "Conversão de Texto em Fala do Microsoft Server (en-IN, Heera, Apollo)" | "en-IN-Heera-Apollo" | ✔️
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-IN, PriyaRUS)” | "en-IN-PriyaRUS" | ✔️
| | | Masculino | "Conversão de Texto em Fala do Microsoft Server (en-IN, Ravi, Apollo)" | "en-IN-Ravi-Apollo" | ✔️
en-US | Inglês (EUA) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-US, ZiraRUS)” | "en-US-ZiraRUS" | ✔️
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-US, JessaRUS)” | "en-US-JessaRUS" | ✔️
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-US, BenjaminRUS)” | "en-US-BenjaminRUS" | ✔️
| | | Feminino | "Conversão de Texto em Fala do Microsoft Server (en-US, Jessa24kRUS)" | "en-US-Jessa24kRUS" | ✔️
| | | Masculino | "Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-US, Guy24kRUS)" | "en-US-Guy24kRUS" | ✔️
es-ES | Espanhol (Espanha) |Feminino | "Conversão de Texto em Fala do Microsoft Server (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo" | ✔️
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-ES, HelenaRUS)” | "es-ES-HelenaRUS" | ✔️
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-ES, Pablo, Apollo)” | "es-ES-Pablo-Apollo" | ✔️
es-MX | Espanhol (México) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-MX, HildaRUS)” | "es-MX-HildaRUS" | ✔️
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-MX, Raul, Apollo)” | "es-MX-Ricardo-Apollo" | ✔️
fi-FI | Finlandês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fi-FI, HeidiRUS)” | "fi-FI-HeidiRUS" | ✔️
fr-CA | Francês (Canadá) |Feminino | "Conversão de Texto em Fala do Microsoft Server (fr-CA, Caroline)" | "fr-CA-Carolina" | ✔️
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-CA, HarmonieRUS)” | "fr-CA-HarmonieRUS" | ✔️
fr-CH | Francês (Suíça)| Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-CH, Guillaume)” | "fr-CH-Guillaume" | ✔️
fr-FR | Francês (França)| Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-FR, Julie, Apollo)” | "fr-FR-Julie-Apollo" | ✔️
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-FR, HortenseRUS)” | "fr-FR-HortenseRUS" | ✔️
| | | Masculino | "Conversão de Texto em Fala do Microsoft Server (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo" | ✔️
he-IL| Hebraico (Israel) | Masculino| "Conversão de Texto em Fala do Microsoft Server (he-IL, Asaf)" | "he-IL-Asaf" | ✔️
hi-IN | Hindi (Índia) | Feminino | "Conversão de Texto em Fala do Microsoft Server (hi-IN, Kalpana, Apollo)" | "hi-IN-Kalpana-Apollo" | ✔️
| | |Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hi-IN, Kalpana)” | "hi-IN-Kalpana" | ✔️
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hi-IN, Hemant)” | "hi-IN-Hemant" | ✔️
hr-HR | Croata | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hr-HR, Matej)” | "HR-HR-Matej" | ✔️
hu-HU | Húngaro | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hu-HU, Szabolcs)” | "hu-HU-Szabolcs" | ✔️
id-ID | Indonésio| Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (id-ID, Andika)” | "ID-ID-andika" | ✔️
it-IT | Italiano | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (it-IT, Cosimo, Apollo)” | "IT-IT-Cosimo-Apollo" | ✔️
| | | Feminino | "Conversão de Texto em Fala do Microsoft Server (it-IT, LuciaRUS)" | "IT-IT-LuciaRUS" | ✔️
ja-JP | Japonês | Feminino | "Conversão de Texto em Fala do Microsoft Server (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo" | ✔️
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ja-JP, Ichiro, Apollo)” | "ja-JP-Ichiro-Apollo" | ✔️
| | | Feminino | "Conversão de Texto em Fala do Microsoft Server (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS" | ✔️
ko-KR | Coreano | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ko-KR, HeamiRUS)” | "ko-KR-HeamiRUS" | ✔️
ms-MY | Malaio | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ms-MY, Rizwan)” | "MS-MY-Rizwan" | ✔️
nb-NO | Norueguês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (nb-NO, HuldaRUS)” | "NB-NO-HuldaRUS" | ✔️
nl-NL | Holandês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (nl-NL, HannaRUS)” | "nl-NL-HannaRUS" | ✔️
pl-PL | Polonês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (pl-PL, PaulinaRUS)” | "pl-PL-PaulinaRUS" | ✔️
pt-BR | Português (Brasil) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (pt-BR, HeloisaRUS)” | "pt-BR-HeloisaRUS" | ✔️
| | | Masculino |“Voz da Conversão de Texto em Fala do Microsoft Server Speech (pt-BR, Daniel, Apollo)” | "pt-BR-Daniel-Apollo" | ✔️
pt-PT | Português (Portugal) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (pt-PT, HeliaRUS)” | "pt-PT-HeliaRUS" | ✔️
ro-RO | Romeno | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ro-RO, Andrei)” | "RO-RO-Andrei" | ✔️
ru-RU |Russo| Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ru-RU, Irina, Apollo)” | "ru-RU-Irina-Apollo" | ✔️
| | | Masculino | "Conversão de Texto em Fala do Microsoft Server (ru-RU, Pavel, Apollo)" | "ru-RU-Pavel-Apollo" | ✔️
| | | Feminino | "Conversão de Texto em Fala do Microsoft Server (ru-RU, EkaterinaRUS)" | RU-RU-EkaterinaRUS | ✔️
sk-SK | Eslovaco | Masculino | "Conversão de Texto em Fala do Microsoft Server (sk-SK, Filip)" | "SK-SK-Filip" | ✔️
sl-SI | Esloveno | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (sl-SI, Lado)” | "SL-SI-lado" | ✔️
sv-SE | Sueco | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (sv-SE, HedvigRUS)” | "SV-SE-HedvigRUS" | ✔️
ta-IN | Tâmil (Índia) | Masculino | "Conversão de Texto em Fala do Microsoft Server (ta-IN, Valluvar)" | "ta-IN-Valluvar" | ✔️
te-IN | Télugo (Índia) | Feminino | "Voz da Conversão de Texto em Fala do Microsoft Server Speech (te-IN, Chitra)" | "Chitra" | ✔️
th-TH | Tailandês | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (th-TH, Pattara)” | "th-TH-Pattara" | ✔️
tr-TR | Turco | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (tr-TR, SedaRUS)” | "tr-TR-SedaRUS" | ✔️
vi-VN | Vietnamita | Masculino | "Conversão de Texto em Fala do Microsoft Server (vi-VN, An)" | "vi-VN-um" | ✔️
zh-CN | Chinês (continente) | Feminino | "Conversão de Texto em Fala do Microsoft Server (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS" | ✔️
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-CN, Yaoyao, Apollo)” | "zh-CN-Yaoyao-Apollo" | ✔️
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-CN, Kangkang, Apollo)” | "zh-CN-Kangkang-Apollo" | ✔️
zh-HK | Chinês (Hong Kong) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-HK, Tracy, Apollo)” | "ZH-HK-Tracy-Apollo" | ✔️
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-HK, TracyRUS)” | "ZH-HK-TracyRUS" | ✔️
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-HK, Danny, Apollo)” | "ZH-HK-Danny-Apollo" | ✔️
zh-TW | Chinês (Taiwan) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-TW, Yating, Apollo)” | "zh-TW-Yating-Apollo" | ✔️
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-TW, HanHanRUS)” | "zh-TW-HanHanRUS" | ✔️
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-TW, Zhiwei, Apollo)” | "zh-TW-Zhiwei-Apollo" | ✔️

\* *ar-EG é compatível com MSA (árabe padrão moderno).*

> [!NOTE]
> Você pode usar o mapeamento de nome de serviço completo ou o nome de voz curto em suas solicitações de síntese de fala.

### <a name="customization"></a>Personalização

A personalização de voz está disponível para de-DE, en-GB, en-IN, en-US, es-MX, fr-FR, it-IT, pt-BR e zh-CN. Selecione a localidade correta que corresponde aos dados de treinamento que você tem para treinar um modelo de voz personalizado. Por exemplo, se os dados de gravação que você tem são falados em inglês com um sotaque britânico, selecione en-GB.  

> [!NOTE]
> Não damos suporte ao treinamento de modelo de bi-idiomas em voz personalizada, exceto para o Português do idioma chinês-inglês. Selecione "chinês-inglês bilíngüe" se você quiser treinar uma voz chinesa que também pode falar em inglês. O treinamento de voz em todas as localidades começa com um conjunto de dados de 2.000 + declarações, exceto para o en-US e o zh-CN, onde você pode começar com qualquer tamanho de dados de treinamento.

## <a name="speech-translation"></a>Tradução de fala

A API de **Tradução de Fala** é compatível com diferentes idiomas para conversão de fala em fala e de fala em texto. O idioma de origem sempre deve ser da tabela de idioma conversão de fala em texto. Os idiomas de destino disponíveis dependem de se o destino de tradução é fala ou texto. Você pode traduzir a entrada de fala em mais de [60 idiomas](https://www.microsoft.com/translator/business/languages/). Um subconjunto desses idiomas está disponível para [síntese de fala](language-support.md#text-languages).

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
| Kiswahili      | `sw`          |
| Klingon      | `tlh`          |
| Klingon (plqaD)      | `tlh-Qaak`          |
| Coreano      | `ko`          |
| Letão      | `lv`          |
| Lituano      | `lt`          |
| Malgaxe      | `mg`          |
| Malaio      | `ms`          |
| Maltese      | `mt`          |
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
| Tailandês      | `th`          |
| Tongan      | `to`          |
| Turco      | `tr`          |
| Ucraniano      | `uk`          |
| Urdu      | `ur`          |
| Vietnamita      | `vi`          |
| Galês      | `cy`          |
| Yucatec Maya      | `yua`          |


## <a name="next-steps"></a>Próximas etapas

* [Obter sua assinatura de avaliação de Serviços de Fala](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como reconhecer a fala em c #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
