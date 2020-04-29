---
title: Práticas recomendadas para o Azure Maps Serviço de Pesquisa | Mapas do Microsoft Azure
description: Saiba como aplicar as práticas recomendadas ao usar o Serviço de Pesquisa de mapas Microsoft Azure.
author: philmea
ms.author: philmea
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8d62d7d278323baa0ae49b9e12f46468efb067a0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335316"
---
# <a name="best-practices-for-azure-maps-search-service"></a>Práticas recomendadas para o Azure Maps Serviço de Pesquisa

O Azure Maps [serviço de pesquisa](https://docs.microsoft.com/rest/api/maps/search) inclui APIs que oferecem vários recursos. Por exemplo, a API de endereço de pesquisa pode encontrar pontos de interesse (POI) ou dados em um local específico. 

Este artigo explica como aplicar práticas sonoras ao chamar dados do Azure Maps Serviço de Pesquisa. Você aprenderá a:

* Crie consultas para retornar correspondências relevantes.
* Limitar os resultados da pesquisa.
* Aprenda as diferenças entre os tipos de resultados.
* Leia a estrutura de resposta de pesquisa de endereço.

## <a name="prerequisites"></a>Pré-requisitos

Para fazer chamadas para as APIs de serviço do Azure Maps, você precisa de uma conta e uma chave do Azure Maps. Para obter mais informações, consulte [criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) e [obter uma chave primária](quick-demo-map-app.md#get-the-primary-key-for-your-account). 

Para obter informações sobre a autenticação no Azure Maps, consulte [gerenciar a autenticação no Azure Maps](./how-to-manage-authentication.md).

> [!TIP]
> Para consultar Serviço de Pesquisa, você pode usar o [aplicativo de postmaster](https://www.getpostman.com/apps) para criar chamadas REST. Ou você pode usar qualquer ambiente de desenvolvimento de API que preferir.

## <a name="best-practices-to-geocode-addresses"></a>Práticas recomendadas para endereços geocódigo

Quando você procura um endereço completo ou parcial usando o Azure Maps Serviço de Pesquisa, a API lê palavras-chave de sua consulta de pesquisa. Em seguida, ele retorna as coordenadas de longitude e latitude do endereço. Esse processo é chamado de *geocodificação*. 

A capacidade de geocódigo em um país depende da disponibilidade dos dados de estrada e da precisão do serviço de geocodificação. Para obter mais informações sobre os recursos de geocodificação do Azure Maps por país ou região, consulte [cobertura de geocodificação](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage).

### <a name="limit-search-results"></a>Limitar os resultados da pesquisa

 A API de pesquisa do Azure Maps pode ajudá-lo a limitar os resultados da pesquisa adequadamente. Você limita os resultados para que você possa exibir dados relevantes para seus usuários.

> [!NOTE]
> As APIs de pesquisa dão suporte a mais parâmetros do que apenas aqueles discutidos neste artigo.

#### <a name="geobiased-search-results"></a>Resultados da pesquisa com tendência

Para resultados geopolares para a área relevante para seu usuário, sempre adicione o máximo possível de detalhes de local. Talvez você queira restringir os resultados da pesquisa especificando alguns tipos de entrada:

* Defina o `countrySet` parâmetro. Você pode defini-lo `US,FR`como, por exemplo. Por padrão, a API pesquisa todo o mundo, para que possa retornar resultados desnecessários. Se a consulta não tiver `countrySet` nenhum parâmetro, a pesquisa poderá retornar resultados imprecisos. Por exemplo, uma pesquisa por uma cidade chamada *Bellevue* retorna resultados dos EUA e da França, pois ambos os países contêm uma cidade chamada *Bellevue*.

* Você pode usar os `btmRight` parâmetros `topleft` e para definir a caixa delimitadora. Esses parâmetros restringem a pesquisa a uma área específica no mapa.

* Para influenciar a área de relevância dos resultados, defina os parâmetros `lat` de `lon` coordenada e. Use o `radius` parâmetro para definir o raio da área de pesquisa.


#### <a name="fuzzy-search-parameters"></a>Parâmetros de pesquisa difusa

Recomendamos que você use a [API difusa de pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) do Azure Maps quando não souber suas entradas de usuário para uma consulta de pesquisa. A API combina a pesquisa de POI e a geocodificação em uma *pesquisa de linha única*canônica: 

* Os `minFuzzyLevel` parâmetros `maxFuzzyLevel` e ajudam a retornar correspondências relevantes mesmo quando os parâmetros de consulta não correspondem exatamente às informações que o usuário deseja. Para maximizar o desempenho e reduzir resultados incomuns, defina consultas de pesquisa para `minFuzzyLevel=1` padrões `maxFuzzyLevel=2`de e. 

    Por exemplo, quando o `maxFuzzyLevel` parâmetro é definido como 2, o termo de pesquisa *restrant* é correspondido a *restaurante*. Você pode substituir os níveis de fuzzing padrão quando precisar. 

* Use o `idxSet` parâmetro para priorizar o conjunto exato de tipos de resultado. Para priorizar um conjunto exato de resultados, você pode enviar uma lista de índices separados por vírgulas. Na lista, a ordem do item não importa. O Azure Maps dá suporte aos seguintes índices:

* `Addr` - **Intervalos de endereços**: pontos de endereço que são interpolados do início e do fim da rua. Esses pontos são representados como intervalos de endereços.
* `Geo` - **Geografia**: divisões administrativas do Land. Uma geografia pode ser um país, um Estado ou uma cidade, por exemplo.
* `PAD` - **Endereços de ponto**: endereços que incluem um nome e número de rua. Os endereços de ponto podem ser encontrados em um índice. Um exemplo é o *Soquel Dr 2501*. Um endereço de ponto fornece o nível mais alto de precisão disponível para endereços.  
* `POI` - **Pontos de interesse**: pontos em um mapa considerados vale a pena receber atenção ou que podem ser interessantes. A [API do endereço de pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) não retorna depois.  
* `Str` - **Ruas**: ruas no mapa.
* `XStr` - **Ruas cruzadas ou interseções**: junções ou lugares onde duas ruas se cruzam.


#### <a name="usage-examples"></a>Exemplos de uso

* `idxSet=POI`-Pesquisar apenas em inglês. 

* `idxSet=PAD,Addr`-Somente endereços de pesquisa. `PAD`indica o endereço do ponto e `Addr` indica o intervalo de endereços.

### <a name="reverse-geocode-and-filter-for-a-geography-entity-type"></a>Reverter-código e filtro para um tipo de entidade de Geografia

Quando você faz uma pesquisa de geocódigo inversa na [API reversa de endereço de pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), o serviço pode retornar polígonos para áreas administrativas.Para restringir a pesquisa a tipos de entidade geography específicos `entityType` , inclua o parâmetro em suas solicitações. 

A resposta resultante contém a ID de Geografia e o tipo de entidade que foi correspondido. Se você fornecer mais de uma entidade, o ponto de extremidade retornará a *menor entidade disponível*. Você pode usar a ID geometry retornada para obter a geometria da geografia por meio do [serviço de polígono de pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

#### <a name="sample-request"></a>Solicitação de exemplo

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

#### <a name="response"></a>Resposta

```JSON
{
    "summary": {
        "queryTime": 14,
        "numResults": 1
    },
    "addresses": [
        {
            "address": {
                "routeNumbers": [],
                "countryCode": "US",
                "countrySubdivision": "WA",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "municipality": "Redmond",
                "country": "United States",
                "countryCodeISO3": "USA",
                "countrySubdivisionName": "Washington"
            },
            "position": "47.639454,-122.130455",
            "dataSources": {
                "geometry": {
                    "id": "00005557-4100-3c00-0000-0000596ae571"
                }
            },
            "entityType": "Municipality"
        }
    ]
}
```

### <a name="set-the-results-language"></a>Definir o idioma dos resultados

Use o `language` parâmetro para definir o idioma dos resultados de pesquisa retornados. Se a solicitação não definir o idioma, por padrão Serviço de Pesquisa usará a linguagem mais comum no país ou na região. Quando nenhum dado está disponível no idioma especificado, o idioma padrão é usado. 

Para obter mais informações, consulte [idiomas com suporte do Azure Maps](https://docs.microsoft.com/azure/azure-maps/supported-languages).


### <a name="use-predictive-mode-automatic-suggestions"></a>Usar o modo de previsão (sugestões automáticas)

Para encontrar mais correspondências para consultas parciais, `typeahead` defina o `true`parâmetro como. Essa consulta é interpretada como uma entrada parcial e a pesquisa entra no modo de previsão. Se você não definir o `typeahead` parâmetro como `true`, o serviço assumirá que todas as informações relevantes foram passadas.

Na consulta de exemplo a seguir, o serviço de endereço de pesquisa é consultado para *Microsoft*. Aqui, o `typeahead` parâmetro definido como `true`. A resposta mostra que o serviço de pesquisa interpretou a consulta como consulta parcial. A resposta contém resultados para uma consulta sugerida automaticamente.

#### <a name="sample-query"></a>Exemplo de consulta

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

#### <a name="response"></a>Resposta

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 18,
        "numResults": 7,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/9438784",
            "score": 2.594099998474121,
            "dist": 314.0590106663596,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
            },
            "position": {
                "lat": 47.63988,
                "lon": -122.12438
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64223,
                    "lon": -122.1256,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1756074",
            "score": 2.592679977416992,
            "dist": 876.0272035824189,
            "address": {
                "streetName": "Microsoft Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64032,
                "lon": -122.1344
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64253,
                    "lon": -122.13535,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63816,
                    "lon": -122.13305,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1470668",
            "score": 2.5290400981903076,
            "dist": 2735.4883918101486,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Bellevue, WA 98007"
            },
            "position": {
                "lat": 47.65784,
                "lon": -122.14335
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.65785,
                    "lon": -122.14335,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65784,
                    "lon": -122.14325,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/12812615",
            "score": 2.527509927749634,
            "dist": 2870.9579016916873,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.66034,
                "lon": -122.1404
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.66039,
                    "lon": -122.14325,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65778,
                    "lon": -122.13749,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/197588",
            "score": 2.4630401134490967,
            "dist": 878.1404663812472,
            "address": {
                "streetName": "157th Avenue Northeast",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "extendedPostalCode": "980525344, 980525398, 980525399",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "157th Avenue Northeast, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64351,
                "lon": -122.13056
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64473,
                    "lon": -122.13058,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.6425,
                    "lon": -122.13016,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/3033991",
            "score": 2.0754499435424805,
            "dist": 3655467.8844475765,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
            },
            "position": {
                "lat": 35.14267,
                "lon": -80.91824
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14287,
                    "lon": -80.91839,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8395877",
            "score": 2.0754499435424805,
            "dist": 3655437.0037482483,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "countrySubdivisionName": "North Carolina",
                "postalCode": "28273",
                "extendedPostalCode": "282738105, 282738106, 282738108, 2827382, 282738200",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28273"
            },
            "position": {
                "lat": 35.14134,
                "lon": -80.9198
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14274,
                    "lon": -80.92159,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14002,
                    "lon": -80.91824,
                    "valid": true
                }
            }
        }
    ]
}
```


### <a name="encode-a-uri-to-handle-special-characters"></a>Codificar um URI para manipular caracteres especiais 

Para localizar endereços de endereço cruzado, você deve codificar o URI para manipular caracteres especiais no endereço. Considere este exemplo de endereço: *1º avenida & rua da União, Seattle*. Aqui, codifique o caractere de e`&`comercial () antes de enviar a solicitação. 

Recomendamos que você codifique os dados de caractere em um URI. Em um URI, você codifica todos os caracteres usando um sinal de porcentagem`%`() e um valor hexadecimal de dois caracteres que corresponde ao código UTF-8 dos caracteres.

#### <a name="usage-examples"></a>Exemplos de uso

Comece com este endereço:

```
query=1st Avenue & E 111th St, New York
```

Codifique o endereço:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```

Você pode usar os métodos a seguir.

JavaScript ou TypeScript:
```Javascript
encodeURIComponent(query)
```

C# ou Visual Basic: 
```csharp
Uri.EscapeDataString(query)
```

Java:
```Java
URLEncoder.encode(query, "UTF-8") 
```

Python:
```Python
import urllib.parse 
urllib.parse.quote(query)
```

C++:
```C++
#include <curl/curl.h>
curl_easy_escape(query)
```

PHP:
```PHP
urlencode(query)
```

Rubi
```Ruby
CGI::escape(query) 
```

Swift:
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Vá
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-searching"></a>Práticas recomendadas para a pesquisa de POI

Em uma pesquisa do POI, você pode solicitar resultados do POI por nome. Por exemplo, você pode pesquisar por um negócio por nome. 

É altamente recomendável que você use `countrySet` o parâmetro para especificar os países em que seu aplicativo precisa de cobertura. O comportamento padrão é Pesquisar o mundo inteiro. Essa pesquisa ampla pode retornar resultados desnecessários e a pesquisa pode levar muito tempo.

### <a name="brand-search"></a>Pesquisa de marca

Para melhorar a relevância dos resultados e as informações na resposta, uma resposta de pesquisa do POI inclui informações da marca. Você pode usar essas informações para analisar a resposta.

Em uma solicitação, você pode enviar uma lista separada por vírgulas de nomes de marca. Use a lista para restringir os resultados a marcas específicas definindo o `brandSet` parâmetro. Em sua lista, a ordem do item não importa. Quando você fornece várias listas de marcas, os resultados retornados devem pertencer a pelo menos uma de suas listas.

Para explorar a pesquisa de marca, vamos fazer uma solicitação de [pesquisa de categoria POI](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) . No exemplo a seguir, procuramos estações de gás perto do campus da Microsoft em Redmond, Washington. A resposta mostra informações da marca para cada POI retornado.

#### <a name="sample-query"></a>Exemplo de consulta

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

#### <a name="response"></a>Resposta

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 276,
        "numResults": 3,
        "offset": 0,
        "totalResults": 762680,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/8831765",
            "score": 5.6631999015808105,
            "dist": 1037.0280221303253,
            "info": "search:ta:840531000004190-US",
            "poi": {
                "name": "Chevron",
                "phone": "+(1)-(425)-6532200",
                "brands": [
                    {
                        "name": "Chevron"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.chevron.com",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2444",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Northeast Bellevue, Bellevue",
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63222,
                        "lon": -122.13312,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831752",
            "score": 5.662710189819336,
            "dist": 1330.1278248163273,
            "info": "search:ta:840539001100326-US",
            "poi": {
                "name": "76",
                "phone": "+(1)-(425)-7472126",
                "brands": [
                    {
                        "name": "76"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.76.com",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2421",
                "streetName": "148Th Ave Ne",
                "municipalitySubdivision": "Redmond, Bridle Trails, Bellevue",
                "municipality": "Redmond, Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "2421 148Th Ave Ne, Bellevue, WA 98007",
                "localName": "Bellevue"
            },
            "position": {
                "lat": 47.63187,
                "lon": -122.14365
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63277,
                    "lon": -122.14498,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63097,
                    "lon": -122.14232,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "minor",
                    "position": {
                        "lat": 47.63187,
                        "lon": -122.14374,
                        "valid": true
                    }
                },
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63186,
                        "lon": -122.14313,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831764",
            "score": 5.662449836730957,
            "dist": 1458.645407416307,
            "info": "search:ta:840539000488527-US",
            "poi": {
                "name": "BROWN BEAR CAR WASH",
                "phone": "+(1)-(425)-6442868",
                "brands": [
                    {
                        "name": "Texaco"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.texaco.com/",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "15248",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Redmond",
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62827,
                        "lon": -122.13628,
                        "valid": true
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>Pesquisa de aeroportos

Usando a API de pesquisa POI, você pode procurar aeroportos usando seu código oficial. Por exemplo, você pode usar o *Sea* para encontrar o Aeroporto Internacional de Seattle-Tacoma: 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Pesquisa de proximidade

Para recuperar resultados de POI em um local específico, você pode tentar usar a [API de pesquisa próxima](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby). O ponto de extremidade retorna apenas resultados de POI. Ele não assume um parâmetro de consulta de pesquisa. 

Para limitar os resultados, recomendamos que você defina o raio.

## <a name="understanding-the-responses"></a>Noções básicas sobre as respostas

Vamos encontrar um endereço em Seattle fazendo uma solicitação de pesquisa de endereço para o Azure Maps Serviço de Pesquisa. Na URL de solicitação a seguir, definimos `countrySet` o parâmetro `US` como para procurar o endereço nos EUA.

### <a name="sample-query"></a>Exemplo de consulta

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

### <a name="supported-types-of-results"></a>Tipos de resultados com suporte

* **Endereço de ponto**: pontos em um mapa que têm um endereço específico com um nome e número de rua. O endereço de ponto fornece o nível mais alto de precisão para endereços. 

* **Intervalo de endereços**: o intervalo de pontos de endereço que são interpolados do início e do fim da rua.  

* **Geografia**: áreas em um mapa que representam divisões administrativas de um terreno, por exemplo, país, estado ou cidade. 

* **POI**: pontos em um mapa que vale a pena dar atenção e que podem ser interessantes.

* **Rua**: ruas no mapa. Os endereços são resolvidos para as coordenadas de latitude e longitude da rua que contém o endereço. O número da casa pode não ser processado. 

* **Entre ruas**: interseções. As ruas cruzadas representam junções nas quais duas ruas se cruzam.

### <a name="response"></a>Resposta

Vamos examinar a estrutura de resposta. Na resposta a seguir, os tipos dos objetos de resultado são diferentes. Se olhar atentamente, você verá três tipos de objetos de resultado:

* Endereço do ponto
* Street
* Entre as ruas

Observe que a pesquisa de endereço não retorna depois.  

O `Score` parâmetro para cada objeto de resposta indica como a pontuação de correspondência se relaciona com as pontuações de outros objetos na mesma resposta. Para obter mais informações sobre parâmetros de objeto de resposta, consulte [obter endereço de pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress).

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 146,
        "numResults": 6,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/28725082",
            "score": 9.893799781799316,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/6700384",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61724,
                "lon": -122.35207
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61825,
                    "lon": -122.35336,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61626,
                    "lon": -122.35078,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/9701953",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61965,
                "lon": -122.349
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62066,
                    "lon": -122.35041,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.34761,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/11721297",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Downtown Seattle, Denny Regrade, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98121",
                "extendedPostalCode": "981211237",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98121"
            },
            "position": {
                "lat": 47.61825,
                "lon": -122.35078
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61857,
                    "lon": -122.35078,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61825,
                    "lon": -122.35041,
                    "valid": true
                }
            }
        },
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/232144",
            "score": 6.754479885101318,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
            },
            "position": {
                "lat": 47.62545,
                "lon": -122.33974
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62635,
                    "lon": -122.34107,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62455,
                    "lon": -122.33841,
                    "valid": true
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Geometry

Um tipo de resposta de *Geometry* pode incluir a ID geometry retornada no `dataSources` objeto em `geometry` e. `id` Por exemplo, você pode usar o [serviço de polígono de pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) para solicitar os dados de geometria em um formato geojson. Usando esse formato, você pode obter uma estrutura de tópicos de cidade ou aeroporto para um conjunto de entidades. Em seguida, você pode usar esses dados de limite para [Configurar uma cerca geográfica](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) ou [Pesquisar depois dentro da geometria](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


As respostas para a API de [endereço de pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) ou a API [difusa de pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) podem incluir a ID de geometria retornada `dataSources` no objeto `geometry` em `id`e:


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte:

> [!div class="nextstepaction"]
> [Como criar solicitações de Serviço de Pesquisa do Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

> [!div class="nextstepaction"]
> [Documentação da API de Serviço de Pesquisa](https://docs.microsoft.com/rest/api/maps/search)