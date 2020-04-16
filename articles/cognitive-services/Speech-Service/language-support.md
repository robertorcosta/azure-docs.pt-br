---
title: Suporte ao idioma - Serviço de fala
titleSuffix: Azure Cognitive Services
description: O serviço de fala suporta inúmeras linguagens para conversão de fala para texto e texto-para-fala, juntamente com a tradução de fala. Este artigo fornece uma lista abrangente de suporte ao idioma por recurso de serviço.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: trbye
ms.custom: seodec18
ms.openlocfilehash: 2a876349af2bd53a46737dda82961afe442bae05
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401081"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Suporte de linguagem e voz para o serviço de fala

O suporte ao idioma varia de acordo com a funcionalidade do serviço de fala. As tabelas a seguir resumem o suporte ao idioma [para ofertas de](#speech-to-text)serviços de fonoaudiologia, [texto-para-fala](#text-to-speech)e de tradução de [fala.](#speech-translation)

## <a name="speech-to-text"></a>Conversão de fala em texto

Tanto o Microsoft Speech SDK quanto a API REST suportam os seguintes idiomas (locais). Para melhorar a precisão, a personalização é oferecida para um subconjunto de idiomas através do upload de Transcrições com rótulo humano ou texto relacionado: frases. A personalização da pronúncia está disponível apenas para `en-US` e `de-DE`. Saiba mais sobre a personalização [aqui](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Local  | Linguagem                          | Com suporte | Personalizações                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | Árabe (Emirados Árabes)                      | Sim       | Não                                                |
| `ar-BH` | Árabe (Bahrein), padrão moderno | Sim       | Modelo de linguagem                                    |
| `ar-EG` | Árabe (Egito)                    | Sim       | Modelo de linguagem                                    |
| `ar-KW` | Árabe (Kuwait)                   | Sim       | Não                                                |
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
| `gu-IN` | Gujarati (indiano)                 | Sim       | Modelo de linguagem                                    |
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
| `tr-TR` | Turco (Turquia)                  | Sim       | Não                                                |
| `zh-CN` | Chinês (mandarim, simplificado)    | Sim       | Modelo acústico<br>Modelo de linguagem                  |
| `zh-HK` | Chinês (cantonês, tradicional)  | Sim       | Modelo de linguagem                                    |
| `zh-TW` | Chinês (Mandarim Taiwanês)      | Sim       | Modelo de linguagem                                    |

## <a name="text-to-speech"></a>Conversão de texto em fala

Tanto o Microsoft Speech SDK quanto o REST APIs suportam essas vozes, cada uma das quais suporta uma linguagem e dialeto específicos, identificados por local.

> [!IMPORTANT]
> Os preços variam para vozes padrão, personalizadas e neurais. Visite a página [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para obter mais informações.

### <a name="neural-voices"></a>Vozes neurais

A conversão de texto em fala neural é um novo tipo de sintetização de fala alimentado por redes neurais profundas. Ao usar uma voz neural, a fala sintetizada é quase indistinguível de gravações de humana.

Vozes neurais podem ser usadas para tornar as interações com chatbots e assistentes de voz mais naturais e envolventes, converter textos digitais, como e-books, em audiolivros e melhorar sistemas de navegação no carro. Com a prosódia natural semelhante à humana e a articulação clara das palavras, as vozes neurais reduzem significativamente a fadiga de escuta quando os usuários interagem com sistemas de inteligência artificial.

Para obter mais informações sobre a disponibilidade regional, consulte [regiões](regions.md#standard-and-neural-voices).

| Local  | Linguagem            | Gênero | Mapeamento completo do nome do serviço                                               | Nome de voz curta        |
|---------|---------------------|--------|-------------------------------------------------------------------------|-------------------------|
| `de-DE` | Alemão (Alemanha)    | Feminino | "Microsoft Server Speech Text to Speech Voice (de-DE, KatjaNeural)"     | "de-DE-KatjaNeural"     |
| `en-US` | Inglês (EUA)        | Feminino | "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)"      | "en-US-AriaNeural"      |
| `en-US` | Inglês (EUA)        | Masculino   | "Conversão de Texto em Fala do Microsoft Server (en-US, GuyNeural)"       | "en-US-GuyNeural"       |
| `it-IT` | Italiano (Itália)     | Feminino | "Microsoft Server Speech Text to Speech Voice (it-IT, ElsaNeural)"      | "it-IT-ElsaNeural"      |
| `pt-BR` | Português (Brasil) | Feminino | "Microsoft Server Speech Text to Speech Voice (pt-BR, FranciscaNeural)" | "pt-BR-FranciscaNeural" |
| `zh-CN` | Chinês (mandarim, simplificado)  | Feminino | "Voz para Conversão de Texto em Fala de Fala do Microsoft Server (zh-CN, XiaoxiaoNeural)"  | "zh-CN-XiaoxiaoNeural"  |

> [!IMPORTANT]
> A `en-US-JessaNeural` voz mudou `en-US-AriaNeural`para. Se você estava usando "Jessa" antes, converta-se em "Aria".

Para saber como você pode configurar e ajustar vozes neurais, consulte [a linguagem de marcação da síntese de fala](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> Você pode usar o mapeamento completo do nome do serviço ou o nome de voz curto nas solicitações de síntese de voz.

### <a name="standard-voices"></a>Vozes padrão

Mais de 75 vozes padrão estão disponíveis em mais de 45 idiomas e localidades, que permitem converter texto em fala sintetizada. Para obter mais informações sobre a disponibilidade regional, consulte [regiões](regions.md#standard-and-neural-voices).

| Local | Linguagem | Gênero | Mapeamento completo do nome do serviço | Nome curto |
|--|--|--|--|--|
| <sup>1</sup>`ar-EG` | Árabe (Egito) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ar-EG, Hoda)” | "ar-EG-Hoda" |
| `ar-SA` | Árabe (Arábia Saudita) | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ar-SA, Naayf)” | "ar-SA-Naayf" |
| `bg-BG` | Búlgaro | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (bg-BG, Ivan)” | "BG-BG-Ivan" |
| `ca-ES` | Catalão (Espanha) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ca-ES, HerenaRUS)” | "ca-ES-HerenaRUS" |
| `cs-CZ` | Tcheco | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (cs-CZ, Jakub)” | "cs-CZ-Jakub" |
| `da-DK` | Dinamarquês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (da-DK, HelleRUS)” | "da-DK-HelleRUS" |
| `de-AT` | Alemão (Áustria) | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-AT, Michael)” | "de-AT-Michael" |
| `de-CH` | Alemão (Suíça) | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-CH, Karsten)” | "de-CH-Karsten" |
| `de-DE` | Alemão (Alemanha) | Feminino | "Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-DE, Hedda)" | "de-DE-Hedda" |
|  |  | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-DE, HeddaRUS)” | "de-DE-HeddaRUS" |
|  |  | Masculino | "Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-DE, Stefan, Apollo)" | "de-DE-Stefan-Apollo" |
| `el-GR` | Grego | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (el-GR, Stefanos)” | "el-GR-Stefanos" |
| `en-AU` | Inglês (Austrália) | Feminino | "Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-AU, Catherine)" | "en-AU-Catherine" |
|  |  | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-AU, HayleyRUS)” | "en-AU-HayleyRUS" |
| `en-CA` | Inglês (Canadá) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-CA, Linda)” | "en-CA-Linda" |
|  |  | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-CA, HeatherRUS)” | "en-CA-HeatherRUS" |
| `en-GB` | Inglês (Reino Unido) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-GB, Susan, Apollo)” | "en-GB-Susan-Apollo" |
|  |  | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-GB, HazelRUS)” | "en-GB-HazelRUS" |
|  |  | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-GB, George, Apollo)” | "en-GB-George-Apollo" |
| `en-IE` | Inglês (Irlanda) | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-IE, Sean)” | "en-IE-Sean" |
| `en-IN` | Inglês (Índia) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-IN, Heera, Apollo)” | "en-IN-Heera-Apollo" |
|  |  | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-IN, PriyaRUS)” | "en-IN-PriyaRUS" |
|  |  | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-IN, Ravi, Apollo)” | "en-IN-Ravi-Apollo" |
| `en-US` | Inglês (EUA) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-US, ZiraRUS)” | "en-US-ZiraRUS" |
|  |  | Feminino | "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)" | "en-US-AriaRUS" |
|  |  | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-US, BenjaminRUS)” | "en-US-BenjaminRUS" |
|  |  | Masculino | "Conversão de Texto em Fala do Microsoft Server (en-US, Guy24kRUS)" | "en-US-Guy24kRUS" |
| `es-ES` | Espanhol (Espanha) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-ES, Laura, Apollo)” | "es-ES-Laura-Apollo" |
|  |  | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-ES, HelenaRUS)” | "ES-ES-HelenaRUS" |
|  |  | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-ES, Pablo, Apollo)” | "es-ES-Pablo-Apollo" |
| `es-MX` | Espanhol (México) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-MX, HildaRUS)” | "es-MX-HildaRUS" |
|  |  | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-MX, Raul, Apollo)” | "es-MX-Raul-Apollo" |
| `fi-FI` | Finlandês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fi-FI, HeidiRUS)” | "fi-FI-HeidiRUS" |
| `fr-CA` | Francês (Canadá) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-CA, Caroline)” | "fr-CA-Caroline" |
|  |  | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-CA, HarmonieRUS)” | "fr-CA-HarmonieRUS" |
| `fr-CH` | Francês (Suíça) | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-CH, Guillaume)” | "fr-CH-Guillaume" |
| `fr-FR` | Francês (França) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-FR, Julie, Apollo)” | "FR-JULIE-Apollo" |
|  |  | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-FR, HortenseRUS)” | "FR-FR-HortenseRUS" |
|  |  | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-FR, Paul, Apollo)” | "FR-PAUL-Apollo" |
| `he-IL` | Hebraico (Israel) | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (he-IL, Asaf)” | "he-IL-Asaf" |
| `hi-IN` | Híndi (Índia) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hi-IN, Kalpana, Apollo)” | "Hi-IN-Kalpana-Apollo" |
|  |  | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hi-IN, Kalpana)” | "Hi-IN-Kalpana" |
|  |  | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hi-IN, Hemant)” | "Hi-IN-Hemant" |
| `hr-HR` | Croata | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hr-HR, Matej)” | "HR-MATEJ" |
| `hu-HU` | Húngaro | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hu-HU, Szabolcs)” | "hu-HU-Szabolcs" |
| `id-ID` | Indonésio | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (id-ID, Andika)” | "ID-ID-Andika" |
| `it-IT` | Italiano | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (it-IT, Cosimo, Apollo)” | "it-It-Cosimo-Apollo" |
|  |  | Feminino | "Conversão de Texto em Fala do Microsoft Server (it-IT, LuciaRUS)" | "it-IT-LuciaRUS" |
| `ja-JP` | Japonês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ja-JP, Ayumi, Apollo)” | "ja-JP-Ayumi-Apollo" |
|  |  | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ja-JP, Ichiro, Apollo)” | "ja-JP-Ichiro-Apollo" |
|  |  | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ja-JP, HarukaRUS)” | "ja-JP-HarukaRUS" |
| `ko-KR` | Coreano | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ko-KR, HeamiRUS)” | "Ko-KR-HeamiRUS" |
| `ms-MY` | Malaio | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ms-MY, Rizwan)” | "Ms-MY-Rizwan" |
| `nb-NO` | Norueguês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (nb-NO, HuldaRUS)” | "nb-NO-HuldaRUS" |
| `nl-NL` | Holandês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (nl-NL, HannaRUS)” | "NL-NL-HannaRUS" |
| `pl-PL` | Polonês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (pl-PL, PaulinaRUS)” | "PL-PL-PaulinaRUS" |
| `pt-BR` | Português (Brasil) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (pt-BR, HeloisaRUS)” | "pt-BR-HeloisaRUS" |
|  |  | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (pt-BR, Daniel, Apollo)” | "pt-BR-Daniel-Apollo" |
| `pt-PT` | Português (Portugal) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (pt-PT, HeliaRUS)” | "pt-PT-HeliaRUS" |
| `ro-RO` | Romeno | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ro-RO, Andrei)” | "ro-RO-Andrei" |
| `ru-RU` | Russo | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ru-RU, Irina, Apollo)” | "ru-RU-Irina-Apollo" |
|  |  | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ru-RU, Pavel, Apollo)” | "ru-RU-Pavel-Apollo" |
|  |  | Feminino | "Conversão de Texto em Fala do Microsoft Server (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS |
| `sk-SK` | Eslovaco | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (sk-SK, Filip)” | "Sk-SK-Filip" |
| `sl-SI` | Esloveno | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (sl-SI, Lado)” | "sl-SI-Lado" |
| `sv-SE` | Sueco | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (sv-SE, HedvigRUS)” | "sv-SE-HedvigRUS" |
| `ta-IN` | Tâmil (Índia) | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ta-IN, Valluvar)” | "ta-IN-Valluvar" |
| `te-IN` | Télugo (Índia) | Feminino | "Voz da Conversão de Texto em Fala do Microsoft Server Speech (te-IN, Chitra)" | "te-IN-Chitra" |
| `th-TH` | Tailandês | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (th-TH, Pattara)” | "th-TH-Pattara" |
| `tr-TR` | Turco (Turquia) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (tr-TR, SedaRUS)” | "TR-TR-SedaRUS" |
| `vi-VN` | Vietnamita | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (vi-VN, An)” | "vi-VN-An" |
| `zh-CN` | Chinês (mandarim, simplificado) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-CN, HuihuiRUS)” | "zh-CN-HuihuiRUS" |
|  |  | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-CN, Yaoyao, Apollo)” | "ZH-CN-Yaoyao-Apollo" |
|  |  | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-CN, Kangkang, Apollo)” | "zh-CN-Kangkang-Apollo" |
| `zh-HK` | Chinês (cantonês, tradicional) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-HK, Tracy, Apollo)” | "zh-HK-Tracy-Apollo" |
|  |  | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-HK, TracyRUS)” | "ZH-HK-TracyRUS" |
|  |  | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-HK, Danny, Apollo)” | "zh-HK-Danny-Apollo" |
| `zh-TW` | Chinês (Mandarim Taiwanês) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-TW, Yating, Apollo)” | "zh-TW-Yating-Apollo" |
|  |  | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-TW, HanHanRUS)” | "zh-TW-HanHanRUS" |
|  |  | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-TW, Zhiwei, Apollo)” | "zh-TW-Zhiwei-Apollo" |

