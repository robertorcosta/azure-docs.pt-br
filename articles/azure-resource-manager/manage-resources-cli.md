---
title: Gerenciar recursos do Azure usando o CLI do Azure | Microsoft Docs
description: Use CLI do Azure e o gerenciamento de recursos do Azure para gerenciar seus recursos.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 78dd51c023bc46ed09219acc3df7e4ee7006ac2b
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001556"
---
# <a name="manage-azure-resources-by-using-azure-cli"></a>Gerenciar recursos do Azure usando o CLI do Azure

Saiba como usar CLI do Azure com [Azure Resource Manager](resource-group-overview.md) para gerenciar os recursos do Azure. Para gerenciar grupos de recursos, consulte [gerenciar grupos de recursos do Azure usando CLI do Azure](./manage-resource-groups-cli.md).

Outros artigos sobre o gerenciamento de recursos:

- [Gerenciar recursos do Azure usando o portal do Azure](./manage-resources-portal.md)
- [Gerenciar recursos do Azure usando o Azure PowerShell](./manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Implantar recursos em um grupo de recursos existente

Você pode implantar recursos do Azure diretamente usando Azure PowerShell ou implantar um modelo do Resource Manager para criar recursos do Azure.

### <a name="deploy-a-resource"></a>Implantar um recurso

O script a seguir cria uma conta de armazenamento.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account create --resource-group $resourceGroupName --name $storageAccountName --location $location --sku Standard_LRS --kind StorageV2 &&
az storage account show --resource-group $resourceGroupName --name $storageAccountName 
```

### <a name="deploy-a-template"></a>Implantar um modelo

O script a seguir cria uma implantação de um modelo de início rápido para criar uma conta de armazenamento. Para obter mais informações, confira [Início Rápido: Criar modelos do Azure Resource Manager usando o Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group deployment create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Para obter mais informações, veja [Implantar recursos com modelos do Resource Manager e a CLI do Azure](./resource-group-template-deploy-cli.md).

## <a name="deploy-a-resource-group-and-resources"></a>Implantar um grupo de recursos e recursos

Você pode criar um grupo de recursos e implantar recursos no grupo. Para saber mais, confira [Create resource group and deploy resources](./deploy-to-subscription.md#resource-group-and-resources) (Criar grupo de recursos e implantar recursos).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Implantar recursos em várias assinaturas ou grupos de recursos

Normalmente, você deve implantar todos os recursos em seu modelo em um único grupo de recursos. No entanto, há cenários em que você deseja implantar um conjunto de recursos de uma vez, mas colocá-los em diferentes grupos de recursos ou assinaturas. Para obter mais informações, consulte [implantar recursos do Azure em várias assinaturas ou grupos de recursos](./resource-manager-cross-resource-group-deployment.md).

## <a name="delete-resources"></a>Excluir recursos

O script a seguir mostra como excluir uma conta de armazenamento.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --resource-group $resourceGroupName --name $storageAccountName 
```

Para obter mais informações sobre como Azure Resource Manager ordena a exclusão de recursos, consulte [Azure Resource Manager exclusão de grupo de recursos](./resource-group-delete.md).

## <a name="move-resources"></a>Mover recursos

O script a seguir mostra como remover uma conta de armazenamento de um grupo de recursos para outro grupo de recursos.

```azurecli-interactive
echo "Enter the source Resource Group name:" &&
read srcResourceGroupName &&
echo "Enter the destination Resource Group name:" &&
read destResourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $srcResourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az resource move --destination-group $destResourceGroupName --ids $storageAccount
```

Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](resource-group-move-resources.md).

## <a name="lock-resources"></a>Bloquear recursos

O bloqueio impede que outros usuários em sua organização excluam ou modifiquem acidentalmente recursos críticos, como assinatura do Azure, grupo de recursos ou recurso. 

O script a seguir bloqueia uma conta de armazenamento para que a conta não possa ser excluída.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock create --name LockSite --lock-type CanNotDelete --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts 
```

O script a seguir obtém todos os bloqueios para uma conta de armazenamento:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock list --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --parent ""
```

O script a seguir exclui um bloqueio de uma conta de armazenamento:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
lockId=$(az lock show --name LockSite --resource-group $resourceGroupName --resource-type Microsoft.Storage/storageAccounts --resource-name $storageAccountName --output tsv --query id)&&
az lock delete --ids $lockId
```

Para saber mais, confira [Bloquear recursos com o Gerenciador de Recursos do Azure](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Recursos de marca

A marcação ajuda a organizar o grupo de recursos e os recursos logicamente. Para obter informações, consulte [usando marcas para organizar os recursos do Azure](./resource-group-using-tags.md#azure-cli).

## <a name="manage-access-to-resources"></a>Gerenciar acesso aos recursos

O [Controle de acesso baseado em função (RBAC)](../role-based-access-control/overview.md) é a maneira de gerenciar o acesso aos recursos no Azure. Para obter mais informações, consulte [gerenciar o acesso usando RBAC e CLI do Azure](../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Próximas etapas

- Para saber Azure Resource Manager, consulte [Azure Resource Manager visão geral](./resource-group-overview.md).
- Para saber mais sobre a sintaxe do modelo do Resource Manager, consulte [entender a estrutura e a sintaxe dos modelos de Azure Resource Manager](./resource-group-authoring-templates.md).
- Para saber como desenvolver modelos, consulte os tutoriais passo a [passo](/azure/azure-resource-manager/).
- Para exibir os esquemas de modelo de Azure Resource Manager, consulte [referência de modelo](/azure/templates/).
