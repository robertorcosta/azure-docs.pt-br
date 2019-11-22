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
ms.openlocfilehash: a4c911fa077c2ec332974fb2f0c5abcdef21307c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74284076"
---
## <a name="general-entity-types"></a>Tipos de entidade geral:

### <a name="person"></a>Pessoa
Nomes reconhecidos e outras pessoas em texto.
Linguagens:
* Visualização pública: `English`

| Nome do subtipo | DESCRIÇÃO             |
|--------------|-------------------------|
| N/D          | Nomes reconhecidos, por exemplo `Bill Gates`, `Marie Curie` |

### <a name="location"></a>Local padrão

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

Números de telefone (somente números de telefone dos EUA). 

Linguagens:


* Visualização pública: `English`

| Nome do subtipo | DESCRIÇÃO                                  |
|----------|----------------------------------------------|
| N/D         | Números de telefone dos EUA, por exemplo `(312) 555-0176`. |

### <a name="email"></a>Email

Endereço de email. 

Linguagens:


* Visualização pública: `English`

| Nome do subtipo | DESCRIÇÃO                                  |
|----------|----------------------------------------------|
| N/D         | Endereço de email, por exemplo `support@contoso.com` |

### <a name="url"></a>URL

URLs da Internet.

Linguagens:


* Visualização pública: `English`

| Nome do subtipo | DESCRIÇÃO                                           |
|----------|-------------------------------------------------------|
| N/D         | URLs para sites, por exemplo `https://www.bing.com`. |

###  <a name="number"></a>NUMBER

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
