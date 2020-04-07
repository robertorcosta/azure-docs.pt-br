---
title: Como gerenciar atribuições com o PowerShell
description: Saiba como gerenciar as atribuições do projeto com o módulo PowerShell oficial do Azure Blueprints, Az.Blueprint.
ms.date: 09/30/2019
ms.topic: how-to
ms.openlocfilehash: 0868e5e207202511c1981a930870bfdc68a77a8f
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677435"
---
# <a name="how-to-manage-assignments-with-powershell"></a>Como gerenciar atribuições com o PowerShell

Uma atribuição de projeto pode ser gerenciada usando o módulo **Az.Blueprint** Azure PowerShell. O módulo suporta buscar, criar, atualizar e remover atribuições. O módulo também pode obter detalhes sobre as definições de projeto existentes. Este artigo abrange como instalar o módulo e começar a usá-lo.

## <a name="add-the-azblueprint-module"></a>Adicione o módulo Az.Blueprint

Para permitir que o Azure PowerShell gerencie as atribuições do projeto, o módulo deve ser adicionado. Esse módulo pode ser usado com o PowerShell instalado localmente, com o [Azure Cloud Shell](https://shell.azure.com) ou com a [imagem do Docker do Azure PowerShell](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Requisitos base

O módulo Azure Blueprints requer o seguinte software:

- Azure PowerShell 1.5.0 ou superior. Se ainda não estiver instalado, siga [estas instruções](/powershell/azure/install-az-ps).
- PowerShellGet 2.0.1 ou superior. Se ele não estiver instalado ou atualizado, siga [estas instruções](/powershell/scripting/gallery/installing-psget).

### <a name="install-the-module"></a>Instalar o módulo

O módulo Azure Blueprints para PowerShell é **Az.Blueprint**.

1. De um prompt **administrativo** do PowerShell, execute o comando a seguir:

   ```azurepowershell-interactive
   # Install the Azure Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Se **a Az.Accounts** já estiver instalada, `-AllowClobber` pode ser necessário usar para forçar a instalação.

1. Valide se o módulo foi importado e é a versão correta (0.2.6):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Obter definições de projeto

O primeiro passo para trabalhar com uma tarefa muitas vezes é obter uma referência a uma definição de projeto.
O `Get-AzBlueprint` cmdlet obtém uma ou mais definições de projeto. O cmdlet pode obter definições de `-ManagementGroupId {mgId}` projeto de `-SubscriptionId {subId}`um grupo de gerenciamento com ou uma assinatura com . O parâmetro **Nome** obtém uma definição de projeto, mas deve ser usado com **ManagementGroupId** ou **SubscriptionId**. **A versão** pode ser usada com **Name** para ser mais explícita sobre qual definição de projeto é retornada. Em vez de `-LatestPublished` **Version,** o switch pega a versão mais recente.

O exemplo `Get-AzBlueprint` a seguir é usa para obter todas as versões de uma definição de `{subId}`projeto chamada '101-blueprints-definition-subscription' a partir de uma assinatura específica representada como :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

A saída de exemplo para uma definição de projeto com várias versões é assim:

```output
Name                 : 101-blueprints-definition-subscription
Id                   : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101
                       -blueprints-definition-subscription
DefinitionLocationId : {subId}
Versions             : {1.0, 1.1}
TimeCreated          : 2019-02-25
TargetScope          : Subscription
Parameters           : {storageAccount_storageAccountType, storageAccount_location,
                       allowedlocations_listOfAllowedLocations, [Usergrouporapplicationname]:Reader_RoleAssignmentName}
ResourceGroups       : ResourceGroup
```

Os [parâmetros](../concepts/parameters.md#blueprint-parameters) do projeto na definição do projeto podem ser expandidos para fornecer mais informações.

```azurepowershell-interactive
$blueprints.Parameters
```

```output
Key                                                    Value
---                                                    -----
storageAccount_storageAccountType                      Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
storageAccount_location                                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
allowedlocations_listOfAllowedLocations                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
[Usergrouporapplicationname]:Reader_RoleAssignmentName Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
```

## <a name="get-blueprint-assignments"></a>Obtenha atribuições de projeto

Se a atribuição de projeto já existir, `Get-AzBlueprintAssignment` você pode obter uma referência a ela com o cmdlet. O cmdlet toma **SubscriptionId** e **Name** como parâmetros opcionais. Se **o SubscriptionId** não for especificado, o contexto de assinatura atual será usado.

O exemplo `Get-AzBlueprintAssignment` a seguir é usa para obter uma única atribuição de projeto `{subId}`chamada 'Grupos de recursos de bloqueio de atribuição' a partir de uma assinatura específica representada como :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

A saída de exemplo para uma atribuição de projeto é assim:

```output
Name              : Assignment-lock-resource-groups
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssignme
                    nts/Assignment-lock-resource-groups
Scope             : /subscriptions/{subId}
LastModified      : 2019-02-19
LockMode          : AllResourcesReadOnly
ProvisioningState : Succeeded
Parameters        :
ResourceGroups    : ResourceGroup
```

## <a name="create-blueprint-assignments"></a>Criar atribuições de projeto

Se a atribuição do projeto ainda não existir, você pode criá-la com o `New-AzBlueprintAssignment` cmdlet. Este cmdlet usa os seguintes parâmetros:

- **Nome** [necessário]
  - Especifica o nome da atribuição do projeto
  - Deve ser único e ainda não existir no **SubscriptionId**
- **Projeto** [necessário]
  - Especifica a definição do projeto para atribuir
  - Use `Get-AzBlueprint` para obter o objeto de referência
- **Localização** [necessária]
  - Especifica a região para o objeto de implantação gerenciada de identidade e assinatura atribuído pelo sistema a ser criado em
- **Assinatura** (opcional)
  - Especifica a assinatura para a atribuição que a atribuição é implantada
  - Se não for fornecido, é padrão para o contexto de assinatura atual
- **Bloqueio** (opcional)
  - Define o [bloqueio de recursos](../concepts/resource-locking.md) do projeto para usar para recursos implantados
  - Opções suportadas: _None,_ _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - Se não for fornecido, é padrão para _Nenhum_
- **SystemAssignedIdentity** (opcional)
  - Selecione para criar uma identidade gerenciada atribuída ao sistema para a atribuição e para implantar os recursos
  - Padrão para o conjunto de parâmetros de "identidade"
  - Não pode ser usado com **UserAssignedIdentity**
- **UserAssignedIdentity** (opcional)
  - Especifica a identidade gerenciada atribuída pelo usuário para usar para a atribuição e para implantar os recursos
  - Parte do conjunto de parâmetros de "identidade"
  - Não pode ser usado com **SystemAssignedIdentity**
- **Parâmetro** (opcional)
  - Uma [tabela hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) de pares de tecla/valor para definir [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters) na atribuição do projeto
  - Padrão para um parâmetro dinâmico é o **padrãoValor** na definição
  - Se um parâmetro não for fornecido e não tiver **padrãoValor,** o parâmetro não é opcional

    > [!NOTE]
    > **O parâmetro** não suporta strings seguros.

- **Parâmetro de grupo de recursos** (opcional)
  - Uma [tabela hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) de artefatos de grupo de recursos
  - Cada espaço reservado para artefatos de grupo de recursos tem pares de chave/valor para definir dinamicamente **nome** e **localização** nesse artefato de grupo de recursos
  - Se um parâmetro de grupo de recursos não for fornecido e não tiver **padrãoValor,** o parâmetro do grupo de recursos não é opcional
- **Arquivo de atribuição** (opcional)
  - O caminho para uma representação de arquivo JSON de uma atribuição de projeto
  - Este parâmetro faz parte de um conjunto de parâmetros do PowerShell que inclui apenas **Name**, **Blueprint**e **SubscriptionId,** além dos parâmetros comuns.

### <a name="example-1-provide-parameters"></a>Exemplo 1: Fornecer parâmetros

O exemplo a seguir cria uma nova atribuição da versão '1.1' `Get-AzBlueprint`da definição de projeto 'my-blueprint' buscada com , define o local de objeto de identidade e atribuição gerenciado para 'westus2', bloqueia os recursos com _AllResourcesReadOnly_e define as tabelas de hash para **parâmetro** e **parâmetro de recursosParameter** em assinatura específica representada como `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

A saída de exemplo para criar uma atribuição de projeto é assim:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

### <a name="example-2-use-a-json-assignment-definition-file"></a>Exemplo 2: Use um arquivo de definição de atribuição JSON

O exemplo a seguir cria quase a mesma atribuição [que exemplo 1](#example-1-provide-parameters).
Em vez de passar parâmetros para o cmdlet, o exemplo mostra o uso de um arquivo de definição de atribuição JSON e do parâmetro **AssignmentFile.** Além disso, a propriedade **excludedPrincipals** é configurada como parte de **bloqueios**. Não há um parâmetro PowerShell para **excluídosPrincipais** e a propriedade só pode ser configurada definindo-a através do arquivo de definição de atribuição JSON.

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "westus2",
  "properties": {
    "description": "Assignment of the 101-blueprint-definition-subscription",
    "blueprintId": "/subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101-blueprints-definition-subscription",
    "locks": {
      "mode": "AllResourcesReadOnly",
      "excludedPrincipals": [
          "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
          "38833b56-194d-420b-90ce-cff578296714"
      ]
    },
    "parameters": {
      "storageAccount_storageAccountType": {
        "value": "Standard_GRS"
      }
    },
    "resourceGroups": {
      "ResourceGroup": {
        "name": "storage_rg",
        "location": "westus2"
      }
    }
  }
}
```

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -SubscriptionId '{subId}' `
    -AssignmentFile '.\assignment.json'
```

Para obter um exemplo do arquivo de definição de atribuição JSON para uma identidade gerenciada atribuída pelo usuário, consulte o órgão de solicitação em [Exemplo: Atribuição com identidade gerenciada atribuída pelo usuário](/rest/api/blueprints/assignments/createorupdate#examples) para API REST.

## <a name="update-blueprint-assignments"></a>Atualizar atribuições do projeto

Às vezes é necessário atualizar uma atribuição de projeto que já foi criada. O `Set-AzBlueprintAssignment` cmdlet lida com esta ação. O cmdlet toma a maioria `New-AzBlueprintAssignment` dos mesmos parâmetros que o cmdlet faz, permitindo que qualquer coisa que foi definida na atribuição seja atualizada. As exceções são _o Nome,_ _Projeto_e _SubscriptionId._ Somente os valores fornecidos são atualizados.

Para entender o que acontece ao atualizar uma atribuição de projeto, consulte [regras para atualizar atribuições](./update-existing-assignments.md#rules-for-updating-assignments).

- **Nome** [necessário]
  - Especifica o nome da atribuição do projeto para atualizar
  - Usado para localizar a atribuição para atualizar, não para alterar a atribuição
- **Projeto** [necessário]
  - Especifica a definição do projeto da atribuição do projeto
  - Use `Get-AzBlueprint` para obter o objeto de referência
  - Usado para localizar a atribuição para atualizar, não para alterar a atribuição
- **Localização** (opcional)
  - Especifica a região para o objeto de implantação gerenciada de identidade e assinatura atribuído pelo sistema a ser criado em
- **Assinatura** (opcional)
  - Especifica a assinatura para a atribuição que a atribuição é implantada
  - Se não for fornecido, é padrão para o contexto de assinatura atual
  - Usado para localizar a atribuição para atualizar, não para alterar a atribuição
- **Bloqueio** (opcional)
  - Define o [bloqueio de recursos](../concepts/resource-locking.md) do projeto para usar para recursos implantados
  - Opções suportadas: _None,_ _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** (opcional)
  - Selecione para criar uma identidade gerenciada atribuída ao sistema para a atribuição e para implantar os recursos
  - Padrão para o conjunto de parâmetros de "identidade"
  - Não pode ser usado com **UserAssignedIdentity**
- **UserAssignedIdentity** (opcional)
  - Especifica a identidade gerenciada atribuída pelo usuário para usar para a atribuição e para implantar os recursos
  - Parte do conjunto de parâmetros de "identidade"
  - Não pode ser usado com **SystemAssignedIdentity**
- **Parâmetro** (opcional)
  - Uma [tabela hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) de pares de tecla/valor para definir [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters) na atribuição do projeto
  - Padrão para um parâmetro dinâmico é o **padrãoValor** na definição
  - Se um parâmetro não for fornecido e não tiver **padrãoValor,** o parâmetro não é opcional

    > [!NOTE]
    > **O parâmetro** não suporta strings seguros.

- **Parâmetro de grupo de recursos** (opcional)
  - Uma [tabela hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) de artefatos de grupo de recursos
  - Cada espaço reservado para artefatos de grupo de recursos tem pares de chave/valor para definir dinamicamente **nome** e **localização** nesse artefato de grupo de recursos
  - Se um parâmetro de grupo de recursos não for fornecido e não tiver **padrãoValor,** o parâmetro do grupo de recursos não é opcional

O exemplo a seguir atualiza a atribuição da versão '1.1' da `Get-AzBlueprint` definição de projeto 'meu-projeto' buscada alterando o modo de bloqueio:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

A saída de exemplo para criar uma atribuição de projeto é assim:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesDoNotDelete
ProvisioningState : Updating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="remove-blueprint-assignments"></a>Remover atribuições do projeto

Quando é hora de uma atribuição de `Remove-AzBlueprintAssignment` projeto ser removida, o cmdlet lida com esta ação. O cmdlet leva **Nome** ou **InputObject** para especificar qual atribuição de projeto remover. **A Assinatura Id** é _necessária_ e deve ser fornecida em todos os casos.

O exemplo a seguir busca uma `Get-AzBlueprintAssignment` atribuição de projeto existente com `{subId}`e, em seguida, remove-a da assinatura específica representada como :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>Exemplo de código de ponta a ponta

Juntando todas as etapas, o exemplo a seguir obtém a definição do projeto, em `{subId}`seguida, cria, atualiza e remove uma atribuição de projeto na assinatura específica representada como :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

#region GetBlueprint
# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'
#endregion

#region CreateAssignment
# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
#endregion CreateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region UpdateAssignment
# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
#endregion UpdateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region RemoveAssignment
# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $bpAssignment -SubscriptionId '{subId}'
#endregion
```

## <a name="next-steps"></a>Próximas etapas

- Conheça o [ciclo de vida](../concepts/lifecycle.md)do projeto .
- Saiba como usar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../concepts/resource-locking.md).
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md).