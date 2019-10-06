---
title: Noções básicas da linguagem de consulta
description: Descreve os operadores Kusto e funções disponíveis utilizáveis com o grafo de recursos do Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 54bb0b4f21752b91ceb9d4004c153ff4d95006aa
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2019
ms.locfileid: "71976770"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Noções básicas sobre a linguagem de consulta do Azure Resource Graph

A linguagem de consulta para o Azure Resource Graph dá suporte a diversos operadores e funções. Cada trabalho e operação com base no [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md).

A melhor maneira de saber mais sobre a linguagem de consulta usada pelo gráfico de recursos é iniciar com documentação para a Consulta de Linguagem do[Azure Data Explorer](/azure/kusto/query/index). Ele fornece uma compreensão sobre como a linguagem é estruturada e como os vários operadores e funções suportados trabalham juntos.

## <a name="supported-tabular-operators"></a>Operadores de tabela compatíveis

Aqui está a lista de operadores de tabela compatíveis no Resource Graph:

- [count](/azure/kusto/query/countoperator)
- [distinct](/azure/kusto/query/distinctoperator)
- [extend](/azure/kusto/query/extendoperator)
- [limit](/azure/kusto/query/limitoperator)
- [order by](/azure/kusto/query/orderoperator)
- [project](/azure/kusto/query/projectoperator)
- [project-away](/azure/kusto/query/projectawayoperator)
- [sample](/azure/kusto/query/sampleoperator)
- [sample-distinct](/azure/kusto/query/sampledistinctoperator)
- [sort by](/azure/kusto/query/sortoperator)
- [summarize](/azure/kusto/query/summarizeoperator)
- [take](/azure/kusto/query/takeoperator)
- [início](/azure/kusto/query/topoperator)
- [top-nested](/azure/kusto/query/topnestedoperator)
- [top-hitters](/azure/kusto/query/tophittersoperator)
- [where](/azure/kusto/query/whereoperator)

## <a name="supported-functions"></a>Funções compatíveis

Aqui está a lista de funções compatíveis no Resource Graph:

- [ago()](/azure/kusto/query/agofunction)
- [buildschema()](/azure/kusto/query/buildschema-aggfunction)
- [strcat()](/azure/kusto/query/strcatfunction)
- [isnotempty()](/azure/kusto/query/isnotemptyfunction)
- [tostring()](/azure/kusto/query/tostringfunction)
- [zip()](/azure/kusto/query/zipfunction)

## <a name="escape-characters"></a>Caracteres de escape

Alguns nomes de propriedade, como aqueles que incluem um `.` ou `$`, devem ser encapsulados ou ter escape na consulta ou o nome da propriedade é interpretado incorretamente e não fornece os resultados esperados.

- `.`-encapsular o nome da propriedade como tal: `['propertyname.withaperiod']`
  
  Exemplo de consulta que encapsula a propriedade _OData. Type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`-escapar do caractere no nome da propriedade. O caractere de escape usado depende do grafo de recursos do Shell em execução.

  - **bash** - `\`

    Exemplo de consulta que escapa a propriedade _\$type_ no bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** -não escapar do caractere `$`.

  - **PowerShell** - ``` ` ```

    Exemplo de consulta que escapa a propriedade _\$type_ no PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Próximas etapas

- Consulte o idioma em uso no [consultas Starter](../samples/starter.md)
- Ver usos avançados em [Consultas avançadas](../samples/advanced.md)
- Aprender a [explorar recursos](explore-resources.md)