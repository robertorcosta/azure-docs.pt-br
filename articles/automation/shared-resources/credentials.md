---
title: Gerenciar credenciais na automação do Azure
description: Os ativos de credenciais na Automação do Azure contêm credenciais de segurança que podem ser usadas para a autenticação em recursos acessados pelo runbook ou pela configuração DSC. Este artigo descreve como criar ativos de credenciais e usá-los em um runbook ou uma configuração DSC.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4226a625918be378b14e14c55fe4dd4ca5c398d5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82136678"
---
# <a name="manage-credentials-in-azure-automation"></a>Gerenciar credenciais na automação do Azure

Um ativo de credencial de automação contém um objeto que contém credenciais de segurança, como um nome de usuário e uma senha. Runbooks e configurações DSC usam cmdlets que aceitam um objeto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) para autenticação. Como alternativa, eles podem extrair o nome de usuário e a senha `PSCredential` do objeto para fornecer a um aplicativo ou serviço que exija autenticação. 

> [!NOTE]
> Os ativos protegidos na Automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados na automação do Azure usando uma chave exclusiva que é gerada para cada conta de automação. Essa chave é armazenada no Key Vault. Antes de armazenar um ativo seguro, a chave é carregada do Key Vault e usada para criptografar o ativo.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](../automation-update-azure-modules.md).

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="azure-powershell-az-cmdlets-used-for-credential-assets"></a>Azure PowerShell cmdlets AZ usados para ativos de credencial

