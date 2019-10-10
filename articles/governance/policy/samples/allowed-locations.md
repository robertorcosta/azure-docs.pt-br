---
title: Exemplo – locais permitido
description: Esta definição de política de exemplo exige a implantação de todos os recursos nas localizações aprovadas.
author: DCtheGeek
ms.service: azure-policy
ms.topic: sample
ms.date: 01/26/2019
ms.author: dacoulte
ms.openlocfilehash: 5c4a23b355a4d7841c42d03042399635a55bac03
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980645"
---
# <a name="sample---allowed-region-locations"></a>Amostra – Localizações de região permitidas

Esta política permite que você restrinja os locais que sua organização pode especificar ao implantar recursos. Use para impor seus requisitos de conformidade geográfica. Exclui grupos de recursos, Microsoft.AzureActiveDirectory/b2cDirectories e recursos que usam a região "global". Especifique uma matriz de locais permitidos.

Você pode implantar essa política de exemplo usando:

- O [Portal do Azure](#azure-portal)
- [PowerShell do Azure](#azure-powershell)
- [CLI do Azure](#azure-cli)
- [API REST](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Política de exemplo

### <a name="policy-definition"></a>Definição de política

A definição completa da política JSON composta, usada pela API REST, pelos botões “Implantar no Azure” e manualmente no portal.

[!code-json[full](../../../../policy-templates/samples/built-in-policy/allowed-locations/azurepolicy.json "Allowed locations")]

> [!NOTE]
> Se criar manualmente uma política no portal, use as partes **properties.parameters** e **properties.policyRule** acima. Encapsular as duas seções junto com as chaves `{}` para tornar o JSON válido.

### <a name="policy-rules"></a>Regras de política

O JSON que define as regras da política, usado pela CLI do Azure e pelo Azure PowerShell.

[!code-json[rule](../../../../policy-templates/samples/built-in-policy/allowed-locations/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Parâmetros de política

O JSON que define os parâmetros da política, usado pela CLI do Azure e pelo Azure PowerShell.

[!code-json[parameters](../../../../policy-templates/samples/built-in-policy/allowed-locations/azurepolicy.parameters.json "Policy parameters (JSON)")]

## <a name="parameters"></a>parâmetros

|NOME |Type |Campo |DESCRIÇÃO |
|---|---|---|---|
|listOfAllowedLocations |Matriz |Locais |A lista de locais permitidos|

Ao criar uma atribuição através do PowerShell ou da CLI do Azure, os valores do parâmetro podem ser passados como JSON através de uma cadeia de caracteres ou de um arquivo usando `-PolicyParameter` (PowerShell) ou `--params` (CLI do Azure).
O PowerShell também suporta `-PolicyParameterObject` que requer que seja passado uma tabela de hash de Nome/Valor para o cmdlet onde **Nome** é o nome do parâmetro e **Valor** é o valor único ou matriz de valores passados durante a atribuição.

Nesse parâmetro de exemplo, somente os locais _eastus2_ ou _westus_ serão permitidos.

```json
{
    "listOfAllowedLocations": {
        "value": [
            "eastus2",
            "westus"
        ]
    }
}
```

## <a name="azure-portal"></a>Portal do Azure

[![Implantar o exemplo de Política no Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fallowed-locations%2Fazurepolicy.json)
[![Implantar o exemplo de Política no Azure Gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fallowed-locations%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Implantar com o Azure PowerShell

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name "allowed-locations" -DisplayName "Allowed locations" -description "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements. Excludes resource groups, Microsoft.AzureActiveDirectory/b2cDirectories, and resources that use the 'global' region." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.parameters.json' -Mode Indexed

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the Policy Parameter (JSON format)
$policyparam = '{ "listOfAllowedLocations": { "value": [ "eastus2", "westus" ] } }'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'allowed-locations-assignment' -DisplayName 'Allowed locations Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition -PolicyParameter $policyparam
```

### <a name="remove-with-azure-powershell"></a>Remover com o Azure PowerShell

Execute os comandos a seguir para remover a definição e a atribuição anteriores:

```azurepowershell-interactive
# Remove the Policy Assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the Policy Definition
Remove-AzPolicyDefinition -Id $definition.ResourceId
```

### <a name="azure-powershell-explanation"></a>Explicação do Azure PowerShell

Os scripts de implantação e remoção usam os comandos a seguir. Cada comando na tabela a seguir redireciona para a documentação específica do comando:

| Comando | Observações |
|---|---|
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-Azpolicydefinition) | Cria uma nova definição do Azure Policy. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-Azresourcegroup) | Obtém um único grupo de recursos. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) | Cria uma nova atribuição do Azure Policy. Neste exemplo, nós fornecemos uma definição, mas também pode ser usada uma iniciativa. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-Azpolicyassignment) | Remove uma atribuição existente do Azure Policy. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-Azpolicydefinition) | Remove uma definição existente do Azure Policy. |

## <a name="azure-cli"></a>CLI do Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>Implantar com a CLI do Azure

```azurecli-interactive
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'allowed-locations' --display-name 'Allowed locations' --description 'This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements. Excludes resource groups, Microsoft.AzureActiveDirectory/b2cDirectories, and resources that use the 'global' region.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.parameters.json' --mode Indexed)

# Set the scope to a resource group; may also be a resource, subscription, or management group
scope=$(az group show --name 'YourResourceGroup')

# Set the Policy Parameter (JSON format)
policyparam='{ "listOfAllowedLocations": { "value": [ "eastus2", "westus" ] } }'

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'allowed-locations-assignment' --display-name 'Allowed locations Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r` --params "$policyparam")
```

### <a name="remove-with-azure-cli"></a>Remover com a CLI do Azure

Execute os comandos a seguir para remover a definição e a atribuição anteriores:

```azurecli-interactive
# Remove the Policy Assignment
az policy assignment delete --name `echo $assignment | jq '.name' -r`

# Remove the Policy Definition
az policy definition delete --name `echo $definition | jq '.name' -r`
```

### <a name="azure-cli-explanation"></a>Explicação da CLI do Azure

| Comando | Observações |
|---|---|
| [az policy definition create](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-create) | Cria uma nova definição do Azure Policy. |
| [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show) | Obtém um único grupo de recursos. |
| [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) | Cria uma nova atribuição do Azure Policy. Neste exemplo, nós fornecemos uma definição, mas também pode ser usada uma iniciativa. |
| [az policy assignment delete](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-delete) | Remove uma atribuição existente do Azure Policy. |
| [az policy definition delete](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-delete) | Remove uma definição existente do Azure Policy. |

## <a name="rest-api"></a>API REST

Há várias ferramentas que podem ser usadas para interagir com a API REST do Gerenciador de recursos, como [ARMClient](https://github.com/projectkudu/ARMClient) ou o PowerShell.

### <a name="deploy-with-rest-api"></a>Implantar com a API REST

- Criar a definição de política (escopo da Assinatura). Use o JSON de [definição de política](#policy-definition) para o corpo da solicitação.

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-locations?api-version=2018-05-01
  ```

- Criar a atribuição de política (escopo do Grupo de Recursos)

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/allowed-locations-assignment?api-version=2018-05-01
  ```

  Use o exemplo de JSON a seguir para o corpo da solicitação:

  ```json
  {
      "properties": {
          "displayName": "Allowed locations Assignment",
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/allowed-locations",
          "parameters": {
              "listOfAllowedLocations": {
                  "value": [
                      "eastus2",
                      "westus"
                  ]
              }
          }
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Remover com a API REST

- Remover a atribuição de política

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/allowed-locations-assignment?api-version=2018-05-01
  ```

- Remover a definição de política

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-locations?api-version=2018-05-01
  ```

### <a name="rest-api-explanation"></a>Explicação da API REST

| Serviço | Agrupar | Operação | Observações |
|---|---|---|---|
| Gerenciamento de recursos | Definições de Política | [Criar](/rest/api/resources/policydefinitions/createorupdate) | Cria uma nova definição do Azure Policy em uma assinatura. Alternativa: [Criar no grupo de gerenciamento](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| Gerenciamento de recursos | Atribuições de Políticas | [Criar](/rest/api/resources/policyassignments/create) | Cria uma nova atribuição do Azure Policy. Neste exemplo, nós fornecemos uma definição, mas também pode ser usada uma iniciativa. |
| Gerenciamento de recursos | Atribuições de Políticas | [Excluir](/rest/api/resources/policyassignments/delete) | Remove uma atribuição existente do Azure Policy. |
| Gerenciamento de recursos | Definições de Política | [Excluir](/rest/api/resources/policydefinitions/delete) | Remove uma definição existente do Azure Policy. Alternativa: [Excluir no grupo de gerenciamento](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>Próximas etapas

- Revisar os outros [exemplos do Azure Policy](index.md)
- Revisar a [estrutura de definição do Azure Policy](../concepts/definition-structure.md)