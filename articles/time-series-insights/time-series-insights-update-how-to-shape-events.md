---
title: Eventos de forma - Azure Time Series Insights | Microsoft Docs
description: Saiba mais sobre as melhores práticas e como moldar eventos para consulta no Azure Time Insights Preview.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650916"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Formatar eventos com Versão Prévia do Azure Time Series Insights

Este artigo define as melhores práticas para moldar suas cargas JSON para ingestão no Azure Time Series Insights e maximizar a eficiência de suas consultas de visualização.

## <a name="best-practices"></a>Práticas recomendadas

É melhor considerar cuidadosamente como você envia eventos para o ambiente de visualização de insights da série do tempo. 

As práticas recomendadas gerais incluem:

* Envie dados pela rede de maneira mais eficiente possível.
* Armazene seus dados de uma maneira que o ajude a agregá-lo mais adequadamente para seu cenário.

Para obter o melhor desempenho de consulta, siga as seguintes regras de ouro:

* Não envie propriedades desnecessárias. Visualização de visualização de insights da série do tempo por uso. É melhor armazenar e processar apenas os dados que você consultará.
* Use os campos de instância para dados estáticos. Essa prática ajuda a evitar o envio de dados estáticos pela rede. Os campos de instância, um componente do Modelo de Série sumida, funcionam como dados de referência no serviço Time Series Insights que geralmente está disponível. Para saber mais sobre os campos de instância, leia [O Modelo de Série sinuosa](./time-series-insights-update-tsm.md).
* Compartilhe propriedades de dimensão entre dois ou mais eventos. Essa prática ajuda você a enviar dados pela rede com mais eficiência.
* Não use o aninhamento profundo de matriz. A visualização de insights da série temporal suporta até dois níveis de matrizes aninhadas que contêm objetos. A Versão Prévia do Time Series Insights nivela matrizes em mensagens em vários eventos com pares de valor da propriedade.
* Se apenas algumas medidas existirem para a maioria ou todos os eventos, será melhor enviar essas medidas como propriedades separadas dentro do mesmo objeto. Enviá-los separadamente reduz o número de eventos e pode melhorar o desempenho da consulta porque menos eventos precisam ser processados.

## <a name="column-flattening"></a>Achatamento da coluna

Durante a ingestão, as cargas que contêm objetos aninhados serão achatadas para que o nome da coluna seja um único valor com um delineador.

* Por exemplo, o seguinte JSON aninhado:

   ```JSON
   "data": {
        "flow": 1.0172575712203979,
   },
   ```

   Torna-se: `data_flow` quando achatado.

> [!IMPORTANT]
> * A visualização de insights da`_`série do tempo do Azure usa sublinhados () para delineamento da coluna.
> * Observe a diferença em relação`.`à Disponibilidade Geral, que usa períodos ( em vez disso.

Cenários mais complexos são ilustrados abaixo.

#### <a name="example-1"></a>Exemplo 1:

O cenário a seguir tem dois (ou mais) dispositivos que enviam as medidas (sinais): *Taxa de Fluxo,* *Pressão do Óleo do Motor,* *Temperatura*e *Umidade*.

Há uma única mensagem do Azure IoT Hub enviada onde a matriz externa contém uma seção compartilhada de valores de dimensão comum (observe as duas entradas de dispositivo contidas na mensagem).

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

**Takeaways:**

* O exemplo JSON tem uma matriz externa que usa dados [de instância de série de tempo](./time-series-insights-update-tsm.md#time-series-model-instances) para aumentar a eficiência da mensagem. Embora os metadados do dispositivo Time Series Instances não sejam alteradores, ele geralmente fornece propriedades úteis para análise de dados.

* O JSON combina duas ou mais mensagens (uma de cada dispositivo) em uma única economia de carga na largura de banda ao longo do tempo.

* Os pontos de dados de séries individuais para cada dispositivo são combinados em um único atributo **de série,** reduzindo a necessidade de transmitir continuamente atualizações para cada dispositivo.

> [!TIP]
> Para reduzir o número de mensagens necessárias para enviar dados e tornar a telemetria mais eficiente, considere o loteamento de valores de dimensão comum e metadados de instância da série temporal em uma única carga JSON.

#### <a name="time-series-instance"></a>Instância de Série Temporal 

Vamos dar uma olhada mais de perto em como usar a [Instância da Série do Tempo](./time-series-insights-update-tsm.md#time-series-model-instances) para moldar seu JSON de forma mais otimizada. 

> [!NOTE]
> Os [IDs da série temporal](./time-series-insights-update-how-to-id.md) abaixo são *deviceIds*.

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

| deviceId  | Type | L1 | L2 |  timestamp | taxa de series_Flow ft3/s | series_Engine pressão do óleo psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULADOR | Sistema de Bateria | 2018-01-17T01:17:00Z |   1,0172575712203979 |    34,7 |
| `FXXX` | Default_Type | SIMULADOR |   Sistema de Bateria |    2018-01-17T01:17:00Z | 2,445906400680542 |  49,2 |
| `FYYY` | LINE_DATA    COMMON | SIMULADOR |    Sistema de Bateria |    2018-01-17T01:18:00Z | 0,58015072345733643 |    22,2 |

> [!NOTE]
>  A tabela anterior representa a exibição de consulta no [Explorador de visualização da série tempo](./time-series-insights-update-explorer.md).

**Takeaways:**

* No exemplo anterior, as propriedades estáticas são armazenadas na Visualização de Insights da série do tempo para otimizar os dados enviados pela rede.
* Os dados de visualização da série do tempo são juntados no momento da consulta através do ID da série temporal definido na instância.
* Duas camadas de ninho são usadas. Esse número é o máximo que o Time Series Insights Preview suporta. É essencial para evitar matrizes profundamente aninhadas.
* Como há algumas medidas, elas são enviadas como propriedades separadas dentro do mesmo objeto. No exemplo, **series_Flow Rate psi**, **series_Engine Oil Pressure psi**e **series_Flow Rate ft3/s** são colunas únicas.

>[!IMPORTANT]
> Os campos de instância não são armazenados com telemetria. Eles são armazenados com metadados no Modelo de Séries Tempo.

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

No exemplo acima, a `data["flow"]` propriedade achatada `data_flow` apresentaria uma colisão de nomeação com a propriedade.

Neste caso, o *valor mais recente* da propriedade substituiria o anterior. 

> [!TIP]
> Entre em contato com a equipe do Time Series Insights para obter mais assistência!

> [!WARNING] 
> * Nos casos em que as propriedades duplicadas estão presentes na mesma carga de evento (singular) devido ao achatamento ou outro mecanismo, o valor > mais recente da propriedade é armazenado, sobreescrevendo quaisquer valores anteriores.
> * Séries de eventos combinados não se sobrepõem.

## <a name="next-steps"></a>Próximas etapas

* Para colocar essas diretrizes em prática, leia [a sintaxe de consulta de visualização de visualização do Azure Time Series Preview](./time-series-insights-query-data-csharp.md). Você aprenderá mais sobre a sintaxe de consulta para a API de [visualização de informações](https://docs.microsoft.com/rest/api/time-series-insights/preview) da série do tempo para acesso a dados.

* Combine as práticas recomendadas da JSON com [o modelo de série de tempo .](./time-series-insights-update-how-to-tsm.md)
