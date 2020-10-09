---
title: Use limites geográficos para filtrar resultados da API de pesquisa do Bing Local Business
titleSuffix: Azure Cognitive Services
description: Use este artigo para aprender a filtrar os resultados da pesquisa da API de pesquisa do Bing Local Business.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 213457bc583494bbe039269b96b25990f7d0a961
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "69906241"
---
# <a name="use-geographic-boundaries-to-filter-results-from-the-bing-local-business-search-api"></a>Use limites geográficos para filtrar resultados da API de pesquisa do Bing Local Business

A API da Pesquisa de empresa local do Bing permite que você defina limites na área geográfica específica que deseja pesquisar usando os parâmetros de consulta `localCircularView` ou `localMapView`. Certifique-se de usar apenas um parâmetro em suas consultas. 

Se um termo de pesquisa contiver um local geográfico explícito, a API do Bing Local Business o usará automaticamente para definir limites para os resultados da pesquisa. Por exemplo, se o termo de pesquisa for `sailing in San Diego`, `San Diego` será usado como o local e todos os outros locais especificados nos parâmetros de consulta ou cabeçalhos de usuários serão ignorados. 

Se uma localização geográfica não for detectada no termo de pesquisa e nenhuma localização geográfica for especificada usando os parâmetros de consulta, a API da Pesquisa de empresa local do Bing tentará determinar o local dos cabeçalhos `X-Search-ClientIP` ou `X-Search-Location` da solicitação. Se nenhum cabeçalho for especificado, a API determinará a localização do IP do cliente da solicitação ou das coordenadas de GPS para dispositivos móveis.

## <a name="localcircularview"></a>localCircularView

O parâmetro `localCircularView` cria uma área geográfica circular em torno de um conjunto de coordenadas de latitude / longitude, definidas por um raio. Ao usar esse parâmetro, as respostas da API da Pesquisa de empresa local do Bing incluirão apenas locais dentro desse círculo, ao contrário do parâmetro `localMapView`, que pode incluir locais ligeiramente fora da área de pesquisa.

Para especificar uma área de pesquisa geográfica circular, escolha uma latitude e longitude para servir como o centro do círculo e um raio em metros. Este parâmetro pode então ser anexado a uma cadeia de caracteres de consulta, por exemplo: `q=Restaurants&localCircularView=47.6421,-122.13715,5000`.

Consulta concluída:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localCircularView=47.6421,-122.13715,5000&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="localmapview"></a>localMapView

O parâmetro `localMapView` especifica uma área geográfica retangular para pesquisa, usando dois conjuntos de coordenadas para especificar seus cantos sudeste e noroeste. Ao usar esse parâmetro, as respostas da API da Pesquisa de empresa local do Bing podem incluir locais dentro e fora da área especificada, ao contrário do parâmetro `localCircularView`, que inclui apenas locais na área de pesquisa.

Para especificar uma área de pesquisa retangular, escolha dois conjuntos de coordenadas de latitude / longitude para servir como os cantos sudeste e noroeste do limite. Certifique-se de definir as coordenadas Sudeste em primeiro lugar, como no exemplo a seguir: `localMapView=47.619987,-122.181671,47.6421,-122.13715`.

Consulta concluída:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localMapView=47.619987,-122.181671,47.6421,-122.13715&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="next-steps"></a>Próximas etapas
- [Início rápido do Java da pesquisa de negócios local](quickstarts/local-search-java-quickstart.md)
- [Início rápido do C# de pesquisa de negócios local](quickstarts/local-quickstart.md)
- [Início rápido do nó de pesquisa comercial local](quickstarts/local-search-node-quickstart.md)
- [Início Rápido do Python em Pesquisa de empresa local](quickstarts/local-search-python-quickstart.md)
