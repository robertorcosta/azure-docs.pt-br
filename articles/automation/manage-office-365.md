---
title: Gerencie os serviços do Office 365 usando o Azure Automation
description: Informa como usar o Azure Automation para gerenciar serviços de assinatura do Office 365.
services: automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9cb505ced907b143fbd6a5f4f30c818092005bb8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550416"
---
# <a name="manage-office-365-services-using-azure-automation"></a>Gerencie os serviços do Office 365 usando o Azure Automation

Você pode usar o Azure Automation para o gerenciamento de serviços de assinatura do Office 365, para produtos como Microsoft Word e Microsoft Outlook. As interações com o Office 365 são habilitadas pelo [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). Consulte [Use o Azure AD no Azure Automation para autenticar o Azure](automation-use-azure-ad.md).

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Pré-requisitos

Você precisa do seguinte para gerenciar os serviços de assinatura do Office 365 no Azure Automation.

* Uma assinatura do Azure. Consulte [o guia de decisão da assinatura](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/).
* Um objeto de Automação no Azure para manter as credenciais da conta de usuário e os runbooks. Veja [uma introdução ao Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro).
* Azure AD. Consulte [Use o Azure AD no Azure Automation para autenticar o Azure](automation-use-azure-ad.md).
* Um inquilino do Office 365, com uma conta. Consulte [Configurar seu inquilino do Office 365](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-developer-tenant).

## <a name="installing-the-msonline-and-msonlineext-modules"></a>Instalando os módulos MSOnline e MSOnlineExt

O uso do Office 365 no Azure Automation requer o`MSOnline` Microsoft Azure Active Directory para Windows PowerShell (módulo). Você também precisará do [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35)módulo , que simplifica o gerenciamento do Azure AD em ambientes únicos e multilocatários. Instale os módulos conforme descrito no [Use Azure AD no Azure Automation para autenticar no Azure](automation-use-azure-ad.md).

>[!NOTE]
>Para usar o MSOnline PowerShell, você deve ser um membro do Azure AD. Os usuários convidados não podem usar o módulo.

## <a name="creating-an-azure-automation-account"></a>Criando uma conta de automação do Azure

Para completar as etapas deste artigo, você precisa de uma conta na Azure Automation. Consulte [Criar uma conta de automação do Azure](automation-quickstart-create-account.md).
 
## <a name="adding-msonline-and-msonlineext-as-assets"></a>Adicionando MSOnline e MSOnlineExt como ativos

Agora adicione os módulos MSOnline e MSOnlineExt instalados para habilitar a funcionalidade do Office 365. Consulte [Gerenciar módulos no Azure Automation](shared-resources/modules.md).

1. No portal Azure, selecione **Contas de Automação**.
2. Escolha sua conta de Automação.
3. Selecione **a Galeria de Módulos** **em Recursos Compartilhados**.
4. Procure o MSOnline.
5. Selecione `MSOnline` o módulo PowerShell e clique **em Importar** para importar o módulo como um ativo.
6. Repita as etapas 4 e `MSOnlineExt` 5 para localizar e importar o módulo. 

## <a name="creating-a-credential-asset-optional"></a>Criação de um ativo de credencial (opcional)

