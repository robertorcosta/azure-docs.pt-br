---
title: Implantar recursos no locatário
description: Descreve como implantar recursos no escopo do locatário em um modelo de Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: aa72116c3e6e98293b28b2d4413fd1dafb1372d9
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942735"
---
# <a name="create-resources-at-the-tenant-level"></a>Criar recursos no nível do locatário

À medida que sua organização amadureceu, talvez seja necessário definir e atribuir [políticas](../../governance/policy/overview.md) ou [controles de acesso baseados em função](../../role-based-access-control/overview.md) em seu locatário do Azure AD. Com os modelos de nível de locatário, você pode aplicar políticas declarativamente e atribuir funções em um nível global.

## <a name="supported-resources"></a>Recursos compatíveis

Você pode implantar os seguintes tipos de recursos no nível do locatário:

* [implantações](/azure/templates/microsoft.resources/deployments) – para modelos aninhados que são implantados em grupos de gerenciamento ou assinaturas.
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Esquema

O esquema usado para implantações de locatário é diferente do esquema para implantações de grupo de recursos.

Para modelos, use:

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

O esquema para um arquivo de parâmetro é o mesmo para todos os escopos de implantação. Para arquivos de parâmetro, use:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="required-access"></a>Acesso necessário

A entidade de segurança que está implantando o modelo deve ter permissões para criar recursos no escopo do locatário. A entidade de segurança deve ter permissão para executar as ações de implantação (`Microsoft.Resources/deployments/*`) e para criar os recursos definidos no modelo. Por exemplo, para criar um grupo de gerenciamento, a entidade de segurança deve ter permissão de colaborador no escopo do locatário. Para criar atribuições de função, a entidade de segurança deve ter permissão de proprietário.

O administrador global do Azure Active Directory não tem automaticamente permissão para atribuir funções. Para habilitar implantações de modelo no escopo do locatário, o administrador global deve executar as seguintes etapas:

1. Eleve o acesso à conta para que o administrador global possa atribuir funções. Para obter mais informações, consulte [elevar o acesso para gerenciar todas as assinaturas e grupos de gerenciamento do Azure](../../role-based-access-control/elevate-access-global-admin.md).

1. Atribua o proprietário ou colaborador à entidade de segurança que precisa implantar os modelos.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

O principal agora tem as permissões necessárias para implantar o modelo.

## <a name="deployment-commands"></a>Comandos de implantação

Os comandos para implantações de locatário são diferentes dos comandos para implantações de grupo de recursos.

Para Azure PowerShell, use [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Location "West US" `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json
```

Para a API REST, use [implantações-criar ou atualizar no escopo do locatário](/rest/api/resources/deployments/createorupdateattenantscope).

## <a name="deployment-location-and-name"></a>Local e nome da implantação

Para implantações em nível de locatário, você deve fornecer um local para a implantação. O local da implantação é separado do local dos recursos que você implanta. O local de implantação especifica onde armazenar os dados de implantação.

Você pode fornecer um nome para a implantação ou usar o nome de implantação padrão. O nome padrão é o nome do arquivo de modelo. Por exemplo, implantar um modelo chamado **azuredeploy.json** cria um nome de implantação padrão de **azuredeploy**.

Para cada nome de implantação, o local é imutável. Não é possível criar uma implantação em um local quando há uma implantação existente com o mesmo nome em um local diferente. Se você receber o código de erro `InvalidDeploymentLocation`, use um nome diferente ou o mesmo local que a implantação anterior para esse nome.

## <a name="use-template-functions"></a>Usar funções de modelo

Para implantações de locatários, há algumas considerações importantes ao usar funções de modelo:

* A função [resourceGroup()](template-functions-resource.md#resourcegroup)**não** é suportada.
* **Não** há suporte para a função [Subscription ()](template-functions-resource.md#subscription) .
* A funções [reference()](template-functions-resource.md#reference) e [list()](template-functions-resource.md#list) são suportadas.
* Use a função [tenantResourceId ()](template-functions-resource.md#tenantresourceid) para obter a ID do recurso para os recursos que são implantados no nível do locatário.

  Por exemplo, para obter a ID de recurso para uma definição de política, use:
  
  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  A ID de recurso retornada tem o seguinte formato:
  
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

O [modelo a seguir](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/tenant-role-assignment) atribui uma função no escopo do locatário.

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

* Para saber mais sobre como atribuir funções, consulte [gerenciar o acesso aos recursos do Azure usando os modelos RBAC e Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Você também pode implantar modelos no nível da [assinatura](deploy-to-subscription.md) ou do [grupo de gerenciamento](deploy-to-management-group.md).
