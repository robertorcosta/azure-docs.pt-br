---
title: Implantar recursos na assinatura
description: Descreve como criar um grupo de recursos em um modelo do Azure Resource Manager. Ele também mostra como implantar recursos no escopo da assinatura do Azure.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 1a76e41b4b2264bc535752e8f765b3303080abbd
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248405"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Criar grupos de recursos e recursos em nível de assinatura

Para simplificar o gerenciamento de recursos em sua assinatura do Azure, você pode definir e atribuir [políticas](../../governance/policy/overview.md) ou [controles de acesso baseados em função](../../role-based-access-control/overview.md) em toda a assinatura. Com os modelos de nível de assinatura, você aplica declarativamente políticas e atribui funções à assinatura. Você também pode criar grupos de recursos e implantar recursos.

Para implantar modelos no nível de assinatura, use CLI do Azure, PowerShell ou API REST. O portal do Azure não dá suporte à implantação em nível de assinatura.

## <a name="supported-resources"></a>Recursos compatíveis

Você pode implantar os seguintes tipos de recursos no nível da assinatura:

* [Orçamentos](/azure/templates/microsoft.consumption/budgets)
* [implantações](/azure/templates/microsoft.resources/deployments) -para modelos aninhados que são implantados em grupos de recursos.
* [peerAsns](/azure/templates/microsoft.peering/peerasns)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Esquema

O esquema usado para implantações em nível de assinatura é diferente do esquema para implantações de grupo de recursos.

Para modelos, use:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

O esquema para um arquivo de parâmetro é o mesmo para todos os escopos de implantação. Para arquivos de parâmetro, use:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>Comandos de implantação

Os comandos para implantações em nível de assinatura são diferentes dos comandos para implantações de grupo de recursos.

Para o CLI do Azure, use [AZ Deployment Create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create). O exemplo a seguir implanta um modelo para criar um grupo de recursos:

```azurecli-interactive
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```


Para o comando de implantação do PowerShell, use [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) ou **New-AzSubscriptionDeployment**. O exemplo a seguir implanta um modelo para criar um grupo de recursos:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

Para a API REST, use [implantações-criar no escopo da assinatura](/rest/api/resources/deployments/createorupdateatsubscriptionscope).

## <a name="deployment-location-and-name"></a>Local e nome da implantação

Para implantações em nível de assinatura, você deve fornecer um local para a implantação. O local da implantação é separado do local dos recursos que você implanta. O local de implantação especifica onde armazenar os dados de implantação.

Você pode fornecer um nome para a implantação ou usar o nome de implantação padrão. O nome padrão é o nome do arquivo de modelo. Por exemplo, implantar um modelo chamado **azuredeploy.json** cria um nome de implantação padrão de **azuredeploy**.

Para cada nome de implantação, o local é imutável. Não é possível criar uma implantação em um local quando há uma implantação existente com o mesmo nome em um local diferente. Se você receber o código de erro `InvalidDeploymentLocation`, use um nome diferente ou o mesmo local que a implantação anterior para esse nome.

## <a name="use-template-functions"></a>Usar funções de modelo

Para implantações em nível de assinatura, há algumas considerações importantes ao usar funções de modelo:

* A função [resourceGroup()](template-functions-resource.md#resourcegroup)**não** é suportada.
* A funções [reference()](template-functions-resource.md#reference) e [list()](template-functions-resource.md#list) são suportadas.
* Use a função [subscriptionResourceId ()](template-functions-resource.md#subscriptionresourceid) para obter a ID de recurso para os recursos que são implantados no nível de assinatura.

  Por exemplo, para obter a ID de recurso para uma definição de política, use:
  
  ```json
  subscriptionResourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))
  ```
  
  A ID de recurso retornada tem o seguinte formato:

  ```json
  /subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-resource-groups"></a>Criar grupos de recursos

Para criar um grupo de recursos em um modelo do Azure Resource Manager, defina um recurso [Microsoft.Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) com um nome e local para o grupo de recursos. Crie um grupo de recursos e implante recursos nesse grupo de recursos no mesmo modelo.

O modelo a seguir cria um grupo de recursos vazio.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2018-05-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Use o [elemento de cópia](copy-resources.md) com grupos de recursos para criar mais de um grupo de recursos.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgNamePrefix": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "instanceCount": {
      "type": "int"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2018-05-01",
      "location": "[parameters('rgLocation')]",
      "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
      "copy": {
        "name": "rgCopy",
        "count": "[parameters('instanceCount')]"
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Para obter informações sobre a iteração de recurso, consulte [implantar mais de uma instância de um recurso em modelos de Azure Resource Manager](./copy-resources.md)e [tutorial: criar várias instâncias de recurso com modelos do Resource Manager](./template-tutorial-create-multiple-instances.md).

## <a name="resource-group-and-resources"></a>Grupo de recursos e recursos

Para criar o grupo de recursos e implantar recursos nele, use um modelo aninhado. O modelo aninhado define os recursos para implantar o grupo de recursos. Defina o modelo aninhado como dependente do grupo de recursos para verificar se o grupo de recursos existe antes de implantar os recursos.

O exemplo a seguir cria um grupo de recursos e implanta uma conta de armazenamento para o grupo de recursos.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "storagePrefix": {
      "type": "string",
      "maxLength": 11
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2018-05-01",
      "location": "[parameters('rgLocation')]",
      "name": "[parameters('rgName')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "storageDeployment",
      "resourceGroup": "[parameters('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2017-10-01",
              "name": "[variables('storageName')]",
              "location": "[parameters('rgLocation')]",
              "sku": {
                "name": "Standard_LRS"
              }
              "kind": "StorageV2",
            }
          ],
          "outputs": {}
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="create-policies"></a>Criar políticas

### <a name="assign-policy"></a>Atribuir política

O exemplo a seguir atribui uma definição de política existente para a assinatura. Se a política usa parâmetros, você deve fornecê-los como um objeto. Se a política não aceita parâmetros, use o objeto vazio padrão.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
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
        "scope": "[subscription().id]",
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

Para implantar este modelo de exemplo com a CLI do Azure, use:

```azurecli-interactive
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Para implantar este modelo com o PowerShell, use:

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzSubscriptionDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="define-and-assign-policy"></a>Definir e atribuir política

Você pode [definir](../../governance/policy/concepts/definition-structure.md) e atribuir uma política no mesmo modelo.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
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
    },
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-05-01",
      "name": "location-lock",
      "dependsOn": [
        "locationpolicy"
      ],
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
      }
    }
  ]
}
```

Para criar a definição de política em sua assinatura e aplicá-la à assinatura, use o seguinte comando CLI:

```azurecli
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

Para implantar este modelo com o PowerShell, use:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="template-samples"></a>Amostras de modelo

* [Crie um grupo de recursos, bloqueie-o e conceda permissões a ele](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment).
* [Crie um grupo de recursos, uma política e uma atribuição de política](https://github.com/Azure/azure-docs-json-samples/blob/master/subscription-level-deployment/azuredeploy.json).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como atribuir funções, consulte [gerenciar o acesso aos recursos do Azure usando os modelos RBAC e Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Para obter um exemplo de implantação de configurações de workspace para a Central de Segurança do Azure, consulte [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Modelos de exemplo podem ser encontrados no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments).
* Você também pode implantar modelos no nível do [grupo de gerenciamento](deploy-to-management-group.md) e no [nível do locatário](deploy-to-tenant.md).
