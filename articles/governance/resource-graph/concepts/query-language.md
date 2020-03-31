---
title: Noções básicas da linguagem de consulta
description: Descreve tabelas de gráficos de recursos e os tipos de dados, operadores e funções disponíveis do Kusto utilizáveis com o Gráfico de Recursos do Azure.
ms.date: 03/07/2020
ms.topic: conceptual
ms.openlocfilehash: 2f4be4d86a340867e1ad3015ff288f98fc54cecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927492"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Noções básicas sobre a linguagem de consulta do Azure Resource Graph

A linguagem de consulta para o Azure Resource Graph dá suporte a diversos operadores e funções. Cada trabalho e operação com base na [Linguagem de Consulta Kusto (KQL)](/azure/kusto/query/index). Para aprender sobre o idioma de consulta usado pelo Resource Graph, comece com o [tutorial para KQL](/azure/kusto/query/tutorial).

Este artigo abrange os componentes de idioma suportados pelo Resource Graph:

- [Tabelas de gráficos de recursos](#resource-graph-tables)
- [Elementos de linguagem KQL suportados](#supported-kql-language-elements)
- [Caracteres de escape](#escape-characters)

## <a name="resource-graph-tables"></a>Tabelas de gráficos de recursos

O Resource Graph fornece várias tabelas para os dados que armazena sobre os tipos de recursos do Resource Manager e suas propriedades. Essas tabelas podem `join` `union` ser usadas com ou operadores para obter propriedades de tipos de recursos relacionados. Aqui está a lista de tabelas disponíveis no Resource Graph:

|Tabelas de gráficos de recursos |Descrição |
|---|---|
|Recursos |A tabela padrão se nenhuma definida na consulta. A maioria dos tipos e propriedades de recursos do Gerenciador de Recursos estão aqui. |
|Contêineres de recursos |Inclui tipos e dados `Microsoft.Resources/subscriptions`de assinatura (em pré-visualização -- ) e grupo de recursos ()`Microsoft.Resources/subscriptions/resourcegroups` |
|Recursos do Advisorr |Inclui recursos relacionados `Microsoft.Advisor` _a_ . |
|AlertsManagementResources |Inclui recursos relacionados `Microsoft.AlertsManagement` _a_ . |
|Recursos de manutenção |Inclui recursos relacionados `Microsoft.Maintenance` _a_ . |
|Recursos de segurança |Inclui recursos relacionados `Microsoft.Security` _a_ . |

Para obter uma lista completa, incluindo tipos de recursos, consulte [Referência: Tabelas e tipos de recursos suportados.](../reference/supported-tables-resources.md)

> [!NOTE]
> _Recursos_ é a tabela padrão. Ao consultar a tabela _Recursos,_ não é necessário fornecer `join` o `union` nome da tabela a menos ou seja usado. No entanto, a prática recomendada é sempre incluir a tabela inicial na consulta.

Use o Resource Graph Explorer no portal para descobrir quais tipos de recursos estão disponíveis em cada tabela. Como alternativa, use uma consulta, como `<tableName> | distinct type` para obter uma lista de tipos de recursos que a tabela de gráficos de recursos dado suporta que existam em seu ambiente.

A consulta a seguir `join`mostra uma simples . O resultado da consulta combina as colunas e quaisquer nomes de colunas duplicadas da tabela juntada, _ResourceContainers_ neste exemplo, são anexados com **1**. Como a tabela _ResourceContainers_ tem tipos para assinaturas e grupos de recursos, qualquer tipo pode ser usado para juntar-se ao recurso da tabela de _recursos._

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

A consulta a seguir mostra `join`um uso mais complexo de . A consulta limita a tabela ingressada aos recursos de assinaturas e com `project` para incluir apenas o campo original _subscriptionId_ e o campo _name_ renomeado para _SubName_. O nome de `join` campo evita adicioná-lo como _nome1,_ uma vez que o campo já existe em _Recursos_. A tabela original é filtrada com `where` e o `project` a seguir inclui colunas das duas tabelas. O resultado da consulta é um cofre de chaves único exibindo o tipo, o nome do cofre de chaves e o nome da assinatura em que ele está.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> Ao limitar `join` os `project`resultados com , `join` a propriedade usada para relacionar as duas tabelas, `project` _subscriptionId_ no exemplo acima, deve ser incluída em .

## <a name="supported-kql-language-elements"></a>Elementos de linguagem KQL suportados

O Resource Graph suporta todos os [tipos de dados](/azure/kusto/query/scalar-data-types/)KQL, [funções escalares,](/azure/kusto/query/scalarfunctions) [operadores escalares](/azure/kusto/query/binoperators)e [funções de agregação](/azure/kusto/query/any-aggfunction). Operadores [tabulares](/azure/kusto/query/queries) específicos são suportados pelo Resource Graph, alguns dos quais têm comportamentos diferentes.

### <a name="supported-tabulartop-level-operators"></a>Operadores tabulares/de nível superior suportados

Aqui está a lista de operadores tabulares KQL suportados pelo Resource Graph com amostras específicas:

|KQL |Consulta de amostra de gráfico de recursos |Observações |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[Conte cofres-chave](../samples/starter.md#count-keyvaults) | |
|[Distintas](/azure/kusto/query/distinctoperator) |[Mostrar valores distintos para um alias específico](../samples/starter.md#distinct-alias-values) | |
|[Estender](/azure/kusto/query/extendoperator) |[Conte máquinas virtuais por tipo de SO](../samples/starter.md#count-os) | |
|[Juntar](/azure/kusto/query/joinoperator) |[Cofre de chaves com nome de assinatura](../samples/advanced.md#join) |Junte-se aos sabores suportados: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [inner,](/azure/kusto/query/joinoperator#inner-join) [leftouter](/azure/kusto/query/joinoperator#left-outer-join). Limite de `join` 3 em uma única consulta. Estratégias de adesão personalizadas, como a transmissão, não são permitidas. Pode ser usado em uma única tabela ou entre as _tabelas Recursos_ e _Contêineres de Recursos._ |
|[Limite](/azure/kusto/query/limitoperator) |[Liste todos os endereços IP públicos](../samples/starter.md#list-publicip) |Sinônimo de`take` |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Operador legado, `mv-expand` use em vez disso. _RowLimit_ máximo de 400. O padrão é 128. |
|[mv-expand](/azure/kusto/query/mvexpandoperator) |[Liste cosmos DB com locais específicos de gravação](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_ máximo de 400. O padrão é 128. |
|[Ordem](/azure/kusto/query/orderoperator) |[Listar recursos classificados por nome](../samples/starter.md#list-resources) |Sinônimo de`sort` |
|[Projeto](/azure/kusto/query/projectoperator) |[Listar recursos classificados por nome](../samples/starter.md#list-resources) | |
|[project-away](/azure/kusto/query/projectawayoperator) |[Remover colunas de resultados](../samples/advanced.md#remove-column) | |
|[Tipo](/azure/kusto/query/sortoperator) |[Listar recursos classificados por nome](../samples/starter.md#list-resources) |Sinônimo de`order` |
|[Resumir](/azure/kusto/query/summarizeoperator) |[Contar recursos do Azure](../samples/starter.md#count-resources) |Apenas a primeira página simplificada |
|[Levar](/azure/kusto/query/takeoperator) |[Liste todos os endereços IP públicos](../samples/starter.md#list-publicip) |Sinônimo de`limit` |
|[Início](/azure/kusto/query/topoperator) |[Mostrar as cinco primeiras máquinas virtuais pelo nome e seu tipo de sistema operacional](../samples/starter.md#show-sorted) | |
|[União](/azure/kusto/query/unionoperator) |[Combine resultados de duas consultas em um único resultado](../samples/advanced.md#unionresults) |Tabela única _T_ `| union` \[ `kind=` `inner` \| `outer` \] \[permitida: T `withsource=` _ColumnName_ \] _Table_. Limite de `union` 3 pernas em uma única consulta. Resolução difusa de mesas de `union` pernas não é permitida. Pode ser usado em uma única tabela ou entre as _tabelas Recursos_ e _Contêineres de Recursos._ |
|[where](/azure/kusto/query/whereoperator) |[Mostrar recursos que contêm armazenamento](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Caracteres de escape

Alguns nomes de propriedades, `.` como `$`aqueles que incluem um ou , devem ser embrulhados ou escapados na consulta ou o nome da propriedade é interpretado incorretamente e não fornece os resultados esperados.

- `.`- Embrulhe o nome da propriedade como tal:`['propertyname.withaperiod']`
  
  Consulta de exemplo que envolve o _impropriedade odata.type:_

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`- Fuja do personagem no nome da propriedade. O caractere de fuga usado depende do gráfico de recursos shell ser executado.

  - **Bash** - `\`

    Exemplo de consulta que escapa do _ \$tipo_ de propriedade em bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** - Não fuja do `$` personagem.

  - **Powershell** - ``` ` ```

    Consulta de exemplo que escapa do _ \$tipo_ de propriedade no PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Próximas etapas

- Consulte o idioma em uso em [consultas iniciais](../samples/starter.md).
- Veja usos avançados em [consultas avançadas](../samples/advanced.md).
- Saiba mais sobre como [explorar recursos](explore-resources.md).