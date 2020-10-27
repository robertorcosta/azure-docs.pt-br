---
title: Implantar recursos entre escopos
description: Mostra como direcionar mais de um escopo durante uma implantação. O escopo pode ser um locatário, grupos de gerenciamento, assinaturas e grupos de recursos.
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 6161401ac039551a814b595715f56df1ac62dd6c
ms.sourcegitcommit: d3c3f2ded72bfcf2f552e635dc4eb4010491eb75
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92558846"
---
# <a name="deploy-azure-resources-across-scopes"></a>Implantar recursos do Azure entre escopos

Com modelos de Azure Resource Manager (modelos ARM), você pode implantar em mais de um escopo em uma única implantação. Os escopos disponíveis são um locatário, grupos de gerenciamento, assinaturas e grupos de recursos. Por exemplo, você pode implantar recursos em um grupo de recursos e, no mesmo modelo, implantar recursos em outro grupo de recursos. Ou, você pode implantar recursos em um grupo de gerenciamento e também implantar recursos em um grupo de recursos dentro desse grupo de gerenciamento.

Você usa [modelos aninhados ou vinculados](linked-templates.md) para especificar escopos diferentes do escopo primário da operação de implantação.

## <a name="available-scopes"></a>Escopos disponíveis

O escopo que você usa para a operação de implantação determina quais outros escopos estão disponíveis. Você pode implantar no [locatário](deploy-to-tenant.md), [grupo de gerenciamento](deploy-to-management-group.md), [assinatura](deploy-to-subscription.md)ou grupo de [recursos](deploy-powershell.md). No nível de implantação primária, você não pode subir níveis na hierarquia. Por exemplo, se você implantar o em uma assinatura, não poderá passar um nível para implantar recursos em um grupo de gerenciamento. No entanto, você pode implantar o grupo de gerenciamento e os níveis de etapa para implantar em uma assinatura ou grupo de recursos.

Para cada escopo, o usuário que está implantando o modelo deve ter as permissões necessárias para criar recursos.

## <a name="cross-resource-groups"></a>Grupos de recursos cruzados

Para usar como destino um grupo de recursos diferente daquele para o modelo pai, use um [modelo aninhado ou vinculado](linked-templates.md). Dentro do tipo de recurso de implantação, especifique valores para a ID de assinatura e para o grupo de recursos onde você deseja implantar o modelo aninhado. Os grupos de recursos podem estar presentes em diferentes assinaturas.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json" range="38-43" highlight="5-6":::

Se você não especificar a ID da assinatura ou o grupo de recursos, serão usados a assinatura e o grupo de recursos do modelo pai. Todos os grupos de recursos devem existir antes da execução da implantação.

> [!NOTE]
> A implantação pode ser feita em **800 grupos de recursos** com uma única implantação. Normalmente, essa limitação significa que você pode implantar em um grupo de recursos especificado para o modelo pai e até 799 grupos de recursos em implantações aninhadas ou vinculadas. No entanto, se o modelo pai contém apenas os modelos aninhados ou vinculados e não implanta, ele mesmo, todos os recursos, você pode incluir até 800 grupos de recursos em implantações aninhadas ou vinculadas.

O exemplo a seguir implanta duas contas de armazenamento. A primeira conta de armazenamento é implantada no grupo de recursos especificado na operação de implantação. A segunda conta de armazenamento é implantada no grupo de recursos especificado nos parâmetros `secondResourceGroup` e `secondSubscriptionID`:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

Se você definir `resourceGroup` como o nome de um grupo de recursos que não existe, a implantação falhará.

Para testar o modelo anterior e ver os resultados, use o PowerShell ou a CLI do Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para implantar duas contas de armazenamento em dois grupos de recursos na **mesma assinatura** , use:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Para implantar duas contas de armazenamento em **duas assinaturas** , use:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzSubscription -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Select-AzSubscription -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para implantar duas contas de armazenamento em dois grupos de recursos na **mesma assinatura** , use:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Para implantar duas contas de armazenamento em **duas assinaturas** , use:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="cross-subscription-management-group-and-tenant"></a>Assinatura cruzada, grupo de gerenciamento e locatário

Ao especificar escopos diferentes para a assinatura, grupo de gerenciamento e implantações de nível de locatário, você usa implantações aninhadas como o exemplo para grupos de recursos. As propriedades que você usa para especificar o escopo podem ser diferentes. Esses cenários são abordados nos artigos sobre os níveis de implantações. Para obter mais informações, consulte:

* [Criar grupos de recursos e recursos no nível da assinatura](deploy-to-subscription.md)
* [Criar recursos no nível do grupo de gerenciamento](deploy-to-management-group.md)
* [Criar recursos no nível do locatário](deploy-to-tenant.md)

## <a name="how-functions-resolve-in-scopes"></a>Como as funções são resolvidas em escopos

Quando você implanta em mais de um escopo, as funções [resourcegroup ()](template-functions-resource.md#resourcegroup) e [Subscription ()](template-functions-resource.md#subscription) são resolvidas de forma diferente com base em como você especifica o modelo. Quando você vincula a um modelo externo, as funções sempre são resolvidas para o escopo desse modelo. Quando você aninha um modelo dentro de um modelo pai, use a propriedade `expressionEvaluationOptions` para especificar se as funções são resolvidas para o grupo de recursos e a assinatura para o modelo pai ou o modelo aninhado. Defina a propriedade como `inner` para resolver para o escopo do modelo aninhado. Defina a propriedade como `outer` para resolver para o escopo do modelo pai.

A tabela a seguir mostra se as funções são resolvidas para o grupo de recursos pai ou aninhado e para a assinatura.

| Tipo do modelo | Escopo | Resolução |
| ------------- | ----- | ---------- |
| aninhado        | externo (padrão) | Grupo de recursos pai |
| aninhado        | interna | Subgrupo de recursos |
| vinculado        | N/D   | Subgrupo de recursos |

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) a seguir mostra:

* o modelo aninhado com escopo padrão (externo)
* o modelo aninhado com escopo interno
* o modelo vinculado

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crossresourcegroupproperties.json":::

Para testar o modelo anterior e ver os resultados, use o PowerShell ou a CLI do Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

O resultado do exemplo anterior é:

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

O resultado do exemplo anterior é:

```output
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---



## <a name="next-steps"></a>Próximas etapas

* Para entender como definir parâmetros em seu modelo, confira [Noções básicas de estrutura e sintaxe dos modelos do Azure Resource Manager](template-syntax.md).
* Para dicas sobre como resolver erros de implantação, consulte [Solução de erros comuns de implantação do Azure com o Azure Resource Manager](common-deployment-errors.md).
* Para saber mais sobre como implantar um modelo que exija um token SAS, veja [Implantar o modelo particular com o token SAS](secure-template-with-sas-token.md).
