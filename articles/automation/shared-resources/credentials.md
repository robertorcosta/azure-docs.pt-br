---
title: Gerenciar credenciais no Azure Automation
description: Os ativos de credenciais na Automação do Azure contêm credenciais de segurança que podem ser usadas para a autenticação em recursos acessados pelo runbook ou pela configuração DSC. Este artigo descreve como criar ativos de credenciais e usá-los em um runbook ou uma configuração DSC.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 59e32087d4489cbb155a9cff7d40094c0606c0cf
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732838"
---
# <a name="manage-credentials-in-azure-automation"></a>Gerenciar credenciais no Azure Automation

Um ativo de credencial de automação contém um objeto que contém credenciais de segurança, como um nome de usuário e uma senha. As configurações runbooks e DSC usam cmdlets que aceitam um objeto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) para autenticação. Alternativamente, eles podem extrair o nome `PSCredential` de usuário e senha do objeto para fornecer a algum aplicativo ou serviço que exija autenticação. 

> [!NOTE]
> Os ativos protegidos na Automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados no Azure Automation usando uma chave única gerada para cada conta de Automação. Essa chave é armazenada no Key Vault. Antes de armazenar um ativo seguro, a chave é carregada do Key Vault e usada para criptografar o ativo.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](../automation-update-azure-modules.md).

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="azure-powershell-az-cmdlets-used-for-credential-assets"></a>Cmdlets Azure PowerShell Az usados para ativos de credencial

