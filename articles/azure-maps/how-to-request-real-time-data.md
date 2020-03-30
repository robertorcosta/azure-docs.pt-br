---
title: Solicitar dados de trânsito em tempo real | Mapas do Microsoft Azure
description: Solicite dados em tempo real usando o Serviço de Mobilidade do Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d3145181a863bf8188dd0b0bb52cd2efc662ce2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335477"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Solicite dados em tempo real usando o Serviço de Mobilidade do Azure Maps

Este artigo mostra como usar o Azure Maps [Mobility Service](https://aka.ms/AzureMapsMobilityService) para solicitar dados de trânsito em tempo real.

Neste artigo, você aprenderá a:


 * Solicite as próximas chegadas em tempo real para todas as linhas que chegam a uma determinada parada
 * Solicite informações em tempo real para uma determinada estação de acoplamento de bicicleta.


## <a name="prerequisites"></a>Pré-requisitos

Primeiro você precisa ter uma conta do Azure Maps e uma chave de assinatura para fazer quaisquer chamadas para as APIs de transporte público do Azure Maps. Para obter informações, siga as instruções em [Criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) para criar uma conta do Azure Maps. Siga os passos para obter a [chave primária](quick-demo-map-app.md#get-the-primary-key-for-your-account) para obter a chave principal da sua conta. Para obter mais informações sobre a autenticação nos Azure Mapas, confira [Gerenciar a autenticação nos Azure Mapas](./how-to-manage-authentication.md).


Este artigo usa o [aplicativo Postman](https://www.getpostman.com/apps) para criar chamadas REST. Você pode usar qualquer ambiente de desenvolvimento de API que você preferir.


## <a name="request-real-time-arrivals-for-a-stop"></a>Solicite chegadas em tempo real para uma parada

Para solicitar dados de chegadas em tempo real de uma determinada parada de transporte público, você precisará solicitar a API de [Chegadas em Tempo Real](https://aka.ms/AzureMapsMobilityRealTimeArrivals) do Serviço de [Mobilidade](https://aka.ms/AzureMapsMobilityService)do Azure Maps. Você precisará do **metroID** e **stopID** para completar a solicitação. Para saber mais sobre como solicitar esses parâmetros, consulte nosso guia sobre como [solicitar rotas de transporte público.](https://aka.ms/AMapsHowToGuidePublicTransitRouting) 

Vamos usar "522" como nosso metro ID, que é a id metro para a área "Seattle-Tacoma-Bellevue, WA". Use "522---2060603" como ponto de parada, este ponto de ônibus é em "Ne 24th St & 162nd Ave Ne, Bellevue WA". Para solicitar os próximos cinco dados de chegada em tempo real, para todas as próximas chegadas ao vivo nesta parada, complete as seguintes etapas:

1. Abra o aplicativo Carteiro e crie uma coleção para armazenar as solicitações. Perto do topo do aplicativo Carteiro, selecione **Novo**. Na **janela Criar nova** janela, selecione **Coleção**.  Nomeie a coleção e selecione o botão **Criar.**

2. Para criar a solicitação, selecione **Novo** novamente. Na **janela Criar nova** janela, selecione **'Solicitar'.** Digite um **nome de solicitação** para a solicitação. Selecione a coleção criada na etapa anterior, como o local em que salvar a solicitação. Em seguida, **selecione Salvar**.

    ![Crie uma solicitação no Carteiro](./media/how-to-request-transit-data/postman-new.png)

3. Selecione o método **GET** HTTP na guia construtor e digite a SEGUINTE URL para criar uma solicitação GET. Substitua `{subscription-key}`, com a tecla principal do Azure Maps.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Após uma solicitação bem sucedida, você receberá a seguinte resposta.  Observe que o parâmetro 'scheduleType' define se o tempo estimado de chegada é baseado em dados estáticos ou em tempo real.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 8,
                "scheduleType": "realTime",
                "patternId": "522---4143196",
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 25,
                "scheduleType": "realTime",
                "patternId": "522---3510227",
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```


## <a name="real-time-data-for-bike-docking-station"></a>Dados em tempo real para estação de acoplamento de bicicletas

A [API Get Transit Dock Info](https://aka.ms/AzureMapsMobilityTransitDock) permite que os usuários solicitem informações estáticas e em tempo real. Por exemplo, os usuários podem solicitar informações de disponibilidade e vaga para uma bicicleta ou uma estação de acoplamento de scooter. A [API Get Transit Dock Info](https://aka.ms/AzureMapsMobilityTransitDock) também faz parte do Serviço de [Mobilidade](https://aka.ms/AzureMapsMobilityService)do Azure Maps.

Para fazer uma solicitação à [API Get Transit Dock Info,](https://aka.ms/AzureMapsMobilityTransitDock)você precisará do **dockId** para aquela estação. Você pode obter o ID do dock fazendo uma solicitação de pesquisa para a [API Get Nearby Transit](https://aka.ms/AzureMapsMobilityNearbyTransit) com o parâmetro **objectType** atribuído a "bikeDock". Siga os passos abaixo para obter dados em tempo real de uma estação de acoplamento para bicicletas.


### <a name="get-dock-id"></a>Obter id dock

Para obter **dockID,** siga as etapas abaixo para fazer uma solicitação à API Get Nearby Transit:

1. Em Carteiro, clique em **Nova solicitação** | **OBTENHA solicitação** e nomeá-lo Obter dock **ID**.

2.  Na guia Construtor, selecione o método **GET** HTTP, digite o URL da solicitação a seguir e clique em **Enviar**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. Após uma solicitação bem sucedida, você receberá a seguinte resposta. Observe que agora temos o **id** na resposta, que pode ser usado posteriormente como um parâmetro de consulta na solicitação para a API Get Transit Dock Info.

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 31,
                    "lastUpdated": "2019-09-07T00:55:19Z",
                    "operatorInfo": {
                        "id": "121---80",
                        "name": "Citi Bike"
                    }
                },
                "position": {
                    "latitude": 40.767128,
                    "longitude": -73.962243
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 40.768039,
                        "longitude": -73.963413
                    },
                    "btmRightPoint": {
                        "latitude": 40.766216,
                        "longitude": -73.961072
                    }
                }
            }
        ]
    }
    ```


### <a name="get-real-time-bike-dock-status"></a>Obtenha status de doca de bicicleta em tempo real

Siga as etapas abaixo para fazer uma solicitação à API Get Transit Dock Info para obter dados em tempo real para o dock selecionado.

1. Em Carteiro, clique em **Nova solicitação** | **OBTENHA** e nomeie-a Obter dados **de doca em tempo real**.

2.  Na guia Construtor, selecione o método **GET** HTTP, digite o URL da solicitação a seguir e clique em **Enviar**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. Após uma solicitação bem sucedida, você receberá uma resposta da seguinte estrutura:

    ```JSON
    {
        "availableVehicles": 0,
        "vacantLocations": 31,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-09-07T00:55:19Z",
        "operatorInfo": {
            "id": "121---80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>Próximas etapas

Saiba como solicitar dados de trânsito usando o Serviço de Mobilidade:

> [!div class="nextstepaction"]
> [Como solicitar dados de trânsito](how-to-request-transit-data.md)

Explorar a documentação da API do Azure Maps Mobility Service:

> [!div class="nextstepaction"]
> [Documentação da API do Serviço de Mobilidade](https://aka.ms/AzureMapsMobilityService)
