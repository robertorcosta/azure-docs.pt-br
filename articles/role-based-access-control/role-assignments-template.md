---
title: Adicionar atribuições de função com modelos RBAC e Azure Resource Manager
description: Saiba como conceder acesso aos recursos do Azure para usuários, grupos, diretores de serviçoou identidades gerenciadas usando os modelos RBAC (Role-Based Access Control, controle de acesso baseado em função do Azure) e do Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9f817880f938f5d03024e3aacd9b84817a5ac721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77138294"
---
# <a name="add-role-assignments-using-azure-rbac-and-azure-resource-manager-templates"></a>Adicionar atribuições de função usando modelos Azure RBAC e Azure Resource Manager

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Além de usar o Azure PowerShell ou o Azure CLI, você pode atribuir funções usando [modelos do Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). Os modelos poderão ser úteis se você precisar implantar recursos de maneira consistente e repetida. Este artigo descreve como atribuir funções usando modelos.

## <a name="get-object-ids"></a>Obtenha iDs de objeto

Para atribuir uma função, você precisa especificar o ID do usuário, grupo ou aplicativo ao qual deseja atribuir a função. O ID tem `11111111-1111-1111-1111-111111111111`o formato: . Você pode obter o ID usando o portal Azure, Azure PowerShell ou Azure CLI.

### <a name="user"></a>Usuário

Para obter o ID de um usuário, você pode usar os comandos [Get-AzADUser](/powershell/module/az.resources/get-azaduser) ou [az ad user show.](/cli/azure/ad/user#az-ad-user-show)

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>Agrupar

Para obter o ID de um grupo, você pode usar os comandos [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) ou [az ad group show.](/cli/azure/ad/group#az-ad-group-show)

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="application"></a>Aplicativo

Para obter o ID de um principal de serviço (identidade usada por um aplicativo), você pode usar os comandos [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) ou [az ad sp list.](/cli/azure/ad/sp#az-ad-sp-list) Para um diretor de serviço, use o ID do objeto e **não** o ID do aplicativo.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="add-a-role-assignment"></a>Adicionar uma atribuição de função

No RBAC, para conceder acesso, você adiciona uma atribuição de função.

### <a name="resource-group-without-parameters"></a>Grupo de recursos (sem parâmetros)

O modelo a seguir mostra uma maneira básica de adicionar uma atribuição de função. Alguns valores são especificados dentro do modelo. O modelo a seguir demonstra:

-  Como atribuir a função [Reader](built-in-roles.md#reader) a um usuário, grupo ou aplicativo em um escopo de grupo de recursos

Para usar o modelo, você deve fazer o seguinte:

- Crie um novo arquivo JSON e copie o modelo
- Substitua pelo `<your-principal-id>` ID de um usuário, grupo ou aplicativo para atribuir a função a

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(resourceGroup().id)]",
            "properties": {
                "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
                "principalId": "<your-principal-id>"
            }
        }
    ]
}
```

Aqui estão exemplos [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e [a implantação do grupo AZ criam](/cli/azure/group/deployment#az-group-deployment-create) comandos para iniciar a implantação em um grupo de recursos chamado ExampleGroup.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json
```

O seguinte mostra um exemplo da atribuição de função Leitor a um usuário para um grupo de recursos após a implantação do modelo.

