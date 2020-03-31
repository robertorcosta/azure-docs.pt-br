---
title: Sincronização escopo para serviços de domínio Azure AD | Microsoft Docs
description: Saiba como configurar a sincronização escopo do Azure AD para um domínio gerenciado do Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: cc126af67a0d8627d61e595cee56f3df8973340d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613052"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services"></a>Configure a sincronização escopo do Azure AD para o Azure Active Directory Domain Services

Para fornecer serviços de autenticação, o Azure Active Directory Domain Services (Azure AD DS) sincroniza usuários e grupos do Azure AD. Em um ambiente híbrido, usuários e grupos de um ambiente AD DS (Active Directory Domain Services, serviços de domínio de diretório ativo) no local podem ser sincronizados primeiro com o Azure AD Connect e, em seguida, sincronizados com o Azure AD DS.

Por padrão, todos os usuários e grupos de um diretório Azure AD estão sincronizados com um domínio Gerenciado pelo Azure AD DS. Se você tiver necessidades específicas, você pode optar por sincronizar apenas um conjunto definido de usuários.

Este artigo mostra como criar um domínio gerenciado pelo Azure AD DS que usa sincronização escopo e, em seguida, alterar ou desativar o conjunto de usuários com escopo.

## <a name="scoped-synchronization-overview"></a>Visão geral da sincronização escopo

Por padrão, todos os usuários e grupos de um diretório Azure AD estão sincronizados com um domínio Gerenciado pelo Azure AD DS. Se apenas alguns usuários precisarem acessar o domínio gerenciado, você poderá sincronizar apenas essas contas de usuário. Esta sincronização escopo é baseada em grupo. Quando você configura a sincronização escopo baseada em grupo, apenas as contas de usuário que pertencem aos grupos especificados são sincronizadas ao domínio gerenciado pelo Azure AD DS.

A tabela a seguir descreve como usar a sincronização escopo:

| Estado atual | Estado desejado | Configuração necessária |
| --- | --- | --- |
| Um domínio gerenciado existente é configurado para sincronizar todas as contas e grupos de usuários. | Você deseja sincronizar apenas contas de usuário que pertencem a grupos específicos. | Você não pode mudar de sincronizar todos os usuários para usar sincronização com escopo. [Exclua o domínio gerenciado existente](delete-aadds.md)e siga as etapas deste artigo para recriar um domínio gerenciado pelo Azure AD DS com sincronização escopo configurada. |
| Nenhum domínio gerenciado existente. | Você deseja criar um novo domínio gerenciado e sincronizar apenas as contas de usuário que pertencem a grupos específicos. | Siga as etapas deste artigo para criar um domínio gerenciado pelo Azure AD DS com sincronização escopo configurada. |
| Um domínio gerenciado existente é configurado para sincronizar apenas contas que pertencem a grupos específicos. | Você deseja modificar a lista de grupos cujos usuários devem ser sincronizados com o domínio gerenciado pelo Azure AD DS. | Siga as etapas deste artigo para modificar a sincronização escopo. |

Você usa o portal Azure ou powerShell para configurar as configurações de sincronização com escopo:

