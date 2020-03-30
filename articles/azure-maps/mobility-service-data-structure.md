---
title: Estruturas de dados do Serviço de Mobilidade no Azure Maps| Mapas do Microsoft Azure
description: Neste artigo, você aprenderá sobre campos comuns e estruturas de dados retornadas através dos Serviços de Mobilidade do Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 30696c5dcb3353ea468aa78dbc107dae4d292edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334436"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Estruturas de dados no Azure Maps Mobility Service

Este artigo introduz o conceito de Área Metropolitana no [Azure Maps Mobility Service](https://aka.ms/AzureMapsMobilityService). Discutimos alguns dos campos comuns que são devolvidos quando este serviço é questionado para paradas e linhas de transporte público. Recomendamos a leitura deste artigo antes de desenvolver com as APIs do Serviço de Mobilidade.

## <a name="metro-area"></a>Área metropolitana

Os dados do Serviço de Mobilidade são agrupados por áreas metropolitanas apoiadas. As áreas metropolitanas não seguem os limites da cidade. Uma área metropolitana pode conter várias cidades, cidade densamente povoada, e cidades vizinhas. Na verdade, um país/região pode ser uma área metropolitana. 

O `metroID` é o ID de uma área metropolitana que pode ser usado para chamar a [API Get Metro Area Info](https://aka.ms/AzureMapsMobilityMetroAreaInfo). Use a API "Get Metro" do Azure Maps para solicitar tipos de trânsito, agências de trânsito, alertas ativos e detalhes adicionais para o metrô escolhido. Você também pode solicitar as áreas de metrô e metroIDs suportados. As ids da área metropolitana estão sujeitas a alterações.

**metroID:** 522 **Nome:** Seattle-Tacoma-Bellevue

![Seattle-metro-área](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Parar iDs

As paradas de trânsito podem ser referidas por dois tipos de IDs, o [General Transit Feed Specification (GFTS)](https://gtfs.org/) ID e o Azure Maps stop ID. O ID GFTS é referido como stopKey e o ID de parada do Azure Maps é referido como stopID. Quando se refere freqüentemente a paradas de trânsito, você é encorajado a usar o ID de parada do Azure Maps. stopID é mais estável e provavelmente permanecerá o mesmo enquanto a parada física existir. O GTFS stop ID é atualizado com mais freqüência. Por exemplo, o GTFS stop ID pode ser atualizado de acordo com a solicitação do provedor GTFS ou quando uma nova versão GTFS for lançada. Embora a parada física não tenha tido alteração, o GTFS stop ID pode mudar.

Para começar, você pode solicitar paradas de trânsito nas proximidades usando [a API Get Nearby Transit](https://aka.ms/AzureMapsMobilityNearbyTransit).

## <a name="line-groups-and-lines"></a>Grupos e Linhas de Linhas

O Mobility Service usa um modelo de dados paralelos para linhas e grupos de linha. Este modelo é usado para lidar melhor com as mudanças herdadas das rotas [GTFS](https://gtfs.org/) e os dados de viagens.


### <a name="line-groups"></a>Grupos de linha

A Line Group é uma entidade que agrupa todas as linhas que fazem logicamente parte do mesmo grupo. Normalmente, um grupo de linhas contém duas linhas, uma do ponto A ao B, e a outra retornando do ponto B para A. Ambas as linhas pertenceriam à mesma agência de Transporte Público e teriam o mesmo número de linha. No entanto, pode haver casos em que um grupo de linhas tenha mais de duas linhas ou apenas uma única linha dentro dele.


### <a name="lines"></a>Linhas

Como discutido acima, cada grupo de linhas é composto por um conjunto de linhas. Cada grupo de linhas é composto por duas linhas, e cada linha descreve uma direção.  No entanto, há casos em que mais linhas compõem um grupo de linhas. Por exemplo, há uma linha que às vezes desvia por um determinado bairro e às vezes não. Em ambos os casos, opera o mesmo número de linha. Além disso, um grupo de linha pode ser composto por uma única linha. Uma linha circular com uma única direção é um grupo ling com uma linha.

Para começar, você pode solicitar grupos de linha usando a [API Get Transit Line](https://aka.ms/AzureMapsMobilityTransitLine).


## <a name="next-steps"></a>Próximas etapas

Saiba como solicitar dados de trânsito usando o Serviço de Mobilidade:

> [!div class="nextstepaction"]
> [Como solicitar dados de trânsito](how-to-request-transit-data.md)

Saiba como solicitar dados em tempo real usando o Mobility Service:

> [!div class="nextstepaction"]
> [Como solicitar dados em tempo real](how-to-request-real-time-data.md)

Explore a documentação da API do Azure Maps Mobility Service

> [!div class="nextstepaction"]
> [Documentação da API do Serviço de Mobilidade](https://aka.ms/AzureMapsMobilityService)
