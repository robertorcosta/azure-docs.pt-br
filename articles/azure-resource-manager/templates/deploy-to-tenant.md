---
title: Implantar recursos para o inquilino
description: Descreve como implantar recursos no escopo do inquilino em um modelo do Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: fcdfc5b1c4333a0d7eeec80a09ad85579a1f8b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460255"
---
# <a name="create-resources-at-the-tenant-level"></a>Criar recursos no nível do inquilino

À medida que sua organização amadurece, você pode precisar definir e atribuir [políticas](../../governance/policy/overview.md) ou [controles de acesso baseados em função](../../role-based-access-control/overview.md) em seu inquilino Azure AD. Com modelos de nível de inquilino, você pode declararmente aplicar políticas e atribuir funções em nível global.

## <a name="supported-resources"></a>Recursos compatíveis

Você pode implantar os seguintes tipos de recursos no nível do inquilino:

* [implantações](/azure/templates/microsoft.resources/deployments) - para modelos aninhados que são implantados em grupos de gerenciamento ou assinaturas.
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Esquema

O esquema que você usa para implantações de inquilinos é diferente do esquema para implantações de grupos de recursos.

Para modelos, use:

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

O esquema para um arquivo de parâmetros é o mesmo para todos os escopos de implantação. Para arquivos de parâmetros, use:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="required-access"></a>Acesso necessário

O principal que implanta o modelo deve ter permissões para criar recursos no escopo do inquilino. O principal deve ter permissão`Microsoft.Resources/deployments/*`para executar as ações de implantação ( ) e criar os recursos definidos no modelo. Por exemplo, para criar um grupo de gerenciamento, o principal deve ter permissão do Contribuinte no escopo do inquilino. Para criar atribuições de função, o principal deve ter a permissão do Proprietário.

O administrador global do Diretório Ativo do Azure não tem automaticamente permissão para atribuir funções. Para habilitar implantações de modelo no escopo do inquilino, o Administrador Global deve fazer as seguintes etapas:

1. Eleve o acesso à conta para que o Administrador Global possa atribuir funções. Para obter mais informações, consulte [O acesso de Elevate para gerenciar todas as assinaturas e grupos de gerenciamento do Azure.](../../role-based-access-control/elevate-access-global-admin.md)

1. Atribuir proprietário ou contribuinte ao principal que precisa implantar os modelos.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

O principal agora tem as permissões necessárias para implantar o modelo.

## <a name="deployment-commands"></a>Comandos de implantação

Os comandos para implantações de inquilinos são diferentes dos comandos para implantações de grupos de recursos.

Para a Cli do Azure, use [acriação de inquilino de implantação az](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create):

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

Para o Azure PowerShell, use [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

Para a API REST, use [Implantações - Crie ou Atualize no escopo do inquilino](/rest/api/resources/deployments/createorupdateattenantscope).

## <a name="deployment-location-and-name"></a>Local e nome de implantação

Para implantações de nível de inquilino, você deve fornecer um local para a implantação. A localização da implantação é separada da localização dos recursos que você implanta. O local de implantação especifica onde armazenar dados de implantação.

Você pode fornecer um nome para a implantação ou usar o nome de implantação padrão. O nome padrão é o nome do arquivo de modelo. Por exemplo, implantar um modelo chamado **azuredeploy.json** cria um nome de implantação padrão de **azuredeploy**.

Para cada nome de implantação, o local é imutável. Você não pode criar uma implantação em um local quando há uma implantação existente com o mesmo nome em um local diferente. Se você receber o código de erro `InvalidDeploymentLocation`, use um nome diferente ou o mesmo local que a implantação anterior para esse nome.

## <a name="use-template-functions"></a>Usar funções de modelo

Para implantações de inquilinos, existem algumas considerações importantes ao usar funções de modelo:

* A função [resourceGroup()](template-functions-resource.md#resourcegroup)**não** é suportada.
* A [função de assinatura ()](template-functions-resource.md#subscription) **não** é suportada.
* A funções [reference()](template-functions-resource.md#reference) e [list()](template-functions-resource.md#list) são suportadas.
* Use a função [tenantResourceId()](template-functions-resource.md#tenantresourceid) para obter o ID de recursos para recursos que são implantados no nível do inquilino.

  Por exemplo, para obter o ID de recurso para uma definição de diretiva, use:
  
  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  O ID de recurso retornado tem o seguinte formato:
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-management-group"></a>Criar grupo de gerenciamento

O [modelo a seguir](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/new-mg) cria um grupo de gerenciamento.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "mgName": {
      "type": "string",
      "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Management/managementGroups",
      "apiVersion": "2019-11-01",
      "name": "[parameters('mgName')]",
      "properties": {
      }
    }
  ]
}
```

## <a name="assign-role"></a>Atribuir função

O [modelo a](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/tenant-role-assignment) seguir atribui uma função no escopo do inquilino.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "principalId if the user that will be given contributor access to the resourceGroup"
      }
    },
    "roleDefinitionId": {
      "type": "string",
      "defaultValue": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
      "metadata": {
        "description": "roleDefinition for the assignment - default is owner"
      }
    }
  },
  "variables": {
    // This creates an idempotent guid for the role assignment
    "roleAssignmentName": "[guid('/', parameters('principalId'), parameters('roleDefinitionId'))]"
  },
  "resources": [
    {
      "name": "[variables('roleAssignmentName')]",
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2019-04-01-preview",
      "properties": {
        "roleDefinitionId": "[tenantResourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "/"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre comoatribuir funções, consulte [Gerenciar o acesso aos recursos do Azure usando modelos RBAC e Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Você também pode implantar modelos no [nível de assinatura](deploy-to-subscription.md) ou nível de grupo de [gerenciamento](deploy-to-management-group.md).