| Ação | | |
|--|--|--|
| Crie um domínio gerenciado pelo Azure AD DS e configure a sincronização com escopo | [Portal Azure](#enable-scoped-synchronization-using-the-azure-portal) | [Powershell](#enable-scoped-synchronization-using-powershell) |
| Modificar sincronização escopo | [Portal Azure](#modify-scoped-synchronization-using-the-azure-portal) | [Powershell](#modify-scoped-synchronization-using-powershell) |
| Desativar a sincronização com escopo | [Portal Azure](#disable-scoped-synchronization-using-the-azure-portal) | [Powershell](#disable-scoped-synchronization-using-powershell) |

> [!WARNING]
> Alterar o escopo da sincronização faz com que o domínio Gerenciado pelo Azure AD DS ressincronizar todos os dados.
> 
>  * Quando você altera o escopo de sincronização de um domínio gerenciado pelo Azure AD DS, ocorre uma ressincronização completa.
>  * Os objetos que não são mais necessários no domínio gerenciado pelo Azure AD DS são excluídos. Novos objetos são criados no domínio gerenciado.
>  * A ressincronização pode levar muito tempo para ser concluída. O tempo de sincronização depende do número de objetos, como usuários, grupos e membros de grupos no domínio gerenciado do Azure AD DS e no diretório Azure AD. Para diretórios grandes com várias centenas de milhares de objetos, a ressincronização pode levar alguns dias.

## <a name="enable-scoped-synchronization-using-the-azure-portal"></a>Habilite a sincronização escopo usando o portal Azure

1. Siga o [tutorial para criar e configurar uma instância Azure AD DS](tutorial-create-instance-advanced.md). Complete todos os pré-requisitos e etapas de implantação que não sejam para o escopo de sincronização.
1. Escolha **Escopo** na etapa de sincronização e selecione os grupos Azure AD para sincronizar a instância Azure AD DS.

O domínio gerenciado pelo Azure AD DS pode levar até uma hora para concluir a implantação. No portal Azure, a página **Visão geral** do domínio gerenciado pelo Azure AD DS mostra o status atual durante esta fase de implantação.

Quando o portal Azure mostra que o domínio gerenciado pelo Azure AD DS terminou o provisionamento, as seguintes tarefas precisam ser concluídas:

* Atualize as configurações de DNS da rede virtual, de modo que as máquinas virtuais possam encontrar o domínio gerenciado para ingresso no domínio ou autenticação.
    * Para configurar o DNS, selecione o domínio gerenciado pelo Azure AD DS no portal. Na **janela Visão geral,** você é solicitado a configurar automaticamente essas configurações de DNS.
* [Habilite a sincronização de senha satisco no Azure AD Domain Services para](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) que os usuários finais possam entrar no domínio gerenciado usando suas credenciais corporativas.

## <a name="modify-scoped-synchronization-using-the-azure-portal"></a>Modificar sincronização escopo usando o portal Azure

Para modificar a lista de grupos cujos usuários devem ser sincronizados com o domínio gerenciado pelo Azure AD DS, complete as seguintes etapas:

1. No portal Azure, procure e selecione **Azure AD Domain Services**. Escolha sua instância, como *aaddscontoso.com*.
1. Selecione **Sincronização** no menu do lado esquerdo.
1. Para adicionar um grupo, escolha **+ Selecione grupos** na parte superior e escolha os grupos a serem adicionados.
1. Para remover um grupo do escopo de sincronização, selecione-o na lista de grupos sincronizados atualmente e escolha **Remover grupos**.
1. Quando todas as alterações forem feitas, selecione **Salvar o escopo de sincronização**.

Alterar o escopo da sincronização faz com que o domínio Gerenciado pelo Azure AD DS ressincronizar todos os dados. Os objetos que não são mais necessários no domínio gerenciado pelo Azure AD DS são excluídos e a ressincronização pode levar muito tempo para ser concluída.

## <a name="disable-scoped-synchronization-using-the-azure-portal"></a>Desativar a sincronização com escopo usando o portal Azure

Para desativar a sincronização com escopo baseada em grupo para um domínio gerenciado pelo Azure AD DS, complete as seguintes etapas:

1. No portal Azure, procure e selecione **Azure AD Domain Services**. Escolha sua instância, como *aaddscontoso.com*.
1. Selecione **Sincronização** no menu do lado esquerdo.
1. Defina o escopo de sincronização de **Escopo** para **Todos**e selecione **Salvar o escopo de sincronização**.

Alterar o escopo da sincronização faz com que o domínio Gerenciado pelo Azure AD DS ressincronizar todos os dados. Os objetos que não são mais necessários no domínio gerenciado pelo Azure AD DS são excluídos e a ressincronização pode levar muito tempo para ser concluída.

## <a name="powershell-script-for-scoped-synchronization"></a>Script PowerShell para sincronização escopo

Para configurar a sincronização escopo usando o PowerShell, primeiro `Select-GroupsToSync.ps1`salve o script a seguir em um arquivo chamado . Este script configura o Azure AD DS para sincronizar grupos selecionados do Azure AD. Todas as contas de usuário que fazem parte dos grupos especificados estão sincronizadas com o domínio gerenciado pelo Azure AD DS.

Este script é usado nas etapas adicionais deste artigo.

```powershell
param (
    [Parameter(Position = 0)]
    [String[]]$groupsToAdd
)

Connect-AzureAD
$sp = Get-AzureADServicePrincipal -Filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
$role = $sp.AppRoles | where-object -FilterScript {$_.DisplayName -eq "User"}

Write-Output "`n****************************************************************************"

Write-Output "Total group-assignments need to be added: $($groupsToAdd.Count)"
$newGroupIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($groupName in $groupsToAdd)
{
    try
    {
        $group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"
        $newGroupIds.Add($group.ObjectId)

        Write-Output "Group-Name: $groupName, Id: $($group.ObjectId)"
    }
    catch
    {
        Write-Error "Failed to find group: $groupName. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

$currentAssignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId
Write-Output "Total current group-assignments: $($currentAssignments.Count), SP-ObjectId: $($sp.ObjectId)"

$currAssignedObjectIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($assignment in $currentAssignments)
{
    Write-Output "Assignment-ObjectId: $($assignment.PrincipalId)"

    if ($newGroupIds.Contains($assignment.PrincipalId) -eq $false)
    {
        Write-Output "This assignment is not needed anymore. Removing it! Assignment-ObjectId: $($assignment.PrincipalId)"
        Remove-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -AppRoleAssignmentId $assignment.ObjectId
    }
    else
    {
        $currAssignedObjectIds.Add($assignment.PrincipalId)
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

foreach ($id in $newGroupIds)
{
    try
    {
        if ($currAssignedObjectIds.Contains($id) -eq $false)
        {
            Write-Output "Adding new group-assignment. Role-Id: $($role.Id), Group-Object-Id: $id, ResourceId: $($sp.ObjectId)"
            New-AzureADGroupAppRoleAssignment -Id $role.Id -ObjectId $id -PrincipalId $id -ResourceId $sp.ObjectId
        }
        else
        {
            Write-Output "Group-ObjectId: $id is already assigned."
        }
    }
    catch
    {
        Write-Error "Exception occurred assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```

## <a name="enable-scoped-synchronization-using-powershell"></a>Habilite a sincronização escopo usando o PowerShell

Use o PowerShell para concluir esse conjunto de etapas. Confira as instruções para [Habilitar o Azure Active Directory Domain Services usando o PowerShell](powershell-create-instance.md). Algumas das etapas neste artigo foram ligeiramente modificadas para configurar a sincronização no escopo.

1. Complete as seguintes tarefas do artigo para habilitar o Azure AD DS usando o PowerShell. Pare na etapa para realmente criar o domínio gerenciado. Você configura a sincronização escopo que você cria o domínio gerenciado pelo Azure AD DS.

   * [Instale os módulos PowerShell necessários](powershell-create-instance.md#prerequisites).
   * [Crie o principal de serviço necessário e o grupo Azure AD para acesso administrativo](powershell-create-instance.md#create-required-azure-ad-resources).
   * [Crie recursos de suporte do Azure como uma rede virtual e sub-redes](powershell-create-instance.md#create-supporting-azure-resources).

1. Determine os grupos e usuários que eles contêm que você deseja sincronizar a partir do Azure AD. Faça uma lista dos nomes de exibição dos grupos para sincronizar com o Azure AD DS.

1. Execute o [script da seção anterior](#powershell-script-for-scoped-synchronization) e use o parâmetro *-groupsToAdd* para passar a lista de grupos para sincronizar.

   > [!WARNING]
   > Você deve incluir o grupo *Administradores AAD DC* na lista de grupos para sincronização escopo. Se você não incluir esse grupo, o domínio gerenciado pelo Azure AD DS será inutilizável.

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

1. Agora crie o domínio gerenciado do Azure AD DS e habilite a sincronização escopo baseada em grupo. Inclua *"filteredSync" = "Ativado"* no parâmetro *-Propriedades.*

    Defina seu ID de assinatura do Azure e, em seguida, forneça um nome para o domínio gerenciado, como *aaddscontoso.com*. Você pode obter seu ID de assinatura usando o [cmdlet Get-AzSubscription.][Get-AzSubscription] Defina o nome do grupo de recursos, o nome da rede virtual e a região para os valores usados nas etapas anteriores para criar os recursos do Azure de suporte:

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "aaddscontoso.com"
   $ResourceGroupName = "myResourceGroup"
   $VnetName = "myVnet"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -Force -Verbose
   ```

Leva alguns minutos para criar o recurso e retornar o controle ao prompt powershell. O domínio gerenciado pelo Azure AD DS continua a ser provisionado em segundo plano e pode levar até uma hora para concluir a implantação. No portal Azure, a página **Visão geral** do domínio gerenciado pelo Azure AD DS mostra o status atual durante esta fase de implantação.

Quando o portal Azure mostra que o domínio gerenciado pelo Azure AD DS terminou o provisionamento, as seguintes tarefas precisam ser concluídas:

* Atualize as configurações de DNS da rede virtual, de modo que as máquinas virtuais possam encontrar o domínio gerenciado para ingresso no domínio ou autenticação.
    * Para configurar o DNS, selecione o domínio gerenciado pelo Azure AD DS no portal. Na **janela Visão geral,** você é solicitado a configurar automaticamente essas configurações de DNS.
* Se você criou um domínio gerenciado pelo Azure AD DS em uma região que suporta Zonas de disponibilidade, crie um grupo de segurança de rede para restringir o tráfego na rede virtual para o domínio gerenciado pelo Azure AD DS. Um balanceador de carga padrão do Azure é criado que exige que essas regras sejam colocadas. Esse grupo de segurança de rede protege o Azure AD DS e é necessário para que o domínio gerenciado funcione corretamente.
    * Para criar o grupo de segurança da rede e as regras necessárias, selecione o domínio gerenciado pelo Azure AD DS no portal. Na janela **Visão geral,** você é solicitado a criar e configurar automaticamente o grupo de segurança da rede.
* [Habilite a sincronização de senha satisco no Azure AD Domain Services para](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) que os usuários finais possam entrar no domínio gerenciado usando suas credenciais corporativas.

## <a name="modify-scoped-synchronization-using-powershell"></a>Modificar sincronização escopo usando powershell

Para modificar a lista de grupos cujos usuários devem ser sincronizados com o domínio gerenciado pelo Azure AD DS, execute novamente o [script PowerShell](#powershell-script-for-scoped-synchronization) e especifique a nova lista de grupos. No exemplo a seguir, os grupos para sincronizar não incluem mais *GroupName2*e agora incluem *GroupName3*.

> [!WARNING]
> Você deve incluir o grupo *Administradores AAD DC* na lista de grupos para sincronização escopo. Se você não incluir esse grupo, o domínio gerenciado pelo Azure AD DS será inutilizável.

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

Alterar o escopo da sincronização faz com que o domínio Gerenciado pelo Azure AD DS ressincronizar todos os dados. Os objetos que não são mais necessários no domínio gerenciado pelo Azure AD DS são excluídos e a ressincronização pode levar muito tempo para ser concluída.

## <a name="disable-scoped-synchronization-using-powershell"></a>Desativar a sincronização com escopo usando o PowerShell

Para desativar a sincronização escopo baseada em grupo para um domínio gerenciado pelo Azure AD DS, defina *"filteredSync" = "Disabled"* no recurso Azure AD DS e atualize o domínio gerenciado. Quando concluído, todos os usuários e grupos são definidos para sincronizar a partir do Azure AD.

```powershell
// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

Alterar o escopo da sincronização faz com que o domínio Gerenciado pelo Azure AD DS ressincronizar todos os dados. Os objetos que não são mais necessários no domínio gerenciado pelo Azure AD DS são excluídos e a ressincronização pode levar muito tempo para ser concluída.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o processo de sincronização, consulte [Entender a sincronização no Azure AD Domain Services](synchronization.md).

<!-- EXTERNAL LINKS -->
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
