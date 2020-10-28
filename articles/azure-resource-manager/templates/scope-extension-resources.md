---
title: Escopo em tipos de recursos de extensão
description: Descreve como usar a Propriedade Scope ao implantar tipos de recursos de extensão.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: bd468d31454c38bd314269243702d7df4f279a5e
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681394"
---
# <a name="setting-scope-for-extension-resources-in-arm-templates"></a>Definindo o escopo para recursos de extensão em modelos ARM

Um recurso de extensão é um recurso que modifica outro recurso. Por exemplo, você pode atribuir uma função a um recurso para limitar o acesso. A atribuição de função é um tipo de recurso de extensão.

Para obter uma lista completa dos tipos de recursos de extensão, consulte [tipos de recursos que estendem funcionalidades de outros recursos](../management/extension-resource-types.md).

Este artigo mostra como definir o escopo de um tipo de recurso de extensão quando implantado com um modelo de Azure Resource Manager (modelo ARM). Ele descreve a Propriedade Scope que está disponível para recursos de extensão ao aplicar a um recurso.

## <a name="apply-at-deployment-scope"></a>Aplicar no escopo da implantação

Para aplicar um tipo de recurso de extensão no escopo de implantação de destino, você adiciona o recurso ao modelo, como faria com qualquer tipo de recurso. Os escopos disponíveis são [grupo de recursos](deploy-to-resource-group.md), [assinatura](deploy-to-subscription.md), [grupo de gerenciamento](deploy-to-management-group.md)e [locatário](deploy-to-tenant.md). O escopo de implantação deve dar suporte ao tipo de recurso.

O modelo a seguir implanta um bloqueio.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/locktargetscope.json":::

Quando implantado em um grupo de recursos, ele bloqueia o grupo de recursos.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
 New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

---

O exemplo a seguir atribui uma função.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/roletargetscope.json":::

Quando implantado em uma assinatura, ele atribui a função à assinatura.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

---

## <a name="apply-to-resource"></a>Aplicar ao recurso

Para aplicar um recurso de extensão a um recurso, use a `scope` propriedade. Defina a propriedade escopo como o nome do recurso ao qual você está adicionando a extensão. A propriedade de escopo é uma propriedade raiz para o tipo de recurso de extensão.

O exemplo a seguir cria uma conta de armazenamento e aplica uma função a ela.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/storageandrole.json" highlight="56":::

## <a name="next-steps"></a>Próximas etapas

* Para entender como definir parâmetros em seu modelo, confira [Noções básicas de estrutura e sintaxe dos modelos do Azure Resource Manager](template-syntax.md).
* Para dicas sobre como resolver erros de implantação, consulte [Solução de erros comuns de implantação do Azure com o Azure Resource Manager](common-deployment-errors.md).
* Para saber mais sobre como implantar um modelo que exija um token SAS, veja [Implantar o modelo particular com o token SAS](secure-template-with-sas-token.md).
