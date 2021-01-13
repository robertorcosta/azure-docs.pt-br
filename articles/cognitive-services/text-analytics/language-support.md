---
title: Idiomas compatíveis – API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Uma lista dos idiomas naturais compatíveis com a API de Análise de Texto. Este artigo explica quais idiomas têm suporte para cada operação.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/17/2020
ms.author: aahi
ms.openlocfilehash: a0cd968c90a27d6f1ae79d08fb2bd027169233db
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133779"
---
# <a name="text-analytics-api-v3-language-support"></a>Suporte à linguagem API de Análise de Texto v3 

#### <a name="sentiment-analysis"></a>[Análise de Sentimento](#tab/sentiment-analysis)

| Linguagem              | Código de idioma | suporte a v2 | suporte v3 | Iniciando a versão do modelo V3: |              Observações |
|:----------------------|:-------------:|:----------:|:----------:|:--------------------------:|-------------------:|
| Chinês simplificado    |   `zh-hans`   |     ✓      |     ✓      |         2019-10-01         | `zh` também é aceito |
| Chinês tradicional   |   `zh-hant`   |            |     ✓      |         2019-10-01         |                    |
| Dinamarquês               |     `da`      |     ✓      |            |                            |                    |
| Holandês                 |     `nl`      |     ✓      |            |                            |                    |
| Inglês               |     `en`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Finlandês               |     `fi`      |     ✓      |            |                            |                    |
| Francês                |     `fr`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Alemão                |     `de`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Grego                 |     `el`      |     ✓      |            |                            |                    |
| Híndi                 |     `hi`      |            |      ✓     |          2020-04-01        |                    |
| Italiano               |     `it`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Japonês              |     `ja`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Coreano                |     `ko`      |            |     ✓      |         2019-10-01         |                    |
| Norueguês (Bokmål)   |     `no`      |     ✓      |     ✓      |         2020-07-01         |                    |
| Polonês                |     `pl`      |     ✓      |            |                            |                    |
| Português (Brasil)   |    `pt-BR`    |            |     ✓      |         2020-04-01         |                    |
| Português (Portugal) |    `pt-PT`    |     ✓      |     ✓      |         2019-10-01         | `pt` também é aceito |
| Russo               |     `ru`      |     ✓      |            |                            |                    |
| Espanhol               |     `es`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Sueco               |     `sv`      |     ✓      |            |                            |                    |
| Turco               |     `tr`      |     ✓      |     ✓       |         2020-07-01        |                    |

### <a name="opinion-mining-v31-preview-only"></a>Mineração de opinião (v 3.1-somente visualização)

| Linguagem              | Código de idioma | Iniciando com a versão do modelo V3: |              Observações |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Inglês               |     `en`      |              2020-04-01              |                    |


#### <a name="named-entity-recognition-ner"></a>[Reconhecimento de entidade nomeada (NER)](#tab/named-entity-recognition)

> [!NOTE]
> * O NER v3 atualmente dá suporte apenas a idiomas inglês e espanhol. Se você chamar NER v3 com um idioma diferente, a API retornará os resultados de v 2.1, desde que a linguagem tenha suporte na versão 2,1.
> * o v 2.1 retorna apenas o conjunto completo de entidades disponíveis para os idiomas inglês, chinês simplificado, francês, alemão e espanhol.  As entidades "pessoa", "local" e "organização" são retornadas para os outros idiomas com suporte.

