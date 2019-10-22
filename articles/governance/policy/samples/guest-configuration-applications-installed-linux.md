---
title: Exemplo – Auditoria de aplicativos ausentes em VMs Linux
description: Essa iniciativa de Configuração de Convidado da Política de amostra e auditoria de definições se os aplicativos especificados não estiverem instalados dentro das máquinas virtuais do Linux.
author: DCtheGeek
ms.service: azure-policy
ms.topic: sample
ms.date: 05/02/2019
ms.author: dacoulte
ms.openlocfilehash: 5f4d4f4c1102c4409d891bb20b54788dc8ed40ee
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255750"
---
# <a name="sample---audit-if-specified-applications-arent-installed-inside-linux-vms"></a>Exemplo – auditar se os aplicativos especificados não estão instalados nas VMs do Linux

Essa iniciativa de Configuração de Convidado de Política cria um evento de auditoria quando os aplicativos especificados não estão instalados em máquinas virtuais do Linux. A ID dessa iniciativa interna é `/providers/Microsoft.Authorization/policySetDefinitions/c937dcb4-4398-4b39-8d63-4a6be432252e`.

> [!IMPORTANT]
> Todas as iniciativas de Configuração do Convidado são compostos por definições de política **auditar** e **deployIfNotExists**. Atribuir somente uma das definições de política fazem com que a Configuração do Convidado não funcione corretamente.

Você pode atribuir este exemplo usando:

