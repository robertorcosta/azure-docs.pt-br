---
title: Gerenciar grupos de recursos - Azure PowerShell
description: Use o Azure PowerShell para gerenciar seus grupos de recursos através do Azure Resource Manager. Mostra como criar, listar e excluir grupos de recursos.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 25e001927d5c06b4a7e5639faaa054ae18b12bb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248353"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Gerencie grupos de recursos do Azure Resource Manager usando o Azure PowerShell

Aprenda a usar o Azure PowerShell com [o Azure Resource Manager](overview.md) para gerenciar seus grupos de recursos do Azure. Para gerenciar os recursos do Azure, consulte [Gerenciar os recursos do Azure usando o Azure PowerShell](manage-resources-powershell.md).

Outros artigos sobre o gerenciamento de grupos de recursos:

- [Gerencie grupos de recursos do Azure usando o portal Azure](manage-resources-portal.md)
- [Gerencie grupos de recursos do Azure usando o Azure CLI](manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>O que é um grupo de recursos

Um grupo de recursos é um contêiner que mantém os recursos relacionados a uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que você deseja gerenciar como um grupo. Você decide como deseja alocar recursos para grupos de recursos com base no que faz mais sentido para sua organização. Em geral, adicione recursos que compartilham o mesmo ciclo de vida no mesmo grupo de recursos, para que você possa implantar, atualizar e excluí-los como um grupo facilmente.

O grupo de recursos armazena metadados sobre os recursos. Portanto, quando você especifica um local para o grupo de recursos, especifica onde os metadados são armazenados. Por motivos de conformidade, você precisa fazer com que os dados sejam armazenados em determinada região.

O grupo de recursos armazena metadados sobre os recursos. Quando você especifica uma localização para o grupo de recursos, você especifica onde os metadados são armazenados.

## <a name="create-resource-groups"></a>Criar grupos de recursos

O script powershell a seguir cria um grupo de recursos e, em seguida, mostra o grupo de recursos.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>Listar os grupos de recursos

O script do PowerShell a seguir lista os grupos de recursos sua assinatura.

```azurepowershell-interactive
Get-AzResourceGroup
```

Para obter um grupo de recursos:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Excluir grupos de recursos

O script do PowerShell a seguir exclui um grupo de recursos:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Para obter mais informações sobre como o Azure Resource Manager ordena a exclusão de recursos, consulte a exclusão do grupo de [recursos do Azure Resource Manager](delete-resource-group.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Implantar recursos para um grupo de recursos existente

Consulte [Implantar recursos em um grupo de recursos existente](manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group).

Para validar uma implantação de grupo de recursos, consulte [Test-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0).

## <a name="deploy-a-resource-group-and-resources"></a>Implantar um grupo de recursos e recursos

Você pode criar um grupo de recursos e implantar recursos para o grupo usando um modelo de Gerenciador de recursos. Para saber mais, confira [Create resource group and deploy resources](../templates/deploy-to-subscription.md#resource-group-and-resources) (Criar grupo de recursos e implantar recursos).

## <a name="redeploy-when-deployment-fails"></a>Reimplantar quando ocorrer falha na implantação

Esse recurso também é conhecido como *Reversão de erro*. Para obter mais informações, consulte [Reimplantar quando a implantação falhar](../templates/rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Mova-se para outro grupo de recursos ou assinatura

Você pode transferir os recursos do grupo para outro grupo de recursos. Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](move-resource-group-and-subscription.md).

## <a name="lock-resource-groups"></a>Grupos de recursos de bloqueio

O bloqueio impede que outros usuários da sua organização excluam acidentalmente ou modifiquem recursos críticos, como assinatura do Azure, grupo de recursos ou recurso. 

O script a seguir bloqueia um grupo de recursos para que o grupo de recursos não possa ser excluído.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

O script a seguir recebe todas as fechaduras para um grupo de recursos:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](lock-resources.md).

## <a name="tag-resource-groups"></a>Grupos de recursos de tag

Você pode aplicar marcas a recursos e grupos de recursos para organizar seus ativos de modo lógico. Para obter informações, consulte [Usando tags para organizar seus recursos do Azure](tag-resources.md#powershell).

## <a name="export-resource-groups-to-templates"></a>Exportar grupos de recursos para modelos

Depois de configurar seu grupo de recursos, você pode visualizar um modelo de Gerenciador de recursos para o grupo de recursos. A exportação do modelo oferece dois benefícios:

- Automatize futuras implantações da solução porque o modelo contém a infra-estrutura completa.
- Aprenda a sintaxe do modelo olhando para a Notação de Objeto JavaScript (JSON) que representa sua solução.

Para exportar todos os recursos em um grupo de recursos, use o cmdlet [Export-AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) e forneça o nome do grupo de recursos.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

Ele salva o modelo como um arquivo local.

Em vez de exportar todos os recursos do grupo de recursos, você pode selecionar quais recursos exportar.

Para exportar um recurso, passe esse ID de recurso.

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

Para exportar mais de um recurso, passe os IDs de recursos em uma matriz.

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

Ao exportar o modelo, você pode especificar se os parâmetros são usados no modelo. Por padrão, os parâmetros para nomes de recursos estão incluídos, mas eles não têm um valor padrão. Você deve passar esse valor de parâmetro durante a implantação.

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

Se você `-IncludeParameterDefaultValue` usar o parâmetro ao exportar o modelo, o parâmetro modelo incluirá um valor padrão definido para o valor atual. Você pode usar esse valor padrão ou substituir o valor padrão passando em um valor diferente.

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

Se você `-SkipResourceNameParameterization` usar o parâmetro ao exportar o modelo, os parâmetros para nomes de recursos não serão incluídos no modelo. Em vez disso, o nome do recurso é definido diretamente no recurso para o seu valor atual. Você não pode personalizar o nome durante a implantação.

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

O recurso modelo de exportação não suporta a exportação de recursos da Fábrica de Dados do Azure. Para saber como você pode exportar recursos da Fábrica de Dados, consulte [Copiar ou clonar uma fábrica de dados na Fábrica de Dados do Azure](https://aka.ms/exportTemplateViaAdf).

Para exportar recursos criados através de um modelo clássico de implantação, você deve [migrá-los para o modelo de implantação do Gerenciador de recursos](https://aka.ms/migrateclassicresourcetoarm).

Para obter mais informações, consulte [Exportação única e multi-recursos para modelo no portal Azure](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Gerencie o acesso a grupos de recursos

O [Controle de acesso baseado em função (RBAC)](../../role-based-access-control/overview.md) é a maneira de gerenciar o acesso aos recursos no Azure. Para obter mais informações, consulte [Gerenciar o acesso usando o RBAC e o Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Próximas etapas

- Para aprender o Azure Resource Manager, consulte [a visão geral do Azure Resource Manager](overview.md).
- Para aprender a sintaxe do modelo Resource Manager, consulte [Entenda a estrutura e a sintaxe dos modelos do Azure Resource Manager](../templates/template-syntax.md).
- Para saber como desenvolver modelos, consulte os [tutoriais passo a passo](/azure/azure-resource-manager/).
- Para exibir os esquemas de modelo do Azure Resource Manager, consulte [referência de modelo](/azure/templates/).