| Linguagem               | Código de idioma | suporte a v 2.1 | suporte v3 | Iniciando com a versão do modelo V3: |       Observações        |
|:-----------------------|:-------------:|:----------:|:----------:|:-------------------------------:|:------------------:|
| Árabe                |     `ar`      |     ✓      |            |                                 |                    |
| Chinês simplificado     |   `zh-hans`   |     ✓      |            |                                 | `zh` também é aceito |
| Chinês tradicional   |   `zh-hant`   |     ✓      |            |                                 |                    |
| Tcheco                 |     `cs`      |     ✓      |            |                                 |                    |
| Dinamarquês                |     `da`      |     ✓      |            |                                 |                    |
| Holandês                 |     `nl`      |     ✓      |            |                                 |                    |
| Inglês                |     `en`      |     ✓      |     ✓      |           2019-10-01            |                    |
| Finlandês               |     `fi`      |     ✓      |            |                                 |                    |
| Francês                 |     `fr`      |     ✓      |            |                                 |                    |
| Alemão                 |     `de`      |     ✓      |            |                                 |                    |
| Hebraico                |     `he`      |     ✓      |            |                                 |                    |
| Húngaro             |     `hu`      |     ✓      |            |                                 |                    |
| Italiano               |     `it`      |     ✓      |            |                                 |                    |
| Japonês              |     `ja`      |     ✓      |            |                                 |                    |
| Coreano                |     `ko`      |     ✓      |            |                                 |                    |
| Norueguês (Bokmål)   |     `no`      |     ✓      |            |                                 | `nb` também é aceito |
| Polonês                |     `pl`      |     ✓      |            |                                 |                    |
| Português (Brasil)   |    `pt-BR`    |     ✓      |            |                                 |                    |
| Português (Portugal) |    `pt-PT`    |     ✓      |            |                                 | `pt` também é aceito |
| Russo              |     `ru`      |     ✓      |            |                                 |                    |
| Espanhol               |     `es`      |     ✓      |     ✓       |              2020-04-01                   |                    |
| Sueco               |     `sv`      |     ✓      |            |                                 |                    |
| Turco               |     `tr`      |     ✓      |            |                                 |                    |

#### <a name="key-phrase-extraction"></a>[Extração de frases-chave](#tab/key-phrase-extraction)

| Linguagem              | Código de idioma | suporte a v2 | suporte v3 | Disponível a partir da versão do modelo V3: |       Observações        |
|:----------------------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:------------------:|
| Dinamarquês                |     `da`      |     ✓      |      ✓     |                2019-10-01                 |                    |
| Holandês                 |     `nl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Inglês               |     `en`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Finlandês               |     `fi`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Francês                |     `fr`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Alemão                |     `de`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Italiano               |     `it`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Japonês              |     `ja`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Coreano                |     `ko`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Norueguês (Bokmål)   |     `no`      |     ✓      |     ✓      |                2019-10-01                 | `nb` também é aceito |
| Polonês                |     `pl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Português (Brasil)   |    `pt-BR`    |     ✓      |     ✓      |                2019-10-01                 |                    |
| Português (Portugal) |    `pt-PT`    |     ✓      |     ✓      |                2019-10-01                 | `pt` também é aceito |
| Russo               |     `ru`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Espanhol               |     `es`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Sueco               |     `sv`      |     ✓      |     ✓      |                2019-10-01                 |                    |

#### <a name="entity-linking"></a>[Vinculação de entidade](#tab/entity-linking)

| Linguagem | Código de idioma | suporte a v2 | suporte v3 | Disponível a partir da versão do modelo V3: | Observações |
|:---------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:-----:|
| Inglês  |     `en`      |     ✓      |     ✓      |                2019-10-01                 |       |
| Espanhol  |     `es`      |     ✓      |     ✓      |                2019-10-01                 |       |

#### <a name="language-detection"></a>[Detecção de Idioma](#tab/language-detection)

