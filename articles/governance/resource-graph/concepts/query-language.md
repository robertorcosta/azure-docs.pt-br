---
title: Noções básicas da linguagem de consulta
description: Descreve as tabelas do Resource Graph e os tipos de dados, operadores e funções do Kusto disponíveis utilizáveis com o Azure Resource Graph.
ms.date: 11/18/2020
ms.topic: conceptual
ms.openlocfilehash: 34aaaa60ed9d757cc1a63ffaebb2225900cff61f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966676"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Noções básicas sobre a linguagem de consulta do Azure Resource Graph

A linguagem de consulta para o Azure Resource Graph dá suporte a diversos operadores e funções. Cada um funciona e opera com base na [linguagem de consulta do Kusto (KQL)](/azure/kusto/query/index). Para saber mais sobre a linguagem de consulta usada pelo Resource Graph, comece pelo [tutorial para KQL](/azure/kusto/query/tutorial).

Este artigo aborda os componentes de linguagem com suporte no Resource Graph:

- [Tabelas do Resource Graph](#resource-graph-tables)
- [Elementos de linguagem personalizada do grafo de recursos](#resource-graph-custom-language-elements)
- [Elementos da linguagem KQL com suporte](#supported-kql-language-elements)
- [Escopo da consulta](#query-scope)
- [Caracteres de escape](#escape-characters)

## <a name="resource-graph-tables"></a>Tabelas do Resource Graph

O grafo de recursos fornece várias tabelas para os dados que ele armazena sobre Azure Resource Manager tipos de recursos e suas propriedades. Algumas tabelas podem ser usadas com `join` `union` operadores or para obter propriedades de tipos de recursos relacionados. Esta é a lista de tabelas disponíveis no Resource Graph:

|Tabela de gráfico de recursos |Pode `join` ? |Descrição |
|---|---|
|Recursos |Yes |A tabela padrão se nenhuma tabela for definida na consulta. A maioria dos tipos de recursos e propriedades do Resource Manager estão aqui. |
|ResourceContainers |Yes |Inclui os tipos de recursos e dados da assinatura (em versão prévia -- `Microsoft.Resources/subscriptions`) e do grupo de recursos (`Microsoft.Resources/subscriptions/resourcegroups`). |
|AdvisorResources |No |Inclui recursos _relacionados_ ao `Microsoft.Advisor`. |
|AlertsManagementResources |No |Inclui recursos _relacionados_ ao `Microsoft.AlertsManagement`. |
|GuestConfigurationResources |No |Inclui recursos _relacionados_ ao `Microsoft.GuestConfiguration`. |
|MaintenanceResources |No |Inclui recursos _relacionados_ ao `Microsoft.Maintenance`. |
|PolicyResources |No |Inclui recursos _relacionados_ ao `Microsoft.PolicyInsights`. (**Visualização**)|
|SecurityResources |No |Inclui recursos _relacionados_ ao `Microsoft.Security`. |
|ServiceHealthResources |No |Inclui recursos _relacionados_ ao `Microsoft.ResourceHealth`. |

Para obter uma lista completa, incluindo os tipos de recursos, veja a Referência de [: tabelas e tipos de recursos com suporte](../reference/supported-tables-resources.md).

> [!NOTE]
> _Resources_ é a tabela padrão. Durante a consulta da tabela _Resources_, não é necessário fornecer o nome da tabela, salvo se `join` ou `union` forem usados. No entanto, recomenda-se incluir sempre a tabela inicial na consulta.

Use o Explorer do Resource Graph no portal para descobrir quais os tipos de recursos que estão disponíveis em cada tabela. Como alternativa, use uma consulta como `<tableName> | distinct type` para obter uma lista dos tipos de recursos que a tabela do Resource Graph fornecida dá suporte e que existem em seu ambiente.

A consulta a seguir mostra uma consulta `join` simples. O resultado da consulta combina as colunas e quaisquer nomes de coluna duplicados da tabela unida, _ResourceContainers_ neste exemplo, são seguidos do número **1**. Como a tabela _ResourceContainers_ possui tipos para assinaturas e grupos de recursos, qualquer tipo pode ser usado para união com o recurso da tabela _Resources_.

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

A consulta a seguir mostra um uso mais complexo da consulta `join`. A consulta limita a tabela ingressada aos recursos de assinaturas e com `project` para incluir apenas o campo original _subscriptionId_ e o campo _name_ renomeado para _SubName_. A renomeação do campo evita que `join` o adicione como _name1_, uma vez que o campo já existe em _Resources_. A tabela original é filtrada com `where` e o `project` a seguir inclui colunas das duas tabelas. O resultado da consulta é um cofre de chaves único exibindo o tipo, o nome do cofre de chaves e o nome da assinatura em que ele está.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> Quando os resultados de `join` são limitados com `project`, a propriedade usada por `join` para relacionar as duas tabelas, _subscriptionId_ no exemplo acima, deve ser incluída em `project`.

## <a name="extended-properties-preview"></a><a name="extended-properties"></a>Propriedades estendidas (visualização)

Como um recurso de _Visualização_ , alguns dos tipos de recursos no grafo de recursos têm propriedades adicionais relacionadas a tipos disponíveis para consulta além das propriedades fornecidas pelo Azure Resource Manager. Esse conjunto de valores, conhecido como _Propriedades estendidas_, existe em um tipo de recurso com suporte no `properties.extended` . Para ver quais tipos de recursos têm _Propriedades estendidas_, use a seguinte consulta:

```kusto
Resources
| where isnotnull(properties.extended)
| distinct type
| order by type asc
```

Exemplo: obter a contagem de máquinas virtuais por `instanceView.powerState.code` :

```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| summarize count() by tostring(properties.extended.instanceView.powerState.code)
```

## <a name="resource-graph-custom-language-elements"></a>Elementos de linguagem personalizada do grafo de recursos

### <a name="shared-query-syntax-preview"></a><a name="shared-query-syntax"></a>Sintaxe de consulta compartilhada (versão prévia)

Como um recurso de visualização, uma [consulta compartilhada](../tutorials/create-share-query.md) pode ser acessada diretamente em uma consulta de grafo de recursos. Esse cenário torna possível criar consultas padrão como consultas compartilhadas e reutilizá-las. Para chamar uma consulta compartilhada dentro de uma consulta de gráfico de recursos, use a `{{shared-query-uri}}` sintaxe. O URI da consulta compartilhada é a _ID de recurso_ da consulta compartilhada na página de **configurações** dessa consulta. Neste exemplo, nosso URI de consulta compartilhada é `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS` .
Esse URI aponta para a assinatura, o grupo de recursos e o nome completo da consulta compartilhada que queremos referenciar em outra consulta. Essa consulta é a mesma criada no [tutorial: criar e compartilhar uma consulta](../tutorials/create-share-query.md).

> [!NOTE]
> Você não pode salvar uma consulta que faça referência a uma consulta compartilhada como uma consulta compartilhada.

Exemplo 1: usar somente a consulta compartilhada

Os resultados dessa consulta de grafo de recursos são os mesmos que a consulta armazenada na consulta compartilhada.

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
```

Exemplo 2: incluir a consulta compartilhada como parte de uma consulta maior

Essa consulta usa primeiro a consulta compartilhada e, em seguida, usa `limit` para restringir ainda mais os resultados.

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
| where properties_storageProfile_osDisk_osType =~ 'Windows'
```

## <a name="supported-kql-language-elements"></a>Elementos da linguagem KQL com suporte

O grafo de recursos dá suporte a um subconjunto de [tipos de dados](/azure/kusto/query/scalar-data-types/)KQL, [funções escalares](/azure/kusto/query/scalarfunctions), [operadores escalares](/azure/kusto/query/binoperators)e [funções de agregação](/azure/kusto/query/any-aggfunction). O Resource Graph dá suporte a [operadores de tabela](/azure/kusto/query/queries) específicos e alguns destes operadores apresentam comportamentos diferentes.

### <a name="supported-tabulartop-level-operators"></a>Operadores de tabela/nível superior com suporte

A seguir está a lista de operadores de tabela da linguagem KQL com suporte do Resource Graph com exemplos específicos:

|KQL |Consulta de exemplo do Resource Graph |Observações |
|---|---|---|
|[contagem](/azure/kusto/query/countoperator) |[Contar cofres de chaves](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/kusto/query/distinctoperator) |[Mostrar valores distintos para um alias específico](../samples/starter.md#distinct-alias-values) | |
|[extend](/azure/kusto/query/extendoperator) |[Contagem de máquinas virtuais por tipo de sistema operacional](../samples/starter.md#count-os) | |
|[join](/azure/kusto/query/joinoperator) |[Cofre de chaves com o nome da assinatura](../samples/advanced.md#join) |Tipos de união com suporte: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [inner](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). Há um limite de 3 `join` em uma única consulta. Estratégias de união personalizadas, como união difundida, não são permitidas. Para as tabelas que podem ser usadas `join` , consulte [tabelas de gráfico de recursos](#resource-graph-tables). |
|[limit](/azure/kusto/query/limitoperator) |[Listar todos os endereços de IP](../samples/starter.md#list-publicip) |Sinônimo de `take` . Não funciona com [Skip](./work-with-data.md#skipping-records). |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Operador herdado, use `mv-expand` como substituto. _RowLimit_ com limite máximo de 400. O padrão é 128. |
|[mv-expand](/azure/kusto/query/mvexpandoperator) |[Listar Cosmos DB com locais de gravação específicos](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_ com limite máximo de 400. O padrão é 128. |
|[order](/azure/kusto/query/orderoperator) |[Listar recursos classificados por nome](../samples/starter.md#list-resources) |Sinônimo de `sort` |
|[project](/azure/kusto/query/projectoperator) |[Listar recursos classificados por nome](../samples/starter.md#list-resources) | |
|[project-away](/azure/kusto/query/projectawayoperator) |[Remover colunas dos resultados](../samples/advanced.md#remove-column) | |
|[sort](/azure/kusto/query/sortoperator) |[Listar recursos classificados por nome](../samples/starter.md#list-resources) |Sinônimo de `order` |
|[summarize](/azure/kusto/query/summarizeoperator) |[Recursos do Count Azure](../samples/starter.md#count-resources) |Somente a primeira página simplificada |
|[take](/azure/kusto/query/takeoperator) |[Listar todos os endereços de IP](../samples/starter.md#list-publicip) |Sinônimo de `limit` . Não funciona com [Skip](./work-with-data.md#skipping-records). |
|[início](/azure/kusto/query/topoperator) |[Mostrar as primeiras cinco máquinas virtuais por nome e tipo do sistema operacional](../samples/starter.md#show-sorted) | |
|[union](/azure/kusto/query/unionoperator) |[Combinar resultados de duas consultas em um único resultado](../samples/advanced.md#unionresults) |Tabela individual permitida: _T_ `| union` \[`kind=` `inner`\|`outer`\] \[`withsource=`_ColumnName_\] _Table_. Há um limite de 3 expressões de `union` em uma única consulta. A resolução difusa de tabelas da expressão de `union` não é permitida. Elas podem ser usadas para uma única tabela ou entre as tabelas _Resources_ e _ResourceContainers_. |
|[where](/azure/kusto/query/whereoperator) |[Mostrar recursos que contêm o armazenamento](../samples/starter.md#show-storage) | |

## <a name="query-scope"></a>Escopo da consulta

O escopo das assinaturas dos quais os recursos são retornados por uma consulta depende do método de acesso ao grafo de recursos. CLI do Azure e Azure PowerShell popular a lista de assinaturas a serem incluídas na solicitação com base no contexto do usuário autorizado. A lista de assinaturas pode ser definida manualmente para cada uma com as **assinaturas** e os parâmetros de **assinatura** , respectivamente.
Na API REST e em todos os outros SDKs, a lista de assinaturas para incluir recursos deve ser explicitamente definida como parte da solicitação.

Como uma **Visualização**, a versão da API REST `2020-04-01-preview` adiciona uma propriedade para o escopo da consulta a um [grupo de gerenciamento](../../management-groups/overview.md). Essa API de visualização também torna a propriedade de assinatura opcional. Se um grupo de gerenciamento ou uma lista de assinaturas não estiver definida, o escopo da consulta será todos os recursos, que inclui recursos delegados do [Lighthouse do Azure](../../../lighthouse/concepts/azure-delegated-resource-management.md) , que o usuário autenticado pode acessar. A nova `managementGroupId` propriedade usa a ID do grupo de gerenciamento, que é diferente do nome do grupo de gerenciamento. Quando `managementGroupId` é especificado, os recursos das primeiras assinaturas 5000 no ou na hierarquia do grupo de gerenciamento especificado são incluídos. `managementGroupId` Não pode ser usado ao mesmo tempo que `subscriptions` .

Exemplo: consultar todos os recursos na hierarquia do grupo de gerenciamento denominado ' meu grupo de gerenciamento ' com a ID ' myMG '.

- URI da API REST

  ```http
  POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2020-04-01-preview
  ```

- Corpo da solicitação

  ```json
  {
      "query": "Resources | summarize count()",
      "managementGroupId": "myMG"
  }
  ```

## <a name="escape-characters"></a>Caracteres de escape

Alguns nomes de propriedade, como aqueles que incluem `.` ou `$`, devem ser encapsulados ou precedidos por um caractere de escape na consulta ou o nome da propriedade é interpretado incorretamente e não fornece os resultados esperados.

- `.` - Encapsule o nome da propriedade da seguinte maneira: `['propertyname.withaperiod']`
  
  Exemplo de consulta que encapsula a propriedade _odata.type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` - Use o caractere de escape no nome da propriedade. O caractere de escape usado depende do shell onde o Resource Graph é executado.

  - **bash** - `\`

    Exemplo de consulta que usa o caractere de escape na propriedade _\$type_ no bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** - Não escape o caractere `$`.

  - **PowerShell** - ``` ` ```

    Exemplo de consulta que usa o caractere de escape na propriedade _\$type_ no PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Próximas etapas

- Consulte a linguagem em uso em [Consultas iniciais](../samples/starter.md).
- Consulte os usos avançados em [Consultas avançadas](../samples/advanced.md).
- Saiba mais sobre como [explorar recursos](explore-resources.md).
