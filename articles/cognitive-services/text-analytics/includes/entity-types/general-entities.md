---
title: Entidades nomeadas gerais
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: aahi
ms.openlocfilehash: e81a1de02c112abd5c52f0f83404a615d738e01c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73508062"
---
## <a name="general-entity-types"></a>Tipos de entidade geral:

### <a name="person"></a>Pessoa
Nomes reconhecidos e outras pessoas em texto.
Linguagens:
* Visualização pública: `English`

| Nome do subtipo | DESCRIÇÃO             |
|--------------|-------------------------|
| N/D          | Nomes reconhecidos, por exemplo `Bill Gates`, `Marie Curie` |

### <a name="location"></a>Localização

Pontos de referência naturais e humanos, estruturas e recursos geográficos.

Linguagens:


* Visualização pública: `English`

| Nome do subtipo | DESCRIÇÃO                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| N/D          | locais, por exemplo `Atlantic Ocean`, `library`, `Eiffel Tower``Statue of Liberty` |

### <a name="organization"></a>Organização  

Organizações reconhecidas, corporações, agências e outros grupos de pessoas. Por exemplo: empresas, grupos políticos, faixas musicais, clubes esporte, órgãos governamentais e organizações públicas. As nacionalidades e Religions não são incluídas nesse tipo de entidade. Linguagens: 

* Visualização pública: `English`

| Nome do subtipo | DESCRIÇÃO                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| N/D          | organizações, por exemplo `Microsoft`, `NASA` `National Oceanic and Atmospheric Administration` |

### <a name="phone-number"></a>Número do telefone

Números de telefone. 

Linguagens:


* Visualização pública: `English`

| Nome do subtipo | DESCRIÇÃO                                  |
|----------|----------------------------------------------|
| N/D         | Números de telefone, por exemplo `+1 123-123-123`. |

### <a name="url"></a>URL

URLs da Internet.

Linguagens:


* Visualização pública: `English`

| Nome do subtipo | DESCRIÇÃO                                           |
|----------|-------------------------------------------------------|
| N/D         | URLs para sites, por exemplo `https://www.bing.com`. |

###  <a name="number"></a>Número

Números e quantidades numéricas. 

Linguagens:


* Visualização pública: `English`

| Nome do subtipo    | Exemplos                     |
|-------------|------------------------------|
| N/D         | `6`, `six`                   |
| Percentual  | `50%`, `fifty percent`       |
| Ordinal     | `2nd`, `second`              |
| Moeda    | `$10.99`, `€30.00`           |
| Dimensão   | `10 miles`, `40 cm`          |
| Temperatura | `32 degrees`, `10°C`         |