**1** *ar-EG suporta árabe padrão moderno (MSA).*

> [!IMPORTANT]
> A `en-US-Jessa` voz mudou `en-US-Aria`para. Se você estava usando "Jessa" antes, converta-se em "Aria".

> [!TIP]
> Você pode usar o mapeamento completo do nome do serviço ou o nome de voz curto nas solicitações de síntese de voz.

### <a name="customization"></a>Personalização

A personalização de `de-DE` `en-GB`voz `en-IN` `en-US`está `es-MX` `fr-FR`disponível para, , , , , , `it-IT`, `pt-BR`e `zh-CN`. Selecione o local certo que corresponda aos dados de treinamento que você tem para treinar um modelo de voz personalizado. Por exemplo, se os dados de gravação que você `en-GB`tem forem falados em inglês com um sotaque britânico, selecione .

> [!NOTE]
> Não suportamos o treinamento de modelo sinol em Voz Personalizada, exceto o bi-lingual chinês-inglês. Selecione "bilíngüe chinês-inglês" se você quiser treinar uma voz chinesa que também possa falar inglês. O treinamento de voz em todos os locais começa com um conjunto de `en-US` `zh-CN` dados de mais de 2.000 expressões, exceto para o e onde você pode começar com qualquer tamanho de dados de treinamento.

## <a name="speech-translation"></a>Tradução de fala

A API de **Tradução de Fala** é compatível com diferentes idiomas para conversão de fala em fala e de fala em texto. O idioma de origem deve ser sempre da tabela de idiomas Fala-a-texto. Os idiomas de destino disponíveis dependem de se o destino de tradução é fala ou texto. Você pode traduzir a entrada de fala em mais de [60 idiomas](https://www.microsoft.com/translator/business/languages/). Um subconjunto de idiomas está disponível para [síntese de fala](language-support.md#text-languages).

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

* [Obtenha sua assinatura de teste do serviço de fala](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como reconhecer fala em C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
