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
ms.date: 09/12/2020
ms.author: aahi
ms.openlocfilehash: 185a6a26350f7803d109e26d8985510380b8cc16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90056584"
---
# <a name="text-analytics-api-v3-language-support"></a>Suporte à linguagem API de Análise de Texto v3 

> [!IMPORTANT]
> A versão 3. x do API de Análise de Texto não está disponível atualmente nas seguintes regiões: Índia central, Norte dos EAU, Norte da China 2 Leste da China.


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
| Italiano               |     `it`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Japonês              |     `ja`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Coreano                |     `ko`      |            |     ✓      |         2019-10-01         |                    |
| Norueguês (Bokmål)   |     `no`      |     ✓      |     ✓       |        2020-07-01         |                    |
| Polonês                |     `pl`      |     ✓      |            |                            |                    |
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
| Tcheco                 |     `cs`      |     ✓      |            |                                 |                    |
| Chinês simplificado     |   `zh-hans`   |     ✓      |            |                                 | `zh` também é aceito |
| Chinês tradicional   |   `zh-hant`   |     ✓      |            |                                 |                    |
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
| Português (Portugal) |    `pt-PT`    |     ✓      |            |                                 | `pt` também é aceito |
| Português (Brasil)   |    `pt-BR`    |     ✓      |            |                                 |                    |
| Russo              |     `ru`      |     ✓      |            |                                 |                    |
| Espanhol               |     `es`      |     ✓      |     ✓       |              2020-04-01                   |                    |
| Sueco               |     `sv`      |     ✓      |            |                                 |                    |
| Turco               |     `tr`      |     ✓      |            |                                 |                    |

#### <a name="key-phrase-extraction"></a>[Extração de frases-chave](#tab/key-phrase-extraction)

> [!NOTE]
> As versões de modelo do Extração de Frases-chave anteriores a 2020-07-01 têm um limite de 64 caracteres. Esse limite não está presente em versões posteriores do modelo.

| Linguagem              | Código de idioma | suporte a v2 | suporte v3 | Disponível a partir da versão do modelo V3: |       Observações        |
|:----------------------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:------------------:|
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
| Português (Portugal) |    `pt-PT`    |     ✓      |     ✓      |                2019-10-01                 | `pt` também é aceito |
| Português (Brasil)   |    `pt-BR`    |     ✓      |     ✓      |                2019-10-01                 |                    |
| Russo               |     `ru`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Espanhol               |     `es`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Sueco               |     `sv`      |     ✓      |     ✓      |                2019-10-01                 |                    |

#### <a name="entity-linking"></a>[Vinculação de entidade](#tab/entity-linking)

| Linguagem | Código de idioma | suporte a v2 | suporte v3 | Disponível a partir da versão do modelo V3: | Observações |
|:---------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:-----:|
| Inglês  |     `en`      |     ✓      |     ✓      |                2019-10-01                 |       |
| Espanhol  |     `es`      |     ✓      |     ✓      |                2019-10-01                 |       |

#### <a name="language-detection"></a>[Detecção de Idioma](#tab/language-detection)

O API de Análise de Texto pode detectar uma ampla gama de linguagens, variantes, dialetos e alguns idiomas regionais/culturais.  A detecção de idioma retorna o "script" de um idioma. Por exemplo, a frase "Eu tenho um cachorro" retornará `en`, em vez de `en-US`. O único especial é chinês, em que a funcionalidade de detecção de idioma retornará `zh_CHS` ou `zh_CHT` se ela puder determinar o script fornecido pelo texto fornecido. Em situações em que um script específico não pode ser identificado para um documento em chinês, ela retornará simplesmente `zh`.

Não publicamos a lista exata de idiomas para esse recurso, mas ele pode detectar uma ampla variedade de idiomas, variantes, dialetos e alguns idiomas regionais/culturais. 

Se você tiver um conteúdo expresso em um idioma usado com menos frequência, experimente a Detecção de Idioma para ver se ela retorna um código. A resposta para idiomas que não pode ser detectada é `unknown`.

---

## <a name="see-also"></a>Confira também

* [O que é o API de Análise de Texto?](overview.md)   