O API de Análise de Texto pode detectar uma ampla gama de linguagens, variantes, dialetos e algumas linguagens regionais/culturais e retornar idiomas detectados com seu nome e código. Análise de Texto Detecção de Idioma parâmetros de código de idioma estão em conformidade com o padrão [bcp-47](https://tools.ietf.org/html/bcp47) com a maioria deles de acordo com os identificadores [ISO-639-1](https://www.iso.org/iso-639-language-codes.html) . 

Se você tiver um conteúdo expresso em um idioma usado com menos frequência, experimente a Detecção de Idioma para ver se ela retorna um código. A resposta para idiomas que não pode ser detectada é `unknown`.

| Idioma | Código de idioma | suporte v3 | Disponível a partir da versão do modelo V3: |
|:-|:-:|:-:|:-:|
| Africâner | `af` | ✓ |  |
| Albanês | `sq` | ✓ |  |
| Árabe | `ar` | ✓ |  |
| Armênia | `hy` | ✓ |  |
| Basco | `eu` | ✓ |  |
| Bielorrusso | `be` | ✓ |  |
| Bengali | `bn` | ✓ |  |
| Bósnio | `bs` | ✓ | 2020-09-01 |
| Búlgaro | `bg` | ✓ |  |
| Birmanês | `my` | ✓ |  |
| Catalão, valenciano | `ca` | ✓ |  |
| Khmer central | `km` | ✓ |  |
| Chinês | `zh` | ✓ |  |
| Chinês simplificado | `zh_chs` | ✓ |  |
| Chinês (tradicional) | `zh_cht` | ✓ |  |
| Croata | `hr` | ✓ |  |
| Tcheco | `cs` | ✓ |  |
| Dinamarquês | `da` | ✓ |  |
| Dari | `prs` | ✓ | 2020-09-01 |
| Divehi, dhivehi, maldivense | `dv` | ✓ |  |
| Holandês; Flamengo | `nl` | ✓ |  |
| Inglês | `en` | ✓ |  |
| Esperanto | `eo` | ✓ |  |
| Estoniano | `et` | ✓ |  |
| Fijiano | `fj` | ✓ | 2020-09-01 |
| Finlandês | `fi` | ✓ |  |
| Francês | `fr` | ✓ |  |
| Galego | `gl` | ✓ |  |
| Georgiano | `ka` | ✓ |  |
| Alemão | `de` | ✓ |  |
| Grego | `el` | ✓ |  |
| Guzerate | `gu` | ✓ |  |
| Crioulo, crioulo haitiano | `ht` | ✓ |  |
| Hebraico | `he` | ✓ |  |
| Híndi | `hi` | ✓ |  |
| Hmong Daw | `mww` | ✓ | 2020-09-01 |
| Húngaro | `hu` | ✓ |  |
| Islandês | `is` | ✓ |  |
| Indonésio | `id` | ✓ |  |
| Inuktitut | `iu` | ✓ |  |
| Irlandês | `ga` | ✓ |  |
| Italiano | `it` | ✓ |  |
| Japonês | `ja` | ✓ |  |
| canarim | `kn` | ✓ |  |
| Cazaque | `kk` | ✓ | 2020-09-01 |
| Coreano | `ko` | ✓ |  |
| Curdo | `ku` | ✓ |  |
| Lao | `lo` | ✓ |  |
| Latim | `la` | ✓ |  |
| Letão | `lv` | ✓ |  |
| Lituano | `lt` | ✓ |  |
| Macedônio | `mk` | ✓ |  |
| Malgaxe | `mg` | ✓ | 2020-09-01 |
| Malaio | `ms` | ✓ |  |
| Malaiala | `ml` | ✓ |  |
| Maltês | `mt` | ✓ |  |
| Maori | `mi` | ✓ | 2020-09-01 |
| Marati | `mr` | ✓ | 2020-09-01 |
| Norueguês | `no` | ✓ |  |
| Norueguês Nynorsk | `nn` | ✓ |  |
| Oriya | `or` | ✓ |  |
| Pachto, Pushto | `ps` | ✓ |  |
| Persa | `fa` | ✓ |  |
| Polonês | `pl` | ✓ |  |
| Português | `pt` | ✓ |  |
| Punjabi, panjabi | `pa` | ✓ |  |
| Queretaro Otomi | `otq` | ✓ | 2020-09-01 |
| Romeno, Moldávia, moldavo | `ro` | ✓ |  |
| Russo | `ru` | ✓ |  |
| Samoano | `sm` | ✓ | 2020-09-01 |
| Sérvio | `sr` | ✓ |  |
| Cingalês, Sinhala | `si` | ✓ |  |
| Eslovaco | `sk` | ✓ |  |
| Esloveno | `sl` | ✓ |  |
| Somali | `so` | ✓ |  |
| Espanhol, castelhano | `es` | ✓ |  |
| Swahili | `sw` | ✓ |  |
| Sueco | `sv` | ✓ |  |
| Tagalo | `tl` | ✓ |  |
| Taitiano | `ty` | ✓ | 2020-09-01 |
| Tâmil | `ta` | ✓ |  |
| Télugo | `te` | ✓ |  |
| Tailandês | `th` | ✓ |  |
| Tongan | `to` | ✓ | 2020-09-01 |
| Turco | `tr` | ✓ |  |
| Ucraniano | `uk` | ✓ |  |
| Urdu | `ur` | ✓ |  |
| Uzbek | `uz` | ✓ |  |
| Vietnamita | `vi` | ✓ |  |
| Galês | `cy` | ✓ |  |
| Iídiche | `yi` | ✓ |  |
| Yucatec Maya | `yua` | ✓ |  |

---

## <a name="see-also"></a>Confira também

* [O que é o API de Análise de Texto?](overview.md)   
