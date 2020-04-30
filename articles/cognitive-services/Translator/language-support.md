---
title: Idiomas compatíveis – API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: O API de Tradução de Texto dá suporte aos seguintes idiomas para conversão de texto em texto usando a conversão de máquina neural (NMT).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/10/2020
ms.author: swmachan
ms.openlocfilehash: 0ecde5acb7dc57ed9e5802c1589d5813a9206643
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81684837"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Idiomas e regiões compatíveis com a API de Tradução de Texto

A API de Tradução de Texto dá suporte aos seguintes idiomas para tradução de texto em texto. A Neural Machine Translation (NMT) é o novo padrão para traduções automáticas de alta qualidade alimentadas por AI e está disponível como padrão usando a V3 da API de texto do tradutor quando um sistema neural está disponível.

[Saiba mais sobre como a tradução automática funciona](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Tradução

**API do Tradutor V2**

> [!NOTE]
> O v2 foi preterido em 30 de abril de 2018. Migre seus aplicativos para v3 a fim de aproveitar as novas funcionalidades disponíveis exclusivamente na v3.

* Somente estatística: nenhum sistema neural está disponível para este idioma.
* Neural disponível: um sistema neural está disponível. Use o parâmetro `category=generalnn` para acessar o sistema neural.
* Padrão neural: neural é o sistema de tradução padrão. Use o parâmetro `category=smt` para acessar o sistema estatístico para uso com o Hub do Microsoft Translator.
* Somente neural: somente a tradução neural está disponível.

**API do Tradutor V3** a API do Tradutor V3 é neural por padrão e sistemas estatísticos estão disponíveis somente quando não houver nenhum sistema neural.

> [!NOTE]
> Atualmente, um subconjunto dos idiomas neurais está disponível no Tradutor personalizado e estamos adicionando gradualmente outros. [Exiba os idiomas disponíveis no momento no Tradutor personalizado](#customization).

|Linguagem|  Código de idioma|  API V3|
|:-----|:-----:|:-----|
|Africâner| `af`|   Neural|
|Árabe|    `ar`    |   Neural|
|Bangla|    `bn`    |   Neural|
|Bósnio (latim)|   `bs`    |   Neural|
|Búlgaro| `bg`    |   Neural|
|Cantonês (tradicional)|   `yue`|  Estatística|
|Catalão|   `ca`    |   Estatística|
|Chinês simplificado|    `zh-Hans`|Neural|
|Chinês (tradicional)|   `zh-Hant`       |Neural|
|Croata|  `hr`    |Neural|
|Tcheco| `cs`    |   Neural|
|Dinamarquês|    `da`        |Neural|
|Holandês| `nl`|   Neural|
|Inglês|   `en`    |   Neural|
|Estoniano|  `et`    |   Neural|
|Fijiano|    `fj`    |   Estatística|
|Filipino|  `fil`   |   Estatística|
|Finlandês|   `fi`    |   Neural|
|Francês|    `fr`    |   Neural|
|Alemão|    `de`    |   Neural|
|Grego| `el`    |   Neural|
|Guzerate|  `gu`    |   Neural|
|Crioulo haitiano|    `ht`        |Estatística|
|Hebraico |`he`   |Neural
|Híndi| `hi`    |   Neural|
|Hmong Daw| `mww`   |   Estatística|
|Húngaro| `hu`    |   Neural|
|Islandês| `is`    |   Neural|
|Indonésio|    `id`    |   Estatística|
|Irlandês | `ga`| Neural
|Italiano|   `it`    |   Neural|
|Japonês|  `ja`    |   Neural|
|canarim|`kn`| Neural
|Suaíli| `sw`    |   Estatística|
|Klingon|   `tlh`   |   Estatística|
|Klingon (plqaD)|   `tlh-Qaak`  |   Estatística|
|Coreano |`ko`   |   Neural|
|Letão|   `lv`    |   Neural|
|Lituano|    `lt`    |   Neural|
|Malgaxe|  `mg`    |   Estatística|
|Malaio| `ms`        |Estatística|
|Malaiala| `ml` | Neural
|Maltês|   `mt`    |   Estatística|
|Maori| `mi`  | Neural|
|Marati| `mr`  | Neural|
|Norueguês| `nb`    |   Neural|
|Persa|   `fa`    |   Neural|
|Polonês|    `pl`    |   Neural|
|Português (Brasil)|   `pt-br` |   Neural|
|Português (Portugal)| `pt-pt` | Neural
|Panjabi|`pa`|Neural
|Queretaro Otomi|   `otq`   |   Estatística|
|Romeno|  `ro`    |   Neural|
|Russo|   `ru`    |   Neural|
|Samoano|    `sm`    |   Estatística|
|Sérvio (cirílico)|    `sr-Cyrl`|  Estatística|
|Sérvio (latino)|   `sr-Latn`       |Estatística|
|Eslovaco|    `sk`    |   Neural|
|Esloveno| `sl`    |   Neural|
|Espanhol|   `es`    |   Neural|
|Sueco|   `sv`    |Neural|
|Taitiano|  `ty`    |Estatística|
|Tâmil| `ta`    |   Neural|
|Télugo|    `te`    |   Neural|
|Tailandês|  `th`    |   Neural|
|Tongan|    `to`    |   Estatística|
|Turco|   `tr`        |Neural|
|Ucraniano| `uk`    |   Neural|
|Urdu|  `ur`    |   Estatística|
|Vietnamita|    `vi`    |   Neural|
|Galês| `cy`    |   Neural|
|Yucatec Maya|  `yua`   |   Estatística|

> [!NOTE]
> O código `pt` de `pt-br`idioma padrão será, Português (Brasil).

## <a name="transliteration"></a>Transliteração

O método Transliterate suporta os seguintes idiomas. Em "Para / De", "<->" indica que o idioma pode ser transliterado de ou para qualquer um dos scripts listados. O "->" indica que o idioma só pode ser transliterado de um script para outro.

| Linguagem    | Código de idioma | script | Para/De | script|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Árabe | `ar` | Árabe `Arab` | <--> | Latim `Latn` |
|Bangla  | `bn` | Bengalês `Beng` | <--> | Latim `Latn` |
| Chinês (Simplificado) | `zh-Hans` | Chinês simplificado `Hans`| <--> | Latim `Latn` |
| Chinês (Simplificado) | `zh-Hans` | Chinês simplificado `Hans`| <--> | Chinês tradicional `Hant`|
| Chinês (Tradicional) | `zh-Hant` | Chinês tradicional `Hant`| <--> | Latim `Latn` |
| Chinês (Tradicional) | `zh-Hant` | Chinês tradicional `Hant`| <--> | Chinês simplificado `Hans` |
| Guzerate | `gu`  | Guzerate `Gujr` | --> | Latim `Latn` |
| Hebraico | `he` | Hebraico `Hebr` | <--> | Latim `Latn` |
| Híndi | `hi` | Devanagari `Deva` | <--> | Latim `Latn` |
| Japonês | `ja` | Japonês `Jpan` | <--> | Latim `Latn` |
| canarim | `kn` | Canarim `Knda` | --> | Latim `Latn` |
| Malaiala | `ml` | Malaiala `Mlym` | --> | Latim `Latn` |
| Marati | `mr` | Devanagari `Deva` | --> | Latim `Latn` |
| Oriya | `or` | Oriya `Orya` | <--> | Latim `Latn` |
| Panjabi | `pa` | Gurmukhi `Guru`  | <--> | Latim `Latn`  |
| Sérvio (cirílico) | `sr-Cyrl` | Cirílico `Cyrl`  | --> | Latim `Latn` |
| Sérvio (latino) | `sr-Latn` | Latim `Latn` | --> | Cirílico `Cyrl`|
| Tâmil | `ta` | Tâmil `Taml` | --> | Latim `Latn` |
| Télugo | `te` | Télugo `Telu` | --> | Latim `Latn` |
| Tailandês | `th` | Tailandês `Thai` | --> | Latim `Latn` |

## <a name="dictionary"></a>Dicionário

O dicionário suporta os seguintes idiomas para ou do inglês usando os métodos Lookup e Examples.

| Linguagem    | Código de idioma |
|:----------- |:-------------:|
| Africâner      | `af`          |
| Árabe       | `ar`          |
| Bangla      | `bn`          |
| Bósnio (latim)      | `bs`          |
| Búlgaro      | `bg`          |
| Catalão      | `ca`          |
| Chinês simplificado      | `zh-Hans`          |
| Croata      | `hr`          |
| Tcheco      | `cs`          |
| Dinamarquês      | `da`          |
| Holandês      | `nl`          |
| Estoniano      | `et`          |
| Finlandês      | `fi`          |
| Francês      | `fr`          |
| Alemão      | `de`          |
| Grego      | `el`          |
| Crioulo haitiano      | `ht`          |
| Hebraico      | `he`          |
| Híndi      | `hi`          |
| Hmong Daw      | `mww`          |
| Húngaro      | `hu`          |
| Islandês    | `is`  |
| Indonésio      | `id`          |
| Italiano      | `it`          |
| Japonês      | `ja`          |
| Suaíli      | `sw`          |
| Klingon      | `tlh`          |
| Coreano      | `ko`          |
| Letão      | `lv`          |
| Lituano      | `lt`          |
| Malaio      | `ms`          |
| Maltês      | `mt`          |
| Norueguês      | `nb`          |
| Persa      | `fa`          |
| Polonês      | `pl`          |
| Português (Brasil)     | `pt-br`          |
| Romeno      | `ro`          |
| Russo      | `ru`          |
| Sérvio (latino)      | `sr-Latn`          |
| Eslovaco     | `sk`          |
| Esloveno      | `sl`          |
| Espanhol      | `es`          |
| Sueco      | `sv`          |
| Tâmil      | `ta`          |
| Tailandês      | `th`          |
| Turco      | `tr`          |
| Ucraniano      | `uk`          |
| Urdu      | `ur`          |
| Vietnamita      | `vi`          |
| Galês      | `cy`          |

## <a name="detect"></a>Detect

API de Tradução de Texto detecta todos os idiomas disponíveis para tradução e transliteração.


## <a name="access-the-translator-text-api-language-list-programmatically"></a>Acessar a lista de idiomas da API de Tradução de Texto de forma programática

Você pode recuperar uma lista de idiomas com suporte para a API de Tradução de Texto v3.0 usando o método de Idiomas. Você pode visualizar a lista por recurso, código de idioma, bem como o nome do idioma em inglês ou qualquer outro idioma suportado. Essa lista é atualizada automaticamente pelo serviço Microsoft Translator conforme novos idiomas são disponibilizados.

[Exibir documentação de referência de operação de idiomas](reference/v3-0-languages.md)

## <a name="customization"></a>Personalização

Os idiomas a seguir estão disponíveis para personalização de ou para o inglês usando o [Tradutor personalizado](https://aka.ms/CustomTranslator).

| Linguagem    | Código de idioma |
|:----------- |:-------------:|
| Árabe       | `ar`          |
| Bangla      | `bn`          |
| Bósnio (latim)      | `bs`          |
| Búlgaro      | `bg`          |
| Chinês simplificado      | `zh-Hans`          |
|Chinês (tradicional)|   `zh-Hant`   |
| Croata      | `hr`          |
| Tcheco      | `cs`          |
| Dinamarquês      | `da`          |
| Holandês      | `nl`          |
| Inglês    | `en`     |
| Estoniano      | `et`          |
| Finlandês      | `fi`          |
| Francês      | `fr`          |
| Alemão      | `de`          |
| Grego      | `el`          |
| Hebraico      | `he`          |
| Híndi      | `hi`          |
| Húngaro      | `hu`          |
| Islandês | `is` |
| Indonésio|   `id`    |
| Irlandês | `ga`  |
| Italiano      | `it`          |
| Japonês      | `ja`          |
| Suaíli|    `sw`    |
| Coreano      | `ko`          |
| Letão      | `lv`          |
| Lituano      | `lt`          |
| Malgaxe| `mg`    |
| Maori| `mi`  |
| Norueguês      | `nb`          |
| Persa      | `fa`          |
| Polonês      | `pl`          |
| Português (Brasil) | `pt-br` |
| Romeno      | `ro`          |
| Russo      | `ru`          |
| Samoano|   `sm`    |
| Sérvio (latino)      | `sr-Latn`          |
| Eslovaco     | `sk`          |
| Esloveno      | `sl`          |
| Espanhol      | `es`          |
| Sueco      | `sv`          |
| Tailandês      | `th`          |
| Turco      | `tr`          |
| Ucraniano      | `uk`          |
| Vietnamita      | `vi`          |
| Galês | `cy` |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Acesse a lista no site do Microsoft Translator

Para uma rápida olhada nos idiomas, o site Microsoft Translator mostra todos os idiomas compatíveis com a Speech API e a API de Tradução de Texto. Esta lista não inclui informações específicas do desenvolvedor, como códigos de idioma.

[Veja a lista de idiomas](https://www.microsoft.com/translator/languages.aspx)
