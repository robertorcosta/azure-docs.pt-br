---
title: Enviar eventos para um ambiente - Azure Time Series Insights | Microsoft Docs
description: Saiba como configurar um hub de eventos, execute um aplicativo de exemplo e envie eventos para o ambiente Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/11/2020
ms.custom: seodec18
ms.openlocfilehash: c3c7f59ecb3a06d80012917e2da4425a899859d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254242"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Enviar eventos para um ambiente do Time Series Insights usando um hub de eventos

Este artigo explica como criar e configurar um hub de eventos no Azure Event Hubs. Ele também descreve como executar um aplicativo de exemplo para empurrar eventos para o Azure Time Series Insights do Event Hubs. Se você tiver um hub de eventos existente com eventos no formato JSON, pule este tutorial e visualize seu ambiente no [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Configurar um hub de eventos

1. Para saber como criar um hub de eventos, leia a [documentação do Event Hubs](https://docs.microsoft.com/azure/event-hubs/).
1. Na caixa de pesquisa, pesquise **Hubs de Eventos**. Na lista retornada, selecione **Hubs de Eventos**.
1. Selecione seu hub de eventos.
1. Quando você cria um hub de eventos, você está criando um namespace do hub de eventos. Se você ainda não criou um hub de eventos dentro do namespace, no menu, em **Entidades,** crie um hub de eventos.  

    [![Lista de hubs de eventos](media/send-events/tsi-connect-event-hub-namespace.png)](media/send-events/tsi-connect-event-hub-namespace.png#lightbox)

1. Depois de criar um hub de eventos, selecione-o na lista de hubs de eventos.
1. No menu, em **Entidades,** selecione **Centros de Eventos**.
1. Selecione o nome do hub de eventos para configurá-lo.
1. Em **Visão Geral,** selecione **Grupos de consumidores**e selecione Grupo de **Consumidores**.

    [![Criar um grupo de consumidores](media/send-events/add-event-hub-consumer-group.png)](media/send-events/add-event-hub-consumer-group.png#lightbox)

1. Certifique-se de criar um grupo de consumidores que seja usado exclusivamente pela fonte de evento Time Series Insights.

    > [!IMPORTANT]
    > Certifique-se de que esse grupo de consumidores não seja usado por nenhum outro serviço, como um trabalho do Azure Stream Analytics ou outro ambiente time series insights. Se o grupo de consumidores é usado por outros serviços, as operações de leitura são prejudicadas em relação a esse ambiente e a outros serviços. Se você usar **$Default** como o grupo de consumidores, outros leitores poderão vir a reutilizar seu grupo de consumidores.

1. No menu, em **Configurações,** selecione **Políticas de acesso compartilhado**e selecione **Adicionar**.

    [![Selecionar Políticas de acesso compartilhado e selecione o botão Adicionar](media/send-events/add-shared-access-policy.png)](media/send-events/add-shared-access-policy.png#lightbox)

1. No painel **Adicionar nova política de acesso compartilhado**, crie um acesso compartilhado denominado **MySendPolicy**. Você usa esta política de acesso compartilhado para enviar eventos nos exemplos C# mais tarde neste artigo.

    [![Na caixa Nome de política, insira MySendPolicy](media/send-events/configure-shared-access-policy-confirm.png)](media/send-events/configure-shared-access-policy-confirm.png#lightbox)

1. Em **Reclame,** selecione a caixa **Enviar** seleção.

## <a name="add-a-time-series-insights-instance"></a>Adicionar uma instância do Time Series Insights

A atualização do Time Series Insights usa instâncias para adicionar dados contextuais aos dados de telemetria recebidos. Os dados são unidos no momento da consulta usando uma **ID da série temporal**. O **ID da série temporal** para o projeto de `id`moinhos de vento de amostra que usamos mais tarde neste artigo é . Para saber mais sobre as instâncias de Visão da Série Do Tempo e **iD da série temporal,** leia [Modelos de Séries Tempo](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Criar uma origem de evento do Time Series Insights

1. Se você ainda não criou uma origem de evento, conclua as etapas para [criar uma origem de evento](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).

1. Defina um valor para `timeSeriesId`. Para saber mais sobre **o Time Series ID**, leia [Modelos de Séries Tempo](./time-series-insights-update-tsm.md).

### <a name="push-events-to-windmills-sample"></a>Empurrar eventos para a amostra de moinhos de vento

1. Na barra de pesquisa, procure **Hubs de Eventos**. Na lista retornada, selecione **Hubs de Eventos**.

1. Selecione a instância do hub de eventos.

1. Vá para **políticas de acesso compartilhado MySendPolicy** > **MySendPolicy**. Copie o valor para **conexão string-primary key**.

    [![Copiar o valor da cadeia de conexão da chave primária](media/send-events/configure-sample-code-connection-string.png)](media/send-events/configure-sample-code-connection-string.png#lightbox)

1. Ir para https://tsiclientsample.azurewebsites.net/windFarmGen.html. A URL cria e executa dispositivos simulados de moinho de vento.
1. Na caixa **'String' Event Hub Connection** na página da web, cole a seqüência de conexões que você copiou no campo de entrada do [moinho de vento](#push-events-to-windmills-sample).
  
    [![Colar a cadeia de conexão da chave primária na caixa Cadeia de Conexão do Hub de Eventos](media/send-events/configure-wind-mill-sim.png)](media/send-events/configure-wind-mill-sim.png#lightbox)

1. Selecione **Clique para iniciar**. 

    > [!TIP]
    > O simulador de moinho de vento também cria JSON que você pode usar como uma carga útil com as APIs de [consulta ga insights](https://docs.microsoft.com/rest/api/time-series-insights/ga-query)da série do tempo .

    > [!NOTE]
    > O simulador continuará a enviar dados até que a guia do navegador seja fechada.

1. Volte ao seu hub de eventos no portal do Azure. Na página **Visão Geral,** os novos eventos recebidos pelo hub de eventos são exibidos.

    [![Uma página de visão geral do hub de eventos que mostra as métricas do hub de eventos](media/send-events/review-windmill-telemetry.png)](media/send-events/review-windmill-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>Formas de JSON com suporte

### <a name="example-one"></a>Exemplo um

* **Entrada:** Um simples objeto JSON.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Saída**: Um evento.

    |id| timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>Exemplo dois

* **Entrada:** Uma matriz JSON com dois objetos JSON. Cada objeto JSON é convertido em um evento.

    ```JSON
    [
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
    ```

* **Saída**: Dois eventos.

    |id| timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>Exemplo três

* **Entrada**: Um objeto JSON com uma matriz JSON aninhada que contém dois objetos JSON.

    ```JSON
    {
        "location":"WestUs",
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z"
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z"
            }
        ]
    }
    ```

* **Saída**: Dois eventos. A propriedade **location** é copiada para cada evento.

    |local|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |Oeste dos EUA|device1|2016-01-08T01:08:00Z|
    |Oeste dos EUA|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Exemplo quatro

* **Entrada**: Um objeto JSON com uma matriz JSON aninhada que contém dois objetos JSON. Essa entrada demonstra que as propriedades globais podem ser representadas pelo objeto JSON complexo.

    ```JSON
    {
        "location":"WestUs",
        "manufacturer":{
            "name":"manufacturer1",
            "location":"EastUs"
        },
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z",
                "data":{
                    "type":"pressure",
                    "units":"psi",
                    "value":108.09
                }
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z",
                "data":{
                    "type":"vibration",
                    "units":"abs G",
                    "value":217.09
                }
            }
        ]
    }
    ```

* **Saída**: Dois eventos.

    |local|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |Oeste dos EUA|manufacturer1|Leste dos EUA|device1|2016-01-08T01:08:00Z|pressão|psi|108.09|
    |Oeste dos EUA|manufacturer1|Leste dos EUA|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Próximas etapas

- [Veja seu ambiente](https://insights.timeseries.azure.com) no explorador Time Series Insights.

- Leia mais sobre [mensagens de dispositivos IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)
