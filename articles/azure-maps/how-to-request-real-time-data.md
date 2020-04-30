---
title: Solicitar dados de trânsito públicos em tempo real | Mapas do Microsoft Azure
description: Solicite dados de trânsito públicos em tempo real usando o serviço de mobilidade mapas de Microsoft Azure.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4743fbe84f5d41b4659e13d96868d2f64a473e4b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82086070"
---
# <a name="request-real-time-public-transit-data-using-the-azure-maps-mobility-service"></a>Solicitar dados de trânsito públicos em tempo real usando o serviço de mobilidade do Azure Maps

Este artigo mostra como usar o [serviço de mobilidade](https://aka.ms/AzureMapsMobilityService) do Azure Maps para solicitar dados de trânsito públicos em tempo real.

Neste artigo, você aprenderá a solicitar as próximas entradas em tempo real para todas as linhas que chegam em uma determinada parada

## <a name="prerequisites"></a>Pré-requisitos

Primeiro, você precisa ter uma conta do Azure Maps e uma chave de assinatura para fazer chamadas para as APIs de trânsito público do Azure Maps. Para obter informações, siga as instruções em [criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) para criar uma conta do Azure Maps. Siga as etapas em [obter chave primária](quick-demo-map-app.md#get-the-primary-key-for-your-account) para obter a chave primária para sua conta. Para obter mais informações sobre a autenticação nos Azure Mapas, confira [Gerenciar a autenticação nos Azure Mapas](./how-to-manage-authentication.md).

Este artigo usa o [aplicativo Postman](https://www.getpostman.com/apps) para criar chamadas REST. Você pode usar qualquer ambiente de desenvolvimento de API que você preferir.

## <a name="request-real-time-arrivals-for-a-stop"></a>Solicitar entradas em tempo real para uma parada

Para solicitar dados de chegada em tempo real de uma parada de trânsito público em particular, você precisará fazer a solicitação para a [API de entradas em tempo real](https://aka.ms/AzureMapsMobilityRealTimeArrivals) do [serviço de mobilidade](https://aka.ms/AzureMapsMobilityService)do Azure Maps. Você precisará de **metroID** e **StopId** para concluir a solicitação. Para saber mais sobre como solicitar esses parâmetros, consulte nosso guia sobre como [solicitar rotas de trânsito públicas](https://aka.ms/AMapsHowToGuidePublicTransitRouting).

Vamos usar "522" como nossa ID metro, que é a ID do metro para a área "Seattle – Tacoma – Bellevue, WA". Use "522---2060603" como a ID de parada, essa parada de barramento está em "ne 24 St & 162nd ave ne, Bellevue WA". Para solicitar os próximos cinco dados de chegada em tempo real, para todas as próximas entradas ao vivo neste ponto de interrupção, conclua as seguintes etapas:

1. Abra o aplicativo do postmaster e vamos criar uma coleção para armazenar as solicitações. Próximo à parte superior do aplicativo do postmaster, selecione **novo**. Na janela **criar nova** , selecione **coleção**.  Nomeie a coleção e selecione o botão **criar** .

2. Para criar a solicitação, selecione **novo** novamente. Na janela **criar nova** , selecione **solicitação**. Insira um **nome de solicitação** para a solicitação. Selecione a coleção que você criou na etapa anterior, como o local no qual salvar a solicitação. Em seguida, selecione **salvar**.

    ![Criar uma solicitação no postmaster](./media/how-to-request-transit-data/postman-new.png)

3. Selecione o método http **Get** na guia Construtor e insira a URL a seguir para criar uma solicitação get. Substitua `{subscription-key}`, por sua chave primária do Azure Maps.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Após uma solicitação bem-sucedida, você receberá a resposta a seguir.  Observe que o parâmetro ' ScheduleType ' define se o tempo de chegada estimado é baseado em dados em tempo real ou estáticos.

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

Saiba como solicitar dados de trânsito usando o serviço de mobilidade:

> [!div class="nextstepaction"]
> [Como solicitar dados de trânsito](how-to-request-transit-data.md)

Explore a documentação da API do serviço de mobilidade do Azure Maps:

> [!div class="nextstepaction"]
> [Documentação da API do serviço de mobilidade](https://aka.ms/AzureMapsMobilityService)
