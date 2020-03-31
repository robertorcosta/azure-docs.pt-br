---
title: Melhores práticas para moldar consultas JSON - Azure Time Series Insights | Microsoft Docs
description: Aprenda a melhorar a eficiência da consulta de consultas do Azure Time Series, moldando o JSON.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 8232fd4c2a1e17800c96854b4ba7298e57ed84b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989885"
---
# <a name="shape-json-to-maximize-query-performance"></a>Modele JSON para maximizar o desempenho da consulta 

Este artigo fornece orientações sobre como moldar o JSON para maximizar a eficiência das consultas do Azure Time Series Insights.

## <a name="video"></a>Vídeo

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Aprenda as melhores práticas para moldar o JSON para atender às suas necessidades de armazenamento.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Práticas recomendadas

Pense em como você envia eventos para o Time Series Insights. Ou seja, você sempre:

1. Envie dados pela rede de maneira mais eficiente possível.
1. Certifique-se de que seus dados sejam armazenados de forma a realizar agregações adequadas ao seu cenário.
1. Certifique-se de que você não atingirá os limites máximos de propriedade do Time Series Insights de:
   - 600 propriedades (colunas) para ambientes S1.
   - 800 propriedades (colunas) para ambientes S2.

> [!TIP]
> Revisar [limites e planejamento](time-series-insights-update-plan.md) no Azure Time Series Insights Preview.

A seguinte orientação ajuda a garantir o melhor desempenho possível de consulta:

1. Não use propriedades dinâmicas, como um id de identificação de marca, como um nome de propriedade. Esse uso contribui para atingir o limite máximo de propriedades.
1. Não envie propriedades desnecessárias. Se uma propriedade de consulta não for necessária, é melhor não enviá-la. Desta forma, você evita limitações de armazenamento.
1. Use [dados de referência](time-series-insights-add-reference-data-set.md) para evitar o envio de dados estáticos pela rede.
1. Compartilhar propriedades de dimensão entre vários eventos para enviar dados pela rede de forma mais eficiente.
1. Não use o aninhamento profundo de matriz. O Time Series Insights suporta até dois níveis de matrizes aninhadas que contêm objetos. O Time Series Insights achata matrizes nas mensagens em vários eventos com pares de valorde propriedade.
1. Se apenas algumas medidas existirem para a maioria ou todos os eventos, será melhor enviar essas medidas como propriedades separadas dentro do mesmo objeto. Enviá-los separadamente reduz o número de eventos e pode melhorar o desempenho da consulta porque menos eventos precisam ser processados. Quando há várias medidas, enviá-las como valores em uma única propriedade minimiza a possibilidade de atingir o limite máximo de propriedade.

## <a name="example-overview"></a>Visão geral do exemplo

Os dois exemplos a seguir demonstram como enviar eventos para destacar as recomendações anteriores. Seguindo cada exemplo, você pode rever como as recomendações foram aplicadas.

Os exemplos são baseados em um cenário onde vários dispositivos enviam sinais ou medidas. As medições ou sinais podem ser taxa de fluxo, pressão do óleo do motor, temperatura e umidade. No primeiro exemplo, há algumas medidas em todos os dispositivos. O segundo exemplo tem muitos dispositivos, e cada dispositivo envia muitas medidas únicas.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Cenário um: Apenas algumas medições existem

> [!TIP]
> Recomendamos que você envie cada medição ou sinal como uma propriedade ou coluna separada.

No exemplo a seguir, há uma única mensagem do Azure IoT Hub onde a matriz externa contém uma seção compartilhada de valores de dimensão comum. A matriz externa usa dados de referência para aumentar a eficiência da mensagem. Os dados de referência contêm metadados do dispositivo que não mudam a cada evento, mas fornecem propriedades úteis para análise de dados. O loteamento de valores de dimensão comum e a contratação de dados de referência economizam em bytes enviados pelo fio, o que torna a mensagem mais eficiente.

Considere a seguinte carga JSON enviada para o ambiente GA do Time Series Insights usando um [objeto de mensagem de dispositivo IoT](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.message?view=azure-dotnet) que é serializado em JSON quando enviado para a nuvem do Azure:


