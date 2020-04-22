---
title: Configurar servidores para um estado desejado e gerenciar o descompasso com a Automação do Azure
description: Tutorial – Gerenciar configurações de servidor com a Configuração do Estado de Automação do Azure
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: a02c664ddf0802ad5ac306f98de14b7c0d5d7271
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678701"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Configurar servidores para um estado desejado e gerenciar dessincronização

A Configuração do Estado de Automação do Azure permite que você especifique configurações para seus servidores e garanta que esses servidores estejam no estado especificado ao longo do tempo.

> [!div class="checklist"]
> - Carregar uma VM para ser gerenciada pela DSC de Automação do Azure
> - Carregar uma configuração para Automação do Azure
> - Compilar uma configuração em uma configuração de nó
> - Atribuir uma configuração de nó a um nó gerenciado
> - Verificar o status de conformidade de um nó gerenciado

Para este tutorial, usamos uma [configuração DSC](/powershell/scripting/dsc/configurations/configurations) simples que garante que o IIS esteja instalado na VM.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

- Uma conta de Automação do Azure. Para obter instruções sobre como criar uma conta Executar Como de Automação do Azure, consulte [Conta Executar Como do Azure](automation-sec-configure-azure-runas-account.md).
- Um VM do Azure Resource Manager (não clássico) executando o Windows Server 2008 R2 ou posterior. Para obter instruções sobre como criar uma VM, consulte [Criar sua primeira máquina virtual do Windows no portal Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
- Versão de módulo do Azure PowerShell 3.6 ou posterior. Execute `Get-Module -ListAvailable Az` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
- Familiaridade com a Configuração do Estado Desejado (DSC). Para obter informações sobre o DSC, consulte [visão geral da configuração do estado desejado do Windows PowerShell](/powershell/scripting/dsc/overview/overview).

## <a name="support-for-partial-configurations"></a>Suporte para configurações parciais

A configuração do estado de automação do Azure suporta o uso de [configurações parciais](/powershell/scripting/dsc/pull-server/partialconfigs). Nesse cenário, o DSC é configurado para gerenciar várias configurações de forma independente, e cada configuração é recuperada do Azure Automation. No entanto, apenas uma configuração pode ser atribuída a um nó por conta de automação. Isso significa que se você estiver usando duas configurações para um nó, você precisará de duas contas de automação.

Para obter detalhes sobre como registrar uma configuração parcial de um serviço de tração, consulte a documentação para [configurações parciais](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode).

Para obter mais informações sobre como as equipes podem trabalhar juntas para gerenciar servidores de forma colaborativa usando a configuração como código, consulte [Entendendo a função do DSC em um pipeline de CI/CD](/powershell/scripting/dsc/overview/authoringadvanced).

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça login na sua assinatura do Azure com o cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) e siga as instruções na tela.

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Criar e carregar uma configuração na Automação do Azure


Em um editor de texto, digite o seguinte e salve-o localmente como **TestConfig.ps1**.

