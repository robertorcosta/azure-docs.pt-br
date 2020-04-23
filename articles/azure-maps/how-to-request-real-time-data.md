---
title: Solicite dados de transporte público em tempo real | Mapas do Microsoft Azure
description: Solicite dados de transporte público em tempo real usando o Serviço de Mobilidade do Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4743fbe84f5d41b4659e13d96868d2f64a473e4b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086070"
---
# <a name="request-real-time-public-transit-data-using-the-azure-maps-mobility-service"></a>Solicite dados de transporte público em tempo real usando o Serviço de Mobilidade do Azure Maps

Este artigo mostra como usar o Azure Maps [Mobility Service](https://aka.ms/AzureMapsMobilityService) para solicitar dados de transporte público em tempo real.

Neste artigo, você aprenderá como solicitar as próximas chegadas em tempo real para todas as linhas que chegam a uma determinada parada

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

## <a name="next-steps"></a>Próximas etapas

Saiba como solicitar dados de trânsito usando o Serviço de Mobilidade:

> [!div class="nextstepaction"]
> [Como solicitar dados de trânsito](how-to-request-transit-data.md)

Explorar a documentação da API do Azure Maps Mobility Service:

> [!div class="nextstepaction"]
> [Documentação da API do Serviço de Mobilidade](https://aka.ms/AzureMapsMobilityService)