```JSON
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 1.0172575712203979,
                "Engine Oil Pressure psi ": 34.7
            },
            {
                "Flow Rate ft3/s": 2.445906400680542,
                "Engine Oil Pressure psi ": 49.2
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

* Tabela de dados de referência que tem o dispositivo de propriedade **chaveId**:

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | DADOS\_LINHA | UE |
   | FAAA | DADOS\_LINHA | EUA |

* Tabela de eventos do Time Series Insights, após o achatamento:

   | deviceId | messageId | deviceLocation |  timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | DADOS\_LINHA | UE | 2018-01-17T01:17:00Z | 1,0172575712203979 | 34,7 |
   | FXXX | DADOS\_LINHA | UE | 2018-01-17T01:17:00Z | 2,445906400680542 | 49,2 |
   | FAAA | DADOS\_LINHA | EUA | 2018-01-17T01:18:00Z | 0,58015072345733643 | 22,2 |

> [!NOTE]
> - A coluna **deviceId** serve como o cabeçalho de coluna para vários dispositivos em uma frota. Fazer com que o **dispositivoId valorize** seu próprio nome de propriedade limita o total de dispositivos a 595 (para ambientes S1) ou 795 (para ambientes S2) com as outras cinco colunas.
> - Propriedades desnecessárias são evitadas (por exemplo, as informações de marca e modelo). Como as propriedades não serão consultadas no futuro, eliminá-las permite uma melhor eficiência de rede e armazenamento.
> - Os dados de referência são usados para reduzir o número de bytes transferidos pela rede. Os dois atributos **messageId** e **deviceLocation** são unidos usando o dispositivo de propriedade **chaveId**. Esses dados são juntados aos dados de telemetria no tempo de ingressamento e, em seguida, são armazenados no Time Series Insights para consulta.
> - Duas camadas de aninhamento são usadas, que é a quantidade máxima de aninhamento suportada pelo Time Series Insights. É essencial para evitar matrizes profundamente aninhadas.
> - As medidas são enviadas como propriedades separadas dentro do mesmo objeto porque há poucas medidas. Aqui, **series.Flow Rate psi** e **series.Engine Oil Pressure ft3/s** são colunas exclusivas.

## <a name="scenario-two-several-measures-exist"></a>Cenário dois: Existem várias medidas

> [!TIP]
> Recomendamos que você envie medidas como "tipo", "unidade" e "valor" de tuplas.

Carga JSON de exemplo:

```JSON
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 1.0172575712203979
            },
            {
                "tagId": "oilPressure",
                "value": 49.2
            },
            {
                "tagId": "pumpRate",
                "value": 2.445906400680542
            },
            {
                "tagId": "oilPressure",
                "value": 34.7
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 0.58015072345733643
            },
            {
                "tagId": "oilPressure",
                "value": 22.2
            }
        ]
    }
]
```

* Tabela de dados de referência que tem as principais propriedades **deviceId** e **series.tagId**:

   | deviceId | series.tagId | messageId | deviceLocation | type | unit |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | DADOS\_LINHA | UE | Taxa de Fluxo | ft3/s |
   | FXXX | oilPressure | DADOS\_LINHA | UE | Pressão de óleo do motor | psi |
   | FAAA | pumpRate | DADOS\_LINHA | EUA | Taxa de Fluxo | ft3/s |
   | FAAA | oilPressure | DADOS\_LINHA | EUA | Pressão de óleo do motor | psi |

* Tabela de eventos do Time Series Insights, após o achatamento:

   | deviceId | series.tagId | messageId | deviceLocation | type | unit |  timestamp | series.value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | DADOS\_LINHA | UE | Taxa de Fluxo | ft3/s | 2018-01-17T01:17:00Z | 1,0172575712203979 | 
   | FXXX | oilPressure | DADOS\_LINHA | UE | Pressão de óleo do motor | psi | 2018-01-17T01:17:00Z | 34,7 |
   | FXXX | pumpRate | DADOS\_LINHA | UE | Taxa de Fluxo | ft3/s | 2018-01-17T01:17:00Z | 2,445906400680542 | 
   | FXXX | oilPressure | DADOS\_LINHA | UE | Pressão de óleo do motor | psi | 2018-01-17T01:17:00Z | 49,2 |
   | FAAA | pumpRate | DADOS\_LINHA | EUA | Taxa de Fluxo | ft3/s | 2018-01-17T01:18:00Z | 0,58015072345733643 |
   | FAAA | oilPressure | DADOS\_LINHA | EUA | Pressão de óleo do motor | psi | 2018-01-17T01:18:00Z | 22,2 |

> [!NOTE]
> - As colunas **deviceId** e **series.tagId** servem como os cabeçalhos da coluna para os vários dispositivos e tags em uma frota. O uso de cada um como atributo limita a consulta a dispositivos totais 594 (para ambientes S1) ou 794 (para ambientes S2) com as outras seis colunas.
> - Foram evitadas propriedades desnecessárias, pelo motivo citado no primeiro exemplo.
> - Os dados de referência são usados para reduzir o número de bytes transferidos pela rede introduzindo **deviceId**, que é usado para o par exclusivo de **messageId** e **dispositivoLocalização**. A série de tecla **composta.tagId** é usada para o par único de **tipo** e **unidade**. A tecla composta permite que o par **deviceId** e **series.tagId** seja usado para se referir a quatro valores: **messageId, deviceLocation, type** e **unit**. Esses dados são juntados aos dados de telemetria no momento da ingressinagem. Em seguida, é armazenado em Time Series Insights para consulta.
> - Duas camadas de aninhamento são usadas, pela razão citada no primeiro exemplo.

### <a name="for-both-scenarios"></a>Para ambos os cenários

Para uma propriedade com um grande número de valores possíveis, é melhor enviar valores distintos dentro de uma única coluna em vez de criar uma nova coluna para cada valor. Dos dois exemplos anteriores:

  - No primeiro exemplo, algumas propriedades têm vários valores, por isso é apropriado fazer de cada uma uma propriedade separada.
  - No segundo exemplo, as medidas não são especificadas como propriedades individuais. Em vez disso, eles são uma matriz de valores ou medidas uma propriedade de série comum. A nova **tecla tagId** é enviada, o que cria a nova **série de colunas.tagId** na tabela achatada. O novo **tipo** de propriedades e **unidade** são criados usando dados de referência para que o limite de propriedade não seja atingido.

## <a name="next-steps"></a>Próximas etapas

- Leia mais sobre o envio de [mensagens de dispositivos IoT Hub para a nuvem](../iot-hub/iot-hub-devguide-messages-construct.md).

- Leia a sintaxe de consulta do [Azure Time Series Insights](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-syntax) para saber mais sobre a sintaxe de consulta para os dados do Time Series Insights acessando a API REST.

- Aprenda [a moldar eventos](./time-series-insights-send-events.md).
