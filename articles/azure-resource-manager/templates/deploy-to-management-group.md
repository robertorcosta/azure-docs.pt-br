---
title: Implantar recursos no grupo de gerenciamento
description: Descreve como implantar recursos no escopo do grupo de gerenciamento em um modelo de Azure Resource Manager.
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: 992882859ed1c67cf66c31f69f21e151081cf087
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88002895"
---
# <a name="create-resources-at-the-management-group-level"></a>Criar recursos no nível do grupo de gerenciamento

À medida que sua organização amadureceu, você pode implantar um modelo de Azure Resource Manager (modelo ARM) para criar recursos no nível do grupo de gerenciamento. Por exemplo, talvez seja necessário definir e atribuir [políticas](../../governance/policy/overview.md) ou [controle de acesso baseado em função do Azure (RBAC do Azure)](../../role-based-access-control/overview.md) para um grupo de gerenciamento. Com os modelos de nível de grupo de gerenciamento, você pode aplicar políticas declarativamente e atribuir funções no nível do grupo de gerenciamento.

## <a name="supported-resources"></a>Recursos compatíveis

Nem todos os tipos de recursos podem ser implantados no nível do grupo de gerenciamento. Esta seção lista os tipos de recursos com suporte.

Para plantas do Azure, use:

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versões](/azure/templates/microsoft.blueprint/blueprints/versions)

Para políticas do Azure, use:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

Para o controle de acesso baseado em função, use:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Para modelos aninhados que são implantados em assinaturas ou grupos de recursos, use:

* [implantações](/azure/templates/microsoft.resources/deployments)

Para gerenciar seus recursos, use:

* [marcas](/azure/templates/microsoft.resources/tags)

### <a name="schema"></a>Esquema

O esquema usado para implantações de grupo de gerenciamento é diferente do esquema para implantações de grupo de recursos.

Para modelos, use:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

O esquema para um arquivo de parâmetro é o mesmo para todos os escopos de implantação. Para arquivos de parâmetros, use:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>Comandos de implantação

Os comandos para implantações de grupo de gerenciamento são diferentes dos comandos para implantações de grupo de recursos.

Para CLI do Azure, use [AZ Deployment mg Create](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create):

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Para Azure PowerShell, use [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Para a API REST, use [implantações-criar no escopo do grupo de gerenciamento](/rest/api/resources/deployments/createorupdateatmanagementgroupscope).

## <a name="deployment-location-and-name"></a>Nome e local da implantação

Para implantações no nível do grupo de gerenciamento, você deve fornecer um local para a implantação. O local da implantação é separado do local dos recursos que você implanta. O local de implantação especifica onde armazenar os dados de implantação.

Você pode fornecer um nome da implantação ou usar o nome da implantação padrão. O nome padrão é o nome do arquivo de modelo. Por exemplo, implantar um modelo chamado **azuredeploy.json** cria um nome de implantação padrão de **azuredeploy**.

O local não pode ser alterado para cada nome de implantação. Você não pode criar uma implantação em um local quando há uma implantação existente com o mesmo nome em um local diferente. Se você receber o código de erro `InvalidDeploymentLocation`, use um nome diferente ou o mesmo local que a implantação anterior para esse nome.

## <a name="deployment-scopes"></a>Escopos de implantação

Ao implantar em um grupo de gerenciamento, você pode direcionar o grupo de gerenciamento especificado no comando de implantação ou outros grupos de gerenciamento no locatário. Você também pode direcionar assinaturas ou grupos de recursos dentro de um grupo de gerenciamento. O usuário que está implantando o modelo deve ter acesso ao escopo especificado.

Os recursos definidos na seção de recursos do modelo são aplicados ao grupo de gerenciamento do comando de implantação.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        management-group-level-resources
    ],
    "outputs": {}
}
```

Para direcionar outro grupo de gerenciamento, adicione uma implantação aninhada e especifique a `scope` propriedade.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
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
            "apiVersion": "2019-10-01",
            "name": "nestedDeployment",
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

Para direcionar uma assinatura dentro do grupo de gerenciamento, use uma implantação aninhada e a `subscriptionId` propriedade. Para direcionar um grupo de recursos dentro dessa assinatura, adicione outra implantação aninhada e a `resourceGroup` propriedade.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "nestedSub",
      "location": "westus2",
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2020-06-01",
              "name": "nestedRG",
              "resourceGroup": "rg2",
              "properties": {
                "mode": "Incremental",
                "template": {
                  nested-template
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="use-template-functions"></a>Usar funções de modelo

Para implantações de grupo de gerenciamento, há algumas considerações importantes ao usar funções de modelo:

* A função [resourceGroup()](template-functions-resource.md#resourcegroup)**não** é suportada.
* A função [subscription()](template-functions-resource.md#subscription) **não** tem suporte.
* A funções [reference()](template-functions-resource.md#reference) e [list()](template-functions-resource.md#list) são suportadas.
* A função [resourceId()](template-functions-resource.md#resourceid) é suportada. Use-o para obter a ID de recurso para recursos que são usados em implantações no nível do grupo de gerenciamento. Não forneça um valor para o parâmetro do grupo de recursos.

  Por exemplo, para obter a ID do recurso para uma definição de política, use:
  
  ```json
  resourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  A ID do recurso retornada tem o seguinte formato:
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="azure-policy"></a>Azure Policy

### <a name="define-policy"></a>Definir política

O exemplo a seguir mostra como [definir](../../governance/policy/concepts/definition-structure.md) uma política no nível do grupo de gerenciamento.

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

## <a name="deploy-to-subscription-and-resource-group"></a>Implantar na assinatura e no grupo de recursos

De uma implantação em nível de grupo de gerenciamento, você pode direcionar uma assinatura dentro do grupo de gerenciamento. O exemplo a seguir cria um grupo de recursos em uma assinatura e implanta uma conta de armazenamento para esse grupo de recursos.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nestedsubId": {
      "type": "string"
    },
    "nestedRG": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "nestedLocation": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "nestedSub",
      "location": "[parameters('nestedLocation')]",
      "subscriptionId": "[parameters('nestedSubId')]",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
          },
          "variables": {
          },
          "resources": [
            {
              "type": "Microsoft.Resources/resourceGroups",
              "apiVersion": "2020-06-01",
              "name": "[parameters('nestedRG')]",
              "location": "[parameters('nestedLocation')]",
            },
            {
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2020-06-01",
              "name": "nestedSubRG",
              "resourceGroup": "[parameters('nestedRG')]",
              "dependsOn": [
                "[parameters('nestedRG')]"
              ],
              "properties": {
                "mode": "Incremental",
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[parameters('storageAccountName')]",
                      "location": "[parameters('nestedLocation')]",
                      "sku": {
                        "name": "Standard_LRS"
                      }
                    }
                  ]
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como atribuir funções, confira [Adicionar atribuições de função do Azure usando modelos de Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Para obter um exemplo de implantação de configurações de workspace para a Central de Segurança do Azure, consulte [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Você também pode implantar modelos no nível de [assinatura](deploy-to-subscription.md) e [nível de locatário](deploy-to-tenant.md).
