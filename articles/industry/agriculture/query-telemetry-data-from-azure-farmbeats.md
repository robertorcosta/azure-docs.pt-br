---
title: Consulta dados de telemetria ingeridos
description: Este artigo descreve como consultar dados de telemetria ingeridos.
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: f717903b3f953e04c793092c86802f2006de7e82
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349799"
---
# <a name="query-ingested-telemetry-data"></a>Consulta dados de telemetria ingeridos

Este artigo descreve como consultar dados de sensores ingeridos do Azure FarmBeats.

A ingestão de dados de recursos de Internet das Coisas (IoT), como dispositivos e sensores, é um cenário comum no FarmBeats. Você cria metadados para dispositivos e sensores e, em seguida, ingere os dados históricos para FarmBeats em um formato canônico. Uma vez que os dados do sensor estejam disponíveis no FarmBeats Datahub, podemos consultar o mesmo para gerar insights acionáveis ou construir modelos.

## <a name="before-you-begin"></a>Antes de começar

Antes de prosseguir com este artigo, certifique-se de que você instalou FarmBeats e ingerido dados de telemetria de sensores de seus dispositivos IoT para FarmBeats.

Para ingerir dados de telemetria de sensores, visite [dados históricos de telemetria](ingest-historical-telemetry-data-in-azure-farmbeats.md)

Antes de prosseguir, você também precisa garantir que você esteja familiarizado com as APIs FarmBeats REST, pois você consultará a telemetria ingerida usando as APIs. Para obter mais informações sobre as APIs do FarmBeats, consulte [FarmBeats REST APIs](rest-api-in-azure-farmbeats.md). **Certifique-se de que você é capaz de fazer solicitações de API para o seu ponto final farmbeats datahub**.

## <a name="query-ingested-sensor-telemetry-data"></a>Query ingerido dados de telemetria do sensor

Existem duas maneiras de acessar e consultar dados de telemetria do FarmBeats:

- API e
- Visão da série temporal (TSI).

### <a name="query-using-rest-api"></a>Consulta usando API REST

Siga as etapas para consultar os dados de telemetria de sensores ingeridos usando apIs FarmBeats REST:

1. Identifique o sensor em que está interessado. Você pode fazer isso fazendo uma solicitação GET na API /Sensor.

> [!NOTE]
> O **id** e o **sensorModelId** do objeto do sensor interessado.

2. Faça um GET/{id} na API /SensorModel para o **sensorModelId,** conforme observado na etapa 1. O "Modelo do Sensor" tem todos os metadados e detalhes sobre a telemetria ingerida a partir do sensor. Por exemplo, **a Medida do Sensor** dentro do objeto Sensor **Model** tem detalhes sobre quais medidas é o envio do sensor e em quais tipos e unidades. Por exemplo,

  ```json
  {
      "name": "moist_soil_last <name of the sensor measure - this is what we will receive as part of the queried telemetry data>",
      "dataType": "Double <Data Type - eg. Double>",
      "type": "SoilMoisture <Type of measure eg. temperature, soil moisture etc.>",
      "unit": "Percentage <Unit of measure eg. Celsius, Percentage etc.>",
      "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation>",
      "description": "<Description of the measure>"
  }
  ```
Anote a resposta da chamada GET/{id} para o Modelo do Sensor.

3. Faça uma api de /Telemetria com a seguinte carga de entrada

  ```json
  {
    "sensorId": "<id of the sensor as noted in step 1>",
    "searchSpan": {
      "from": "<desired start timestamp in ISO 8601 format; default is UTC>",
      "to": "<desired end timestamp in ISO 8601 format; default is UTC>"
    },
    "filter": {
      "tsx": "string"
    },
    "projectedProperties": [
      {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    ]
  }
  ```
4. A resposta da API /Telemetria será mais ou menos assim:

  ```json
  {
    "timestamps": [
      "2020-XX-XXT07:30:00Z",
      "2020-XX-XXT07:45:00Z"
    ],
    "properties": [
      {
        "values": [
          "<id of the sensor>",
          "<id of the sensor>"
        ],
        "name": "Id",
        "type": "String"
      },
      {
        "values": [
          2.1,
          2.2
        ],
        "name": "moist_soil_last <name of the SensorMeasure as defined in the SensorModel object>",
        "type": "Double <Data Type of the value - eg. Double>"
      }
    ]
  }
  ```
Na resposta ao exemplo acima, a telemetria do sensor consultada fornece dados para dois carimbos de tempo, juntamente com o nome da medida ("moist_soil_last") e valores da telemetria relatada nos dois carimbos de tempo. Você precisará consultar o modelo do sensor associado (conforme descrito na etapa 2) para interpretar o tipo e a unidade dos valores relatados.

### <a name="query-using-azure-time-series-insights-tsi"></a>Consulta usando O Zure Time Series Insights (TSI)

O FarmBeats aproveita [o Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) para ingerir, armazenar, consultar e visualizar dados em escala IoT — dados altamente contextualizados e otimizados para séries tempo.

Os dados de telemetria são recebidos em um EventHub e, em seguida, processados e empurrados para um ambiente TSI dentro do grupo de recursos FarmBeats. Os dados podem então ser consultados diretamente a partir do TSI. Para obter mais informações, consulte [a documentação do TSI](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)

Siga as etapas para visualizar dados no TSI:

1. Vá para o grupo de recursos **Azure Portal** > **FarmBeats DataHub** > ambiente selecionado **de Insights de séries** de tempo (tsi-xxxx) > **Políticas de Acesso a Dados**. Adicione o usuário com acesso ao Leitor ou Contribuinte.
2. Vá para a página **Visão Geral** do ambiente Time **Series Insights** (tsi-xxxx) e selecione a **URL do Explorador de Insights da série do tempo**. Agora você poderá visualizar a telemetria ingerida.

Além de armazenar, consultar e visualizar a telemetria, o TSI também permite a integração a um painel Power BI. Para mais informações, consulte [aqui]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)

## <a name="next-steps"></a>Próximas etapas

Agora você tem dados de sensores consultados da sua instância Azure FarmBeats. Agora, aprenda a [gerar mapas](generate-maps-in-azure-farmbeats.md#generate-maps) para suas fazendas.
