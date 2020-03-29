---
title: Diretrizes de transcrições com rótulo humano - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Para melhorar a precisão do reconhecimento de fala, como quando as palavras são excluídas ou substituídas incorretamente, você pode usar transcrições rotuladas por humanos junto com seus dados de áudio. Transcrições rotuladas por humanos são transcrições palavra por palavra, verbais de um arquivo de áudio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 81b4ffc8f77673e52bb78f891e3de618b67e0d1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806055"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>Como criar transcrições rotuladas por humanos

Se você está procurando melhorar a precisão do reconhecimento, especialmente problemas que são causados quando as palavras são excluídas ou substituídas incorretamente, você vai querer usar transcrições rotuladas por humanos junto com seus dados de áudio. O que são transcrições rotuladas por humanos? Isso é fácil, eles são palavra por palavra, transcrições verbais de um arquivo de áudio.

Uma grande amostra de dados de transcrição é necessária para melhorar o reconhecimento, sugerimos fornecer entre 10 e 1.000 horas de dados de transcrição. Nesta página, revisaremos as diretrizes projetadas para ajudá-lo a criar transcrições de alta qualidade. Este guia é dividido por localidade, com seções para inglês americano, mandarim chinês e alemão.

## <a name="us-english-en-us"></a>Inglês dos EUA (en-US)

As transcrições rotuladas por humanos para áudio em inglês devem ser fornecidas como texto simples, usando apenas caracteres ASCII. Evite o uso de caracteres de pontuação Latin-1 ou Unicode. Esses caracteres são frequentemente adicionados inadvertidamente ao copiar texto de um aplicativo de processamento de texto ou raspar dados de páginas da Web. Se esses caracteres estiverem presentes, certifique-se de atualizá-los com a substituição ASCII apropriada.

Veja alguns exemplos:

| Caracteres a evitar | Substituição | Observações |
| ------------------- | ------------ | ----- |
| "Olá mundo" | "Olá, Mundo" | As aspas de abertura e fechamento foram substituídas por caracteres ASCII apropriados. |
| Dia de João | Dia de João | O apóstrofo foi substituído pelo caráter ASCII apropriado. |
| it was good—no, it was great! | it was good--no, it was great! | O traço em foi substituído por dois hífens. |

### <a name="text-normalization-for-us-english"></a>Normalização de texto para inglês nos EUA

Normalização de texto é a transformação das palavras em um formato consistente usado ao treinar um modelo. Algumas regras de normalização são aplicadas ao texto automaticamente, no entanto, recomendamos usar essas diretrizes enquanto você prepara seus dados de transcrição rotulados por humanos:

- Escreva abreviaturas em palavras.
- Escreva strings numéricas não padronizadas em palavras (como termos contábeis).
- Caracteres não alfabéticos ou caracteres alfanuméricos mistos devem ser transcritos como pronunciados.
- Abreviaturas que são pronunciadas como palavras não devem ser editadas (como "radar", "laser", "RAM" ou "OTAN").
- Escreva abreviaturas que são pronunciadas como letras separadas com cada letra separada por um espaço.

Aqui estão alguns exemplos de normalização que você deve executar na transcrição:

| Texto original               | Texto após a normalização              |
| --------------------------- | ------------------------------------- |
| Dr. Bruce Banner            | Doctor Bruce Banner                   |
| James Bond, 007             | James Bond, double oh seven           |
| Ke$ha                       | Kesha                                 |
| How long is the 2x4         | How long is the two by four           |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| Meu tipo sanguíneo é O+         | My blood type is O positive           |
| Água é H20                | Água é H 2 O                        |
| Jogar OU812 por Van Halen     | Jogar O U 8 12 por Van Halen           |
| UTF-8 with BOM              | U T F 8 with BOM                      |

As seguintes regras de normalização são aplicadas automaticamente às transcrições:

- Use letras minúsculas.
- Remova toda a pontuação, exceto apóstrofes dentro das palavras.
- Expanda os números em palavras/forma falada, como valores em dólares.

Aqui estão alguns exemplos de normalização realizados automaticamente na transcrição:

| Texto original                          | Texto após a normalização          |
| -------------------------------------- | --------------------------------- |
| "Holy cow!" said Batman.               | holy cow said batman              |
| "What?" said Batman's sidekick, Robin. | what said batman's sidekick robin |
| Go get -em!                            | go get em                         |
| I'm double-jointed                     | I'm double jointed                |
| 104 Elm Street                         | one oh four Elm street            |
| Tune to 102.7                          | tune to one oh two point seven    |
| Pi is about 3.14                       | pi is about three point one four  |
| Custa \$3.14                        | it costs three fourteen           |

## <a name="mandarin-chinese-zh-cn"></a>Mandarim chinês (zh-CN)

As transcrições rotuladas por humanos para áudio chinês mandarim devem ser codificadas utf-8 com um marcador de ordem de byte. Evite o uso de caracteres de pontuação de meia largura. Esses caracteres podem ser incluídos inadvertidamente quando você prepara os dados em um programa de processamento de palavras ou raspa dados de páginas da Web. Se esses caracteres estiverem presentes, certifique-se de atualizá-los com a substituição de largura total apropriada.

