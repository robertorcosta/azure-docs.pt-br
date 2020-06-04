---
title: Use Azure Policy para implementar governança e controles para recursos de Azure Cosmos DB
description: Saiba como usar Azure Policy para implementar governança e controles para recursos de Azure Cosmos DB.
author: plzm
ms.author: paelaz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 2249dbdebecc52a8f5d6decccb83d3b1fc0777f7
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747369"
---
# <a name="use-azure-policy-to-implement-governance-and-controls-for-azure-cosmos-db-resources"></a>Use Azure Policy para implementar governança e controles para recursos de Azure Cosmos DB

O [Azure Policy](../governance/policy/overview.md) ajuda a reforçar os padrões de governança organizacional, a avaliar a conformidade dos recursos e a implementar a correção automática. Os casos de uso comuns incluem segurança, gerenciamento de custos e consistência de configuração.

Definições de política interna do Azure Policy. Você pode criar definições de política personalizadas para cenários que não são abordados pelas definições de política internas. Para obter mais detalhes, confira a [documentação do Azure Policy](../governance/policy/overview.md).

## <a name="assign-a-built-in-policy-definition"></a>Atribuir uma definição de política incorporada

As definições de política descrevem as condições de conformidade do recurso e o efeito a ser tomada se uma condição for atendida. As _atribuições_ de política são criadas com base nas _definições_ de política. Você pode usar definições de política internas ou personalizadas para seus recursos do Microsoft Azure Cosmos DB. As atribuições de política têm como escopo um grupo de gerenciamento do Azure, uma assinatura do Azure ou um grupo de recursos e são aplicadas aos recursos dentro do escopo selecionado. Opcionalmente, você pode excluir recursos específicos do escopo.

Você pode criar atribuições de política com o [portal do Azure](../governance/policy/assign-policy-portal.md), [Azure PowerShell](../governance/policy/assign-policy-powershell.md), [CLI do Azure](../governance/policy/assign-policy-azurecli.md)ou [modelo ARM](../governance/policy/assign-policy-template.md).

Para criar uma atribuição de política de uma definição de política interna para Azure Cosmos DB, use as etapas no artigo [Criar uma atribuição de política com o portal do Azure](../governance/policy/assign-policy-portal.md).

Na etapa para selecionar uma definição de política, insira `Cosmos DB` no campo de pesquisa para filtrar a lista de definições de políticas internas disponíveis. Selecione uma das definições de política internas disponíveis e escolha **Selecionar** para continuar criando a atribuição de política.

> [!TIP]
> Você também pode usar os nomes de definição de política interna mostrados no painel **Definições disponíveis** com modelos Azure PowerShell, CLI do Azure ou ARM para criar atribuições de política.

:::image type="content" source="./media/policy/available-definitions.png" alt-text="Pesquisar definições de políticas internas para o Azure Cosmos DB":::

## <a name="create-a-custom-policy-definition"></a>Criar uma definição de política personalizada

Para cenários que não são abordados pelas definições de política internas, você pode criar [uma definição de política personalizada](../governance/policy/tutorials/create-custom-policy-definition.md). Posteriormente, você cria uma _atribuição_ de política com a sua _definição_ de política personalizada.

### <a name="property-types-and-property-aliases-in-policy-rules"></a>Tipos de propriedade e aliases de propriedade em regras de política

Use as [etapas de definição de política personalizada](../governance/policy/tutorials/create-custom-policy-definition.md) para identificar as propriedades de recurso e os aliases de propriedade, que são necessários para criar regras de política.

Para identificar Azure Cosmos DB aliases de propriedade específicos, use o namespace `Microsoft.DocumentDB` com um dos métodos mostrados no artigo etapas de definição de política personalizada.

