---
title: Modelo de Série temporal - Azure Time Series Insights | Microsoft Docs
description: Conheça o Time Series Model in Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: seodec18
ms.openlocfilehash: 648578563a0e53d3ed5bda6ab47f85c3c6a2a24e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476647"
---
# <a name="time-series-model-in-azure-time-series-insights-preview"></a>Modelo de série temporal na visualização de insights da série do tempo do Azure

Este artigo descreve o Modelo de Série stime, os recursos e como começar a construir e atualizar seus próprios modelos no ambiente Azure Time Series Insights Preview.

> [!TIP]
>  * Vá ao ambiente de demonstração do [Parque Eólico contoso](https://insights.timeseries.azure.com/preview/samples) para um exemplo de modelo de série de tempo ao vivo.
> * Leia sobre o [explorador de visualização de insights da série do tempo do Azure](time-series-insights-update-explorer.md) para aprender como navegar na ui modelo da série do tempo.
> * Aprenda [a trabalhar com o Time Series Model](time-series-insights-update-how-to-tsm.md) usando o web explorer do Time Series Insights.

## <a name="summary"></a>Resumo

Tradicionalmente, os dados que são coletados de dispositivos IoT não têm informações contextuais, o que dificulta encontrar e analisar sensores rapidamente. A principal motivação para o Time Series Model é simplificar a localização e análise de dados de IoT ou Time Series. Ele alcança esse objetivo, permitindo a curadoria, manutenção e enriquecimento de dados de séries tempois para ajudar a preparar conjuntos de dados prontos para o consumidor para análise.

## <a name="scenario-contosos-new-smart-oven"></a>Cenário: O novo forno inteligente do Contoso

**Considere o cenário fictício de um forno inteligente Contoso.** Neste cenário, suponha que cada forno inteligente Contoso tenha cinco sensores de temperatura, um para cada um dos quatro queimadores superiores e um para o próprio forno. Até recentemente, cada sensor de temperatura de Contoso enviava, armazenava e visualizava seus dados individualmente. Para o monitoramento de aparelhos de cozinha, Contoso contava com gráficos básicos, um para cada sensor individual.

Enquanto Contoso estava satisfeito com sua solução inicial de dados e visualização, várias limitações se tornaram evidentes:

* Os clientes queriam saber o quão quente o forno geral ficaria quando a maioria dos queimadores superiores estavam ligados. Contoso teve mais dificuldade em analisar e apresentar uma resposta unificada sobre as condições do forno geral.
* Os engenheiros da Contoso queriam verificar se os queimadores superiores que estão sendo executados simultaneamente não resultariam em um empate de energia ineficiente. Houve dificuldade em cruzar o que os sensores de temperatura e tensão estavam associados entre si e como localizá-los na loja.
* A equipe de garantia de qualidade da Contoso quis auditar e comparar o histórico entre duas versões de sensores. Houve dificuldade em determinar quais dados pertenciam à versão do sensor.

Sem a capacidade de estruturar, organizar e definir o modelo de séries de tempo de forno inteligente, cada sensor de temperatura manteve pontos de dados deslocados, isolados e menos informativos. Transformar esses pontos de dados em insights acionáveis foi mais difícil, uma vez que cada conjunto de dados vivia independentemente dos outros.

Essas limitações revelaram a importância de ferramentas inteligentes de agregação e visualização de dados para acompanhar o novo forno de Contoso:

* A visualização de dados se mostra útil quando você é capaz de associar e combinar dados em uma visão conveniente. Um exemplo é mostrar sensores de tensão junto com sensores de temperatura.
* Gerenciar dados multidimensionais para várias entidades, juntamente com as funcionalidades de comparação, zoom e intervalo de tempo, pode ser difícil de realizar.

**O Time Series Model fornece uma solução conveniente** para muitos dos cenários encontrados neste exemplo fictício:

[![Exemplo de mapeamento de forno inteligente modelo de série de tempo](media/v2-update-tsm/time-series-model-smart-oven.png)](media/v2-update-tsm/time-series-model-smart-oven.png#lightbox)

* O Time Series Model desempenha um papel vital nas consultas e na navegação porque contextualiza os dados, permitindo que comparações sejam desenhadas entre intervalos de tempo e entre os tipos de sensores e dispositivos. **(A)** 
* Os dados são ainda mais contextualizados porque os dados persistidos no Time Series Model preservam as computação de consultas de séries tempois como variáveis e os reutilizam no momento da consulta.
* O Time Series Model organiza e agrega dados para melhores recursos de visualização e gerenciamento. **(B)** 

### <a name="key-capabilities"></a>Principais recursos

Com o objetivo facilitar o gerenciamento da série temporal, o modelo do Time Series habilita os recursos a seguir na Versão Prévia do Time Series Insights. Ele ajuda a:

* Autor e gerenciar cálculos ou fórmulas aproveitando funções escalares, operações agregadas e assim por diante.
* Defina as relações pai-filho para permitir a navegação, a pesquisa e a referência.
* Defina propriedades associadas às instâncias, definidas como *campos de instância,* e use-as para criar hierarquias.

### <a name="components"></a>Componentes

O Modelo de Série Temporal tem três componentes principais:

* [Instâncias do modelo do Time Series](#time-series-model-instances)
* [Hierarquias do modelo do Time Series](#time-series-model-hierarchies)
* [Tipos do modelo do Time Series](#time-series-model-types)

Esses componentes são combinados para especificar um modelo de série socada e organizar seus dados do Azure Time Series Insights.

[![Gráfico de visão geral do modelo da série temporal](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

Um modelo de série temporal pode ser criado e gerenciado através da interface [Time Series Insights Preview.](time-series-insights-update-how-to-tsm.md) As configurações do modelo da série de tempo podem ser gerenciadas através da [API de configurações de modelo](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="time-series-model-instances"></a>Instâncias do modelo do Time Series

As instâncias do Modelo de *Séries* Tempoé representações virtuais das próprias séries tempo.

Na maioria dos casos, as instâncias são identificadas exclusivamente por **deviceId** ou **assetId**, que são salvos como IDs de séries tempo.

As instâncias têm informações descritivas associadas a elas chamadas *propriedades de instância,* como um ID de série soda, tipo, nome, descrição, hierarquias e campos de instância. As propriedades de instância incluem, no mínimo, informações de hierarquia.

*Os campos de instância* são uma coleção de informações descritivas que podem incluir valores para níveis de hierarquia, bem como fabricante, operador e assim por diante.

Depois que uma fonte de evento é configurada para o ambiente Time Series Insights, as instâncias são automaticamente descobertas e criadas em um modelo de série stime. As instâncias podem ser criadas ou atualizadas através do explorador Time Series Insights usando consultas do Modelo de Série soda.

A [demonstração do Parque Eólico contoso](https://insights.timeseries.azure.com/preview/samples) fornece vários exemplos de instâncias ao vivo.

[![Exemplo de exemplo do modelo de série de tempo](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>Propriedades de instância

As instâncias são definidas por **timeSeriesId,** **typeId,** **name,** **description,** **hierarchyIds**e **instanceFields**. Cada instância mapeia para apenas um *tipo*, e uma ou mais *hierarquias*.

| Propriedade | Descrição |
| --- | ---|
| timeSeriesId | O UUID da série temporal com a a que a instância está associada. |
| typeId | O uuid do tipo Modelo de série do tempo com o que a instância está associada. Por padrão, todas as novas instâncias descobertas são associadas a um tipo padrão.
| name | A propriedade **do nome** é opcional e sensível a maiúsculas e minúsculas. Se o **nome** não estiver disponível, ele é padrão para **timeSeriesId**. Se um nome for fornecido, **o timeSeriesId** ainda está disponível no [poço](time-series-insights-update-explorer.md#4-time-series-well). |
| descrição | Uma descrição de texto da instância. |
| hierarquiaids | Define a quais hierarquias a instância pertence. |
| instanceCampos | As propriedades de uma instância e quaisquer dados estáticos que definam uma instância. Elas definem os valores das propriedades hierárquicas e não hierárquicas e também dão suporte à indexação para executar operações de pesquisa. |

> [!NOTE]
> Hierarquias são construídas usando campos de instância. **Instância adicionalOs campos** podem ser adicionados para outras definições de propriedade de instâncias.

As instâncias têm a seguinte representação JSON:

```JSON
{
  "timeSeriesId": ["PU2"],
  "typeId": "545314a5-7166-4b90-abb9-fd93966fa39b",
  "hierarchyIds": ["95f0a8d1-a3ef-4549-b4b3-f138856b3a12"],
  "description": "Pump #2",
  "instanceFields": {
    "Location": "Redmond",
    "Fleet": "Fleet 5",
    "Unit": "Pump Unit 3",
    "Manufacturer": "Contoso",
    "ScalePres": "0.54",
    "scaleTemp": "0.54"
  }
}
```

> [!TIP]
> Para a API de exemplo de insights da série de tempo e criar, ler, atualizar e excluir (CRUD) suporte, leia o artigo [de consulta de dados](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) e a [documentação Instance API REST](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

## <a name="time-series-model-hierarchies"></a>Hierarquias do modelo do Time Series

Hierarquias do Modelo de Séries tempois organizam *instâncias* especificando nomes de propriedades e seus relacionamentos.

Você pode configurar várias hierarquias em um determinado ambiente de Insights de séries de tempo. Uma instância de modelo de série de tempo pode mapear para uma única hierarquia ou múltiplas hierarquias (relacionamento de muitos a muitos).

A interface [cliente dedemonstração do Parque Eólico Contoso](https://insights.timeseries.azure.com/preview/samples) exibe uma instância padrão e hierarquia de tipo.

[![Exemplo de hierarquia do modelo de série soda](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>Definição de hierarquia

As hierarquias são definidas por **id**hierárquico, **nome**e **fonte.**

| Propriedade | Descrição |
| ---| ---|
| id | O identificador único para a hierarquia, que é usado, por exemplo, quando você define uma instância. |
| name | Uma seqüência usada para fornecer um nome para a hierarquia. |
| source | Especifica a hierarquia organizacional ou o caminho, que é uma ordem de cima para baixo da hierarquia que os usuários desejam criar. As propriedades de pai-filho mapeiamcampos de instância. |

Hierarquias são representadas no JSON como:

```JSON
{
  "hierarchies": [
    {
      "id": "6e292e54-9a26-4be1-9034-607d71492707",
      "name": "Location",
      "source": {
        "instanceFieldNames": [
          "state",
          "city"
        ]
      }
    },
    {
      "id": "a28fd14c-6b98-4ab5-9301-3840f142d30e",
      "name": "ManufactureDate",
      "source": {
        "instanceFieldNames": [
          "year",
          "month"
        ]
      }
    }
  ]
}
```

No exemplo json anterior:

* `Location`define uma hierarquia com `states` pai `cities`e filho. Cada `location` um `states`pode ter múltiplos, `cities`que por sua vez podem ter múltiplos .
* `ManufactureDate`define uma hierarquia com `year` pai `month`e filho. Cada `ManufactureDate` um `years`pode ter múltiplos, `months`que por sua vez podem ter múltiplos .

> [!TIP]
> Para obter o suporte à API e ao CRUD de insights da série de tempo, leia o artigo [de consulta de dados](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) e a [documentação Hierárquico API REST](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-example"></a>Exemplo de hierarquia

Considere um exemplo em que `building` `floor`a `room` hierarquia **H1** tem , e como parte de sua **instânciaFieldNames** definição:

```JSON
{
  "id": "aaaaaa-bbbbb-ccccc-ddddd-111111",
  "name": "H1",
  "source": {
    "instanceFieldNames": [
      "building",
      "floor",
      "room"
    ]
  }
}
```

Dadas as instâncias de campos usados na definição anterior e em várias séries tempoares, os atributos e valores da hierarquia aparecem conforme mostrado na tabela a seguir:

| ID da série temporal | Campos de instância |
| --- | --- |
| ID1 | "edifício" = "1000", "piso" = "10", "quarto" = "55"  |
| ID2 | "construção" = "1000", "quarto" = "55" |
| ID3 | "Chão" = "10" |
| ID4 | "edifício" = "1000", "piso" = "10"  |
| ID5 | Nenhum "edifício", "chão" ou "quarto" está definido. |

As séries time **ID1** e **ID4** são exibidas como parte da hierarquia **H1** no [explorador Azure Time Series Insights](time-series-insights-update-explorer.md) porque eles têm parâmetros de *construção,* *piso*e *sala* totalmente definidos e ordenados corretamente.

Os outros são classificados em *Instâncias Não-Parentadas* porque não estão de acordo com a hierarquia de dados especificada.

## <a name="time-series-model-types"></a>Tipos do modelo do Time Series

Os *tipos* do modelo do Time Series ajudam a definir variáveis ou fórmulas para fazer cálculos. Os tipos estão associados a uma instância específica do Time Series Insights.

Um tipo pode ter uma ou mais variáveis. Por exemplo, uma instância do Modelo de Série Temporal pode ser do tipo Sensor de *Temperatura,* que consiste nas variáveis *temperatura avg,* *temperatura mínima*e *temperatura máxima.*

A [demonstração do Parque Eólico contoso](https://insights.timeseries.azure.com/preview/samples) visualiza vários tipos de modelos de séries tempontos associados às suas respectivas instâncias.

[![Exemplo do tipo modelo de série de tempo](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> Para obter o suporte à API e ao CRUD de insights da série de tempo, leia o artigo [de consulta de dados](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) e a [documentação Type API REST](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

### <a name="type-properties"></a>Propriedades de tipo

Os tipos de modelos de séries tempoé definido por **id,** **nome,** **descrição**e **variáveis.**

| Propriedade | Descrição |
| ---| ---|
| id | O UUID para o tipo. |
| name | Uma seqüência usada para fornecer um nome para o tipo. |
| descrição | Uma descrição da seqüência para o tipo. |
| variáveis | Especificar variáveis associadas ao tipo. |

Os tipos estão em conformidade com o seguinte exemplo json:

```JSON
{
  "types": [
    {
      "id": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
      "name": "DefaultType",
      "description": "Default type",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "value": null,
          "filter": null,
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Interpolated Speed": {
          "kind": "numeric",
          "value": {
              "tsx": "$event.[speed].Double"
          },
          "filter": null,
          "interpolation": {
              "kind": "step",
              "boundary": {
                  "span": "P1D"
              }
          },
          "aggregation": {
              "tsx": "left($value)"
          }
        }
      }
    }
  ]
}
```

### <a name="variables"></a>Variáveis

Os tipos de Insights de Séries Tempo podem ter muitas variáveis que especificam regras de fórmula e computação sobre eventos.

Cada variável pode ser um dos três *tipos*: *numérico,* *categórico*e *agregado*.

* Os tipos **numéricos** trabalham com valores contínuos. 
* Os tipos **categóricos** trabalham com um conjunto definido de valores discretos.
* **Valores agregados** combinam múltiplas variáveis de um único tipo (todos numéricos ou todos categóricos).

A tabela a seguir exibe quais propriedades são relevantes para cada tipo de variável.

[![Tabela variável modelo de série de tempo](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

#### <a name="numeric-variables"></a>Variáveis numéricas

| Propriedade variável | Descrição |
| --- | ---|
| Filtro de variável | Os filtros são cláusulas condicionais opcionais para restringir o número de linhas que estão sendo consideradas para computação. |
| Valor da variável | Valores de telemetria utilizados para computação provenientes do dispositivo ou sensores ou transformados usando Expressões de Séries Tempo. As variáveis do tipo numérico devem ser do tipo *Duplo*.|
| Interpolação variável | A Interpolação especifica como reconstruir um sinal usando dados existentes. *As* opções de interpolação step e *linear* estão disponíveis para variáveis numéricas. |
| Agregação de variáveis | Suporte computação através de operadores *Avg*, *Min*, *Max*, *Sum*, *Count*, *First*, *Last* and time-weighted *(Avg*, *Min*, *Max*, *Sum*, *Left*) operadores. |

As variáveis estão em conformidade com o seguinte exemplo json:

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event.[speed].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "left($value)"
  }
}
```

#### <a name="categorical-variables"></a>Variáveis categóricas

| Propriedade variável | Descrição |
| --- | ---|
| Filtro de variável | Os filtros são cláusulas condicionais opcionais para restringir o número de linhas que estão sendo consideradas para computação. |
| Valor da variável | Valores de telemetria utilizados para computação provenientes do dispositivo ou sensores. As variáveis de tipo categórico devem ser *longas* ou *string*. |
| Interpolação variável | A Interpolação especifica como reconstruir um sinal usando dados existentes. A opção de interpolação *Step* está disponível para variáveis categóricas. |
| Categorias variáveis | As categorias criam um mapeamento entre os valores provenientes do dispositivo ou sensores para uma etiqueta. |
| Categoria padrão variável | A categoria padrão é para todos os valores que não estão sendo mapeados na propriedade "categorias". |

As variáveis estão em conformidade com o seguinte exemplo json:

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "toLong($event.[Status].Double)"
},
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2],
      "label": "Good"
    },
    {
      "values": [3],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

#### <a name="aggregate-variables"></a>Variáveis agregadas

| Propriedade variável | Descrição |
| --- | ---|
| Filtro de variável | Os filtros são cláusulas condicionais opcionais para restringir o número de linhas que estão sendo consideradas para computação. |
| Agregação de variáveis | Suporte à computação através *de Avg,* *Min,* *Max,* *Soma,* *Contagem,* *Primeiro*, *Último*. |

As variáveis estão em conformidade com o seguinte exemplo json:

```JSON
"Aggregate Speed": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "avg($event.Speed.Double)"
  }
}
```

As variáveis são armazenadas na definição de tipo de um modelo de série sumária e podem ser fornecidas inline via [APIs de consulta](time-series-insights-update-tsq.md) para substituir a definição armazenada.

## <a name="next-steps"></a>Próximas etapas

- Leia [o armazenamento e a ingestão de visualização](./time-series-insights-update-storage-ingress.md)de visualização do Azure Time Series .

- Conheça as operações comuns do Modelo de Série sumida em [modelagem de dados na visualização de insights da série de tempo do Azure](./time-series-insights-update-how-to-tsm.md)

- Leia a nova documentação de referência [do Modelo de Série do Tempo.](https://docs.microsoft.com/rest/api/time-series-insights/preview-model)
