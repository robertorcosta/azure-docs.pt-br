---
title: Entidades nomeadas gerais
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: ba516a548fc8385ca86526a7f2dd082b27e53208
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "77211399"
---
## <a name="general-entity-types"></a>Tipos de entidade geral:

### <a name="person"></a>Person

Reconhece nomes de pessoas no texto.

Linguagens:
* Visualização pública: `Arabic`, `Czech` `Chinese-Simplified` `Danish` `Dutch` `Korean` `Norwegian (Bokmål)` `Polish` `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` `Spanish` `Swedish` ,, `Finnish`,,,,,,,,,,,,,,, e `English` `French` `German` `Hungarian` `Italian` `Japanese``Turkish`

| Nome do subtipo | Descrição                                                      | Disponível a partir da versão do modelo |
|--------------|------------------------------------------------------------------|---------------------------------------|
| N/D          | Nomes de pessoas reconhecidos `Bill Gates`, por exemplo,`Marie Curie` | `2019-10-01`                          | 

### <a name="persontype"></a>Persontype
Tipo de trabalho ou função mantida por uma pessoa.

Linguagens:
* Visualização pública:`English`

| Nome do subtipo | Descrição                                                                                | Disponível a partir da versão do modelo |
|--------------|--------------------------------------------------------------------------------------------|----------------------------------------|
| N/D          | Tipos de trabalho, `civil engineer` `salesperson` `chef`por exemplo, `librarian`,,,`nursing aide` | `2020-02-01`                           |

### <a name="location"></a>Location

Pontos de referência naturais e humanos, estruturas, recursos geográficos e entidades geopolítica.

Linguagens:

* Visualização pública: `Arabic`, `Czech` `Chinese-Simplified` `Danish` `Dutch` `Korean` `Norwegian (Bokmål)` `Polish` `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` `Spanish` `Swedish` ,, `Finnish`,,,,,,,,,,,,,,, e `English` `French` `German` `Hungarian` `Italian` `Japanese``Turkish`

| Nome do subtipo              | Descrição                                                                              | Disponível a partir da versão do modelo |
|---------------------------|------------------------------------------------------------------------------------------|----------------------------------------|
| N/D                       | locais, por exemplo `Atlantic Ocean` `library` `Eiffel Tower`,,,`Statue of Liberty`  | `2019-10-01`                           |
| Geopolítica Entity (GPE) – somente em inglês| Cidades, países, Estados, por `Seattle`exemplo `Pennsylvania`, `South Africa`,,`Tokyo` | `2020-02-01`                           |

### <a name="organization"></a>Organização  

Organizações reconhecidas, corporações, agências e outros grupos de pessoas. Por exemplo: empresas, grupos políticos, faixas musicais, clubes esporte, órgãos governamentais e organizações públicas. As nacionalidades e Religions não são incluídas nesse tipo de entidade. 

Linguagens: 

* Visualização pública: `Arabic`, `Czech` `Chinese-Simplified` `Danish` `Dutch` `Korean` `Norwegian (Bokmål)` `Polish` `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` `Spanish` `Swedish` ,, `Finnish`,,,,,,,,,,,,,,, e `English` `French` `German` `Hungarian` `Italian` `Japanese``Turkish`

| Nome do subtipo | Descrição                                                                                             | Disponível a partir da versão do modelo |
|--------------|---------------------------------------------------------------------------------------------------------|----------------------------------------|
| N/D          | organizações, por exemplo `Microsoft` `NASA` `National Oceanic and Atmospheric Administration`,,,`VOA` | `2019-10-01`                           |

### <a name="event"></a>Evento  

Eventos históricos, sociais e naturais.  

Linguagens: 

* Visualização pública:`English`

| Nome do subtipo | Descrição                                                            | Disponível a partir da versão do modelo |
|--------------|------------------------------------------------------------------------|----------------------------------------|
| N/D          | Eventos como `wedding`, `hurricane`, `car accident`,, `solar eclipse``American Revolution` | `2020-02-01`                           |

