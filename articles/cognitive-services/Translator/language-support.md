---
title: Idiomas compatíveis – API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: A API de texto tradutor suporta os seguintes idiomas para tradução de texto para texto usando A Tradução de Máquina Neural (NMT).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/10/2020
ms.author: swmachan
ms.openlocfilehash: a4f9833e8dd14dc7c8ec5849cb809bf2089a5dae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77206116"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Idiomas e regiões compatíveis com a API de Tradução de Texto

A API de Tradução de Texto dá suporte aos seguintes idiomas para tradução de texto em texto. A Neural Machine Translation (NMT) é o novo padrão para traduções automáticas de alta qualidade alimentadas por AI e está disponível como padrão usando a V3 da API de texto do tradutor quando um sistema neural está disponível.

[Saiba mais sobre como a tradução automática funciona](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Tradução

**API do Tradutor V2**

> [!NOTE]
> V2 foi preterido em 30 de abril de 2018. Por favor, migre seus aplicativos para V3 para aproveitar as novas funcionalidades disponíveis exclusivamente em V3.

* Apenas estatística: Nenhum sistema neural está disponível para esta linguagem.
* Neural disponível: Um sistema neural está disponível. Use o parâmetro `category=generalnn` para acessar o sistema neural.
* Padrão neural: Neural é o sistema de tradução padrão. Use o parâmetro `category=smt` para acessar o sistema estatístico para uso com o Hub do Microsoft Translator.
* Somente neural: Apenas a tradução neural está disponível.

**API do Tradutor V3** a API do Tradutor V3 é neural por padrão e sistemas estatísticos estão disponíveis somente quando não houver nenhum sistema neural.

> [!NOTE]
> Atualmente, um subconjunto das linguagens neurais estão disponíveis no Custom Translator e estamos gradualmente adicionando outras. [Exibir idiomas atualmente disponíveis no Personal Translator](#customization).

|Idioma|  Código de idioma|  API V3|
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
> O `pt` código de `pt-br`idioma será padrão para , Português (Brasil).

## <a name="transliteration"></a>Transliteração

O método Transliterate suporta os seguintes idiomas. Em "Para / De", "<->" indica que o idioma pode ser transliterado de ou para qualquer um dos scripts listados. O "->" indica que o idioma só pode ser transliterado de um script para outro.

| Idioma    | Código de idioma | Script | Para/De | Script|
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

| Idioma    | Código de idioma |
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

A API de texto do tradutor detecta todos os idiomas disponíveis para tradução e transliteração.


## <a name="access-the-translator-text-api-language-list-programmatically"></a>Acessar a lista de idiomas da API de Tradução de Texto de forma programática

Você pode recuperar uma lista de idiomas com suporte para a API de Tradução de Texto v3.0 usando o método de Idiomas. Você pode visualizar a lista por recurso, código de idioma, bem como o nome do idioma em inglês ou qualquer outro idioma suportado. Essa lista é atualizada automaticamente pelo serviço Microsoft Translator conforme novos idiomas são disponibilizados.

[Exibir documentação de referência de operação de idiomas](reference/v3-0-languages.md)

## <a name="customization"></a>Personalização

Os seguintes idiomas estão disponíveis para personalização para ou a partir do inglês usando [O Tradutor Personalizado](https://aka.ms/CustomTranslator).

| Idioma    | Código de idioma |
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
