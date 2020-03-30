---
title: Ativos variáveis na Automação do Azure
description: Ativos de variáveis são valores que estão disponíveis para todos os runbooks e configurações DSC na Automação do Azure.  Este artigo explica os detalhes das variáveis e como trabalhar com elas na criação de textos e gráficos.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d4a4a92feb3e1b400c0f40076148f7898c4bdef1
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365832"
---
# <a name="variable-assets-in-azure-automation"></a>Ativos variáveis na Automação do Azure

Ativos variáveis são valores disponíveis para todos os runbooks e configurações de DSC em sua conta de Automação. Você pode gerenciá-los a partir do portal Azure, do PowerShell, dentro de um runbook ou em uma configuração DSC.

As variáveis de automação são úteis para os seguintes cenários:

- Compartilhando um valor entre vários runbooks ou configurações de DSC.

- Compartilhando um valor entre vários trabalhos da mesma configuração de runbook ou DSC.

- Gerenciamento de um valor usado por runbooks ou configurações DeDC a partir do portal ou da linha de comando PowerShell. Um exemplo é um conjunto de itens de configuração comuns, como uma lista específica de nomes de VM, um grupo de recursos específico, um nome de domínio AD e muito mais.  

O Azure Automation persiste com variáveis e as disponibiliza mesmo se um runbook ou configuração DSC falhar. Esse comportamento permite que um runbook ou configuração DSC defina um valor que é usado por outro runbook ou pela mesma configuração de runbook ou DSC na próxima vez que for executado.

A Azure Automation armazena cada variável criptografada com segurança. Ao criar uma variável, você pode especificar sua criptografia e armazenamento pelo Azure Automation como um ativo seguro. Outros ativos seguros incluem credenciais, certificados e conexões. A Azure Automation criptografa esses ativos e os armazena usando uma chave única gerada para cada conta de Automação. A chave é armazenada em um cofre de chaves gerenciado pelo sistema. Antes de armazenar um ativo seguro, o Azure Automation carrega a chave do Key Vault e, em seguida, usa-a para criptografar o ativo. 

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](../automation-update-azure-modules.md).

## <a name="variable-types"></a>Tipos de variável

Ao criar uma variável com o portal Azure, você deve especificar um tipo de dados da lista de isto para que o portal possa exibir o controle apropriado para inserir o valor variável. A seguir estão os tipos de variáveis disponíveis no Azure Automation:

* String
* Integer
* Datetime
* Boolean
* Nulo

A variável não está restrita ao tipo de dados designado. Você deve definir a variável usando o Windows PowerShell se quiser especificar um valor de um tipo diferente. Se você `Not defined`indicar, o valor da variável será definido como Nulo e você deve definir o `Set-AutomationVariable` valor com o cmdlet [Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) ou com a atividade.

