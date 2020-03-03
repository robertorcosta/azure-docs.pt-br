---
title: Implantar recursos na assinatura
description: Descreve como criar um grupo de recursos em um modelo do Azure Resource Manager. Ele também mostra como implantar recursos no escopo da assinatura do Azure.
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: 2e747b7faa6e9766a577b472cc3e283d6223109e
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2020
ms.locfileid: "78228130"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Criar grupos de recursos e recursos em nível de assinatura

Normalmente, você implanta recursos do Azure em um grupo de recursos em sua assinatura do Azure. No entanto, você também pode criar recursos no nível da assinatura. Você usa implantações de nível de assinatura para executar ações que fazem sentido nesse nível, como a criação de grupos de recursos ou a atribuição [de controle de acesso baseado em função](../../role-based-access-control/overview.md).

Para implantar modelos no nível de assinatura, use CLI do Azure, PowerShell ou API REST. O portal do Azure não dá suporte à implantação em nível de assinatura.

## <a name="supported-resources"></a>Recursos com suporte

Você pode implantar os seguintes tipos de recursos no nível da assinatura:

* [Orçamentos](/azure/templates/microsoft.consumption/budgets)
* [implantações](/azure/templates/microsoft.resources/deployments)
* [peerAsns](/azure/templates/microsoft.peering/peerasns)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Schema

O esquema usado para implantações em nível de assinatura é diferente do esquema para implantações de grupo de recursos.

Para modelos, use:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

Para arquivos de parâmetro, use:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#
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
* A função [resourceId()](template-functions-resource.md#resourceid) é suportada. Use-a para obter a ID de recurso para recursos que são usados em implantações de nível de assinatura. Não forneça um valor para o parâmetro do grupo de recursos.

  Por exemplo, para obter a ID de recurso para uma definição de política, use:
  
  ```json
  resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))
  ```
  
  A ID de recurso retornada tem o seguinte formato:

  ```json
  /subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

  Ou use a função [subscriptionResourceId ()](template-functions-resource.md#subscriptionresourceid) para obter a ID de recurso para um recurso de nível de assinatura.

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

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* Para saber mais sobre como atribuir funções, consulte [gerenciar o acesso aos recursos do Azure usando os modelos RBAC e Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Para obter um exemplo de implantação de configurações de workspace para a Central de Segurança do Azure, consulte [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Modelos de exemplo podem ser encontrados no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments).
* Para saber mais sobre a criação de modelos do Gerenciador de Recursos do Azure, consulte [Criando modelos](template-syntax.md).
* Para obter uma lista das funções disponíveis em um modelo, consulte [Funções de modelo](template-functions.md).
