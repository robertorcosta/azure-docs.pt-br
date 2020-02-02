---
title: Ativos de credenciais na Automação do Azure
description: Os ativos de credenciais na Automação do Azure contêm credenciais de segurança que podem ser usadas para a autenticação em recursos acessados pelo runbook ou pela configuração DSC. Este artigo descreve como criar ativos de credenciais e usá-los em um runbook ou uma configuração DSC.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 767c1fddbc3d1f46d4341a70c990c2b57ad40e54
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76930409"
---
# <a name="credential-assets-in-azure-automation"></a>Ativos de credenciais na Automação do Azure

Um ativo de credencial de automação contém um objeto, que contém credenciais de segurança, como nome de usuário e senha. Runbooks e configurações DSC podem usar cmdlets que aceitam um objeto PSCredential para autenticação ou eles podem extrair o nome de usuário e a senha do objeto PSCredential para fornecê-los a algum aplicativo ou serviço que exija a autenticação. As propriedades de uma credencial são armazenadas com segurança na Automação do Azure e podem ser acessadas no runbook ou na configuração DSC com a atividade [Get-AutomationPSCredential](#activities) .

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Os ativos protegidos na Automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados na Automação do Azure usando uma chave exclusiva que é gerada para cada conta de automação. Essa chave é armazenada no Key Vault. Antes de armazenar um ativo seguro, a chave é carregada do Key Vault e usada para criptografar o ativo.

## <a name="azure-powershell-az-cmdlets"></a>Azure PowerShell cmdlets AZ

Para Azure PowerShell módulo AZ, os cmdlets na tabela a seguir são usados para criar e gerenciar ativos de credenciais de automação com o Windows PowerShell. Eles são fornecidos como parte do [módulo AzureAz. Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0), que está disponível para uso em Runbooks de automação e configurações DSC.

| Cmdlets | Description |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Recupera informações sobre um ativo de credencial. Isso não retorna um objeto PSCredential.  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Cria uma nova credencial de Automação. |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Remove uma credencial de Automação. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Define as propriedades de uma credencial de Automação existente. |

## <a name="activities"></a>Atividades

As atividades na tabela a seguir são usadas para acessar credenciais em um runbook ou em uma configuração DSC.

| Atividades | Description |
|:--- |:--- |
| Get-AutomationPSCredential |Obtém uma credencial a ser usada em um runbook ou configuração DSC. Retorna um objeto [System.Management.Automation.PSCredential](/dotnet/api/system.management.automation.pscredential) . |

> [!NOTE]
> Evite usar variáveis no parâmetro –Name de Get-AutomationPSCredential, pois isso pode complicar a descoberta de dependências entre runbooks ou configurações DSC e ativos de credenciais no momento do design.

## <a name="python2-functions"></a>Funções Python2

A função na tabela a seguir é usada para acessar credenciais em um runbook em Python2.

| Função | Description |
|:---|:---|
| automationassets.get_automation_credential | Recupera informações sobre um ativo de credencial. |

> [!NOTE]
> É necessário importar o módulo "automationassets", na parte superior do runbook Python para acessar as funções do ativo.

## <a name="creating-a-new-credential-asset"></a>Criando um novo ativo de credencial

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Para criar um novo ativo de credencial com o portal do Azure

1. Na sua conta de automação, selecione **Credenciais** em **Recursos Compartilhados**.
1. Selecione **Adicionar uma credencial**.
1. Preencha o formulário e selecione **criar** para salvar a nova credencial.

> [!NOTE]
> As contas de usuário que usam autenticação multifator não têm suporte para uso na Automação do Azure.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Para criar um novo ativo de credencial com o Windows PowerShell

Os comandos de exemplo a seguir mostram como criar uma nova credencial de automação. Um objeto PSCredential é criado pela primeira vez com o nome e a senha e, em seguida, é usado para criar o ativo de credencial. Como alternativa, você pode usar o cmdlet **Get-Credential** para ser solicitado a digitar um nome e uma senha.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Usando uma credencial do PowerShell

Recupere um ativo de credencial em um runbook ou configuração DSC com a atividade **Get-AutomationPSCredential** . Isso retorna um [objeto PSCredential](/dotnet/api/system.management.automation.pscredential) que você pode usar com uma atividade ou cmdlet que exija um parâmetro PSCredential. Você também pode recuperar as propriedades do objeto de credencial para usar individualmente. O objeto tem uma propriedade para o nome de usuário e a senha segura, ou você pode usar o método **GetNetworkCredential** para retornar um objeto [NetworkCredential](/dotnet/api/system.net.networkcredential) que fornecerá uma versão sem segurança da senha.

> [!NOTE]
> **Get-AzAutomationCredential** não retorna um **PSCredential** que pode ser usado para autenticação. Ele fornece apenas informações sobre a credencial. Se você precisar usar uma credencial em um runbook, deverá usar o **Get-AutomationPSCredential** para recuperar o objeto **PSCredential** .

### <a name="textual-runbook-sample"></a>Exemplo de runbook textual

Os comandos de exemplo a seguir mostram como usar uma credencial do PowerShell em um runbook. Neste exemplo, a credencial é recuperada e seu nome de usuário e senha são atribuídos a variáveis.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Você também pode usar uma credencial para autenticar no Azure com [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0). Na maioria das circunstâncias, você deve usar uma [conta Executar como](../manage-runas-account.md) e recuperá-la com [Get-AzAutomationConnection](../automation-connections.md).

```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzureRmAccount -Credential $myPsCred
```

### <a name="graphical-runbook-sample"></a>Exemplo de runbook gráfico

Adicione uma atividade **Get-AutomationPSCredential** a um runbook gráfico clicando com o botão direito na credencial no painel Biblioteca do editor gráfico e selecionando **Adicionar à tela**.

![Adicionar a credencial à tela](../media/credentials/credential-add-canvas.png)

A imagem a seguir mostra um exemplo do uso de uma credencial em um runbook gráfico. Nesse caso, ele está sendo usado para fornecer autenticação para um runbook para recursos do Azure, conforme descrito em [autenticar Runbooks com a conta de usuário do Azure ad](../automation-create-aduser-account.md). A primeira atividade recupera a credencial que tem acesso à assinatura do Azure. Em seguida, a atividade **Connect-AzureRmAccount** usa essa credencial para fornecer autenticação para todas as atividades que vierem depois dela. Um [link de pipeline](../automation-graphical-authoring-intro.md#links-and-workflow) está disponível, já que **Get-AutomationPSCredential** está esperando um único objeto.  

![Adicionar a credencial à tela](../media/credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Usando uma credencial do PowerShell na DSC

Embora as configurações de DSC na automação do Azure possam referenciar ativos de credencial usando **Get-AutomationPSCredential**, os ativos de credencial também podem ser passados por meio de parâmetros, se desejado. Para obter mais informações, veja [Compilando configurações na DSC de Automação do Azure](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Usando credenciais no Python2

A seguir, veja um exemplo de como acessar as credenciais em runbooks Python2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Próximos passos

* Para saber mais sobre links na criação gráfica, veja [Links na criação gráfica](../automation-graphical-authoring-intro.md#links-and-workflow)
* Para entender os diferentes métodos de autenticação com Automação, consulte [Segurança da Automação do Azure](../automation-security-overview.md)
* Para começar a usar os runbooks Gráficos, consulte [Meu primeiro runbook gráfico](../automation-first-runbook-graphical.md)
* Para começar a usar runbooks de fluxo de trabalho do PowerShell, veja [Meu primeiro runbook de Fluxo de Trabalho do PowerShell](../automation-first-runbook-textual.md)
* Para começar a usar runbooks Python2, consulte [Meu primeiro runbook Python2](../automation-first-runbook-textual-python2.md) 
