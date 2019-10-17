---
title: Gerenciar grupos de Azure Resource Manager usando Azure PowerShell | Microsoft Docs
description: Use Azure PowerShell para gerenciar seus grupos de recursos por meio de Azure Resource Manager. Mostra como criar, listar e excluir grupos de recursos.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 1e057bc484a0b526927654fafa8f88ae4a5b4deb
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390406"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Gerenciar Azure Resource Manager grupos de recursos usando Azure PowerShell

Saiba como usar Azure PowerShell com [Azure Resource Manager](resource-group-overview.md) para gerenciar seus grupos de recursos do Azure. Para gerenciar recursos do Azure, consulte [gerenciar recursos do Azure usando Azure PowerShell](./manage-resources-powershell.md).

Outros artigos sobre como gerenciar grupos de recursos:

- [Gerenciar grupos de recursos do Azure usando o portal do Azure](./manage-resources-portal.md)
- [Gerenciar grupos de recursos do Azure usando o CLI do Azure](./manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>O que é um grupo de recursos

Um grupo de recursos é um contêiner que mantém os recursos relacionados a uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que você deseja gerenciar como um grupo. Você decide como deseja alocar recursos para grupos de recursos com base no que faz mais sentido para sua organização. Em geral, adicione recursos que compartilham o mesmo ciclo de vida no mesmo grupo de recursos, para que você possa implantar, atualizar e excluí-los como um grupo facilmente.

O grupo de recursos armazena metadados sobre os recursos. Portanto, quando você especifica um local para o grupo de recursos, especifica onde os metadados são armazenados. Por motivos de conformidade, você precisa fazer com que os dados sejam armazenados em determinada região.

O grupo de recursos armazena metadados sobre os recursos. Quando você especifica uma localização para o grupo de recursos, você especifica onde os metadados são armazenados.

## <a name="create-resource-groups"></a>Criar grupos de recursos

O script do PowerShell a seguir cria um grupo de recursos e, em seguida, mostra o grupo de recursos.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>Listar os grupos de recursos

O script do PowerShell a seguir lista os grupos de recursos em sua assinatura.

```azurepowershell-interactive
Get-AzResourceGroup
```

Para obter um grupo de recursos:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Excluir grupos de recursos

O seguinte script do PowerShell exclui um grupo de recursos:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Para obter mais informações sobre como Azure Resource Manager ordena a exclusão de recursos, consulte [Azure Resource Manager exclusão de grupo de recursos](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Implantar recursos em um grupo de recursos existente

Consulte [implantar recursos em um grupo de recursos existente](./manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group).

Para validar uma implantação de grupo de recursos, consulte [Test-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0).

## <a name="deploy-a-resource-group-and-resources"></a>Implantar um grupo de recursos e recursos

Você pode criar um grupo de recursos e implantar recursos no grupo usando um modelo do Resource Manager. Para saber mais, confira [Create resource group and deploy resources](./deploy-to-subscription.md#resource-group-and-resources) (Criar grupo de recursos e implantar recursos).

## <a name="redeploy-when-deployment-fails"></a>Reimplantar quando ocorrer falha na implantação

Esse recurso também é conhecido como *reversão em caso de erro*. Para obter mais informações, consulte [reimplantar quando a implantação falhar](./rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Mover para outro grupo de recursos ou assinatura

Você pode mover os recursos do grupo para outro grupo de recursos. Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](./resource-group-move-resources.md).

## <a name="lock-resource-groups"></a>Bloquear grupos de recursos

O bloqueio impede que outros usuários em sua organização excluam ou modifiquem acidentalmente recursos críticos, como assinatura do Azure, grupo de recursos ou recurso. 

O script a seguir bloqueia um grupo de recursos para que o grupo de recursos não possa ser excluído.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

O script a seguir obtém todos os bloqueios para um grupo de recursos:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

Para saber mais, confira [Bloquear recursos com o Azure Resource Manager](resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Marcar grupos de recursos

Você pode aplicar marcas a recursos e grupos de recursos para organizar seus ativos de modo lógico. Para obter informações, consulte [usando marcas para organizar os recursos do Azure](./resource-group-using-tags.md#powershell).

## <a name="export-resource-groups-to-templates"></a>Exportar grupos de recursos para modelos

Depois de configurar seu grupo de recursos, você pode exibir um modelo do Resource Manager para o grupo de recursos. A exportação do modelo oferece dois benefícios:

- Automatize implantações futuras da solução porque o modelo contém a infraestrutura completa.
- Aprenda a sintaxe do modelo examinando o JavaScript Object Notation (JSON) que representa sua solução.

Para exportar todos os recursos em um grupo de recursos, use o cmdlet [Export-AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) e forneça o nome do grupo de recursos.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

Ele salva o modelo como um arquivo local.

Em vez de exportar todos os recursos no grupo de recursos, você pode selecionar quais recursos exportar.

Para exportar um recurso, passe essa ID de recurso.

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

Para exportar mais de um recurso, passe as IDs de recurso em uma matriz.

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

Ao exportar o modelo, você pode especificar se os parâmetros são usados no modelo. Por padrão, os parâmetros para nomes de recursos são incluídos, mas não têm um valor padrão. Você deve passar esse valor de parâmetro durante a implantação.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": null,
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": null,
    "type": "String"
  }
}
```

No recurso, o parâmetro é usado para o nome.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

Se você usar o parâmetro `-IncludeParameterDefaultValue` ao exportar o modelo, o parâmetro de modelo incluirá um valor padrão definido como o valor atual. Você pode usar esse valor padrão ou substituir o valor padrão passando um valor diferente.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

Se você usar o parâmetro `-SkipResourceNameParameterization` ao exportar o modelo, os parâmetros para nomes de recursos não serão incluídos no modelo. Em vez disso, o nome do recurso é definido diretamente no recurso para seu valor atual. Não é possível personalizar o nome durante a implantação.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

Para obter mais informações, consulte [exportação única e de vários recursos para o modelo no portal do Azure](./export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Gerenciar o acesso a grupos de recursos

O [RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md) é a maneira de gerenciar o acesso aos recursos no Azure. Para obter mais informações, consulte [gerenciar o acesso usando RBAC e Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Próximos passos

- Para saber Azure Resource Manager, consulte [Azure Resource Manager visão geral](./resource-group-overview.md).
- Para saber mais sobre a sintaxe do modelo do Resource Manager, consulte [entender a estrutura e a sintaxe dos modelos de Azure Resource Manager](./resource-group-authoring-templates.md).
- Para saber como desenvolver modelos, consulte os tutoriais passo a [passo](/azure/azure-resource-manager/).
- Para exibir os esquemas de modelo de Azure Resource Manager, consulte [referência de modelo](/azure/templates/).