Como parte do módulo Azure PowerShell AZ, os cmdlets na tabela a seguir são usados para criar e gerenciar ativos de credenciais de automação com o Windows PowerShell. Eles são fornecidos no [módulo AZ. Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0), que está disponível para uso em Runbooks de automação e configurações DSC. Consulte [suporte ao módulo AZ na automação do Azure](https://docs.microsoft.com/azure/automation/az-modules).

| Cmdlet | Descrição |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Recupera um objeto [CredentialInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo?view=azurerm-ps) que contém metadados sobre a credencial. O cmdlet não recupera o `PSCredential` objeto em si.  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Cria uma nova credencial de Automação. |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Remove uma credencial de Automação. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Define as propriedades de uma credencial de Automação existente. |

## <a name="activities-used-to-access-credentials"></a>Atividades usadas para acessar credenciais

As atividades na tabela a seguir são usadas para acessar credenciais em runbooks gráficos e configurações DSC. Para obter exemplos de uso de atividades, consulte [criação gráfica na automação do Azure](../automation-graphical-authoring-intro.md#activities).

| Atividade | Descrição |
|:--- |:--- |
| `Get-AutomationPSCredential` |Obtém um `PSCredential` objeto a ser usado em uma configuração de RUNBOOK ou DSC. Geralmente, você deve usar essa atividade em vez do `Get-AzAutomationCredential` cmdlet, pois o último recupera apenas informações de credenciais. Normalmente, essas informações não são úteis para passar para outro cmdlet. |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |Obtém uma credencial com um prompt de nome de usuário e senha. |
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | Cria um ativo de credencial. |

Para recuperar `PSCredential` objetos em seu código, você deve importar o `Orchestrator.AssetManagement.Cmdlets` módulo. Para obter mais informações, consulte [gerenciar módulos na automação do Azure](modules.md).

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Evite usar variáveis no `Name` parâmetro de. `Get-AutomationPSCredential` Seu uso pode complicar a descoberta de dependências entre runbooks ou configurações DSC e ativos de credencial em tempo de design.

## <a name="python-2-functions-that-access-credentials"></a>Funções do Python 2 que acessam credenciais

A função na tabela a seguir é usada para acessar credenciais em um runbook Python 2.

| Função | Descrição |
|:---|:---|
| `automationassets.get_automation_credential` | Recupera informações sobre um ativo de credencial. |

> [!NOTE]
> Importe o `automationassets` módulo na parte superior do seu runbook do Python para acessar as funções de ativo.

## <a name="creating-a-new-credential-asset"></a>Criando um novo ativo de credencial

Você pode criar um novo ativo de credencial usando o portal do Azure ou usando o Windows PowerShell.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Criar um novo ativo de credencial com o portal do Azure

1. Na sua conta de automação, selecione **credenciais** em **recursos compartilhados**.
1. Selecione **Adicionar uma credencial**.
2. No painel nova credencial, insira um nome de credencial apropriado após seus padrões de nomenclatura. 
3. Digite sua ID de acesso no campo **nome de usuário** . 
4. Para ambos os campos de senha, insira sua chave de acesso secreta.

    ![Criar nova credencial](../media/credentials/credential-create.png)

5. Se a caixa autenticação multifator estiver marcada, desmarque-a. 
6. Clique em **criar** para salvar o novo ativo de credencial.

> [!NOTE]
> A automação do Azure não oferece suporte a contas de usuário que usam a autenticação multifator.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Criar um novo ativo de credencial com o Windows PowerShell

O exemplo a seguir mostra como criar um novo ativo de credencial de automação. Um `PSCredential` objeto é criado pela primeira vez com o nome e a senha e, em seguida, usado para criar o ativo de credencial. Em vez disso, você pode `Get-Credential` usar o cmdlet para solicitar que o usuário digite um nome e uma senha.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Usando uma credencial do PowerShell

Uma configuração de runbook ou DSC recupera um ativo de credencial com a `Get-AutomationPSCredential` atividade. Essa atividade recupera um `PSCredential` objeto que você pode usar com uma atividade ou um cmdlet que requer uma credencial. Você também pode recuperar as propriedades do objeto de credencial para usar individualmente. O objeto tem propriedades para o nome de usuário e a senha segura. Como alternativa, você pode usar o método [GetNetworkCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0) para recuperar um objeto [NetworkCredential](/dotnet/api/system.net.networkcredential) que representa uma versão não segura da senha.

> [!NOTE]
> `Get-AzAutomationCredential`Não recupera um `PSCredential` objeto que pode ser usado para autenticação. Ele fornece apenas informações sobre a credencial. Se você precisar usar uma credencial em um runbook, deverá recuperá-la como um `PSCredential` objeto usando `Get-AutomationPSCredential`.

### <a name="textual-runbook-example"></a>Exemplo de runbook textual

O exemplo a seguir mostra como usar uma credencial do PowerShell em um runbook. Ele recupera a credencial e atribui seu nome de usuário e senha a variáveis.


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Você também pode usar uma credencial para autenticar no Azure com [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0). Na maioria das circunstâncias, você deve usar uma [conta Executar como](../manage-runas-account.md) e recuperar a conexão com [Get-AzAutomationConnection](../automation-connections.md).


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>Exemplo de runbook gráfico

Você pode adicionar uma `Get-AutomationPSCredential` atividade a um runbook gráfico clicando com o botão direito do mouse na credencial no painel Biblioteca do editor gráfico e selecionando **Adicionar à tela**.

![Adicionar a credencial à tela](../media/credentials/credential-add-canvas.png)

A imagem a seguir mostra um exemplo do uso de uma credencial em um runbook gráfico. Nesse caso, a credencial fornece autenticação para um runbook para recursos do Azure, conforme descrito em [usar o Azure ad na automação do Azure para autenticar no Azure](../automation-use-azure-ad.md). A primeira atividade recupera a credencial que tem acesso à assinatura do Azure. A atividade de conexão de conta usa essa credencial para fornecer autenticação para qualquer atividade que vier depois dela. Um [link de pipeline](../automation-graphical-authoring-intro.md#links-and-workflow) é usado aqui `Get-AutomationPSCredential` , pois está esperando um único objeto.  

![Adicionar a credencial à tela](../media/credentials/get-credential.png)

## <a name="using-credentials-in-a-dsc-configuration"></a>Usando credenciais em uma configuração DSC

Embora as configurações de DSC na automação do Azure possam trabalhar com `Get-AutomationPSCredential`ativos de credencial usando, elas também podem passar ativos de credencial por meio de parâmetros. Para obter mais informações, veja [Compilando configurações na DSC de Automação do Azure](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python-2"></a>Usando credenciais no Python 2

O exemplo a seguir mostra um exemplo de acesso a credenciais em runbooks do Python 2.


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre links na criação gráfica, confira [links na criação gráfica](../automation-graphical-authoring-intro.md#links-and-workflow).
* Para entender os diferentes métodos de autenticação para automação, consulte [segurança de automação do Azure](../automation-security-overview.md).
* Para começar a usar runbooks gráficos, consulte [meu primeiro runbook gráfico](../automation-first-runbook-graphical.md).
* Para começar a usar runbooks de fluxo de trabalho do PowerShell, consulte [meu primeiro runbook de fluxo de trabalho do PowerShell](../automation-first-runbook-textual.md).
* Para começar com runbooks do Python 2, consulte [meu primeiro runbook do Python 2](../automation-first-runbook-textual-python2.md). 
