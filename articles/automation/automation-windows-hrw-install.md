---
title: Hybrid Runbook Worker do Windows de Automação do Azure
description: Este artigo fornece informações sobre a instalação de um Trabalhador de Runbook Híbrido de Automação do Azure que você pode usar para executar runbooks em computadores baseados no Windows em seu datacenter ou ambiente de nuvem local.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 53dfe07ebd4925c96290db140b6e613c38eef564
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617340"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Implantar um Windows híbrido Runbook Worker

É possível usar o recurso Hybrid Runbook Worker da Automação do Azure para executar os runbooks diretamente no computador que hospeda a função e os recursos do ambiente para gerenciar esses recursos locais. A Azure Automation armazena e gerencia runbooks e, em seguida, os entrega a um ou mais computadores designados. Este artigo descreve como implantar um Hybrid Runbook Worker em uma máquina Windows.

Depois de implantar com êxito um trabalhador de runbook, revise [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para saber como configurar seus runbooks para automatizar processos em seu datacenter local ou em outro ambiente de nuvem.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Instalação e configuração do Windows Hybrid Runbook Worker

Para instalar e configurar um Windows Hybrid Runbook Worker, você pode usar um dos seguintes métodos.

* Para VMs do Azure, instale o agente Log Analytics para Windows usando a [extensão](../virtual-machines/extensions/oms-windows.md)da máquina virtual para Windows . A extensão instala o agente Log Analytics em máquinas virtuais do Azure e inscreve máquinas virtuais em um espaço de trabalho existente do Log Analytics usando um modelo do Azure Resource Manager ou o PowerShell. Uma vez instalado o agente, a VM pode ser adicionada a um grupo Hybrid Runbook Worker em sua conta de Automação. Consulte as etapas 3 e 4 na seção [de implantação Manual.](#manual-deployment)

* Use um manual de automação para automatizar completamente o processo de configuração de um computador Windows. Este é o método recomendado para máquinas em seu data center ou outro ambiente em nuvem.

* Siga um procedimento passo-a-passo para instalar e configurar manualmente a função Hybrid Runbook Worker em sua VM não-Azure.

> [!NOTE]
> Para gerenciar a configuração de servidores que suportam a função Hybrid Runbook Worker com DSC (Desired State Configuration, configuração do estado desejado), você deve adicionar os servidores como nóS DSC.

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>Requisitos mínimos para o Windows Hybrid Runbook Worker

Os requisitos mínimos para um Windows Hybrid Runbook Worker são:

* Windows Server 2012 ou posterior
* Windows PowerShell 5.1 ou posterior[(download WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 ou posterior
* Dois núcleos
* 4 GB de RAM
* Porta 443 (saída)

### <a name="network-configuration"></a>Configuração de rede

Para obter mais requisitos de rede para o Hybrid Runbook Worker, consulte [ Configurando sua rede ](automation-hybrid-runbook-worker.md#network-planning).

### <a name="server-onboarding-for-management-with-automation-dsc"></a>Onboarding de servidor para gerenciamento com DSC de automação

Para obter informações sobre servidores de onboarding para gerenciamento com DSC, consulte [máquinas de onboarding para gerenciamento pelo Azure Automation DSC](automation-dsc-onboarding.md).

Habilitar a [solução de gerenciamento de atualizações](../operations-management-suite/oms-solution-update-management.md) configura automaticamente qualquer computador Windows conectado ao seu espaço de trabalho do Log Analytics como um Trabalhador de Runbook Híbrido para suportar runbooks incluídos na solução. No entanto, esse trabalhador não está registrado em nenhum grupo de Trabalhadores híbridos de runbook já definidos em sua conta de Automação.

### <a name="addition-of-the-computer-to-a-hybrid-runbook-worker-group"></a>Adição do computador a um grupo hybrid Runbook Worker

Você pode adicionar o computador do trabalhador a um grupo hybrid Runbook Worker em sua conta de Automação. Observe que você deve suportar os runbooks de automação, desde que você esteja usando a mesma conta tanto para a solução quanto para a associação do grupo Hybrid Runbook Worker. Essa funcionalidade foi adicionada à versão 7.2.12024.0 do Hybrid Runbook Worker.

## <a name="automated-deployment"></a>Implantação automatizada

Na máquina de destino, execute as seguintes etapas para automatizar a instalação e a configuração da função Do Trabalhador Híbrido do Windows.

### <a name="step-1---download-the-powershell-script"></a>Passo 1 - Baixe o script do PowerShell

Baixe o script **New-OnPremiseHybridWorker.ps1** na [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). O download deve ser diretamente do computador executando a função Hybrid Runbook Worker ou de outro computador em seu ambiente. Quando você tiver baixado o script, copie-o para o seu trabalhador. O **script New-OnPremiseHybridWorker.ps1** usa os parâmetros descritos abaixo durante a execução.

| Parâmetro | Status | Descrição |
| --------- | ------ | ----------- |
| `AAResourceGroupName` | Obrigatório | O nome do grupo de recursos associado à sua conta de automação. |
| `AutomationAccountName` | Obrigatório | O nome da sua conta de automação.
| `Credential` | Opcional | As credenciais a serem usados ao fazer login no ambiente Azure. |
| `HybridGroupName` | Obrigatório | O nome de um grupo do Hybrid Runbook Worker que você especifica como um destino para os runbooks que dão suporte a esse cenário. |
| `OMSResourceGroupName` | Opcional | O nome do grupo de recursos do espaço de trabalho do Log Analytics. Se este grupo de recursos não `AAResourceGroupName` for especificado, o valor do é usado. |
| `SubscriptionID` | Obrigatório | O identificador da assinatura do Azure associado à sua conta de Automação. |
| `TenantID` | Opcional | O identificador da organização do inquilino associado à sua conta de Automação. |
| `WorkspaceName` | Opcional | O nome do espaço de trabalho do Log Analytics. Se você não tiver um espaço de trabalho do Log Analytics, o script cria e configura um. |

> [!NOTE]
> Ao habilitar soluções, o Azure Automation só suporta determinadas regiões para vincular um espaço de trabalho do Log Analytics e uma conta de Automação. Para obter uma lista dos pares de mapeamento suportados, consulte [Mapeamento de região para conta de automação e espaço de trabalho do Log Analytics](how-to/region-mappings.md).

### <a name="step-2---open-windows-powershell-command-line-shell"></a>Passo 2 - Abrir shell da linha de comando Windows PowerShell

Abra o **Windows PowerShell** a partir da tela **Iniciar** no modo Administrador.

### <a name="step-3---run-the-powershell-script"></a>Passo 3 - Executar o script PowerShell

No shell da linha de comando PowerShell, navegue até a pasta que contém o script que você baixou. Alterar os valores `AutomationAccountName` `AAResourceGroupName`para `OMSResourceGroupName` `HybridGroupName`os `SubscriptionID`parâmetros, e `WorkspaceName`. Em seguida, execute o script.

Você é solicitado a autenticar com o Azure depois de executar o script. Você deve fazer login com uma conta que seja membro da função Administradores da inscrição e co-administrador da assinatura.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionID <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="step-4---install-nuget"></a>Passo 4 - Instalar NuGet

Você é solicitado a concordar em instalar o NuGet e autenticar com suas credenciais do Azure. Se você não tiver a versão mais recente do NuGet, você pode obtê-la nas [versões de distribuição disponíveis do NuGet](https://www.nuget.org/downloads).

### <a name="step-5---verify-the-deployment"></a>Passo 5 - Verificar a implantação

Depois que o script for concluído, a página Grupos de Trabalhadores Hybrid mostrará o novo grupo e o número de membros. Se for um grupo existente, o número de membros é incrementado. Você pode selecionar o grupo na lista na página Grupos de Trabalhadores Híbridos e escolher o bloco **Trabalhadores Híbridos.** Na página Trabalhadores Híbridos, você pode ver cada membro do grupo listado.

## <a name="manual-deployment"></a>Implantação manual

Na máquina de destino, execute os dois primeiros passos uma vez para o ambiente de Automação. Em seguida, execute as etapas restantes para cada computador trabalhador.

### <a name="step-1---create-a-log-analytics-workspace"></a>Passo 1 - Criar um espaço de trabalho do Log Analytics

Se você ainda não tiver um espaço de trabalho do Log Analytics, revise a [orientação de design do Azure Monitor Log](../azure-monitor/platform/design-logs-deployment.md) antes de criar o espaço de trabalho.

### <a name="step-2---add-the-automation-solution-to-the-log-analytics-workspace"></a>Passo 2 - Adicionar a solução de automação ao espaço de trabalho do Log Analytics

A solução de Automação adiciona funcionalidade para automação azure, incluindo suporte para o Hybrid Runbook Worker. Quando você adiciona a solução ao seu espaço de trabalho log analytics, ele empurra automaticamente para o computador do agente os componentes do trabalhador que você instala conforme descrito na próxima etapa.

Para adicionar a solução de Automação ao seu espaço de trabalho, execute o seguinte cmdlet PowerShell.

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="step-3---install-the-log-analytics-agent-for-windows"></a>Passo 3 - Instalar o agente log analytics para Windows

O agente Log Analytics para Windows conecta computadores a um espaço de trabalho do Azure Monitor Log Analytics. Quando você instala o agente em seu computador e o conecta ao seu espaço de trabalho, ele baixa automaticamente os componentes necessários para o Hybrid Runbook Worker.

Para instalar o agente no computador, siga as instruções em [Conectar computadores Windows aos logs do Monitor do Azure](../log-analytics/log-analytics-windows-agent.md). Você poderá repetir esse processo em vários computadores para adicionar vários trabalhos ao ambiente.

Quando o agente estiver conectado com sucesso ao seu espaço de trabalho do Log Analytics após alguns minutos, você poderá executar a seguinte consulta para verificar se ele está enviando dados de batimentos cardíacos para o espaço de trabalho.

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

Nos resultados da pesquisa, você deve ver registros de batimentos cardíacos para o computador, indicando que ele está conectado e reportando ao serviço. Por padrão, cada agente encaminha um registro de batimentos cardíacos para o seu espaço de trabalho atribuído. 

Use as seguintes etapas para concluir a instalação e a configuração do agente.

1. Habilite a solução a bordo da máquina do agente. Consulte [máquinas a bordo no espaço de trabalho](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-automation-account#onboard-machines-in-the-workspace).
2. Verifique se o agente baixou corretamente a solução De Automação. 
3. Para confirmar a versão do Hybrid Runbook Worker, navegue por **C:\Arquivos do programa\Microsoft Monitoring Agent\Agent\Agent\AzureAutomation** e observe a subpasta da **versão.**

### <a name="step-4---install-the-runbook-environment-and-connect-to-azure-automation"></a>Passo 4 - Instale o ambiente do runbook e conecte-se à Automação Azure

Quando você configura um agente para reportar a um espaço de trabalho `HybridRegistration` do Log Analytics, a solução De Automação empurra para baixo o módulo PowerShell, que contém o `Add-HybridRunbookWorker` cmdlet. Use este cmdlet para instalar o ambiente de runbook no computador e registrá-lo com o Azure Automation.

Abra uma sessão do PowerShell no modo de Administrador e execute os comandos a seguir para importar o módulo.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Agora execute `Add-HybridRunbookWorker` o cmdlet usando a seguinte sintaxe.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Você pode obter as informações necessárias para esse cmdlet na página Gerenciar Chaves no portal do Azure. Abra esta página selecionando **Chaves** na página Configurações em sua conta automação.

![Gerenciar página Chaves](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* Para `GroupName` o parâmetro, use o nome do grupo Hybrid Runbook Worker. Se esse grupo já existir na conta de automação, o computador atual será adicionado a ele. Se esse grupo não existir, será adicionado.
* Para `EndPoint` o parâmetro, use a entrada **de URL** na página Gerenciar chaves.
* Para `Token` o parâmetro, use a entrada **KEY DE ACESSO PRIMÁRIO** na página Gerenciar chaves.
* Se necessário, `Verbose` defina o parâmetro para receber detalhes sobre a instalação.

### <a name="step-5----install-powershell-modules"></a>Passo 5 - Instalar módulos PowerShell

Os Runbooks podem usar qualquer uma das atividades e cmdlets definidos nos módulos instalados em seu ambiente de Automação do Azure. Como esses módulos não são automaticamente implantados em computadores locais, você deve instalá-los manualmente. A exceção é o módulo Azure. Este módulo é instalado por padrão e fornece acesso a cmdlets para todos os serviços e atividades do Azure para a Automação Azure.

Como o objetivo principal do recurso Hybrid Runbook Worker é gerenciar recursos locais, é provável que `PowerShellGet` você precise instalar os módulos que suportam esses recursos, particularmente o módulo. Para obter informações sobre a instalação de módulos Windows PowerShell, consulte [O Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell).

Os módulos instalados devem estar em um `PSModulePath` local referenciado pela variável ambiente para que o trabalhador híbrido possa importá-los automaticamente. Para obter mais informações, consulte [Install Modules in PSModulePath](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="next-steps"></a>Próximas etapas

* Para saber como configurar os runbooks para automatizar processos no datacenter local ou em outro ambiente de nuvem, consulte [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Para obter instruções sobre como remover os trabalhadores do runbook híbrido, consulte [Remover trabalhadores híbridos de runbook de automação do Azure](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Para saber como solucionar problemas com os trabalhadores do runbook híbrido, consulte [Trabalhadores do runbook híbrido do Windows](troubleshoot/hybrid-runbook-worker.md#windows).
* Para obter etapas adicionais para solução de problemas de gerenciamento de atualizações, consulte [Gerenciamento de atualizações: solução de problemas](troubleshoot/update-management.md).