#### <a name="use-the-azure-cli"></a>Usar a CLI do Azure:
```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for namespace Microsoft.DocumentDB
az provider show --namespace Microsoft.DocumentDB --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

#### <a name="use-azure-powershell"></a>Usar PowerShell do Azure:
```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.DocumentDB namespace
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.DocumentDB').Aliases
```

Esses comandos geram a lista de nomes de alias de propriedade para a propriedade do Microsoft Azure Cosmos DB. Veja a seguir um trecho da saída:

```json
[
  "Microsoft.DocumentDB/databaseAccounts/sku.name",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*]",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*].id",
  "Microsoft.DocumentDB/databaseAccounts/isVirtualNetworkFilterEnabled",
  "Microsoft.DocumentDB/databaseAccounts/consistencyPolicy.defaultConsistencyLevel",
  "Microsoft.DocumentDB/databaseAccounts/enableAutomaticFailover",
  "Microsoft.DocumentDB/databaseAccounts/Locations",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*]",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*].locationName",
  "..."
]
```

Você pode usar qualquer um desses nomes de alias de propriedade nas [regras de definição de política personalizada](../governance/policy/tutorials/create-custom-policy-definition.md#policy-rule).

Veja a seguir um exemplo de definição de política que verifica se uma taxa de transferência provisionada do banco de dados SQL do Microsoft Azure Cosmos DB é maior que um limite máximo permitido de 400 RU/s. Uma definição de política personalizada inclui duas regras: uma para verificar o tipo específico de alias de propriedade e a segunda para a propriedade específica do tipo. Ambas as regras usam os nomes de alias.

```json
"policyRule": {
  "if": {
    "allOf": [
      {
      "field": "type",
      "equals": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings"
      },
      {
      "field": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/default.resource.throughput",
      "greater": 400
      }
    ]
  }
}
```

As definições de política personalizadas podem ser usadas para criar atribuições de política, como acontece com as definições de política internas.

## <a name="policy-compliance"></a>Conformidade da política

Depois que as atribuições de política são criadas, o Azure Policy avalia os recursos no escopo da atribuição. A_conformidade_de cada recurso com a política é avaliada. O _efeito_ especificado na política é então aplicado a recursos sem conformidade.

Você pode examinar os resultados de conformidade e os detalhes de correção no [portal do Azure](../governance/policy/how-to/get-compliance-data.md#portal) ou por meio da [CLI do Azure](../governance/policy/how-to/get-compliance-data.md#command-line) ou dos logs do [Azure monitor](../governance/policy/how-to/get-compliance-data.md#azure-monitor-logs).

A captura de tela a seguir mostra dois exemplos de atribuições de política. Uma atribuição é baseada em uma definição de política interna, que verifica se os recursos do Microsoft Azure Cosmos DB são implantados somente nas regiões do Azure permitidas. A outra atribuição é baseada em uma definição de política personalizada. Essa atribuição verifica se a taxa de transferência provisionada nos recursos do Microsoft Azure Cosmos DB não excede um limite máximo especificado.

Depois que as atribuições de política são implantadas, o painel de conformidade mostra os resultados da avaliação. Observe que isso pode levar até 30 minutos após a implantação de uma atribuição de política.

A captura de tela mostra os seguintes resultados de avaliação de conformidade:

- Zero de uma das contas do Microsoft Azure Cosmos DB no escopo especificado são compatíveis com a atribuição de política para verificar se os recursos foram implantados em regiões permitidas.
- Um de dois banco de dados ou recursos de coleção do Microsoft Azure Cosmos DB no escopo especificado é compatível com a atribuição de política para verificar a taxa de transferência provisionada que excede o limite máximo especificado.

:::image type="content" source="./media/policy/compliance.png" alt-text="Pesquisar definições de políticas internas para o Azure Cosmos DB":::

Para corrigir os recursos sem conformidade, consulte o artigo [corrigido com Azure Policy](../governance/policy/how-to/remediate-resources.md).

## <a name="next-steps"></a>Próximas etapas

- [Revisar definições de política personalizada para o Microsoft Azure Cosmos DB](https://github.com/Azure/azure-policy/tree/master/samples/CosmosDB)
- [Criar uma atribuição de política no portal do Azure](../governance/policy/assign-policy-portal.md)
- [Revisar as definições de políticas internas do Azure Policy para o Microsoft Azure Cosmos DB](./policy-samples.md)
