---
title: Gerenciar serviços do Office 365 usando a Automação do Azure
description: Informa como usar a automação do Azure para gerenciar serviços de assinatura do Office 365.
services: automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9cb505ced907b143fbd6a5f4f30c818092005bb8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80550416"
---
# <a name="manage-office-365-services-using-azure-automation"></a>Gerenciar serviços do Office 365 usando a Automação do Azure

Você pode usar a automação do Azure para gerenciamento de serviços de assinatura do Office 365, para produtos como o Microsoft Word e o Microsoft Outlook. As interações com o Office 365 são habilitadas pelo [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). Consulte [usar o Azure ad na automação do Azure para autenticar no Azure](automation-use-azure-ad.md).

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Pré-requisitos

Você precisa do seguinte para gerenciar os serviços de assinatura do Office 365 na automação do Azure.

* Uma assinatura do Azure. Consulte [Guia de decisão da assinatura](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/).
* Um objeto de automação no Azure para manter as credenciais de conta de usuário e os runbooks. Consulte [uma introdução à automação do Azure](https://docs.microsoft.com/azure/automation/automation-intro).
* Azure AD. Consulte [usar o Azure ad na automação do Azure para autenticar no Azure](automation-use-azure-ad.md).
* Um locatário do Office 365, com uma conta. Consulte [configurar seu locatário do Office 365](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-developer-tenant).

## <a name="installing-the-msonline-and-msonlineext-modules"></a>Instalando os módulos MSOnline e MSOnlineExt

O uso do Office 365 na automação do Azure requer Microsoft Azure Active Directory para o`MSOnline` Windows PowerShell (módulo). Você também precisará do módulo [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35), o que simplifica o gerenciamento do Azure AD em ambientes de único e multilocatário. Instale os módulos conforme descrito em [usar o Azure ad na automação do Azure para autenticar no Azure](automation-use-azure-ad.md).

>[!NOTE]
>Para usar o MSOnline PowerShell, você deve ser um membro do Azure AD. Os usuários convidados não podem usar o módulo.

## <a name="creating-an-azure-automation-account"></a>Criando uma conta de automação do Azure

Para concluir as etapas neste artigo, você precisa de uma conta na automação do Azure. Consulte [criar uma conta de automação do Azure](automation-quickstart-create-account.md).
 
## <a name="adding-msonline-and-msonlineext-as-assets"></a>Adicionando MSOnline e MSOnlineExt como ativos

Agora, adicione os módulos MSOnline e MSOnlineExt instalados para habilitar a funcionalidade do Office 365. Consulte [gerenciar módulos na automação do Azure](shared-resources/modules.md).

1. No portal do Azure, selecione **contas de automação**.
2. Escolha sua conta de automação.
3. Selecione **Galeria de módulos** em **recursos compartilhados**.
4. Procure MSOnline.
5. Selecione o `MSOnline` módulo PowerShell e clique em **importar** para importar o módulo como um ativo.
6. Repita as etapas 4 e 5 para localizar e importar `MSOnlineExt` o módulo. 

## <a name="creating-a-credential-asset-optional"></a>Criando um ativo de credencial (opcional)

É opcional criar um ativo de credencial para o usuário administrativo do Office 365 que tem permissões para executar o script. No entanto, pode ajudar a manter a exposição de nomes de usuário e senhas dentro de scripts do PowerShell. Para obter instruções, consulte [criando um ativo de credencial](automation-use-azure-ad.md#creating-a-credential-asset).

## <a name="creating-an-office-365-service-account"></a>Criando uma conta de serviço do Office 365

Para executar os serviços de assinatura do Office 365, você precisa de uma conta de serviço do Office 365 com permissões para fazer o que você deseja. Você pode usar uma conta de administrador global, uma conta por serviço ou ter uma função ou script para executar. Em qualquer caso, a conta de serviço requer uma senha complexa e segura. Consulte [Configurar o Office 365 para empresas](https://docs.microsoft.com/microsoft-365/admin/setup/setup?view=o365-worldwide). 

## <a name="connecting-to-the-azure-ad-online-service"></a>Conectando-se ao serviço online do Azure AD

>[!NOTE]
>Para usar os cmdlets do módulo MSOnline, você deve executá-los do Windows PowerShell. O PowerShell Core não oferece suporte a esses cmdlets.

Você pode usar o módulo MSOnline para se conectar ao Azure AD da assinatura do Office 365. A conexão usa um nome de usuário e uma senha do Office 365 ou usa a MFA (autenticação multifator). Você pode se conectar usando o portal do Azure ou um prompt de comando do Windows PowerShell (não precisa ser elevado).

Um exemplo do PowerShell é mostrado abaixo. O cmdlet [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) solicita credenciais e as armazena na `Msolcred` variável. Em seguida, o cmdlet [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) usa as credenciais para se conectar ao serviço online do Azure Directory. Se você quiser se conectar a um ambiente específico do Azure, use `AzureEnvironment` o parâmetro.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

Se você não receber nenhum erro, você se conectou com êxito. Um teste rápido é executar um cmdlet do Office 365, por exemplo `Get-MsolUser`, e ver os resultados. Se você receber erros, observe que um problema comum é uma senha incorreta.

>[!NOTE]
>Você também pode usar o módulo AzureRM ou o módulo AZ para se conectar ao Azure AD da assinatura do Office 365. O cmdlet de conexão principal é [Connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0). Este cmdlet dá suporte `AzureEnvironmentName` ao parâmetro para ambientes específicos do Office 365.

## <a name="creating-a-powershell-runbook-from-an-existing-script"></a>Criando um runbook do PowerShell com base em um script existente

Você acessa a funcionalidade do Office 365 a partir de um script do PowerShell. Aqui está um exemplo de um script para uma credencial `Office-Credentials` chamada com o nome `admin@TenantOne.com`de usuário de. Ele usa `Get-AutomationPSCredential` para importar a credencial do Office 365.

```powershell
$emailFromAddress = "admin@TenantOne.com" 
$emailToAddress = "servicedesk@TenantOne.com" 
$emailSMTPServer = "outlook.office365.com" 
$emailSubject = "Office 365 License Report" 

$credObject = Get-AutomationPSCredential -Name "Office-Credentials" 
Connect-MsolService -Credential $credObject

$O365Licenses = Get-MsolAccountSku | Out-String 
Send-MailMessage -Credential $credObject -From $emailFromAddress -To $emailToAddress -Subject $emailSubject -Body 

$O365Licenses -SmtpServer $emailSMTPServer -UseSSL
```

## <a name="running-the-script-in-a-runbook"></a>Executando o script em um runbook

Você pode usar o script em um runbook de automação do Azure. Para fins de exemplo, usaremos o tipo de runbook do PowerShell.

1. Crie um novo runbook do PowerShell. Consulte [criar um runbook de automação do Azure](https://docs.microsoft.com/azure/automation/automation-quickstart-create-runbook).
2. Em sua conta de automação, selecione **Runbooks** em **automação de processo**.
3. Selecione o novo runbook e clique em **Editar**.
4. Copie o script e cole-o no editor de texto do runbook.
5. Selecione **ativos**e, em seguida, expanda **credenciais** e verifique se a credencial do Office 365 está lá.
6. Clique em **Salvar**.
7. Selecione o **painel teste**e clique em **Iniciar** para começar a testar seu runbook. Consulte [gerenciar runbooks na automação do Azure](https://docs.microsoft.com/azure/automation/manage-runbooks).
8. Quando o teste estiver concluído, saia do painel de teste.

## <a name="publishing-and-scheduling-the-runbook"></a>Publicando e agendando o runbook

Para publicar e, em seguida, agendar o runbook, consulte [gerenciar runbooks na automação do Azure](https://docs.microsoft.com/azure/automation/manage-runbooks).

## <a name="next-steps"></a>Próximas etapas

* Você pode encontrar informações sobre ativos de credenciais de automação em [ativos de credencial na automação do Azure](shared-resources/credentials.md).
* Consulte [gerenciar módulos na automação do Azure](shared-resources/modules.md) para saber como trabalhar com módulos de automação.
* Para obter uma visão geral do gerenciamento de runbook, consulte [gerenciar runbooks na automação do Azure](https://docs.microsoft.com/azure/automation/manage-runbooks).
* Para saber mais sobre os métodos que podem ser usados para iniciar um runbook na automação do Azure, consulte [iniciando um runbook na automação do Azure](automation-starting-a-runbook.md).
* Para obter mais informações sobre o PowerShell, incluindo referência de linguagem e módulos de aprendizado, consulte os [documentos do PowerShell](https://docs.microsoft.com/powershell/scripting/overview).