Como parte do módulo Azure PowerShell Az, os cmdlets na tabela a seguir são usados para criar e gerenciar ativos de credencial de automação com o Windows PowerShell. Eles são enviados no [módulo Az.Automation,](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)que está disponível para uso em runbooks de automação e configurações de DSC. Consulte [o suporte ao módulo Az no Azure Automation](https://docs.microsoft.com/azure/automation/az-modules).

| Cmdlet | Descrição |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Recupera informações sobre um ativo de credencial. Este cmdlet não retorna `PSCredential` um objeto.  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Cria uma nova credencial de Automação. |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Remove uma credencial de Automação. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Define as propriedades de uma credencial de Automação existente. |

## <a name="activities-used-to-access-credentials"></a>Atividades utilizadas para acessar credenciais

As atividades na tabela a seguir são usadas para acessar credenciais em runbooks e configurações de DSC.

| Atividade | Descrição |
|:--- |:--- |
| `Get-AutomationPSCredential` |Obtém uma credencial a ser usada em um runbook ou configuração DSC. A credencial é na forma `PSCredential` de um objeto. Para obter mais informações sobre o cmdlet correspondente a esta atividade, consulte [os ativos do Módulo na Automação Azure](modules.md). |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |Obtém uma credencial com um prompt para nome de usuário e senha. |
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | Cria um ativo de credencial. |

Para `PSCredential` recuperar objetos em seu código, você pode instalar o complemento ISE de automação do Microsoft Azure para o PowerShell ISE. Para obter mais informações, consulte [os ativos do Módulo no Azure Automation](modules.md).

```azurepowershell
Install-Module AzureAutomationAuthoringToolkit -Scope CurrentUser -Force
```

Seu script também pode importar o módulo necessário quando necessário, como no exemplo a seguir: 

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Você deve evitar usar `Name` variáveis no `Get-AutomationPSCredential`parâmetro de . Seu uso pode complicar a descoberta de dependências entre runbooks ou configurações de DSC e ativos de credencial no momento do projeto.

## <a name="python-2-functions-that-access-credentials"></a>Funções Python 2 que acessam credenciais

A função na tabela a seguir é usada para acessar credenciais em um runbook Python 2.

| Função | Descrição |
|:---|:---|
| `automationassets.get_automation_credential` | Recupera informações sobre um ativo de credencial. |

> [!NOTE]
> Importe `automationassets` o módulo na parte superior do seu runbook Python para acessar as funções de ativos.

## <a name="creating-a-new-credential-asset"></a>Criando um novo ativo de credencial

Você pode criar um novo recurso de credencial usando o portal Azure ou usando o Windows PowerShell.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Crie um novo recurso de credencial com o portal Azure

1. A partir de sua conta de automação, selecione **Credenciais** em **Recursos Compartilhados**.
1. Selecione **Adicionar uma credencial**.
2. No painel Nova Credencial, digite um nome de credencial apropriado seguindo seus padrões de nomeação. 
3. Digite seu ID de acesso no campo Nome do **Usuário.** 
4. Para ambos os campos de senha, digite sua chave de acesso secreta.

    ![Criar nova credencial](../media/credentials/credential-create.png)

5. Se a caixa de autenticação de vários fatores for verificada, desfaça-a. 
6. Clique **em Criar** para salvar o novo recurso de credencial.

> [!NOTE]
> O Azure Automation não suporta contas de usuário que usam autenticação multifatorial.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Crie um novo recurso de credencial com o Windows PowerShell

O exemplo a seguir mostra como criar um novo ativo de credencial de Automação. Um `PSCredential` objeto é criado primeiro com o nome e senha e, em seguida, usado para criar o ativo de credencial. Em vez disso, `Get-Credential` você pode usar o cmdlet para solicitar ao usuário que digite um nome e senha.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Usando uma credencial do PowerShell

Uma configuração de runbook ou DSC recupera `Get-AutomationPSCredential` um ativo de credencial com a atividade. Esta atividade recupera `PSCredential` um objeto que você pode usar com uma atividade ou cmdlet que requer uma credencial. Você também pode recuperar as propriedades do objeto de credencial para usar individualmente. O objeto tem propriedades para o nome de usuário e a senha segura. Alternativamente, você pode usar o método [GetNetworkCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0) para recuperar um objeto [NetworkCredential](/dotnet/api/system.net.networkcredential) que representa uma versão não segura da senha.

> [!NOTE]
> `Get-AzAutomationCredential`não recupera `PSCredential` um objeto que pode ser usado para autenticação. Ele só fornece informações sobre a credencial. Se você precisar usar uma credencial em um runbook, `PSCredential` você `Get-AutomationPSCredential`deve recuperá-la como um objeto usando .

### <a name="textual-runbook-example"></a>Exemplo de runbook textual

O exemplo a seguir mostra como usar uma credencial PowerShell em um runbook. Ele recupera a credencial e atribui seu nome de usuário e senha a variáveis.


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Você também pode usar uma credencial para autenticar o Azure com [connect-azAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0). Na maioria das circunstâncias, você deve usar uma [conta Executar como](../manage-runas-account.md) e recuperar a conexão com [get-AzAutomationConnection](../automation-connections.md).


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>Exemplo de runbook gráfico

Você pode `Get-AutomationPSCredential` adicionar uma atividade a um runbook gráfico clicando com o botão direito do mouse na credencial no painel biblioteca do editor gráfico e selecionando **Adicionar à tela**.

![Adicionar a credencial à tela](../media/credentials/credential-add-canvas.png)

A imagem a seguir mostra um exemplo do uso de uma credencial em um runbook gráfico. Neste caso, a credencial fornece autenticação para um runbook para recursos do Azure, conforme descrito no [Use Azure AD no Azure Automation para autenticar o Azure](../automation-use-azure-ad.md). A primeira atividade recupera a credencial que tem acesso à assinatura do Azure. A atividade de conexão da conta usa essa credencial para fornecer autenticação para quaisquer atividades que venham depois dela. Um [link de](../automation-graphical-authoring-intro.md#links-and-workflow) pipeline `Get-AutomationPSCredential` é usado aqui, pois está esperando um único objeto.  

![Adicionar a credencial à tela](../media/credentials/get-credential.png)

## <a name="using-credentials-in-a-dsc-configuration"></a>Usando credenciais em uma configuração DSC

Embora as configurações de DSC no Azure `Get-AutomationPSCredential`Automation possam funcionar com ativos de credencial usando, eles também podem passar ativos de credencial através de parâmetros. Para obter mais informações, veja [Compilando configurações na DSC de Automação do Azure](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python-2"></a>Usando credenciais no Python 2

O exemplo a seguir mostra um exemplo de acesso a credenciais em runbooks Python 2.


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre links na autoria gráfica, consulte [Links em autoria gráfica](../automation-graphical-authoring-intro.md#links-and-workflow).
* Para entender os diferentes métodos de autenticação para automação, consulte [Azure Automation Security](../automation-security-overview.md).
* Para começar com runbooks gráficos, consulte [Meu primeiro runbook gráfico](../automation-first-runbook-graphical.md).
* Para começar com os runbooks do fluxo de trabalho PowerShell, consulte [Meu primeiro runbook de fluxo de trabalho PowerShell](../automation-first-runbook-textual.md).
* Para começar com os runbooks do Python 2, consulte [Meu primeiro runbook Python 2](../automation-first-runbook-textual-python2.md). 