```powershell
configuration TestConfig {
   Node WebServer {
      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

> [!NOTE]
> Em cenários mais avançados, onde você precisa de vários módulos para serem `Import-DscResource` importados que fornecem recursos DSC, certifique-se de que cada módulo tenha uma linha única em sua configuração.

Chame o [cmdlet import-AzAutomationDscconfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration?view=azps-3.7.0) para carregar a configuração em sua conta de Automação.

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Compilar uma configuração em uma configuração de nó

Uma configuração DSC deverá ser compilada em uma configuração de nó para que ela possa ser atribuída a um nó. Consulte [as configurações do DSC](/powershell/scripting/dsc/configurations/configurations).

Ligue para o [cmdlet Start-AzAutomationDscCompilationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob?view=azps-3.7.0) para compilar `TestConfig` `TestConfig.WebServer` a configuração em uma configuração de nó nomeada em sua conta de Automação.

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Registrar uma VM a ser gerenciada pela Configuração do Estado

É possível usar a Configuração de Estado da Automação do Azure para gerenciar VMs do Azure (tanto Clássica quanto do Gerenciador de Recursos), VMs locais, computadores Linux, VMs de AWS e computadores físicos locais. Neste tópico, abordaremos como registrar somente VMs do Azure Resource Manager. Para obter mais informações sobre como registrar outros tipos de computadores, consulte [Integrar computadores para gerenciamento por Configuração de Estado da Automação do Azure](automation-dsc-onboarding.md).

Ligue para o [cmdlet Register-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationDscNode?view=azps-3.7.0) para registrar sua VM com a Configuração do Estado de Automação do Azure como um nó gerenciado. 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>Especificar definições do modo de configuração

Use o [cmdlet Register-AzAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) para registrar uma VM como um nó gerenciado e especificar propriedades de configuração. Por exemplo, você pode especificar que o estado da máquina `ApplyOnly` deve ser aplicado `ConfigurationMode` apenas uma vez, especificando como o valor da propriedade. A configuração do estado não tenta aplicar a configuração após a verificação inicial.

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Você também pode especificar com que frequência o `ConfigurationModeFrequencyMins` DSC verifica o estado de configuração usando a propriedade. Para obter mais informações sobre as definições de configuração de DSC, consulte [Configurando o Gerenciador de Configurações Local](/powershell/scripting/dsc/managing-nodes/metaConfig).

```powershell
# Run a DSC check every 60 minutes
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Atribuir uma configuração de nó a um nó gerenciado

Agora podemos atribuir a configuração de nó compilada à VM que queremos configurar.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Isso atribui a configuração `TestConfig.WebServer` do nó nomeada ao `DscVm`nó DSC registrado . Por padrão, o nó de DSC é verificado quanto à conformidade com a configuração do nó a cada 30 minutos. Para obter informações de como alterar o intervalo de verificação de conformidade, consulte [Configurando System Center Configuration Manager Local](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Verificar o status de conformidade de um nó gerenciado

Você pode obter relatórios sobre o status de conformidade de um nó gerenciado usando o [cmdlet Get-AzAutomationDscNodeReport.](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport?view=azps-3.7.0)

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>Remova os nódulos do serviço

Quando você adiciona um nó à configuração do estado de automação do Azure, as configurações do Local Configuration Manager são definidas para registrar-se com as configurações de serviço e puxar e módulos necessários para configurar a máquina.
Se você optar por remover o nó do serviço, você pode fazê-lo usando o portal Azure ou os cmdlets Az.

> [!NOTE]
> Cancelar o registro de um nó do serviço define apenas as configurações do Gerenciador de configuração local para que o nó não esteja mais conectado ao serviço.
> Isso não afeta a configuração que é aplicada atualmente ao nó.
> Para remover a configuração atual, use o [PowerShell](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) ou exclua o arquivo de configuração local (esta é a única opção para nós Linux).

### <a name="azure-portal"></a>Portal do Azure

A partir do Azure Automation, clique na **configuração de Estado (DSC)** na tabela de conteúdos.
Em seguida, clique **em Nós** para visualizar a lista de nós registrados no serviço.
Clique no nome do nó que deseja remover.
Na exibição Nó que abre, clique em **Descadastrar**.

### <a name="powershell"></a>PowerShell

Para cancelar o registro de um nó do serviço de configuração do Estado de Automação do Azure usando o PowerShell, siga a documentação do cmdlet [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0).

## <a name="next-steps"></a>Próximas etapas

- Para começar, consulte [Como começar com a configuração do estado de automação do Azure](automation-dsc-getting-started.md).
- Para aprender como a bordo de nódulos, consulte [máquinas de onboarding para gerenciamento pela Configuração do Estado de Automação do Azure](automation-dsc-onboarding.md).
- Para saber mais sobre a compilação das configurações do DSC para que você possa atribuí-las a nós de destino, consulte [Compilando configurações na configuração do estado de automação do Azure](automation-dsc-compile.md).
- Para obter referência de cmdlet PowerShell, consulte [cmdlets de configuração do estado de automação do Azure](/powershell/module/azurerm.automation/#automation).
- Para obter informações sobre preços, consulte os preços de [configuração do estado de automação do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para ver um exemplo de uso da Configuração de Estado da Automação do Azure em um pipeline de implantação contínua, consulte [Implantação contínua usando Configuração de Estado da Automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md)