### <a name="product"></a>Produto  

Objetos físicos de várias categorias.  

Linguagens: 

* Visualização pública:`English`

| Nome do subtipo | Descrição                                                                        | Disponível a partir da versão do modelo |
|--------------|------------------------------------------------------------------------------------|----------------------------------------|
| N/D          | `Microsoft Surface laptop`Por exemplo `sunglasses` `motorcycle`,,,,, `bag``Xbox` | `2020-02-01`                           |
| Computação    | `Azure Cosmos DB`, `Azure Kubernetes Service`                                     | `2020-02-01`                           |

### <a name="skill"></a>Habilidade  

Uma entidade que descreve um recurso ou uma experiência.  

Linguagens: 

* Visualização pública:`English`

| Nome do subtipo | Descrição                                                                 | Disponível a partir da versão do modelo |
|--------------|-----------------------------------------------------------------------------|----------------------------------------|
| N/D          | `nursing`, `data mining`, `linguistics`, `critical thinking`, `photography` | `2020-02-01`                           |

### <a name="phone-number"></a>Número do telefone

Números de telefone (somente números de telefone dos EUA). 

Linguagens:

* Visualização pública:`English`

| Nome do subtipo | Descrição                                    | Disponível a partir da versão do modelo |
|--------------|------------------------------------------------|----------------------------------------|
| N/D          | Números de telefone dos EUA, por exemplo`(312) 555-0176` | `2019-10-01`                           |

### <a name="email"></a>Email

Endereço de email. 

Linguagens:

* Visualização pública:`English`

| Nome do subtipo | Descrição                                      | Disponível a partir da versão do modelo |
|--------------|--------------------------------------------------|----------------------------------------|
| N/D          | Endereço de email, por exemplo`support@contoso.com` | `2019-10-01`                           |

### <a name="url"></a>URL

URLs da Internet.

Linguagens:

* Visualização pública:`English`

| Nome do subtipo | Descrição                                          | Disponível a partir da versão do modelo |
|--------------|------------------------------------------------------|----------------------------------------|
| N/D          | URLs para sites, por exemplo`https://www.bing.com` | `2019-10-01`                           |

### <a name="ip-address"></a>Endereço IP

Endereço de protocolo de Internet

Linguagens:

* Visualização pública:`English`

| Nome do subtipo | Descrição                              | Disponível a partir da versão do modelo |
|--------------|------------------------------------------|----------------------------------------|
| N/D          | Endereço de rede, por exemplo`10.0.0.101` | `2019-10-01`                           |

###  <a name="datetime"></a>Datetime

Entidades de data e hora. 

* Disponível a partir da versão do modelo`2019-10-01`

Linguagens:

* Visualização pública: `Chinese-Simplified`, `English`, `French` `German` e`Spanish`

| Nome do subtipo    | Exemplos                     |
|-------------|------------------------------|
| N/D         | `6:30PM February 4, 2012`, `4/1/2011 2:45`                   |
| Data  | `May 2nd, 2017`, `05/02/2017`       |
| Hora     | `8:15`, `6AM`              |
| DateRange    | `August 2nd to August 5th`         |
| TimeRange   | `4-6PM`, `10:00AM to Noon`          |
| Duration | `2.5 minutes`, `one and a half hours`         |
| Definir | `every Saturday`         |

###  <a name="quantity"></a>Quantidade

Números e quantidades numéricas. 

* Disponível a partir da versão do modelo`2019-10-01`

Linguagens:

* Visualização pública: `Chinese-Simplified`, `English`, `French` `German` e`Spanish`

| Nome do subtipo    | Exemplos                     |
|-------------|------------------------------|
| Número         | `6`, `six`                   |
| Porcentagem  | `50%`, `fifty percent`       |
| Ordinal     | `2nd`, `second`              |
| Idade         | `90 day old`, `30 years old` |
| Currency    | `$10.99`, `€30.00`           |
| Dimensão   | `10 miles`, `40 cm`          |
| Temperatura | `32 degrees`, `10°C`         |
