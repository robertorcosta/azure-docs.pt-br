---
title: Usar o Azure AD na Automação do Azure para autenticação no Azure
description: Saiba como usar o Azure AD na automação do Azure como o provedor para autenticação no Azure.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 90338a1ffa79e6c2347832cb2e74633db02ec72d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80548332"
---
# <a name="use-azure-ad-in-azure-automation-to-authenticate-to-azure"></a>Usar o Azure AD na Automação do Azure para autenticação no Azure

O serviço do [Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) permite uma série de tarefas administrativas, como gerenciamento de usuários, gerenciamento de domínio e configuração de logon único. Este artigo descreve como usar o Azure AD na automação do Azure como o provedor para autenticação no Azure. 

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](automation-update-azure-modules.md).

## <a name="installing-azure-ad-modules"></a>Instalando módulos do Azure AD

Você pode habilitar o Azure AD por meio dos seguintes módulos do PowerShell:

* Azure Active Directory o PowerShell for Graph (módulos AzureRM e AZ). A automação do Azure é fornecida com o módulo AzureRM e sua atualização recente, o módulo AZ. A funcionalidade inclui autenticação não interativa para o Azure usando a autenticação baseada em credencial do usuário do Azure AD (OrgId). Consulte [Azure ad 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76).

* Microsoft Azure Active Directory para Windows PowerShell (módulo MSOnline). Este módulo permite interações com a Microsoft Online, incluindo o Office 365.

>[!NOTE]
>O PowerShell Core não dá suporte ao módulo MSOnline. Para usar os cmdlets do módulo, você deve executá-los do Windows PowerShell. Você é incentivado a usar os módulos mais recentes do Azure Active Directory PowerShell para Graph em vez do módulo MSOnline. 

### <a name="preinstallation"></a>Preinstallation

Antes de instalar os módulos do Azure AD em seu computador:

* Desinstale todas as versões anteriores do módulo AzureRM/AZ e o módulo MSOnline. 

* Desinstale o assistente de conexão do Microsoft Online Services para garantir a operação correta dos novos módulos do PowerShell.  

### <a name="install-the-azurerm-and-az-modules"></a>Instalar os módulos AzureRM e AZ

>[!NOTE]
>Para trabalhar com esses módulos, você deve usar o PowerShell versão 5,1 ou posterior com uma versão de 64 bits do Windows. 

1. Instale o Windows Management Framework (WMF) 5,1. Consulte [instalar e configurar o WMF 5,1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure?view=powershell-7).

2. Instale o AzureRM e/ou AZ usando as instruções em [instalar o Azure PowerShell no Windows com o PowerShellGet](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0).

### <a name="install-the-msonline-module"></a>Instalar o módulo MSOnline

>[!NOTE]
>Para instalar o módulo MSOnline, você deve ser membro de uma função de administrador do Office 365. Consulte [sobre funções de administrador](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide).

1. Verifique se o recurso Microsoft .NET Framework 3.5. x está habilitado no seu computador. É provável que o computador tenha uma versão mais recente instalada, mas a compatibilidade com versões anteriores do .NET Framework pode ser habilitada ou desabilitada. 

