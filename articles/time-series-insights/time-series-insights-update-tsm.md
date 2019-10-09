---
title: Modelo do Time Series na Versão Prévia do Azure Time Series Insights | Microsoft Docs
description: Entendendo o modelo do Time Series do Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/07/2019
ms.custom: seodec18
ms.openlocfilehash: 8f4ed6de5ff47efa441c371b80670c500f57c9bb
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034052"
---
# <a name="time-series-model"></a>Modelo do Time Series

Este artigo descreve a parte do modelo do Time Series da Versão Prévia do Azure Time Series Insights. Ele aborda o modelo em si, seus recursos e como começar a criar e atualizar seu próprio modelo.

Tradicionalmente, os dados que são coletados de dispositivos IoT não têm informações contextuais, o que dificulta encontrar e analisar sensores rapidamente. A principal razão de ser do modelo do Time Series é simplificar a localização e a análise de dados de IoT. Ela atinge esse objetivo permitindo a curadoria, a manutenção e o enriquecimento dos dados de série temporal para ajudar a preparar conjuntos de dados prontos para o consumidor.

Os Modelos do Time Series desempenham um papel vital em consultas e navegação porque eles contextualizam os dispositivo e as entidades que não são dispositivos. Os dados persistidos no modelo do Time Series permitem os cálculos de consulta de série temporal aproveitando as fórmulas armazenadas neles.

