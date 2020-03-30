---
title: Implantar recursos para o grupo de gestão
description: Descreve como implantar recursos no escopo do grupo de gerenciamento em um modelo de Gerenciador de Recursos do Azure.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 863d1330412fa238b820eb0f1f05351fc723de6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460306"
---
# <a name="create-resources-at-the-management-group-level"></a>Criar recursos no nível do grupo de gestão

À medida que sua organização amadurece, você pode precisar definir e atribuir [políticas](../../governance/policy/overview.md) ou [controles de acesso baseados em funções](../../role-based-access-control/overview.md) para um grupo de gerenciamento. Com os modelos de nível de grupo de gerenciamento, você pode declararmente aplicar políticas e atribuir funções no nível do grupo de gerenciamento.

## <a name="supported-resources"></a>Recursos compatíveis

Você pode implantar os seguintes tipos de recursos no nível do grupo de gerenciamento:

* [implantações](/azure/templates/microsoft.resources/deployments) - para modelos aninhados que implantam em assinaturas ou grupos de recursos.
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Esquema

O esquema que você usa para implantações de grupos de gerenciamento é diferente do esquema para implantações de grupos de recursos.

Para modelos, use:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

O esquema para um arquivo de parâmetros é o mesmo para todos os escopos de implantação. Para arquivos de parâmetros, use:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>Comandos de implantação

Os comandos para implantações de grupos de gerenciamento são diferentes dos comandos para implantações de grupos de recursos.

Para a Cli do Azure, use [aaz deployment mg criar](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create):

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Para o Azure PowerShell, use [new-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Para a API REST, use [Implantações - Crie no escopo do grupo de gerenciamento](/rest/api/resources/deployments/createorupdateatmanagementgroupscope).

## <a name="deployment-location-and-name"></a>Local e nome de implantação

Para implantações de nível de grupo de gerenciamento, você deve fornecer um local para a implantação. A localização da implantação é separada da localização dos recursos que você implanta. O local de implantação especifica onde armazenar dados de implantação.

Você pode fornecer um nome para a implantação ou usar o nome de implantação padrão. O nome padrão é o nome do arquivo de modelo. Por exemplo, implantar um modelo chamado **azuredeploy.json** cria um nome de implantação padrão de **azuredeploy**.

Para cada nome de implantação, o local é imutável. Você não pode criar uma implantação em um local quando há uma implantação existente com o mesmo nome em um local diferente. Se você receber o código de erro `InvalidDeploymentLocation`, use um nome diferente ou o mesmo local que a implantação anterior para esse nome.

## <a name="use-template-functions"></a>Usar funções de modelo

Para implantações de grupos de gerenciamento, existem algumas considerações importantes ao usar funções de modelo:

* A função [resourceGroup()](template-functions-resource.md#resourcegroup)**não** é suportada.
* A [função de assinatura ()](template-functions-resource.md#subscription) **não** é suportada.
* A funções [reference()](template-functions-resource.md#reference) e [list()](template-functions-resource.md#list) são suportadas.
* A função [resourceId()](template-functions-resource.md#resourceid) é suportada. Use-o para obter o ID de recursos para recursos usados em implantações de nível de grupo de gerenciamento. Não forneça um valor para o parâmetro do grupo de recursos.

  Por exemplo, para obter o ID de recurso para uma definição de diretiva, use:
  
  ```json
  resourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  O ID de recurso retornado tem o seguinte formato:
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-policies"></a>Criar políticas

### <a name="define-policy"></a>Definir política

O exemplo a seguir mostra como [definir](../../governance/policy/concepts/definition-structure.md) uma política no nível do grupo de gestão.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
      "properties": {
        "policyType": "Custom",
        "parameters": {},
        "policyRule": {
          "if": {
            "field": "location",
            "equals": "northeurope"
          },
          "then": {
            "effect": "deny"
          }
        }
      }
    }
  ]
}
```

### <a name="assign-policy"></a>Atribuir política

O exemplo a seguir atribui uma definição de política existente ao grupo de gerenciamento. Se a política usa parâmetros, você deve fornecê-los como um objeto. Se a política não aceita parâmetros, use o objeto vazio padrão.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyDefinitionID": {
      "type": "string"
    },
    "policyName": {
      "type": "string"
    },
    "policyParameters": {
      "type": "object",
      "defaultValue": {}
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
      "properties": {
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

## <a name="template-sample"></a>Amostra de modelo

* [Crie um grupo de recursos, uma política e uma atribuição de políticas.](https://github.com/Azure/azure-docs-json-samples/blob/master/management-level-deployment/azuredeploy.json)

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre comoatribuir funções, consulte [Gerenciar o acesso aos recursos do Azure usando modelos RBAC e Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Para obter um exemplo de implantação de configurações de workspace para a Central de Segurança do Azure, consulte [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Você também pode implantar modelos no [nível de assinatura](deploy-to-subscription.md) e nível de [inquilino](deploy-to-tenant.md).