É opcional criar um recurso de credencial para o usuário administrativo do Office 365 que tenha permissões para executar seu script. Ele pode ajudar, no entanto, a evitar expor nomes de usuários e senhas dentro de scripts PowerShell. Para obter instruções, [consulte Criando um ativo de credencial](automation-use-azure-ad.md#creating-a-credential-asset).

## <a name="creating-an-office-365-service-account"></a>Criando uma conta de serviço do Office 365

Para executar os serviços de assinatura do Office 365, você precisa de uma conta de serviço do Office 365 com permissões para fazer o que quiser. Você pode usar uma conta de administrador global, uma conta por serviço, ou ter uma função ou script para executar. De qualquer forma, a conta de serviço requer uma senha complexa e segura. Consulte [Configurar o Office 365 para negócios](https://docs.microsoft.com/microsoft-365/admin/setup/setup?view=o365-worldwide). 

## <a name="connecting-to-the-azure-ad-online-service"></a>Conectando-se ao serviço on-line do Azure AD

>[!NOTE]
>Para usar os cmdlets do módulo MSOnline, você deve executá-los a partir do Windows PowerShell. O PowerShell Core não suporta esses cmdlets.

Você pode usar o módulo MSOnline para se conectar ao Azure AD a partir da assinatura do Office 365. A conexão usa um nome de usuário e senha do Office 365 ou usa autenticação multifatorial (MFA). Você pode se conectar usando o portal Azure ou um prompt de comando Do Windows PowerShell (não precisa ser elevado).

Um exemplo de PowerShell é mostrado abaixo. O [cmdlet Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) solicita credenciais e `Msolcred` armazena-as na variável. Em seguida, o [cmdlet Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) usa as credenciais para se conectar ao serviço on-line do diretório Azure. Se você quiser se conectar a um ambiente `AzureEnvironment` Azure específico, use o parâmetro.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

Se você não receber nenhum erro, você se conectou com sucesso. Um teste rápido é executar um Office 365 `Get-MsolUser`cmdlet, por exemplo, e ver os resultados. Se você receber erros, observe que um problema comum é uma senha incorreta.

>[!NOTE]
>Você também pode usar o módulo AzureRM ou o módulo Az para se conectar ao Azure AD a partir da assinatura do Office 365. O cmdlet de conexão principal é [Connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0). Este cmdlet suporta `AzureEnvironmentName` o parâmetro para ambientes específicos do Office 365.

## <a name="creating-a-powershell-runbook-from-an-existing-script"></a>Criando um runbook PowerShell a partir de um script existente

Você acessa a funcionalidade do Office 365 a partir de um script PowerShell. Aqui está um exemplo de um script `Office-Credentials` para uma `admin@TenantOne.com`credencial nomeada com o nome de usuário de . Ele `Get-AutomationPSCredential` usa para importar a credencial do Office 365.

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

Você pode usar seu script em um runbook da Azure Automation. Por exemplo, usaremos o tipo de runbook do PowerShell.

1. Crie um novo runbook PowerShell. Consulte [Criar um runbook de automação do Azure](https://docs.microsoft.com/azure/automation/automation-quickstart-create-runbook).
2. Na sua conta de Automação, selecione **Runbooks** em **Automação de Processos**.
3. Selecione o novo manual e clique em **Editar**.
4. Copie seu script e cole-o no editor textual para o runbook.
5. Selecione **ATIVOS,** depois expanda **as Credenciais** e verifique se a credencial do Office 365 está lá.
6. Clique em **Salvar**.
7. Selecione **Test pane**, em seguida, clique **em Começar** a testar seu runbook. Consulte [Gerenciar runbooks no Azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks).
8. Quando o teste estiver concluído, saia do painel de teste.

## <a name="publishing-and-scheduling-the-runbook"></a>Publicando e agendando o runbook

Para publicar e, em seguida, agendar seu runbook, consulte [Gerenciar runbooks no Azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks).

## <a name="next-steps"></a>Próximas etapas

* Você pode encontrar informações sobre ativos de credencial de automação em [ativos de credencial no Azure Automation](shared-resources/credentials.md).
* Consulte [Gerenciar módulos no Azure Automation](shared-resources/modules.md) para descobrir como trabalhar com módulos de Automação.
* Para obter uma visão geral do gerenciamento de runbook, consulte [Gerenciar runbooks no Azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks).
* Para saber mais sobre os métodos que podem ser usados para iniciar um runbook no Azure Automation, consulte [Iniciar um runbook no Azure Automation](automation-starting-a-runbook.md).
* Para obter mais informações sobre o PowerShell, incluindo os módulos de referência e aprendizagem do idioma, consulte o [PowerShell Docs](https://docs.microsoft.com/powershell/scripting/overview).