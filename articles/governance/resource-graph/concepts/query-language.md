---
title: Noções básicas da linguagem de consulta
description: Descreve as tabelas de gráfico de recursos e os tipos de dados, operadores e funções do Kusto disponíveis utilizáveis com o grafo de recursos do Azure.
ms.date: 03/07/2020
ms.topic: conceptual
ms.openlocfilehash: 2f4be4d86a340867e1ad3015ff288f98fc54cecf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78927492"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Noções básicas sobre a linguagem de consulta do Azure Resource Graph

A linguagem de consulta para o Azure Resource Graph dá suporte a diversos operadores e funções. Cada trabalho e opera com base na [linguagem de consulta Kusto (KQL)](/azure/kusto/query/index). Para saber mais sobre a linguagem de consulta usada pelo grafo de recursos, comece com o [tutorial para KQL](/azure/kusto/query/tutorial).

Este artigo aborda os componentes de linguagem com suporte no grafo de recursos:

- [Tabelas do grafo de recursos](#resource-graph-tables)
- [Elementos de linguagem KQL com suporte](#supported-kql-language-elements)
- [Caracteres de escape](#escape-characters)

## <a name="resource-graph-tables"></a>Tabelas do grafo de recursos

O grafo de recursos fornece várias tabelas para os dados que ele armazena sobre os tipos de recursos do Resource Manager e suas propriedades. Essas tabelas podem ser usadas com `join` operadores `union` or para obter propriedades de tipos de recursos relacionados. Aqui está a lista de tabelas disponíveis no grafo de recursos:

|Tabelas do grafo de recursos |Descrição |
|---|---|
|Recursos |A tabela padrão se nenhuma for definida na consulta. A maioria dos tipos de recursos e propriedades do Resource Manager estão aqui. |
|ResourceContainers |Inclui a assinatura (em visualização- `Microsoft.Resources/subscriptions`-) e os tipos`Microsoft.Resources/subscriptions/resourcegroups`de recurso e os dados do grupo de recursos (). |
|AdvisorResources |Inclui recursos _relacionados_ ao `Microsoft.Advisor`. |
|AlertsManagementResources |Inclui recursos _relacionados_ ao `Microsoft.AlertsManagement`. |
|MaintenanceResources |Inclui recursos _relacionados_ ao `Microsoft.Maintenance`. |
|SecurityResources |Inclui recursos _relacionados_ ao `Microsoft.Security`. |

Para obter uma lista completa, incluindo tipos de recursos, consulte [referência: tabelas e tipos de recursos com suporte](../reference/supported-tables-resources.md).

> [!NOTE]
> _Recursos_ é a tabela padrão. Ao consultar a tabela de _recursos_ , não é necessário fornecer o nome da tabela, `join` a `union` menos que ou sejam usados. No entanto, a prática recomendada é sempre incluir a tabela inicial na consulta.

Use o Gerenciador de grafo de recursos no portal para descobrir quais tipos de recursos estão disponíveis em cada tabela. Como alternativa, use uma consulta como, por `<tableName> | distinct type` exemplo, para obter uma lista de tipos de recursos para os quais a tabela de grafo de recursos fornecida dá suporte, que existem em seu ambiente.

A consulta a seguir mostra um `join`simples. O resultado da consulta mistura as colunas e quaisquer nomes de coluna duplicados da tabela unida, _ResourceContainers_ neste exemplo, são acrescentados com **1**. Como a tabela _ResourceContainers_ tem tipos para assinaturas e grupos de recursos, qualquer tipo pode ser usado para ingressar no recurso da tabela de _recursos_ .

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

A consulta a seguir mostra um uso mais complexo `join`de. A consulta limita a tabela ingressada aos recursos de assinaturas e com `project` para incluir apenas o campo original _subscriptionId_ e o campo _name_ renomeado para _SubName_. A renomeação de `join` campo evita adicioná-lo como _Nome1_ , pois o campo já existe nos _recursos_. A tabela original é filtrada com `where` e o `project` a seguir inclui colunas das duas tabelas. O resultado da consulta é um cofre de chaves único exibindo o tipo, o nome do cofre de chaves e o nome da assinatura em que ele está.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> Ao limitar os `join` resultados com `project`, a propriedade usada pelo `join` para relacionar as duas tabelas, _SubscriptionId_ no exemplo acima, deve ser incluída em `project`.

## <a name="supported-kql-language-elements"></a>Elementos de linguagem KQL com suporte

O grafo de recursos dá suporte a todos os [tipos de dados](/azure/kusto/query/scalar-data-types/)KQL, [funções escalares](/azure/kusto/query/scalarfunctions), [operadores escalares](/azure/kusto/query/binoperators)e [funções de agregação](/azure/kusto/query/any-aggfunction). Há suporte para [operadores tabulares](/azure/kusto/query/queries) específicos pelo grafo de recursos, alguns dos quais têm comportamentos diferentes.

### <a name="supported-tabulartop-level-operators"></a>Operadores de nível de tabela/superior com suporte

Aqui está a lista de operadores de tabela KQL com suporte do grafo de recursos com exemplos específicos:

|KQL |Consulta de exemplo de grafo de recursos |Anotações |
|---|---|---|
|[contagem](/azure/kusto/query/countoperator) |[Contar cofres de chaves](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/kusto/query/distinctoperator) |[Mostrar valores distintos para um alias específico](../samples/starter.md#distinct-alias-values) | |
|[estender](/azure/kusto/query/extendoperator) |[Contar máquinas virtuais por tipo de so](../samples/starter.md#count-os) | |
|[join](/azure/kusto/query/joinoperator) |[Key Vault com o nome da assinatura](../samples/advanced.md#join) |Tipos de junção com suporte: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [Inner](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). Limite de 3 `join` em uma única consulta. Estratégias de junção personalizadas, como junção de difusão, não são permitidas. Pode ser usado em uma única tabela ou entre as tabelas de _recursos_ e _ResourceContainers_ . |
|[limite](/azure/kusto/query/limitoperator) |[Listar todos os endereços IP públicos](../samples/starter.md#list-publicip) |Sinônimo de`take` |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Operador herdado, `mv-expand` use em vez disso. _Limite_ máximo de 400. O padrão é 128. |
|[MV-expandir](/azure/kusto/query/mvexpandoperator) |[Listar Cosmos DB com locais de gravação específicos](../samples/advanced.md#mvexpand-cosmosdb) |_Limite_ máximo de 400. O padrão é 128. |
|[Ordene](/azure/kusto/query/orderoperator) |[Listar recursos classificados por nome](../samples/starter.md#list-resources) |Sinônimo de`sort` |
|[projeto](/azure/kusto/query/projectoperator) |[Listar recursos classificados por nome](../samples/starter.md#list-resources) | |
|[project-away](/azure/kusto/query/projectawayoperator) |[Remover colunas dos resultados](../samples/advanced.md#remove-column) | |
|[sort](/azure/kusto/query/sortoperator) |[Listar recursos classificados por nome](../samples/starter.md#list-resources) |Sinônimo de`order` |
|[resumir](/azure/kusto/query/summarizeoperator) |[Contar recursos do Azure](../samples/starter.md#count-resources) |Somente primeira página simplificada |
|[ter](/azure/kusto/query/takeoperator) |[Listar todos os endereços IP públicos](../samples/starter.md#list-publicip) |Sinônimo de`limit` |
|[Início](/azure/kusto/query/topoperator) |[Mostrar as cinco primeiras máquinas virtuais por nome e seu tipo de so](../samples/starter.md#show-sorted) | |
|[unida](/azure/kusto/query/unionoperator) |[Combinar resultados de duas consultas em um único resultado](../samples/advanced.md#unionresults) |Tabela única permitida: _T_ `| union` \[ `kind=` `inner` \| `outer` \] _tabela_T `withsource=` _ColumnName_ . \] \[ Limite de 3 `union` pernas em uma única consulta. A resolução difusa de `union` tabelas de segmento não é permitida. Pode ser usado em uma única tabela ou entre as tabelas de _recursos_ e _ResourceContainers_ . |
|[where](/azure/kusto/query/whereoperator) |[Mostrar recursos que contêm armazenamento](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Caracteres de escape

Alguns nomes de propriedade, como aqueles que incluem um `.` ou `$`, devem ser encapsulados ou ter escape na consulta ou o nome da propriedade é interpretado incorretamente e não fornece os resultados esperados.

- `.`-Encapsular o nome da propriedade como tal:`['propertyname.withaperiod']`
  
  Exemplo de consulta que encapsula a propriedade _OData. Type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`-Escape o caractere no nome da propriedade. O caractere de escape usado depende do grafo de recursos do Shell em execução.

  - **raso** - `\`

    Exemplo de consulta que escapa o _ \$tipo_ de propriedade no bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** -não escapar do `$` caractere.

  - **PowerShell** - ``` ` ```

    Exemplo de consulta que escapa o _ \$tipo_ de propriedade no PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Próximas etapas

- Consulte o idioma em uso em [consultas de início](../samples/starter.md).
- Consulte usos avançados em [consultas avançadas](../samples/advanced.md).
- Saiba mais sobre como [explorar recursos](explore-resources.md).