Veja alguns exemplos:

| Caracteres a evitar | Substituição   | Observações |
| ------------------- | -------------- | ----- |
| "のの" | "のの" | As aspas de abertura e fechamento foram substituídas por caracteres apropriados. |
| 需要什么帮助? | 需要什么帮助？| O ponto de interrogação foi substituído com caráter apropriado. |

### <a name="text-normalization-for-mandarin-chinese"></a>Normalização de texto para mandarim chinês

Normalização de texto é a transformação das palavras em um formato consistente usado ao treinar um modelo. Algumas regras de normalização são aplicadas ao texto automaticamente, no entanto, recomendamos usar essas diretrizes enquanto você prepara seus dados de transcrição rotulados por humanos:

- Escreva abreviaturas em palavras.
- Grave cadeia de caracteres numéricas na forma falada.

Aqui estão alguns exemplos de normalização que você deve executar na transcrição:

| Texto original | Texto após a normalização |
| ------------- | ------------------------ |
| Dia 21. | 我今年二十一 |
| 3 004 | 三号 楼 五 零 四 |

As seguintes regras de normalização são aplicadas automaticamente às transcrições:

- Remova toda a pontuação
- Expandir números para a forma falada
- Converta letras de largura total em letras de meia largura
- Usando letras maiusculas para todas as palavras em inglês

Aqui estão alguns exemplos de normalização realizados automaticamente na transcrição:

| Texto original | Texto após a normalização |
| ------------- | ------------------------ |
| 3.1415 | 三 点 一 四 一 五 |
| ¥ 3,5 | 三 元 五 角 |
| w f y z | w f y z |
| 1992 の 8 8 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 5:00 | 下午 五点 的 航班 |
| 21. | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>Alemão (de-DE) e outras línguas

As transcrições rotuladas por humanos para áudio alemão (e outras línguas chinesas não inglesas ou mandarim) devem ser codificadas utf-8 com um marcador de ordem de byte. Uma transcrição com rótulo humano deve ser fornecida para cada arquivo de áudio.

### <a name="text-normalization-for-german"></a>Normalização de texto para alemão

Normalização de texto é a transformação das palavras em um formato consistente usado ao treinar um modelo. Algumas regras de normalização são aplicadas ao texto automaticamente, no entanto, recomendamos usar essas diretrizes enquanto você prepara seus dados de transcrição rotulados por humanos:

- Escreva pontos decimais como "" e não ".".
- Escreva separadores de tempo como ":" e não "." (por exemplo: 12:00 Uhr).
- Abreviações como "ca". não são substituídas. Recomendamos que você use o formulário falado completo.
- Os quatro operadores matemáticos principais (+, -, \*, e /) são removidos. Recomendamos substituí-los pela forma escrita: "mais", "menos", "mal" e "geteilt".
- Os operadores de comparação são removidos (=, < e >). É recomendável substituí-los por "gleich", "kleiner als," e "als grösser".
- Escreva frações, como 3/4, na forma escrita (por exemplo: "drei viertel" em vez de 3/4).
- Substitua o símbolo "€" por sua forma escrita "Euro".

Aqui estão alguns exemplos de normalização que você deve executar na transcrição:

| Texto original    | Texto após a normalização do usuário | Texto após a normalização do sistema       |
| ---------------- | ----------------------------- | ------------------------------------- |
| Es ist 12.23 Uhr | Es ist 12:23 Uhr              | es ist zwölf uhr drei und zwanzig uhr |
| {12.45}          | {12,45}                       | zwölf komma vier fünf                 |
| 2 + 3 - 4        | 2 plus 3 minus 4              | zwei plus drei minus vier             |

As seguintes regras de normalização são aplicadas automaticamente às transcrições:

- Use letras minúsculas para todos os textos.
- Remova todas as pontuações, incluindo vários tipos de aspas ("teste", "teste", "teste" e "teste" são OK).
- Descarte as linhas com quaisquer caracteres especiais deste conjunto: ¢ ◗ ¶ ¶ â € © ® ° ± 2 μ × ÿ Ø¶¶.
- Expandir os números para a forma falada, incluindo valores em dólar ou euro.
- Aceite umlauts apenas para um, o, e você. Outros serão substituídos por "th" ou serão descartados.

Aqui estão alguns exemplos de normalização realizados automaticamente na transcrição:

| Texto original    | Texto após a normalização |
| ---------------- | ------------------------ |
| Frankfurter Ring | frankfurter ring         |
| ¡Eine Frage!     | eine frage               |
| wir, haben       | wir haben                |

## <a name="next-steps"></a>Próximas etapas

- [Prepare e teste seus dados](how-to-custom-speech-test-data.md)
- [Inspecione seus dados](how-to-custom-speech-inspect-data.md)
- [Avalie seus dados](how-to-custom-speech-evaluate-data.md)
- [Treinar seu modelo](how-to-custom-speech-train-model.md)
- [Implantar o seu modelo](how-to-custom-speech-deploy-model.md)
