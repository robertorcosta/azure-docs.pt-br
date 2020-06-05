---
title: Implantação de recursos em assinaturas e grupos de recursos
description: Mostra como usar mais de um destino de assinatura e de grupo de recursos do Azure durante a implantação.
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 2ef68dcb933075833c323d973b023cdaee61bd2f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650628"
---
# <a name="deploy-azure-resources-across-subscriptions-or-resource-groups"></a>Implantação de recursos do Azure em assinaturas ou grupos de recursos

O Resource Manager permite a implantação em mais de um grupo de recursos com uma única implantação. Modelos aninhados são usados para especificar grupos de recursos que são diferentes do grupo de recursos na operação de implantação. Os grupos de recursos podem estar presentes em diferentes assinaturas.

> [!NOTE]
> A implantação pode ser feita em **800 grupos de recursos** com uma única implantação. Normalmente, essa limitação significa que você pode implantar em um grupo de recursos especificado para o modelo pai e até 799 grupos de recursos em implantações aninhadas ou vinculadas. No entanto, se o modelo pai contém apenas os modelos aninhados ou vinculados e não implanta, ele mesmo, todos os recursos, você pode incluir até 800 grupos de recursos em implantações aninhadas ou vinculadas.

## <a name="specify-subscription-and-resource-group"></a>Especifique uma assinatura e um grupo de recursos

Para usar como destino um grupo de recursos diferente daquele para o modelo pai, use um [modelo aninhado ou vinculado](linked-templates.md). Dentro do tipo de recurso de implantação, especifique valores para a ID de assinatura e para o grupo de recursos onde você deseja implantar o modelo aninhado.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json" range="38-43" highlight="5-6":::

Se você não especificar a ID da assinatura ou o grupo de recursos, serão usados a assinatura e o grupo de recursos do modelo pai. Todos os grupos de recursos devem existir antes da execução da implantação.

A conta que implanta o modelo deve ter permissões para implantar a ID da assinatura especificada. Se a assinatura especificada existe em um locatário diferente do Azure Active Directory, você deve [adicionar usuários convidados em outro diretório](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

O exemplo a seguir implanta duas contas de armazenamento. A primeira conta de armazenamento é implantada no grupo de recursos especificado na operação de implantação. A segunda conta de armazenamento é implantada no grupo de recursos especificado nos parâmetros `secondResourceGroup` e `secondSubscriptionID`:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

Se você definir `resourceGroup` como o nome de um grupo de recursos que não existe, a implantação falhará.

Para testar o modelo anterior e ver os resultados, use o PowerShell ou a CLI do Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para implantar duas contas de armazenamento em dois grupos de recursos na **mesma assinatura**, use:

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

Para implantar duas contas de armazenamento em **duas assinaturas**, use:

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

Para implantar duas contas de armazenamento em dois grupos de recursos na **mesma assinatura**, use:

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

Para implantar duas contas de armazenamento em **duas assinaturas**, use:

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

## <a name="use-functions"></a>Usar funções

As funções [resourceGroup()](template-functions-resource.md#resourcegroup) e [subscription()](template-functions-resource.md#subscription) são resolvidas de forma diferente conforme a maneira que você especificou o modelo. Quando você vincula a um modelo externo, as funções sempre são resolvidas para o escopo desse modelo. Quando você aninha um modelo dentro de um modelo pai, use a propriedade `expressionEvaluationOptions` para especificar se as funções são resolvidas para o grupo de recursos e a assinatura para o modelo pai ou o modelo aninhado. Defina a propriedade como `inner` para resolver para o escopo do modelo aninhado. Defina a propriedade como `outer` para resolver para o escopo do modelo pai.

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
