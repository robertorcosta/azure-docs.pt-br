---
title: Variáveis de modelo-Azure Time Series Insights Gen2 | Microsoft Docs
description: Variáveis de modelo
author: shreyasharmamsft
ms.author: shresha
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 73d5c3abb2edc940bee9727ce1f3b0c4e8e0a62e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289953"
---
# <a name="time-series-model-variables"></a>Variáveis de modelo de série temporal

Este artigo descreve as variáveis de modelo de série temporal que especificam fórmulas e regras de computação em eventos.

Cada variável pode ser um dos três tipos: *numeric*, *categórico*e *Aggregate*.

* Tipos **numéricos** funcionam com valores numéricos contínuos.
* Os tipos **categóricos** funcionam com um conjunto definido de valores discretos.
* Os tipos **agregados** combinam várias variáveis de um único tipo (todos numéricos ou todos categóricos).

A tabela a seguir exibe quais propriedades são relevantes para cada tipo de variável.

[![Tabela de variáveis de modelo de série temporal](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

#### <a name="numeric-variables"></a>Variáveis numéricas

| Propriedade Variable | Descrição |
| --- | ---|
| Filtro de variável | Os filtros são cláusulas condicionais opcionais para restringir o número de linhas que estão sendo consideradas para computação. |
| Valor da variável | Valores de telemetria usados para computação provenientes do dispositivo ou sensores ou transformados usando expressões de série temporal. Variáveis de tipo numérico devem ser do tipo *Double*.|
| Interpolação de variável | Interpolação especifica como reconstruir um sinal usando dados existentes. As opções de interpolação de *etapa* e *linear* estão disponíveis para variáveis numéricas. |
| Agregação de variáveis | Execute computações por meio das [funções de agregação com suporte para tipos de variáveis numéricas](https://docs.microsoft.com/rest/api/time-series-insights/preview#numeric-variable-kind-1). |

As variáveis estão em conformidade com o seguinte exemplo de JSON:

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event['Speed-Sensor'].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "right($value)"
  }
}
```

#### <a name="categorical-variables"></a>Variáveis categóricas

| Propriedade Variable | Descrição |
| --- | ---|
| Filtro de variável | Os filtros são cláusulas condicionais opcionais para restringir o número de linhas que estão sendo consideradas para computação. |
| Valor da variável | Valores de telemetria usados para computação provenientes do dispositivo ou dos sensores. As variáveis de tipo categórico devem ser *longas* ou de *cadeia de caracteres*. |
| Interpolação de variável | Interpolação especifica como reconstruir um sinal usando dados existentes. A opção de interpolação de *etapa* está disponível para variáveis categóricas. |
| Categorias variáveis | Categorias crie um mapeamento entre os valores provenientes do dispositivo ou dos sensores para um rótulo. |
| Categoria padrão da variável | A categoria padrão é para todos os valores que não estão sendo mapeados na propriedade "Categories". |

As variáveis estão em conformidade com o seguinte exemplo de JSON:

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "$event.Status.Long" 
},
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2, 3],
      "label": "Good"
    },
    {
      "values": [4],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

#### <a name="aggregate-variables"></a>Variáveis de agregação

| Propriedade Variable | Descrição |
| --- | ---|
| Filtro de variável | Os filtros são cláusulas condicionais opcionais para restringir o número de linhas que estão sendo consideradas para computação. |
| Agregação de variáveis | Execute computações por meio das [funções de agregação com suporte para tipos de variáveis de agregação](https://docs.microsoft.com/rest/api/time-series-insights/preview#aggregate-variable-kind-1). |

As variáveis estão em conformidade com o seguinte exemplo de JSON:

```JSON
"Speed Range": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "max($event.Speed.Double) - min($event.Speed.Double)"
  }
}
```

As variáveis são armazenadas na definição de tipo de um modelo de série temporal e podem ser fornecidas embutidas por meio de APIs para substituir ou complementar a definição armazenada.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [modelo de série temporal](./concepts-model-overview.md).

* Leia mais sobre como definir variáveis embutidas usando [APIs de consulta](./concepts-query-overview.md).

