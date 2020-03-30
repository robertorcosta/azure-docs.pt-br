---
title: Gerenciar grupos de recursos - Azure CLI
description: Use o Azure CLI para gerenciar seus grupos de recursos através do Azure Resource Manager. Mostra como criar, listar e excluir grupos de recursos.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 7face572f545153ea92efbdb345bbaabda5dd126
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248327"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Gerencie grupos de recursos do Azure Resource Manager usando o Azure CLI

Aprenda a usar o Azure CLI com [o Azure Resource Manager](overview.md) para gerenciar seus grupos de recursos do Azure. Para gerenciar os recursos do Azure, consulte [Gerenciar os recursos do Azure usando o Azure CLI](manage-resources-cli.md).

Outros artigos sobre o gerenciamento de grupos de recursos:

- [Gerencie grupos de recursos do Azure usando o portal Azure](manage-resources-portal.md)
- [Gerencie grupos de recursos do Azure usando o Azure PowerShell](manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>O que é um grupo de recursos

Um grupo de recursos é um contêiner que mantém os recursos relacionados a uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que você deseja gerenciar como um grupo. Você decide como deseja alocar recursos para grupos de recursos com base no que faz mais sentido para sua organização. Em geral, adicione recursos que compartilham o mesmo ciclo de vida no mesmo grupo de recursos, para que você possa implantar, atualizar e excluí-los como um grupo facilmente.

O grupo de recursos armazena metadados sobre os recursos. Portanto, quando você especifica um local para o grupo de recursos, especifica onde os metadados são armazenados. Por motivos de conformidade, você precisa fazer com que os dados sejam armazenados em determinada região.

O grupo de recursos armazena metadados sobre os recursos. Quando você especifica uma localização para o grupo de recursos, você especifica onde os metadados são armazenados.

## <a name="create-resource-groups"></a>Criar grupos de recursos

O script CLI a seguir cria um grupo de recursos e, em seguida, mostra o grupo de recursos.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location $location
```

## <a name="list-resource-groups"></a>Listar os grupos de recursos

O script CLI a seguir lista os grupos de recursos sua assinatura.

```azurecli-interactive
az group list
```

Para obter um grupo de recursos:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Excluir grupos de recursos

O script CLI a seguir exclui um grupo de recursos:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Para obter mais informações sobre como o Azure Resource Manager ordena a exclusão de recursos, consulte a exclusão do grupo de [recursos do Azure Resource Manager](delete-resource-group.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Implantar recursos para um grupo de recursos existente

Consulte [Implantar recursos em um grupo de recursos existente](manage-resources-cli.md#deploy-resources-to-an-existing-resource-group).

## <a name="deploy-a-resource-group-and-resources"></a>Implantar um grupo de recursos e recursos

Você pode criar um grupo de recursos e implantar recursos para o grupo usando um modelo de Gerenciador de recursos. Para saber mais, confira [Create resource group and deploy resources](../templates/deploy-to-subscription.md#resource-group-and-resources) (Criar grupo de recursos e implantar recursos).

## <a name="redeploy-when-deployment-fails"></a>Reimplantar quando ocorrer falha na implantação

Esse recurso também é conhecido como *Reversão de erro*. Para obter mais informações, consulte [Reimplantar quando a implantação falhar](../templates/rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Mova-se para outro grupo de recursos ou assinatura

Você pode transferir os recursos do grupo para outro grupo de recursos. Para obter mais informações, consulte [Mover recursos](manage-resources-cli.md#move-resources).

## <a name="lock-resource-groups"></a>Grupos de recursos de bloqueio

O bloqueio impede que outros usuários da sua organização excluam acidentalmente ou modifiquem recursos críticos, como assinatura do Azure, grupo de recursos ou recurso. 

O script a seguir bloqueia um grupo de recursos para que o grupo de recursos não possa ser excluído.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

O script a seguir recebe todas as fechaduras para um grupo de recursos:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName  
```

O script a seguir exclui um bloqueio:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](lock-resources.md).

## <a name="tag-resource-groups"></a>Grupos de recursos de tag

Você pode aplicar marcas a recursos e grupos de recursos para organizar seus ativos de modo lógico. Para obter informações, consulte [Usando tags para organizar seus recursos do Azure](tag-resources.md#azure-cli).

## <a name="export-resource-groups-to-templates"></a>Exportar grupos de recursos para modelos

Depois de configurar seu grupo de recursos com sucesso, você pode querer visualizar o modelo de Gerenciador de recursos para o grupo de recursos. A exportação do modelo oferece dois benefícios:

- Automatize futuras implantações da solução porque o modelo contém toda a infra-estrutura completa.
- Aprenda a sintaxe do modelo olhando para a Notação de Objeto JavaScript (JSON) que representa sua solução.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

O script exibe o modelo no console.  Copie o JSON e salve como um arquivo.

O recurso modelo de exportação não suporta a exportação de recursos da Fábrica de Dados do Azure. Para saber como você pode exportar recursos da Fábrica de Dados, consulte [Copiar ou clonar uma fábrica de dados na Fábrica de Dados do Azure](https://aka.ms/exportTemplateViaAdf).

Para exportar recursos criados através de um modelo clássico de implantação, você deve [migrá-los para o modelo de implantação do Gerenciador de recursos](https://aka.ms/migrateclassicresourcetoarm).

Para obter mais informações, consulte [Exportação única e multi-recursos para modelo no portal Azure](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Gerencie o acesso a grupos de recursos

O [Controle de acesso baseado em função (RBAC)](../../role-based-access-control/overview.md) é a maneira de gerenciar o acesso aos recursos no Azure. Para obter mais informações, consulte [Gerenciar o acesso usando o RBAC e o Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Próximas etapas

- Para aprender o Azure Resource Manager, consulte [a visão geral do Azure Resource Manager](overview.md).
- Para aprender a sintaxe do modelo Resource Manager, consulte [Entenda a estrutura e a sintaxe dos modelos do Azure Resource Manager](../templates/template-syntax.md).
- Para saber como desenvolver modelos, consulte os [tutoriais passo a passo](/azure/azure-resource-manager/).
- Para exibir os esquemas de modelo do Azure Resource Manager, consulte [referência de modelo](/azure/templates/).