---
title: Eventos de forma-Azure Time Series Insights | Microsoft Docs
description: Saiba mais sobre as práticas recomendadas e como formatar eventos para consulta na visualização do Azure time insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/24/2020
ms.custom: seodec18
ms.openlocfilehash: 99a2f32c3f76d7fec475c9b299f7208b4db29cfe
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650916"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Formatar eventos com Versão Prévia do Azure Time Series Insights

Este artigo define as práticas recomendadas para formatar suas cargas JSON para ingestão no Azure Time Series Insights e maximizar a eficiência de suas consultas de visualização.

## <a name="best-practices"></a>Práticas recomendadas

É melhor considerar cuidadosamente como você envia eventos para seu ambiente de Time Series Insights visualização. 

As práticas recomendadas gerais incluem:

* Envie dados pela rede de maneira mais eficiente possível.
* Armazene seus dados de uma maneira que o ajude a agregá-lo mais adequadamente para seu cenário.

Para obter o melhor desempenho de consulta, siga as seguintes regras gerais:

* Não envie propriedades desnecessárias. Time Series Insights Visualizar faturas por uso. É melhor armazenar e processar apenas os dados que você consultará.
* Use os campos de instância para dados estáticos. Essa prática ajuda a evitar o envio de dados estáticos pela rede. Os campos de instância, um componente do modelo de série temporal, funcionam como dados de referência no serviço de Time Series Insights que está geralmente disponível. Para saber mais sobre os campos de instância, leia [modelo de série temporal](./time-series-insights-update-tsm.md).
* Compartilhe propriedades de dimensão entre dois ou mais eventos. Essa prática ajuda você a enviar dados pela rede com mais eficiência.
* Não use o aninhamento profundo de matriz. Time Series Insights visualização dá suporte a até dois níveis de matrizes aninhadas que contêm objetos. A Versão Prévia do Time Series Insights nivela matrizes em mensagens em vários eventos com pares de valor da propriedade.
* Se apenas algumas medidas existirem para a maioria ou todos os eventos, será melhor enviar essas medidas como propriedades separadas dentro do mesmo objeto. Enviá-los separadamente reduz o número de eventos e pode melhorar o desempenho da consulta porque menos eventos precisam ser processados.

## <a name="column-flattening"></a>Nivelamento de colunas

Durante a ingestão, as cargas que contêm objetos aninhados serão achatadas para que o nome da coluna seja um único valor com um delimitador.

* Por exemplo, o JSON aninhado a seguir:

   ```JSON
   "data": {
        "flow": 1.0172575712203979,
   },
   ```

   Torna-se: `data_flow` quando achatado.

> [!IMPORTANT]
> * Azure Time Series Insights visualização usa sublinhados (`_`) para a delineação de coluna.
> * Observe a diferença da disponibilidade geral que usa períodos (`.`) em vez disso.

Cenários mais complexos são ilustrados abaixo.

#### <a name="example-1"></a>Exemplo 1:

O cenário a seguir tem dois (ou mais) dispositivos que enviam as medições (sinais): *taxa de fluxo*, pressão de *óleo do motor*, *temperatura*e *umidade*.

Há uma única mensagem do Hub IoT do Azure enviada onde a matriz externa contém uma seção compartilhada de valores de dimensão comuns (Observe as duas entradas de dispositivo contidas na mensagem).

```JSON
[
  {
    "deviceId":"FXXX",
    "timestamp":"2018-01-17T01:17:00Z",
    "series":[
      {
        "Flow Rate ft3/s":1.0172575712203979,
        "Engine Oil Pressure psi ":34.7
      },
      {
        "Flow Rate ft3/s":2.445906400680542,
        "Engine Oil Pressure psi ":49.2
      }
    ]
  },
  {
    "deviceId":"FYYY",
    "timestamp":"2018-01-17T01:18:00Z",
    "series":[
      {
        "Flow Rate ft3/s":0.58015072345733643,
        "Engine Oil Pressure psi ":22.2
      }
    ]
  }
]
```

**Pedidas**

