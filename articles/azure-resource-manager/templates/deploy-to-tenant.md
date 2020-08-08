---
title: Implantar recursos no locatário
description: Descreve como implantar recursos no escopo do locatário em um modelo de Azure Resource Manager.
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 2f5249eb54a62e4df082a18b22625bb93a0f09f8
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88002764"
---
# <a name="create-resources-at-the-tenant-level"></a>Criar recursos no nível do locatário

À medida que sua organização amadureceu, talvez seja necessário definir e atribuir [políticas](../../governance/policy/overview.md) ou o [controle de acesso baseado em função do Azure (RBAC do Azure)](../../role-based-access-control/overview.md) em seu locatário do Azure AD. Com os modelos de nível de locatário, você pode aplicar políticas declarativamente e atribuir funções em um nível global.

## <a name="supported-resources"></a>Recursos compatíveis

Nem todos os tipos de recursos podem ser implantados no nível de locatário. Esta seção lista os tipos de recursos com suporte.

Para políticas do Azure, use:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)

Para o controle de acesso baseado em função, use:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)

Para modelos aninhados que são implantados em grupos de gerenciamento, assinaturas ou grupos de recursos, use:

* [implantações](/azure/templates/microsoft.resources/deployments)

Para criar grupos de gerenciamento, use:

* [managementGroups](/azure/templates/microsoft.management/managementgroups)

Para gerenciar custos, use:

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [sobre](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [invoiceSections](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

### <a name="schema"></a>Esquema

O esquema que você pode usar para implantações de locatário são diferentes dos esquemas para implantações do grupo de recursos.

Para modelos, use:

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

O esquema para um arquivo de parâmetro é o mesmo para todos os escopos de implantação. Para arquivos de parâmetros, use:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="required-access"></a>Acesso necessário

A entidade de segurança que está implantando o modelo deve ter permissões para criar recursos no escopo do locatário. A entidade de segurança deve ter permissão para executar as ações de implantação (`Microsoft.Resources/deployments/*`) e para criar os recursos definidos no modelo. Por exemplo, para criar um grupo de gerenciamento, a entidade de segurança deve ter permissão de colaborador no escopo do locatário. Para criar atribuições de função, a entidade de segurança deve ter permissão de proprietário.

O administrador global do Microsoft Azure Active Directory não tem automaticamente permissão para atribuir funções. Para habilitar implantações de modelo no escopo do locatário, o administrador global deve executar as seguintes etapas:

1. Eleve o acesso à conta para que o administrador global possa atribuir funções. Para obter mais informações, confira [Elevar o acesso para gerenciar todas as assinaturas e grupos de gerenciamento do Azure](../../role-based-access-control/elevate-access-global-admin.md).

1. Atribua o proprietário ou colaborador à entidade de segurança que precisa implantar os modelos.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

O principal agora tem as permissões necessárias para implantar o modelo.

## <a name="deployment-commands"></a>Comandos de implantação

Os comandos para implantações de locatário são diferentes dos comandos para implantações do grupo de recursos.

Para CLI do Azure, use [az deployment tenant create](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create):

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

Para Azure PowerShell, use [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

Para REST API, use [Implantações – Criar ou atualizar no escopo do locatário](/rest/api/resources/deployments/createorupdateattenantscope).

## <a name="deployment-location-and-name"></a>Nome e local da implantação

Para implantações no nível do locatário, você deve fornecer um local para a implantação. O local da implantação é separado do local dos recursos que você implanta. O local de implantação especifica onde armazenar os dados de implantação.

Você pode fornecer um nome da implantação ou usar o nome da implantação padrão. O nome padrão é o nome do arquivo de modelo. Por exemplo, implantar um modelo chamado **azuredeploy.json** cria um nome de implantação padrão de **azuredeploy**.

O local não pode ser alterado para cada nome de implantação. Você não pode criar uma implantação em um local quando há uma implantação existente com o mesmo nome em um local diferente. Se você receber o código de erro `InvalidDeploymentLocation`, use um nome diferente ou o mesmo local que a implantação anterior para esse nome.

## <a name="deployment-scopes"></a>Escopos de implantação

Ao implantar em um locatário, você pode direcionar o locatário ou grupos de gerenciamento, assinaturas e grupos de recursos no locatário. O usuário que está implantando o modelo deve ter acesso ao escopo especificado.

Os recursos definidos na seção de recursos do modelo são aplicados ao locatário.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        tenant-level-resources
    ],
    "outputs": {}
}
```

Para direcionar um grupo de gerenciamento dentro do locatário, adicione uma implantação aninhada e especifique a `scope` propriedade.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mgName": {
            "type": "string"
        }
    },
    "variables": {
        "mgId": "[concat('Microsoft.Management/managementGroups/', parameters('mgName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "nestedMG",
            "scope": "[variables('mgId')]",
            "location": "eastus",
            "properties": {
                "mode": "Incremental",
                "template": {
                    nested-template
                }
            }
        }
    ],
    "outputs": {}
}
```

## <a name="use-template-functions"></a>Usar funções de modelo

Para implantações de locatário, há algumas considerações importantes ao usar funções de modelo:

* A função [resourceGroup()](template-functions-resource.md#resourcegroup)**não** é suportada.
* A função [subscription()](template-functions-resource.md#subscription) **não** tem suporte.
* A funções [reference()](template-functions-resource.md#reference) e [list()](template-functions-resource.md#list) são suportadas.
* Use a função [tenantResourceId()](template-functions-resource.md#tenantresourceid) para obter a ID do recurso para os recursos que são implantados no nível do locatário.

  Por exemplo, para obter a ID do recurso para uma definição de política, use:

  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```

  A ID do recurso retornada tem o seguinte formato:

  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-management-group"></a>Criar grupo de gerenciamento

O [modelo a seguir](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-deployments/new-mg) cria um grupo de recursos vazio.

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

O [modelo a seguir](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-deployments/tenant-role-assignment) atribui uma função no escopo do locatário.

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

* Para saber mais sobre como atribuir funções, confira [Adicionar atribuições de função do Azure usando modelos de Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Você também pode implantar modelos no [nível de assinatura](deploy-to-subscription.md) e [nível do grupo de gerenciamento](deploy-to-management-group.md).