Você não pode usar o portal Azure para criar ou alterar o valor para um tipo de variável complexa. No entanto, você pode fornecer um valor de qualquer tipo usando o Windows PowerShell. Tipos complexos são recuperados como um [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Você pode armazenar vários valores para uma única variável criando uma matriz ou hashtable e salvando-a na variável.

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>Cmdlets powershell que criam e gerenciam ativos variáveis

Para o módulo Az, os cmdlets na tabela a seguir são usados para criar e gerenciar ativos variáveis de automação com o Windows PowerShell. Eles são enviados como parte do [módulo Az.Automation,](/powershell/azure/overview)que está disponível para uso em runbooks de automação e configurações de DSC.

| Cmdlet | Descrição |
|:---|:---|
|[Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | Recupera o valor de uma variável existente. Você não pode usar este cmdlet para recuperar o valor de uma variável criptografada. A única maneira de fazer `Get-AutomationVariable` isso é usando a atividade em um runbook ou configuração DSC. |
|[New-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Cria uma nova variável e define o seu valor.|
|[Remove-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Remove uma variável existente.|
|[Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Define o valor de uma variável existente. |

## <a name="activities-to-access-variables-in-runbooks-and-dsc-configurations"></a>Atividades para acessar variáveis em runbooks e configurações de DSC

As atividades na tabela a seguir são usadas para acessar variáveis em runbooks e configurações de DSC. Os cmdlets para essas atividades `Orchestrator.AssetManagement.Cmdlets`vêm com o módulo global.

| Atividade | Descrição |
|:---|:---|
|`Get-AutomationVariable`|Recupera o valor de uma variável existente.|
|`Set-AutomationVariable`|Define o valor de uma variável existente.|

> [!NOTE]
> Evite usar variáveis `Name` no parâmetro `Get-AutomationVariable` de uma configuração de runbook ou DSC. O uso deste parâmetro pode complicar a descoberta de dependências entre runbooks ou configurações de DSC e variáveis de automação na hora do projeto.

Observe `Get-AutomationVariable` que não funciona no PowerShell, mas apenas em um runbook ou configuração DSC. Por exemplo, para ver o valor de uma variável criptografada, você pode criar um runbook para obter a variável e, em seguida, gravá-la no fluxo de saída:
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="functions-to-access-variables-in-python-2-runbooks"></a>Funções para acessar variáveis em runbooks Python 2

As funções na tabela a seguir são usadas para acessar variáveis em um runbook Python 2.

|Funções python 2|Descrição|
|:---|:---|
|`automationassets.get_automation_variable`|Recupera o valor de uma variável existente. |
|`automationassets.set_automation_variable`|Define o valor de uma variável existente. |

> [!NOTE]
> Você deve `automationassets` importar o módulo na parte superior do seu runbook Python para acessar as funções de ativos.

## <a name="working-with-automation-variables"></a>Trabalhando com variáveis de Automação

>[!NOTE]
>Se você quiser remover a criptografia de uma variável, você deve excluir a variável e recriá-la como não criptografada.

### <a name="create-a-new-variable-using-the-azure-portal"></a>Crie uma nova variável usando o portal Azure

1. Na sua conta de Automação, clique no azulejo **Ativos** e, em seguida, na lâmina **Ativos** e selecione **Variáveis**.
2. No bloco **Variáveis**, clique em **Adicionar uma variável**.
3. Complete as opções na lâmina **Nova variável** e clique em **Criar** para salvar a nova variável.

> [!NOTE]
> Uma vez salva uma variável criptografada, ela não pode ser visualizada no portal. Só pode ser atualizado.

### <a name="create-and-use-a-variable-in-windows-powershell"></a>Crie e use uma variável no Windows PowerShell

Um script PowerShell `New-AzAutomationVariable` usa o cmdlet, ou seu equivalente ao módulo AzureRM, para criar uma nova variável e definir seu valor inicial. Se a variável estiver criptografada, a chamada deve usar o `Encrypted` parâmetro.

O script recupera o valor da variável usando [Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0). Se o valor for um tipo simples, o cmdlet recupera esse mesmo tipo. Se for um tipo complexo, um `PSCustomObject` tipo é recuperado.

>[!NOTE]
>Um script PowerShell não pode recuperar um valor criptografado. A única maneira de fazer `Get-AutomationVariable` isso é usar uma atividade em um runbook ou configuração DSC.

O exemplo a seguir mostra como criar uma variável do tipo String e, em seguida, retornar seu valor.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

O exemplo a seguir mostra como criar uma variável com um tipo complexo e, em seguida, recuperar suas propriedades. Neste caso, um objeto de máquina virtual do [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) é usado.

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

### <a name="create-and-use-a-variable-in-a-runbook-or-dsc-configuration"></a>Criar e usar uma variável em um runbook ou configuração DSC

A única maneira de criar uma nova variável dentro de um `New-AzAutomationVariable` runbook ou configuração DSC é usar o cmdlet, ou seu equivalente a módulo AzureRM. O script usa este cmdlet para definir o valor inicial da variável. O script pode então `Get-AzAutomationVariable`recuperar o valor usando . Se o valor for um tipo simples, esse mesmo tipo será recuperado. Se é um tipo complexo, `PSCustomObject` então um tipo é recuperado.

>[!NOTE]
>A única maneira de recuperar um valor `Get-AutomationVariable` criptografado é usando a atividade no manual ou na configuração do DSC. 

### <a name="textual-runbook-samples"></a>Exemplos de runbook textual

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>Definir e recuperar um valor simples de uma variável

Os comandos de exemplo a seguir mostram como definir e recuperar uma variável em um runbook textual. Esta amostra assume a criação de variáveis `NumberOfIterations` `NumberOfRunnings` inteiras nomeadas `SampleMessage`e uma variável de string denominada .

```powershell
$NumberOfIterations = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

#### <a name="set-and-retrieve-a-variable-in-a-python-2-runbook"></a>Definir e recuperar uma variável em um runbook Python 2

A amostra a seguir mostra como usar uma variável, definir uma variável e lidar com uma exceção para uma variável inexistente em um runbook Python 2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print "variable not found"
```

### <a name="graphical-runbook-samples"></a>Exemplos de runbook gráfico

Em um runbook gráfico, você `Get-AutomationVariable` `Set-AutomationVariable` pode adicionar a atividade ou atividade. Basta clicar com o botão direito do mouse na variável no painel Biblioteca do editor gráfico e selecionar a atividade desejada.

![Adicionar variável à tela](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>Definir valores em uma variável

A imagem a seguir mostra as atividades de exemplo para atualizar uma variável com um valor simples em um runbook gráfico. Nesta amostra, `Get-AzVM` recupera uma única máquina virtual Azure e salva o nome do computador para uma variável de seqüência de string sinuosa de Automação existente. Não importa se o [link é um pipeline ou uma seqüência,](../automation-graphical-authoring-intro.md#links-and-workflow) já que o código espera apenas um único objeto na saída.

![Definir variável simples](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como conectar atividades na autoria gráfica, consulte [Links em autoria gráfica](../automation-graphical-authoring-intro.md#links-and-workflow).
- Para começar com runbooks gráficos, consulte [Meu primeiro runbook gráfico](../automation-first-runbook-graphical.md).
