---
title: Use o Azure AD no Azure Automation para autenticar o Azure
description: Aprenda a usar o Azure AD no Azure Automation como provedor de autenticação do Azure.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 90338a1ffa79e6c2347832cb2e74633db02ec72d
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548332"
---
# <a name="use-azure-ad-in-azure-automation-to-authenticate-to-azure"></a>Use o Azure AD no Azure Automation para autenticar o Azure

O serviço [Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) permite uma série de tarefas administrativas, como gerenciamento de usuário, gerenciamento de domínio e configuração de login único. Este artigo descreve como usar o Azure AD dentro do Azure Automation como o provedor de autenticação do Azure. 

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

## <a name="installing-azure-ad-modules"></a>Instalando módulos AD do Azure

Você pode habilitar o Azure AD através dos seguintes módulos PowerShell:

* Azure Active Directory PowerShell for Graph (módulos AzureRM e Az). A Azure Automation é fornecido com o módulo AzureRM e sua recente atualização, o módulo Az. A funcionalidade inclui autenticação não interativa no Azure usando autenticação baseada em credenciais do usuário Azure AD (OrgId). Consulte [Azure AD 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76).

* Microsoft Azure Active Directory for Windows PowerShell (módulo MSOnline). Este módulo permite interações com o Microsoft Online, incluindo o Office 365.

>[!NOTE]
>O PowerShell Core não suporta o módulo MSOnline. Para usar os cmdlets do módulo, você deve executá-los a partir do Windows PowerShell. Você é encorajado a usar o mais novo PowerShell do Azure Active Directory para módulos gráficos em vez do módulo MSOnline. 

### <a name="preinstallation"></a>Pré-instalação

Antes de instalar os módulos AD do Azure no seu computador:

* Desinstale todas as versões anteriores do módulo AzureRM/Az e do módulo MSOnline. 

* Desinstale o Assistente de Login de Serviços Online da Microsoft para garantir o funcionamento correto dos novos módulos PowerShell.  

### <a name="install-the-azurerm-and-az-modules"></a>Instale os módulos AzureRM e Az

>[!NOTE]
>Para trabalhar com esses módulos, você deve usar a versão 5.1 ou posterior do PowerShell com uma versão de 64 bits do Windows. 

1. Instale o Windows Management Framework (WMF) 5.1. Consulte [Instalar e configurar o WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure?view=powershell-7).

2. Instale o AzureRM e/ou Az usando instruções no [Install Azure PowerShell no Windows com o PowerShellGet](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0).

### <a name="install-the-msonline-module"></a>Instale o módulo MSOnline

>[!NOTE]
>Para instalar o módulo MSOnline, você deve ser membro de uma função de admin do Office 365. Consulte [Sobre os papéis de admin](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide).

1. Certifique-se de que o recurso Microsoft .NET Framework 3.5.x esteja ativado no seu computador. É provável que seu computador tenha uma versão mais recente instalada, mas a compatibilidade retrógrada com versões mais antigas do .NET Framework pode ser ativada ou desativada. 