2. Instale a versão de 64 bits do [Assistente de conexão do Microsoft Online Services](https://www.microsoft.com/download/details.aspx?id=41950).

3. Execute o Windows PowerShell como administrador para criar um prompt de comando elevado do Windows PowerShell.

4. Implante Azure Active Directory do [MSOnline 1,0](http://www.powershellgallery.com/packages/MSOnline/1.0).

5. Se você for solicitado a instalar o provedor do NuGet, digite Y e pressione ENTER.

6. Se você for solicitado a instalar o módulo do [PSGallery](https://www.powershellgallery.com/), digite Y e pressione Enter.

### <a name="install-support-for-pscredential"></a>Instalar suporte para PSCredential

A automação do Azure usa a classe [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) para representar um ativo de credencial. Os scripts recuperam `PSCredential` objetos usando `Get-AutomationPSCredential` o cmdlet. Para obter mais informações, consulte [ativos de credencial na automação do Azure](shared-resources/credentials.md).

## <a name="assigning-a-subscription-administrator"></a>Atribuindo um administrador de assinatura

Você deve atribuir um administrador para a assinatura do Azure. Essa pessoa tem a função de proprietário para o escopo da assinatura. Consulte [controle de acesso baseado em função na automação do Azure](automation-role-based-access-control.md). 

## <a name="changing-the-azure-ad-users-password"></a>Alterando a senha do usuário do Azure AD

Para alterar a senha do usuário do Azure AD:

1. Faça logoff do Azure.

2. Peça ao administrador que faça logon no Azure como o usuário do Azure AD acabou de criar, usando o nome de usuário completo (incluindo o domínio) e uma senha temporária. 

3. Peça ao administrador para alterar a senha quando solicitado.

## <a name="configuring-azure-automation-to-use-the-azure-ad-user-to-manage-the-azure-subscription"></a>Configurando a automação do Azure para usar o usuário do Azure AD para gerenciar a assinatura do Azure

Para que a automação do Azure se comunique com o Azure AD, você deve recuperar as credenciais associadas à conexão do Azure com o Azure AD. Exemplos dessas credenciais são ID de locatário, ID de assinatura e similares. Para obter mais informações sobre a conexão entre o Azure e o Azure AD, consulte [conectar sua organização ao Azure Active Directory](https://docs.microsoft.com/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=azure-devops).

## <a name="creating-a-credential-asset"></a>Criando um ativo de credencial

Com as credenciais do Azure para o Azure AD disponíveis, é hora de criar um ativo de credencial de automação do Azure para armazenar com segurança as credenciais do Azure AD para que os runbooks e os scripts de DSC (configuração de estado desejado) possam acessá-las. Você pode fazer isso usando os cmdlets portal do Azure ou PowerShell.

### <a name="create-the-credential-asset-in-azure-portal"></a>Criar o ativo de credencial no portal do Azure

Você pode usar o portal do Azure para criar o ativo de credencial. Execute esta operação da sua conta de automação usando **credenciais** em **recursos compartilhados**. Consulte [ativos de credencial na automação do Azure](shared-resources/credentials.md).

### <a name="create-the-credential-asset-with-windows-powershell"></a>Criar o ativo de credencial com o Windows PowerShell

Para preparar um novo ativo de credencial no Windows PowerShell, seu script cria `PSCredential` primeiro um objeto usando o nome de usuário e a senha atribuídos. Em seguida, o script usa esse objeto para criar o ativo por meio de uma chamada para o cmdlet [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) . Como alternativa, o script pode chamar o cmdlet [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) para solicitar que o usuário digite um nome e uma senha. Consulte [ativos de credencial na automação do Azure](shared-resources/credentials.md). 

## <a name="managing-azure-resources-from-an-azure-automation-runbook"></a>Gerenciando recursos do Azure de um runbook de automação do Azure

Você pode gerenciar recursos do Azure de runbooks de automação do Azure usando o ativo de credencial. Veja abaixo um exemplo de runbook do PowerShell que coleta o ativo de credencial a ser usado para parar e iniciar máquinas virtuais em uma assinatura do Azure. Este runbook usa `Get-AutomationPSCredential` primeiro para recuperar a credencial a ser usada para autenticar no Azure. Em seguida, ele chama o cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.6.1) para se conectar ao Azure usando a credencial. O script usa o cmdlet [Select-AzureSubscription](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0) para escolher a assinatura com a qual trabalhar. 

```azurepowershell
Workflow Stop-Start-AzureVM 
{ 
    Param 
    (    
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureSubscriptionId, 
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureVMList="All", 
        [Parameter(Mandatory=$true)][ValidateSet("Start","Stop")] 
        [String] 
        $Action 
    ) 
     
    $credential = Get-AutomationPSCredential -Name 'AzureCredential' 
    Connect-AzAccount -Credential $credential 
    Select-AzureSubscription -SubscriptionId $AzureSubscriptionId 
 
    if($AzureVMList -ne "All") 
    { 
        $AzureVMs = $AzureVMList.Split(",") 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
    } 
    else 
    { 
        $AzureVMs = (Get-AzVM).Name 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
 
    } 
 
    foreach($AzureVM in $AzureVMsToHandle) 
    { 
        if(!(Get-AzVM | ? {$_.Name -eq $AzureVM})) 
        { 
            throw " AzureVM : [$AzureVM] - Does not exist! - Check your inputs " 
        } 
    } 
 
    if($Action -eq "Stop") 
    { 
        Write-Output "Stopping VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Stop-AzVM -Force 
        } 
    } 
    else 
    { 
        Write-Output "Starting VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Start-AzVM 
        } 
    } 
}
```  

## <a name="next-steps"></a>Próximas etapas

* Você pode encontrar informações sobre ativos de credenciais de automação em [ativos de credencial na automação do Azure](shared-resources/credentials.md).
* Consulte [gerenciar módulos na automação do Azure](shared-resources/modules.md) para saber como trabalhar com módulos de automação.
* Para saber mais sobre os métodos que podem ser usados para iniciar um runbook na automação do Azure, consulte [iniciando um runbook na automação do Azure](automation-starting-a-runbook.md).
* Para obter mais informações sobre o PowerShell, incluindo referência de linguagem e módulos de aprendizado, consulte os [documentos do PowerShell](https://docs.microsoft.com/powershell/scripting/overview).