[Visão geral do modelo da série @no__t 1Time](media/v2-update-tsm/tsm.png)](media/v2-update-tsm/tsm.png#lightbox)

## <a name="key-capabilities"></a>Principais recursos

Com o objetivo facilitar o gerenciamento da série temporal, o modelo do Time Series habilita os recursos a seguir na Versão Prévia do Time Series Insights. Ele ajuda a:

* Criar e gerenciar cálculos ou fórmulas, transformar dados aproveitando funções escalares, operações de agregação e assim por diante.
* Definir relações de pai-filho para habilitar navegação e referência e fornecer contexto para dados telemétricos de série temporal.
* Definir as propriedades que estão associadas com a parte de instâncias dos *campos de instância* e usá-las para criar hierarquias.

## <a name="entity-components"></a>Componentes de entidade

Os modelos de série temporal têm três componentes principais:

* <a href="#time-series-model-types">Tipos de modelo de série temporal</a>
* <a href="#time-series-model-hierarchies">Hierarquias de modelo de série temporal</a>
* <a href="#time-series-model-instances">Instâncias de modelo de série temporal</a>

Esses componentes são combinados para especificar um modelo de série temporal e para organizar seus dados de Azure Time Series Insights.

## <a name="time-series-model-types"></a>Tipos do modelo do Time Series

Os *tipos* do modelo do Time Series ajudam a definir variáveis ou fórmulas para fazer cálculos. Os tipos são associados a uma instância específica do Time Series Insights. Um tipo pode ter uma ou mais variáveis. Por exemplo, uma instância do Time Series Insights pode ser do tipo *Sensor de Temperatura*, que consiste nas variáveis *temperatura média*, *temperatura mín*e *temperatura máx*. Criamos um tipo padrão quando os dados começam a fluir para o Time Series Insights. O tipo padrão pode ser recuperado e atualizado a partir das configurações do modelo. Os tipos padrão têm uma variável que conta o número de eventos.

### <a name="time-series-model-type-json-example"></a>Exemplo JSON do tipo do modelo do Time Series

Exemplo:

```JSON
{
    "name": "SampleType",
    "description": "This is sample type",
    "variables": {
        "Avg Temperature": {
            "kind": "numeric",
            "filter": null,
            "value": { "tsx": "$event.temperature.Double" },
            "aggregation": {"tsx": "avg($value)"}
        },
        "Count Temperature": {
            "kind": "aggregate",
            "filter": null,
            "value": null,
            "aggregation": {"tsx": "count()"}
        }
    }
}
```

Para obter mais informações sobre tipos do modelo do Time Series, consulte a [documentação de referência](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

### <a name="variables"></a>variáveis

Os tipos do Time Series Insights têm variáveis que são cálculos nomeados usando valores dos eventos. As definições de variável do Time Series Insights contêm regras de fórmula e de computação. As definições de variáveis incluem *kind*, *value*, *filter*, *reduction* e *boundaries*. As variáveis são armazenadas na definição de tipo no modelo do Time Series e podem ser fornecidas embutidas por meio de APIs de consulta para substituir a definição armazenada.

A matriz abaixo funciona como uma legenda para definições de variáveis:

[tabela de definição de variável ![Type](media/v2-update-tsm/table.png)](media/v2-update-tsm/table.png#lightbox)

| Definição | DESCRIÇÃO |
| --- | ---|
| Tipo de variável |  Há suporte para tipos *numéricos* e *agregados* |
| Filtro de variável | Os filtros de variável especificam uma cláusula de filtro opcional para restringir o número de linhas que está sendo considerado para a computação com base nas condições. |
| Valor da variável | Os valores de variável são e devem ser usados na computação. O campo relevante para se referir ao ponto de dados em questão. |
| Agregação de variáveis | A função de agregação da variável permite parte da computação. O Time Series Insights dá suporte a agregações regulares (ou seja, *min*, *mxx*, *avg*, *sum* e *count*). |

## <a name="time-series-model-hierarchies"></a>Hierarquias do modelo do Time Series

As hierarquias organizam instâncias especificando nomes de propriedade e suas relações. Você pode ter uma única hierarquia ou várias hierarquias. Elas não precisam ser uma parte atual dos seus dados, mas cada instância deve ser mapeada para uma hierarquia. Uma instância do modelo do Time Series pode ser mapeada para uma única hierarquia ou para várias.

As hierarquias são definidas por *Hierarchy ID*, *name* e *source*. As hierarquias têm um caminho, que é a ordem de pai-filho, de cima para baixo, que os usuários desejam criar. As propriedades de pai-filho mapeiam*campos de instância*.

### <a name="time-series-model-hierarchy-json-example"></a>Exemplo JSON de hierarquia do modelo do Time Series

Exemplo:

```JSON
{
    "id": "4c6f1231-f632-4d6f-9b63-e366d04175e3",
    "name": "Location",
    "source": {
        "instanceFieldNames": [
                "state",
                "city"
            ]
    }
}
```

Para obter mais informações sobre hierarquias do modelo do Time Series, consulte a [documentação de referência](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-definition-behavior"></a>Comportamento da definição de hierarquia

Considere o exemplo abaixo, em que a hierarquia H1 tem *building*, *floor* e *room* como parte de sua definição:

```plaintext
 H1 = [“building”, “floor”, “room”]
```

Dependendo dos *campos de instância*, os atributos e valores da hierarquia aparecem conforme mostrado na tabela abaixo:

| ID da série temporal | Campos de instância |
| --- | --- |
| ID1 | “building” = “1000”, “floor” = “10”, “room” = “55”  |
| ID2 | “building” = “1000”, “room” = “55” |
| ID3 | “floor” = “10” |
| ID4 | “building” = “1000”, “floor” = “10”  |
| ID5 | Nenhum de "edifício", "andar" ou "sala" está definido |

No exemplo anterior, **ID1** e **ID4** mostram como parte da hierarquia H1 no Azure Time Series insights Explorer, e o resto é classificado em instâncias sem *pai* porque eles não estão em conformidade com a hierarquia de dados especificada.

## <a name="time-series-model-instances"></a>Instâncias do modelo do Time Series

As instâncias são a série temporal em si. Na maioria dos casos, o *deviceId* ou *assetId* é o identificador exclusivo do ativo no ambiente. As instâncias têm informações descritivas associadas a elas, chamadas propriedades de instância. As propriedades de instância incluem, no mínimo, informações de hierarquia. Elas também podem incluir dados úteis e descritivos, como fabricante, operador ou última data de serviço.

As instâncias são definidas por *typeId*, *timeSeriesId*, *name*, *description*, *hierarchyIds* e *instanceFields*. Cada instância é mapeada para apenas um *tipo* e para uma ou mais hierarquias. As instâncias herdam todas as propriedades das hierarquias, e mais *instanceFields* podem ser adicionados para definir propriedades de instância.

*instanceFields* são propriedades de uma instância e todos os dados estáticos que definem uma instância. Elas definem os valores das propriedades hierárquicas e não hierárquicas e também dão suporte à indexação para executar operações de pesquisa.

A propriedade *Name* é opcional e diferencia maiúsculas de minúsculas. Se *name* não estiver disponível, o padrão será a ID do Time Series. Se um *nome* for fornecido, a ID do Time Series ainda estará disponível no espaço (a grade abaixo dos gráficos no Explorer).

### <a name="time-series-model-instance-json-example"></a>Exemplo JSON de instância do modelo do Time Series

Exemplo:

```JSON
{
    "typeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
    "timeSeriesId": ["sampleTimeSeriesId"],
    "name": "sampleName",
    "description": "Sample Instance",
    "hierarchyIds": [
        "1643004c-0a84-48a5-80e5-7688c5ae9295"
    ],
    "instanceFields": {
        "state": "California",
        "city": "Los Angeles"
    }
}
```

Para obter mais informações sobre instâncias do modelo do Time Series, consulte a [documentação de referência](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

### <a name="time-series-model-settings-example"></a>Exemplo de configurações do modelo do Time Series

Exemplo:

```JSON
{
    "modelSettings": {
        "name": "DefaultModel",
        "timeSeriesIdProperties": [
            {
                "name": "id",
                "type": "String"
            }
        ],
        "defaultTypeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393"
    }
}
```

Para obter mais informações sobre configurações do modelo do Time Series, consulte a [documentação de referência](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="next-steps"></a>Próximas etapas

- Confira [Armazenamento e entrada da Versão Prévia do Azure Time Series Insights](./time-series-insights-update-storage-ingress.md).

- Confira o novo [modelo do Time Series](https://docs.microsoft.com/rest/api/time-series-insights/preview-model).