- O [Portal do Azure](#azure-portal)
- [PowerShell do Azure](#azure-powershell)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="components-of-the-initiative"></a>Principais componentes da iniciativa

Essa iniciativa de [Configuração do Convidado](../concepts/guest-configuration.md) iniciativa é composta das políticas a seguir:

- [audit](#audit-definition) – auditar se os aplicativos não estão instalados nas VMs do Linux
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/fee5cb2b-9d9b-410e-afe3-2902d90d0004`
- [deployIfNotExists](#deployIfNotExists-definition) – implantar a extensão da VM para auditar se um aplicativo está instalado nas VMs do Linux
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/4d1c04de-2172-403f-901b-90608c35c721`

### <a name="initiative-definition"></a>Definição de iniciativa

A iniciativa é criada unindo a **auditoria** e as definições **deployIfNotExists** definições e os [parâmetros de iniciativa](#initiative-parameters). Esse é o JSON da definição.

[!code-json[initiative-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/azurepolicyset.json "Initiative definition (JSON)")]

### <a name="initiative-parameters"></a>Parâmetros de iniciativa

|NOME |Type |DESCRIÇÃO |
|---|---|---|
|applicationName |Cadeia de caracteres |Nomes de aplicativo. Exemplo: 'python', 'powershell' ou uma lista separada por vírgulas, como 'python, powershell'. Use \* para correspondência de curingas, como 'liga\*'. |

Ao criar uma atribuição através do PowerShell ou da CLI do Azure, os valores do parâmetro podem ser passados como JSON através de uma cadeia de caracteres ou de um arquivo usando `-PolicyParameter` (PowerShell) ou `--params` (CLI do Azure).
O PowerShell também suporta `-PolicyParameterObject` que requer que seja passado uma tabela de hash de Nome/Valor para o cmdlet onde **Nome** é o nome do parâmetro e **Valor** é o valor único ou matriz de valores passados durante a atribuição.

Nesse parâmetro de exemplo, a instalação dos aplicativos _python_ e _powershell_ é auditada.

```json
{
    "applicationName": {
        "value": "python,powershell"
    }
}
```

Somente a definição de política **deployIfNotExists** faz uso de parâmetros de iniciativa.

### <a name="audit-definition"></a>definição de lista

O JSON definindo as regras da definição de política de **auditoria**.

[!code-json[audit-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json "audit policy rules (JSON)")]

### <a name="deployifnotexists-definition"></a>definição deployIfNotExists

O JSON definindo as regras da definição de política de **deployIfNotExists**.

[!code-json[deployIfNotExists-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json "deployIfNotExists policy rules (JSON)")]

A definição da política **deployIfNotExists** efine as imagens do Azure, a política foi validada em:

|Publicador |Oferta |SKU |
|-|-|-|
|OpenLogic |CentOS\* |Todos, exceto 6\* |
|RedHat |RHEL |Todos, exceto 6\* |
|RedHat |osa | Todos |
|credativ |Debian | Todos, exceto 7\* |
|Suse |SLES\* |Todos, exceto 11\* |
|Canônico| UbuntuServer |Todos, exceto 12\* |
|microsoft-dsvm |linux-data-science-vm-ubuntu |Todos |
|microsoft-dsvm |azureml |Todos |
|cloudera |cloudera-centos-os |Todos, exceto 6\* |
|cloudera |cloudera-altus-centos-os |Todos |
|microsoft-ads |linux\* |Todos |
|microsoft-aks |Todos |Todos |
|AzureDatabricks |Todos |Todos |
|qubole-inc |Todos |Todos |
|datastax |Todos |Todos |
|couchbase |Todos |Todos |
|scalegrid |Todos |Todos |
|ponto de verificação |Todos |Todos |
|paloaltonetworks |Todos |Todos |

A parte de **implantação** da regra passa o parâmetro _installedApplication_ para o agente de configuração de convidado na máquina virtual. Essa configuração permite que o agente realize as validações e relate a conformidade de volta por meio da definição de política da **auditoria**.

## <a name="azure-portal"></a>Portal do Azure

Após as definições de **auditoria** e **deployIfNotExists** são criadas no portal, é recomendável agrupá-los em uma [iniciativa](../concepts/definition-structure.md#initiatives) para atribuição.

### <a name="create-copy-of-audit-definition"></a>Criar cópia da definição de auditoria

[![Implantar o exemplo de Política no Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)
[![Implantar o exemplo de Política no Azure Gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)

Usar esses botões para implantar por meio do portal cria uma cópia da definição de política de **auditoria**.
Sem a definição de política **deployIfNotExists** emparelhada, a configuração de convidado não funcionará corretamente.

### <a name="create-copy-of-deployifnotexists-definition"></a>Crie uma cópia de definição deployIfNotExists

[![Implantar o exemplo de Política no Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)
[![Implantar o exemplo de Política no Azure Gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)

Usar esses botões para implantar por meio do portal cria uma cópia da definição de política de **deployIfNotExists**. Sem a definição de política de **auditoria** emparelhada, a configuração de convidado não funcionará corretamente.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Implantar com o Azure PowerShell

#### <a name="copy-and-assign-the-initiative"></a>Copiar e atribuir a iniciativa

Essas etapas criam uma cópia da iniciativa que inclui as políticas internas para **auditar** e **deployIfNotExists** e atribui a iniciativa a um grupo de recursos.

```azurepowershell-interactive
# Create the policy initiative (Subscription scope)
$initDef = New-AzPolicySetDefinition -Name 'guestconfig-installed-application-linux' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This initiative will both deploy the policy requirements and audit that the specified application is installed inside Linux virtual machines.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.definitions.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the initiative parameter (JSON format)
$initParam = '{ "applicationName": { "value": "python,powershell" } }'

# Create the initiative assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicySetDefinition $initDef -PolicyParameter $initParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

Execute os comandos a seguir para remover a definição e a atribuição anteriores:

```azurepowershell-interactive
# Remove the initiative assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the initiative definition
Remove-AzPolicySetDefinition -Id $initDef
```

#### <a name="copy-and-assign-the-audit-definition"></a>Copiar e atribuir a definição de auditoria

Estas etapas criam uma cópia da definição de **auditoria** e a atribuem a um grupo de recursos. Essa definição não funcionará corretamente sem a definição emparelhada **deployIfNotExists** que também está sendo atribuída.

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-audit' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This policy audits that the specified application is installed inside Linux virtual machines. This policy should only be used along with its corresponding deploy policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-audit-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicyDefinition $definition
```

Execute os comandos a seguir para remover a definição e a atribuição anteriores:

```azurepowershell-interactive
# Remove the policy definition
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

#### <a name="copy-and-assign-the-deployifnotexists-definition"></a>Copiar e atribuir a definição de deployIfNotExists

Estas etapas criam uma cópia da definição de **deployIfNotExists** e a atribuem a um grupo de recursos.
Essa definição não funcionará corretamente sem a definição de **auditoria** emparelhada que também está sendo atribuída.

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-deployIfNotExists' -DisplayName 'GuestConfig - Deploy VM extension to audit that an application is installed inside Linux VMs' -description 'Include this rule to deploy the VM extension for Microsoft Guest Configuration, the VM extension for Microsoft Azure Managed Service Identity, and the content required to audit that an application is installed inside Linux virtual machines. This policy should only be used along with its corresponding audit policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the definition parameter (JSON format)
$policyParam  = '{ "applicationName": { "value": "python,powershell" } }'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-deployIfNotExists-assignment' -DisplayName 'GuestConfig - Deploy VM extension to audit that Python and PowerShell are installed inside Linux VMs' -Scope $scope.ResourceID -PolicyDefinition $definition -PolicyParameter $policyParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

Execute os comandos a seguir para remover a definição e a atribuição anteriores:

```azurepowershell-interactive
# Remove the policy assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

### <a name="azure-powershell-explanation"></a>Explicação do Azure PowerShell

Os scripts de implantação e remoção usam os comandos a seguir. Cada comando na tabela a seguir redireciona para a documentação específica do comando:

| Comando | Observações |
|---|---|
| [New-AzPolicySetDefinition](/powershell/module/az.resources/New-AzPolicySetDefinition) | Cria uma iniciativa do Azure Policy. |
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-AzPolicyDefinition) | Cria uma definição do Azure Policy. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup) | Obtém um único grupo de recursos. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-AzPolicyAssignment) | Cria uma nova atribuição de Azure Policy para uma iniciativa ou definição. |
| [New-AzRoleAssignment](/powershell/module/az.resources/New-AzRoleAssignment) | Fornece uma atribuição de função existente para o principal específico. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-AzPolicyAssignment) | Remove uma atribuição existente do Azure Policy. |
| [Remove-AzPolicySetDefinition](/powershell/module/az.resources/Remove-AzPolicySetDefinition) | Remove uma iniciativa. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-AzPolicyDefinition) | Remove uma definição. |

## <a name="next-steps"></a>Próximas etapas

- Revisar as amostras [Azure Policy adicionais](index.md).
- Para saber mais sobre a [Configuração de Convidado do Azure Policy](../concepts/guest-configuration.md).
- Revisar a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