2. Instale a versão de 64 bits do [Microsoft Online Services Sign-in Assistant](https://www.microsoft.com/download/details.aspx?id=41950).

3. Execute o Windows PowerShell como administrador para criar um prompt de comando PowerShell elevado do Windows.

4. Implantar o Diretório Ativo do Azure do [MSOnline 1.0](http://www.powershellgallery.com/packages/MSOnline/1.0).

5. Se você for solicitado a instalar o provedor NuGet, digite Y e pressione ENTER.

6. Se você for solicitado a instalar o módulo do [PSGallery,](https://www.powershellgallery.com/)tipo Y e pressione ENTER.

### <a name="install-support-for-pscredential"></a>Instale o suporte para PSCredential

A Azure Automation usa a classe [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) para representar um ativo de credencial. Seus scripts `PSCredential` recuperam `Get-AutomationPSCredential` objetos usando o cmdlet. Para obter mais informações, consulte [os ativos credenciais na Azure Automation](shared-resources/credentials.md).

## <a name="assigning-a-subscription-administrator"></a>Atribuindo um administrador de assinatura

Você deve atribuir um administrador para a assinatura do Azure. Essa pessoa tem o papel de Proprietário para o escopo da assinatura. Consulte [o controle de acesso baseado em função no Azure Automation](automation-role-based-access-control.md). 

## <a name="changing-the-azure-ad-users-password"></a>Alterando a senha do usuário do Azure AD

Para alterar a senha do usuário do Azure AD:

1. Saia do Azure.

2. Faça com que o administrador faça login no Azure como o usuário Azure AD acabou de criar, usando o nome de usuário completo (incluindo o domínio) e uma senha temporária. 

3. Peça ao administrador para alterar a senha quando solicitado.

## <a name="configuring-azure-automation-to-use-the-azure-ad-user-to-manage-the-azure-subscription"></a>Configurando o Azure Automation para usar o usuário Azure AD para gerenciar a assinatura do Azure

Para que a Azure Automation se comunique com o Azure AD, você deve recuperar as credenciais associadas à conexão Azure com o Azure AD. Exemplos dessas credenciais são ID de inquilino, ID de assinatura e afins. Para obter mais informações sobre a conexão entre o Azure e o Azure AD, consulte [Conecte sua organização ao Azure Active Directory](https://docs.microsoft.com/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=azure-devops).

## <a name="creating-a-credential-asset"></a>Criando um ativo de credencial

Com as credenciais do Azure para Azure AD disponíveis, é hora de criar um recurso de credencial de Automação Azure para armazenar com segurança as credenciais do Azure AD para que os runbooks e scripts De configuração do Estado do Desejo (DSC) possam acessá-los. Você pode fazer isso usando o portal Azure ou os cmdlets PowerShell.

### <a name="create-the-credential-asset-in-azure-portal"></a>Crie o ativo de credencial no portal Azure

Você pode usar o portal Azure para criar o ativo de credencial. Faça esta operação a partir de sua conta de automação usando **credenciais** em **Recursos Compartilhados**. Consulte [os ativos credenciais na Azure Automation](shared-resources/credentials.md).

### <a name="create-the-credential-asset-with-windows-powershell"></a>Crie o ativo de credencial com o Windows PowerShell

Para preparar um novo recurso de credencial no Windows `PSCredential` PowerShell, seu script primeiro cria um objeto usando o nome de usuário e senha atribuídos. Em seguida, o script usa esse objeto para criar o recurso através de uma chamada para o cmdlet [New-AzureAutomationCredential.](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) Alternativamente, o script pode chamar o [cmdlet Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) para solicitar ao usuário que digite um nome e senha. Consulte [os ativos credenciais na Azure Automation](shared-resources/credentials.md). 

## <a name="managing-azure-resources-from-an-azure-automation-runbook"></a>Gerenciamento de recursos do Azure a partir de um runbook do Azure Automation

Você pode gerenciar os recursos do Azure nos runbooks do Azure Automation usando o ativo de credencial. Abaixo está um exemplo do runbook PowerShell que coleta o recurso de credencial a ser usado para parar e iniciar máquinas virtuais em uma assinatura do Azure. Este runbook `Get-AutomationPSCredential` usa primeiro para recuperar a credencial para usar para autenticar o Azure. Em seguida, ele chama o cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.6.1) para se conectar ao Azure usando a credencial. O script usa o [cmdlet Select-AzureSubscription](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0) para escolher a assinatura para trabalhar. 

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

* Você pode encontrar informações sobre ativos de credencial de automação em [ativos de credencial no Azure Automation](shared-resources/credentials.md).
* Consulte [Gerenciar módulos no Azure Automation](shared-resources/modules.md) para descobrir como trabalhar com módulos de Automação.
* Para saber mais sobre os métodos que podem ser usados para iniciar um runbook no Azure Automation, consulte [Iniciar um runbook no Azure Automation](automation-starting-a-runbook.md).
* Para obter mais informações sobre o PowerShell, incluindo os módulos de referência e aprendizagem do idioma, consulte o [PowerShell Docs](https://docs.microsoft.com/powershell/scripting/overview).