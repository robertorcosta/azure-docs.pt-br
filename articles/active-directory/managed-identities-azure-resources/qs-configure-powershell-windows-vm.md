---
title: Configurar identidades gerenciadas em uma VM do Azure usando o PowerShell-Azure AD
description: Instruções passo a passo para configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando PowerShell.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e17b4a3f71e67b99bfbd4c52edc00f98d549ef2
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74183703"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, usando o PowerShell, você aprenderá como executar as seguintes identidades gerenciadas para operações de recursos do Azure em uma VM do Azure.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a seção de [visão geral](overview.md). **Revise a [diferença entre uma identidade gerenciada atribuída ao sistema e atribuída ao usuário](overview.md#how-does-it-work)** .
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Instale [a versão mais recente do Azure PowerShell](/powershell/azure/install-az-ps) se ainda não o fez.

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

Nesta seção, você aprenderá como habilitar e desabilitar a identidade gerenciada atribuída ao sistema usando o Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Habilitar identidade gerenciada atribuída ao sistema durante a criação de uma VM do Azure

Para criar uma VM do Azure com a identidade gerenciada atribuída ao sistema habilitada, a conta precisará da atribuição de função [Colaborador da Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Nenhuma atribuição adicional de função de diretório do Azure AD será necessária.

1. Consulte um dos seguintes inícios rápidos de VM do Azure, concluindo apenas as seções necessárias ("entrar no Azure", "Criar grupo de recursos", "Criar grupo de rede", "criar a VM").
    
    Quando você chegar à seção "Criar a VM", faça uma pequena modificação na sintaxe do cmdlet [New-AzVMConfig](/powershell/module/az.compute/new-azvm). Certifique-se de adicionar um parâmetro `-AssignIdentity:$SystemAssigned` para provisionar a VM com a identidade atribuída ao sistema habilitada, por exemplo:
      
    ```powershell
    $vmConfig = New-AzVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [Crie uma Máquina Virtual do Windows usando o PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Crie uma Máquina Virtual do Linux usando o PowerShell](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Habilitar identidade gerenciada atribuída ao sistema em uma VM do Azure existente

Para habilitar a identidade gerenciada atribuída ao sistema em uma VM que foi originalmente provisionada sem ela, a conta precisará da atribuição de função [Colaborador da Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Nenhuma atribuição adicional de função de diretório do Azure AD será necessária.

1. Entre no Azure usando `Connect-AzAccount`. Use uma conta que esteja associada com uma assinatura do Azure que contenha uma VM.

   ```powershell
   Connect-AzAccount
   ```

2. Primeiro, recupere as propriedades da VM usando o cmdlet `Get-AzVM`. Em seguida, para habilitar uma identidade gerenciada atribuída ao sistema, use a opção `-AssignIdentity` no cmdlet [Update-AzVM](/powershell/module/az.compute/update-azvm):

   ```powershell
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```



### <a name="add-vm-system-assigned-identity-to-a-group"></a>Adicionar uma identidade atribuída pelo sistema de uma VM a um grupo

Depois de habilitar a identidade atribuída pelo sistema em uma VM, você pode adicioná-la a um grupo.  O procedimento a seguir adiciona uma identidade atribuída pelo sistema de uma VM a um grupo.

1. Entre no Azure usando `Connect-AzAccount`. Use uma conta que esteja associada com uma assinatura do Azure que contenha uma VM.

   ```powershell
   Connect-AzAccount
   ```

2. Recupere e tome nota do `ObjectID` (conforme especificado no campo `Id` dos valores retornados) da entidade de serviço da VM:

   ```powerhshell
   Get-AzADServicePrincipal -displayname "myVM"
   ```

3. Recupere e tome nota do `ObjectID` (conforme especificado no campo `Id` dos valores retornados) do grupo:

   ```powershell
   Get-AzADGroup -searchstring "myGroup"
   ```

4. Adicionar a entidade de serviço da VM ao grupo:

   ```powershell
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Desativar identidade gerenciada atribuída pelo sistema de uma VM do Azure

Para desabilitar a identidade gerenciada atribuída ao sistema em uma VM, a conta precisará da atribuição de função [Colaborador da Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Nenhuma atribuição adicional de função de diretório do Azure AD será necessária.

Se você tiver uma máquina virtual que não precise mais da identidade gerenciada atribuída ao sistema, mas ainda precisar de identidades gerenciadas atribuídas ao usuário, use o seguinte cmdlet:

1. Entre no Azure usando `Connect-AzAccount`. Use uma conta que esteja associada com uma assinatura do Azure que contenha uma VM.

   ```powershell
   Connect-AzAccount
   ```

2. Recupere as propriedades da VM usando o cmdlet `Get-AzVM` e defina o parâmetro `-IdentityType` como `UserAssigned`:

   ```powershell   
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Se você tiver uma máquina virtual que não precise mais da identidade gerenciada atribuída ao sistema e não tiver identidades gerenciadas atribuídas ao usuário, use os comandos a seguir:

```powershell
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```



## <a name="user-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo usuário

Nesta seção, você aprenderá como adicionar e remover uma identidade atribuída ao usuário de uma VM usando Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Atribuir uma identidade gerenciada atribuída ao usuário a uma VM durante a criação

Para atribuir uma identidade atribuída pelo usuário a uma VM, sua conta precisa das atribuições de função [Contribuidor de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [Operador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator). Nenhuma atribuição adicional de função de diretório do Azure AD será necessária.

1. Consulte um dos seguintes inícios rápidos de VM do Azure, concluindo apenas as seções necessárias ("entrar no Azure", "Criar grupo de recursos", "Criar grupo de rede", "criar a VM"). 
  
    Quando você chegar à seção "Criar a VM", faça uma pequena modificação na  [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm)sintaxe do cmdlet. Adicione os parâmetros `-IdentityType UserAssigned` e `-IdentityID` para provisionar a VM com uma identidade atribuída ao usuário.  Substitua `<VM NAME>`,`<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores.  Por exemplo:
    
    ```powershell 
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```
    
    - [Crie uma Máquina Virtual do Windows usando o PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Crie uma Máquina Virtual do Linux usando o PowerShell](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Atribuir uma identidade gerenciada usuário atribuído a uma VM existente do Azure

Para atribuir uma identidade atribuída pelo usuário a uma VM, sua conta precisa das atribuições de função [Contribuidor de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [Operador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator). Nenhuma atribuição adicional de função de diretório do Azure AD será necessária.

1. Entre no Azure usando `Connect-AzAccount`. Use uma conta que esteja associada com uma assinatura do Azure que contenha uma VM.

   ```powershell
   Connect-AzAccount
   ```

2. Crie uma identidade gerenciada atribuída ao usuário, usando o cmdlet [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity).  Observe o `Id` na saída porque isso será necessário na próxima etapa.

   > [!IMPORTANT]
   > A criação de identidades gerenciadas atribuídas pelo usuário dá suporte apenas a caracteres alfanuméricos, sublinhados e hifens (0-9 ou a-z ou A-Z, \_ ou-). Além disso, o nome deve ser limitado de 3 a 128 de comprimento de caractere para que a atribuição a VM/VMSS funcione corretamente. Para mais informações, consulte [Perguntas frequentes e problemas conhecidos](known-issues.md)

   ```powershell
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. Recupere as propriedades da VM usando o cmdlet `Get-AzVM`. Em seguida, para atribuir uma identidade gerenciada atribuída ao usuário à VM do Azure, use a opção `-IdentityType` e `-IdentityID` no cmdlet [Update-AzVM](/powershell/module/az.compute/update-azvm).  O valor para o`-IdentityId` parâmetro é o `Id` você anotou na etapa anterior.  Substitua`<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED IDENTITY NAME>` e {5} pelos seus próprios valores.

   > [!WARNING]
   > Para manter qualquer identidades gerenciadas anteriormente atribuído ao usuário atribuídas à VM, consulte o `Identity` propriedade do objeto da VM (por exemplo, `$vm.Identity`).  Se qualquer usuário atribuído identidades gerenciadas são retornadas, incluí-los no comando a seguir, juntamente com o novo usuário atribuído a identidade gerenciada que você deseja atribuir à VM.

   ```powershell
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```



### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Remover uma identidade gerenciada atribuída ao usuário de uma VM do Azure

Para remover uma identidade atribuída ao usuário a uma VM, a conta precisará da atribuição de função [Colaborador da Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).

Se a VM tiver várias identidades gerenciadas atribuídas ao usuário, será possível remover todas, exceto a última, usando os seguintes comandos. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<VM NAME>` pelos seus próprios valores. O `<USER ASSIGNED IDENTITY NAME>` é a propriedade do nome da identidade gerenciada atribuída ao usuário, que deve permanecer na VM. Essas informações podem ser encontradas ao consultar o `Identity` propriedade do objeto da VM.  Por exemplo `$vm.Identity`:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Se a VM não tiver uma identidade gerenciada atribuída ao sistema e você quiser remover todas as identidades gerenciadas atribuídas ao usuário, use o comando a seguir:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Se a VM tiver ambas as identidades gerenciadas atribuídas ao sistema e atribuídas ao usuário, será possível remover todas as identidades gerenciadas atribuídas ao usuário, alternando para usar apenas as identidades gerenciadas atribuídas ao sistema.

```powershell 
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Próximas etapas

- [Identidades gerenciadas para visão geral de recursos do Azure](overview.md)
- Para os guias de início rápido completos sobre VM do Azure, consulte:
  
  - [Crie uma máquina virtual Windows com o PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Crie uma máquina virtual Linux com o PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 