![Atribuição de função no escopo do grupo de recursos](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription"></a>Grupo de recursos ou assinatura

O modelo anterior não é muito flexível. O modelo a seguir usa parâmetros e pode ser usado em diferentes escopos. O modelo a seguir demonstra:

- Como atribuir uma função a um usuário, grupo ou aplicativo em um grupo de recursos ou escopo de assinatura
- Como especificar as funções de Proprietário, Colaborador e Leitor como um parâmetro

Para usar o modelo, você deve especificar as seguintes entradas:

- O ID de um usuário, grupo ou aplicativo para atribuir a função a
- Um ID exclusivo que será usado para a atribuição de função, ou você pode usar o ID padrão

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

> [!NOTE]
> Este modelo não é impotente a `roleNameGuid` menos que o mesmo valor seja fornecido como parâmetro para cada implantação do modelo. Se `roleNameGuid` não for fornecido, por padrão um novo GUID será gerado em `Conflict: RoleAssignmentExists` cada implantação e as implantações subseqüentes falharão com um erro.

O escopo da atribuição da função é determinado a partir do nível da implantação. Aqui estão exemplos [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e [a implantação do grupo AZ criam](/cli/azure/group/deployment#az-group-deployment-create) comandos para iniciar a implantação em um escopo de grupo de recursos.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

Aqui estão exemplos [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) e [a implantação az criar](/cli/azure/deployment#az-deployment-create) comandos para iniciar a implantação em um escopo de assinatura e especificar o local.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource"></a>Recurso

Se você precisar adicionar uma atribuição de função ao nível de um recurso, o formato da atribuição de função é diferente. Você fornece o namespace do provedor de recursos e o tipo de recurso do recurso para atribuir a função. Você também inclui o nome do recurso no nome da atribuição da função.

Para o tipo e nome da atribuição da função, use o seguinte formato:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

O modelo a seguir demonstra:

- Como criar uma nova conta de armazenamento
- Como atribuir uma função a um usuário, grupo ou aplicativo no escopo da conta de armazenamento
- Como especificar as funções de Proprietário, Colaborador e Leitor como um parâmetro

Para usar o modelo, você deve especificar as seguintes entradas:

- O ID de um usuário, grupo ou aplicativo para atribuir a função a

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        },
        {
            "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[concat(variables('storageName'), '/Microsoft.Authorization/', guid(uniqueString(variables('storageName'))))]",
            "dependsOn": [
                "[variables('storageName')]"
            ],
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

Para implantar o modelo anterior, use os comandos do grupo de recursos. Aqui estão exemplos [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e [a implantação do grupo AZ criam](/cli/azure/group/deployment#az-group-deployment-create) comandos para iniciar a implantação em um escopo de recursos.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

O seguinte mostra um exemplo da atribuição de função Contribuinte para um usuário para uma conta de armazenamento depois de implantar o modelo.

![Atribuição de função no escopo de recursos](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>Novo diretor de serviços

Se você criar um novo diretor de serviço e tentar imediatamente atribuir uma função a esse diretor de serviço, essa atribuição de função pode falhar em alguns casos. Por exemplo, se você criar uma nova identidade gerenciada e tentar atribuir uma função a esse principal de serviço no mesmo modelo do Azure Resource Manager, a atribuição de função pode falhar. A razão para essa falha é provavelmente um atraso de replicação. O principal serviço é criado em uma região; no entanto, a atribuição de função pode ocorrer em uma região diferente que ainda não replicou o principal de serviço. Para resolver esse cenário, `principalType` você `ServicePrincipal` deve definir a propriedade para quando criar a atribuição de função.

O modelo a seguir demonstra:

- Como criar um novo principal de serviço de identidade gerenciado
- Como especificar o`principalType`
- Como atribuir a função contribuinte a esse principal de serviço em um escopo de grupo de recursos

Para usar o modelo, você deve especificar as seguintes entradas:

- O nome base da identidade gerenciada, ou você pode usar a seqüência padrão

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "baseName": {
            "type": "string",
            "defaultValue": "msi-test"
        }
    },
    "variables": {
        "identityName": "[concat(parameters('baseName'), '-bootstrap')]",
        "bootstrapRoleAssignmentId": "[guid(concat(resourceGroup().id, 'contributor'))]",
        "contributorRoleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]"
    },
    "resources": [
        {
            "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
            "name": "[variables('identityName')]",
            "apiVersion": "2018-11-30",
            "location": "[resourceGroup().location]"
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[variables('bootstrapRoleAssignmentId')]",
            "dependsOn": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
            ],
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionId')]",
                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName')), '2018-11-30').principalId]",
                "scope": "[resourceGroup().id]",
                "principalType": "ServicePrincipal"
            }
        }
    ]
}
```

Aqui estão exemplos [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e [a implantação do grupo AZ criam](/cli/azure/group/deployment#az-group-deployment-create) comandos para iniciar a implantação em um escopo de grupo de recursos.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup2 --template-file rbac-test.json
```

O seguinte mostra um exemplo da atribuição de função Contribuinte para um novo diretor de serviço de identidade gerenciado após a implantação do modelo.

![Atribuição de função para um novo diretor de serviço de identidade gerenciado](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>Próximas etapas

- [Início Rápido: Criar e implantar modelos do Azure Resource Manager usando o portal do Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [Entenda a estrutura e a sintaxe dos modelos do Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
- [Criar grupos de recursos e recursos em nível de assinatura](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/?term=rbac)
