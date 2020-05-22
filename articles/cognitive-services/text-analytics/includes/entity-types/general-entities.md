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
ms.openlocfilehash: 60cd6b8ba2cbfca497ae1b92113b12e85a34bf26
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83778228"
---
## <a name="general-entity-types"></a>Tipos de entidade geral:

### <a name="person"></a>Person

Reconhece nomes de pessoas no texto.

Idiomas:
* Visualização pública:,,,,,,,,,,,, `Arabic` `Czech` `Chinese-Simplified` `Danish` `Dutch` `English` `Finnish` `French` `German` `Hungarian` `Italian` `Japanese` `Korean` , `Norwegian (Bokmål)` , `Polish` , `Portuguese (Portugal)` , `Portuguese (Brazil)` , `Russian` , `Spanish` `Swedish` e`Turkish`

| Nome do subtipo | Description                                                      | Disponível a partir da versão do modelo |
|--------------|------------------------------------------------------------------|---------------------------------------|
| N/D          | Nomes de pessoas reconhecidos, por exemplo `Bill Gates` ,`Marie Curie` | `2019-10-01`                          | 

### <a name="persontype"></a>Persontype
Tipo de trabalho ou função mantida por uma pessoa.

Idiomas:
* Visualização pública:`English`

| Nome do subtipo | Description                                                                                | Disponível a partir da versão do modelo |
|--------------|--------------------------------------------------------------------------------------------|----------------------------------------|
| N/D          | Tipos de trabalho, por exemplo,,, `civil engineer` `salesperson` `chef` `librarian` ,`nursing aide` | `2020-02-01`                           |

### <a name="location"></a>Localização

Pontos de referência naturais e humanos, estruturas, recursos geográficos e entidades geopolítica.

Idiomas:

* Visualização pública:,,,,,,,,,,,, `Arabic` `Czech` `Chinese-Simplified` `Danish` `Dutch` `English` `Finnish` `French` `German` `Hungarian` `Italian` `Japanese` `Korean` , `Norwegian (Bokmål)` , `Polish` , `Portuguese (Portugal)` , `Portuguese (Brazil)` , `Russian` , `Spanish` `Swedish` e`Turkish`

| Nome do subtipo              | Description                                                                              | Disponível a partir da versão do modelo |
|---------------------------|------------------------------------------------------------------------------------------|----------------------------------------|
| N/D                       | locais, `Atlantic Ocean` por exemplo,,, `library` `Eiffel Tower``Statue of Liberty`  | `2019-10-01`                           |
| Geopolítica Entity (GPE) – somente em inglês| Cidades, países/regiões, Estados, por exemplo `Seattle` , `Pennsylvania` ,, `South Africa``Tokyo` | `2020-02-01`                           |

### <a name="organization"></a>Organização  

Organizações reconhecidas, corporações, agências e outros grupos de pessoas. Por exemplo: empresas, grupos políticos, faixas musicais, clubes esporte, órgãos governamentais e organizações públicas. As nacionalidades e Religions não são incluídas nesse tipo de entidade. 

Idiomas: 

* Visualização pública:,,,,,,,,,,,, `Arabic` `Czech` `Chinese-Simplified` `Danish` `Dutch` `English` `Finnish` `French` `German` `Hungarian` `Italian` `Japanese` `Korean` , `Norwegian (Bokmål)` , `Polish` , `Portuguese (Portugal)` , `Portuguese (Brazil)` , `Russian` , `Spanish` `Swedish` e`Turkish`

| Nome do subtipo | Description                                                                                             | Disponível a partir da versão do modelo |
|--------------|---------------------------------------------------------------------------------------------------------|----------------------------------------|
| N/D          | organizações, `Microsoft` por exemplo,,, `NASA` `National Oceanic and Atmospheric Administration``VOA` | `2019-10-01`                           |

### <a name="event"></a>Evento  

Eventos históricos, sociais e naturais.  

Idiomas: 

* Visualização pública:`English`

| Nome do subtipo | Description                                                            | Disponível a partir da versão do modelo |
|--------------|------------------------------------------------------------------------|----------------------------------------|
| N/D          | Eventos como `wedding` ,, `hurricane` `car accident` , `solar eclipse` ,`American Revolution` | `2020-02-01`                           |

### <a name="product"></a>Produto  

Objetos físicos de várias categorias.  

Idiomas: 

* Visualização pública:`English`

| Nome do subtipo | Description                                                                        | Disponível a partir da versão do modelo |
|--------------|------------------------------------------------------------------------------------|----------------------------------------|
| N/D          | Por `Microsoft Surface laptop` exemplo,, `sunglasses` , `motorcycle` , `bag` ,`Xbox` | `2020-02-01`                           |
| Computação    | `Azure Cosmos DB`, `Azure Kubernetes Service`                                     | `2020-02-01`                           |

### <a name="skill"></a>Habilidade  

Uma entidade que descreve um recurso ou uma experiência.  

Idiomas: 

* Visualização pública:`English`

| Nome do subtipo | Description                                                                 | Disponível a partir da versão do modelo |
|--------------|-----------------------------------------------------------------------------|----------------------------------------|
| N/D          | `nursing`, `data mining`, `linguistics`, `critical thinking`, `photography` | `2020-02-01`                           |

### <a name="phone-number"></a>Número do telefone

Números de telefone (somente números de telefone dos EUA). 

Idiomas:

* Visualização pública:`English`

| Nome do subtipo | Description                                    | Disponível a partir da versão do modelo |
|--------------|------------------------------------------------|----------------------------------------|
| N/D          | Números de telefone dos EUA, por exemplo`(312) 555-0176` | `2019-10-01`                           |

### <a name="email"></a>Email

Endereço de email. 

Idiomas:

* Visualização pública:`English`

| Nome do subtipo | Description                                      | Disponível a partir da versão do modelo |
|--------------|--------------------------------------------------|----------------------------------------|
| N/D          | Endereço de email, por exemplo`support@contoso.com` | `2019-10-01`                           |

### <a name="url"></a>URL

URLs da Internet.

Idiomas:

* Visualização pública:`English`

| Nome do subtipo | Description                                          | Disponível a partir da versão do modelo |
|--------------|------------------------------------------------------|----------------------------------------|
| N/D          | URLs para sites, por exemplo`https://www.bing.com` | `2019-10-01`                           |

### <a name="ip-address"></a>Endereço IP

Endereço de protocolo de Internet

Idiomas:

* Visualização pública:`English`

| Nome do subtipo | Description                              | Disponível a partir da versão do modelo |
|--------------|------------------------------------------|----------------------------------------|
| N/D          | Endereço de rede, por exemplo`10.0.0.101` | `2019-10-01`                           |

###  <a name="datetime"></a>Datetime

Entidades de data e hora. 

* Disponível a partir da versão do modelo`2019-10-01`

Idiomas:

* Visualização pública: `Chinese-Simplified` , `English` , `French` `German` e`Spanish`

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

Idiomas:

* Visualização pública: `Chinese-Simplified` , `English` , `French` `German` e`Spanish`

| Nome do subtipo    | Exemplos                     |
|-------------|------------------------------|
| Número         | `6`, `six`                   |
| Percentual  | `50%`, `fifty percent`       |
| Ordinal     | `2nd`, `second`              |
| Idade         | `90 day old`, `30 years old` |
| Moeda    | `$10.99`, `&euro;30.00`           |
| Dimensão   | `10 miles`, `40 cm`          |
| Temperatura | `32 degrees`, `10°C`         |