* O JSON de exemplo tem uma matriz externa que usa dados de [instância de série temporal](./time-series-insights-update-tsm.md#time-series-model-instances) para aumentar a eficiência da mensagem. Embora os metadados de dispositivo de instâncias de série temporal não possam ser alterados, ele geralmente fornece propriedades úteis para análise de dados.

* O JSON combina duas ou mais mensagens (uma de cada dispositivo) em um único conteúdo que economiza largura de banda ao longo do tempo.

* Pontos de dados de série individuais para cada dispositivo são combinados em um atributo de **série** única, reduzindo a necessidade de transmitir continuamente atualizações para cada dispositivo.

> [!TIP]
> Para reduzir o número de mensagens necessárias para enviar dados e tornar a telemetria mais eficiente, considere o envio em lote de valores de dimensão comuns e metadados de instância de série temporal em um único conteúdo JSON.

#### <a name="time-series-instance"></a>Instância de Série Temporal 

Vamos examinar mais de perto como usar a instância de [série temporal](./time-series-insights-update-tsm.md#time-series-model-instances) para moldar o JSON de forma mais ideal. 

> [!NOTE]
> As [IDs de série temporal](./time-series-insights-update-how-to-id.md) abaixo são *deviceIds*.

```JSON
[
  {
    "timeSeriesId":[
      "FXXX"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"REVOLT SIMULATOR",
      "L2":"Battery System"
    }
  },
  {
    "timeSeriesId":[
      "FYYY"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"COMMON SIMULATOR",
      "L2":"Battery System"
    }
  }
]
```

A Versão Prévia do Time Series Insights une uma tabela (após o nivelamento) durante o tempo de consulta. A tabela inclui colunas adicionais, como **Tipo**.

| deviceId  | Type | L1 | L2 | timestamp | Taxa de series_Flow ft3/s | series_Engine de pressão do óleo psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULADOR | Sistema de Bateria | 2018-01-17T01:17:00Z |   1,0172575712203979 |    34,7 |
| `FXXX` | Default_Type | SIMULADOR |   Sistema de Bateria |    2018-01-17T01:17:00Z | 2,445906400680542 |  49,2 |
| `FYYY` | LINE_DATA    COMMON | SIMULADOR |    Sistema de Bateria |    2018-01-17T01:18:00Z | 0,58015072345733643 |    22,2 |

> [!NOTE]
>  A tabela anterior representa o modo de exibição de consulta no [Gerenciador de visualização de série temporal](./time-series-insights-update-explorer.md).

**Pedidas**

* No exemplo anterior, as propriedades estáticas são armazenadas em Time Series Insights visualização para otimizar os dados enviados pela rede.
* Time Series Insights dados de visualização são Unidos no momento da consulta por meio da ID de série temporal definida na instância.
* São usadas duas camadas de aninhamento. Esse número é o mais que Time Series Insights visualização dá suporte. É essencial para evitar matrizes profundamente aninhadas.
* Como há algumas medidas, elas são enviadas como propriedades separadas dentro do mesmo objeto. No exemplo, **Series_Flow taxa psi**, **Series_Engine psi de pressão do óleo**e **series_Flow taxa ft3/s** são colunas exclusivas.

>[!IMPORTANT]
> Os campos de instância não são armazenados com telemetria. Eles são armazenados com metadados no modelo de série temporal.

#### <a name="example-2"></a>Exemplo 2:

Considere o seguinte JSON:

```JSON
{
  "deviceId": "FXXX",
  "timestamp": "2019-01-18T01:17:00Z",
  "data": {
        "flow": 1.0172575712203979,
    },
  "data_flow" : 1.76435072345733643
}
```

No exemplo acima, a propriedade de `data["flow"]` achatada apresentaria uma colisão de nomenclatura com a propriedade `data_flow`.

Nesse caso, o valor da propriedade *mais recente* substituiria o anterior. 

> [!TIP]
> Contate a equipe de Time Series Insights para obter mais assistência!

> [!WARNING] 
> * Nos casos em que as propriedades duplicadas estão presentes no mesmo conteúdo de evento (singular) devido ao nivelamento ou outro mecanismo, o valor mais recente da propriedade > é armazenado, substituindo todos os valores anteriores.
> * A série de eventos combinados não substituirá um ao outro.

## <a name="next-steps"></a>Próximas etapas

* Para colocar essas diretrizes em prática, leia [Azure Time Series insights sintaxe de consulta de visualização](./time-series-insights-query-data-csharp.md). Você aprenderá mais sobre a sintaxe de consulta para o Time Series Insights [API REST de visualização](https://docs.microsoft.com/rest/api/time-series-insights/preview) para acesso a dados.

* Combine as práticas recomendadas de JSON com [o modelo de série temporal](./time-series-insights-update-how-to-tsm.md).
