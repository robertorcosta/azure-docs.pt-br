---
title: Habilitar o Azure DS Domain Services usando o PowerShell | Microsoft Docs
description: Saiba como configurar e habilitar o Azure Active Directory Domain Services usando o PowerShell do Azure AD e o Azure PowerShell.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 09/05/2019
ms.author: iainfou
ms.openlocfilehash: e99ad2d53bc26b4e13a34097baaec929058a61a0
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654798"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Habilitar o Azure Active Directory Domain Services usando PowerShell

O Azure AD DS (Azure Active Directory Domain Services) fornece serviços de domínio gerenciado, como ingresso no domínio, Política de Grupo, LDAP e autenticação Kerberos/NTLM, que são totalmente compatíveis com o Active Directory do Windows Server. Você consome esses serviços de domínio sem implantar, gerenciar e aplicar patches aos controladores de domínio por conta própria. O Azure AD DS integra-se com o seu locatário existente do Azure AD. Essa integração permite que os usuários entrem usando suas credenciais corporativas e você pode usar os grupos e as contas de usuário existentes para proteger o acesso aos recursos.

Este artigo mostra como habilitar o Azure AD DS usando o PowerShell.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa dos seguintes recursos:

* Instalar e configurar o PowerShell do Azure.
    * Se necessário, siga as instruções para [instalar o módulo do Azure PowerShell e conectar-se à sua assinatura do Azure](/powershell/azure/install-az-ps).
    * Lembre-se de entrar em sua assinatura do Azure usando o cmdlet [Connect-AzAccount][Connect-AzAccount].
* Instale e configure o Azure AD PowerShell.
    * Se necessário, siga as instruções para [instalar o módulo do Azure AD PowerShell e conectar-se ao Azure AD](/powershell/azure/active-directory/install-adv2).
    * Lembre-se de entrar em seu locatário do Azure AD usando o cmdlet [Connect-AzureAD][Connect-AzureAD].
* É necessário ter privilégios de *administrador global* no locatário do Azure AD para habilitar o Azure AD DS.
* Você precisa de privilégios de *Colaborador* em sua assinatura do Azure para criar os recursos necessários do Azure AD DS.

## <a name="create-required-azure-ad-resources"></a>Criar recursos do Azure AD necessários

O Azure AD DS requer uma entidade de serviço e um grupo do Azure AD. Esses recursos permitem que o domínio gerenciado do Azure AD DS sincronize dados e defina quais usuários têm permissões administrativas no domínio gerenciado.

Primeiro, crie uma entidade de serviço do Azure AD para o Azure AD DS para se comunicar e se autenticar. É usada uma ID de aplicativo específica denominada *Serviços de Controlador de Domínio* com uma ID de *2565bd9d-da50-47d4-8b85-4c97f669dc36*. Não altere essa ID de aplicativo.

Crie uma entidade de serviço do Azure AD usando o cmdlet [New-AzureADServicePrincipal][New-AzureADServicePrincipal]:

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Agora crie um grupo do Azure AD chamado *Administradores do AAD DC*. Os usuários adicionados a esse grupo recebem permissões para executar tarefas de administração no domínio gerenciado do Azure AD DS.

Crie o grupo *Administradores do AAD DC* usando o cmdlet [New-AzureADGroup][New-AzureADGroup]:

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Com o grupo *Administradores do AAD DC* criado, adicione um usuário a ele usando o cmdlet [Add-AzureADGroupMember][Add-AzureADGroupMember]. Primeiro, você obtém a ID de objeto do grupo *Administradores do AAD DC* usando o cmdlet [Get-AzureADGroup][Get-AzureADGroup] e, em seguida, a ID de objeto do usuário desejado usando o cmdlet [Get-AzureADUser][Get-AzureADUser].

No exemplo a seguir, a ID de objeto do usuário para a conta com um UPN de `admin@aaddscontoso.onmicrosoft.com`. Substitua essa conta de usuário pelo UPN do usuário que você deseja adicionar ao grupo *Administradores do AAD DC*:

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@aaddscontoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="create-supporting-azure-resources"></a>Criar recursos de suporte do Azure

Primeiro, registre o provedor de recursos do Azure AD Domain Services usando o cmdlet [Register-AzResourceProvider][Register-AzResourceProvider]:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Em seguida, crie um grupo de recursos usando o cmdlet [New-AzResourceGroup][New-AzResourceGroup]. No exemplo a seguir, o grupo de recursos é chamado *myResourceGroup* e é criado na região *westus*. Use seu nome e sua região desejada:

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Crie a rede virtual e as sub-redes para o Azure AD Domain Services. Duas sub-redes são criadas: uma para *DomainServices* e outra para *Workloads*. O Azure AD DS é implantado na sub-rede *DomainServices* dedicada. Não implante outros aplicativos ou cargas de trabalho na sub-rede. Use a *Workloads* separada ou outras sub-redes para o restante de suas VMs.

Crie as sub-redes usando o cmdlet [New-AzVirtualNetworkSubnetConfig][New-AzVirtualNetworkSubnetConfig] e, em seguida, crie o rede virtual usando o cmdlet [New-AzVirtualNetwork][New-AzVirtualNetwork].

```powershell
$VnetName = "myVnet"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet= New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```

## <a name="create-an-azure-ad-ds-managed-domain"></a>Criar um domínio gerenciado do Azure AD DS

Agora vamos criar um domínio gerenciado do Azure AD DS. Defina sua ID de assinatura do Azure e forneça um nome para o domínio gerenciado, como *aaddscontoso.com*. Você pode obter a ID da assinatura usando o cmdlet [Get-AzSubscription][Get-AzSubscription].

Se você escolher uma região com suporte a Zonas de Disponibilidade, os recursos do Azure AD DS serão distribuídos entre as zonas para proporcionar redundância adicional.

As Zonas de Disponibilidade são locais físicos exclusivos em uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas.

Não é preciso configurar nada para que o Azure AD DS seja distribuído entre as zonas. A plataforma do Azure lida automaticamente com a distribuição de recursos na zona. Para obter mais informações e ver a disponibilidade da região, confira [O que são as Zonas de Disponibilidade no Azure?][availability-zones].

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

Leva alguns minutos para criar o recurso e retornar o controle para o prompt do PowerShell. O domínio gerenciado do Azure AD DS continua sendo provisionado em segundo plano e a implantação pode levar até uma hora para ser concluída. No portal do Azure, a página **Visão Geral** do seu domínio gerenciado do Azure AD DS mostra o status atual durante essa fase de implantação.

Quando o portal do Azure mostra que o domínio gerenciado do Azure AD DS concluiu o provisionamento, as seguintes tarefas precisam ser concluídas:

* Atualize as configurações de DNS da rede virtual, de modo que as máquinas virtuais possam encontrar o domínio gerenciado para ingresso no domínio ou autenticação.
    * Para configurar o DNS, selecione o domínio gerenciado do Azure AD DS no portal. Na janela **Visão Geral**, você deve definir automaticamente essas configurações de DNS.
* Se você tiver criado um domínio gerenciado do Azure AD DS em uma região que dê suporte a Zonas de Disponibilidade, crie um grupo de segurança de rede para restringir o tráfego na rede virtual para o domínio gerenciado do Azure AD DS. Um Azure Standard Load Balancer é criado e exige que essas regras sejam implementadas. Esse grupo de segurança de rede protege o Azure AD DS e é necessário para que o domínio gerenciado funcione corretamente.
    * Para criar o grupo de segurança de rede e as regras necessárias, selecione seu domínio gerenciado do Azure AD DS no portal. Na janela **Visão Geral**, você deve criar e configurar automaticamente o grupo de segurança de rede.
* [Habilite a sincronização de senha para o Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) para que os usuários possam entrar no domínio gerenciado usando as credenciais corporativas deles.

## <a name="complete-powershell-script"></a>Concluir script do PowerShell

O script completo do PowerShell a seguir combina todas as tarefas mostradas neste artigo. Copie o script e salve-o em um arquivo com uma extensão `.ps1`. Execute o script em um console local do PowerShell ou no [Azure Cloud Shell][cloud-shell].

> [!NOTE]
> Para habilitar o Azure AD DS, você deve ser um administrador global do locatário do Azure AD. Você também precisa, pelo menos, de privilégios de *Colaborador* na assinatura do Azure.

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@aaddscontoso.onmicrosoft.com"
$ResourceGroupName = "myResourceGroup"
$VnetName = "myVnet"
$AzureLocation = "westus"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Connect-AzAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"

# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"

# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq '$AaddsAdminUserUpn'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId

# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

Leva alguns minutos para criar o recurso e retornar o controle para o prompt do PowerShell. O domínio gerenciado do Azure AD DS continua sendo provisionado em segundo plano e a implantação pode levar até uma hora para ser concluída. No portal do Azure, a página **Visão Geral** do seu domínio gerenciado do Azure AD DS mostra o status atual durante essa fase de implantação.

Quando o portal do Azure mostra que o domínio gerenciado do Azure AD DS concluiu o provisionamento, as seguintes tarefas precisam ser concluídas:

* Atualize as configurações de DNS da rede virtual, de modo que as máquinas virtuais possam encontrar o domínio gerenciado para ingresso no domínio ou autenticação.
    * Para configurar o DNS, selecione o domínio gerenciado do Azure AD DS no portal. Na janela **Visão Geral**, você deve definir automaticamente essas configurações de DNS.
* Se você tiver criado um domínio gerenciado do Azure AD DS em uma região que dê suporte a Zonas de Disponibilidade, crie um grupo de segurança de rede para restringir o tráfego na rede virtual para o domínio gerenciado do Azure AD DS. Um Azure Standard Load Balancer é criado e exige que essas regras sejam implementadas. Esse grupo de segurança de rede protege o Azure AD DS e é necessário para que o domínio gerenciado funcione corretamente.
    * Para criar o grupo de segurança de rede e as regras necessárias, selecione seu domínio gerenciado do Azure AD DS no portal. Na janela **Visão Geral**, você deve criar e configurar automaticamente o grupo de segurança de rede.
* [Habilite a sincronização de senha para o Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) para que os usuários possam entrar no domínio gerenciado usando as credenciais corporativas deles.

## <a name="next-steps"></a>Próximas etapas

Para ver o domínio gerenciado do Azure AD DS em ação, você pode [ingressar uma VM do Windows em domínio][windows-join], [configurar o LDAP Seguro][tutorial-ldaps] e [configurar a sincronização de hash de senha][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[New-AzVirtualNetworkSubnetConfig]: /powershell/module/Az.Network/New-AzVirtualNetworkSubnetConfig
[New-AzVirtualNetwork]: /powershell/module/Az.Network/New-AzVirtualNetwork
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: /azure/cloud-shell/cloud-shell-windows-users
[availability-zones]: ../availability-zones/az-overview.md
