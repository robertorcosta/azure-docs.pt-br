---
title: Hybrid Runbook Worker do Windows de Automação do Azure
description: Este artigo fornece informações sobre a instalação de um Trabalhador de Runbook Híbrido de Automação do Azure que você pode usar para executar runbooks em computadores baseados no Windows em seu datacenter ou ambiente de nuvem local.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 53dfe07ebd4925c96290db140b6e613c38eef564
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617340"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Implantar um Windows híbrido Runbook Worker

É possível usar o recurso Hybrid Runbook Worker da Automação do Azure para executar os runbooks diretamente no computador que hospeda a função e os recursos do ambiente para gerenciar esses recursos locais. A automação do Azure armazena e gerencia runbooks e, em seguida, os entrega a um ou mais computadores designados. Este artigo descreve como implantar um Hybrid Runbook Worker em um computador Windows.

Depois de implantar com êxito um trabalhador de runbook, revise [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para saber como configurar seus runbooks para automatizar processos em seu datacenter local ou em outro ambiente de nuvem.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](automation-update-azure-modules.md).

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Instalação e configuração do Windows Hybrid Runbook Worker

Para instalar e configurar um Hybrid Runbook Worker do Windows, você pode usar um dos métodos a seguir.

* Para VMs do Azure, instale o agente do Log Analytics para Windows usando a [extensão da máquina virtual para Windows](../virtual-machines/extensions/oms-windows.md). A extensão instala o agente de Log Analytics em máquinas virtuais do Azure e registra as máquinas virtuais em um espaço de trabalho Log Analytics existente usando um modelo Azure Resource Manager ou o PowerShell. Depois que o agente é instalado, a VM pode ser adicionada a um grupo de Hybrid Runbook Worker em sua conta de automação. Consulte as etapas 3 e 4 na seção [implantação manual](#manual-deployment) .

* Use um runbook de automação para automatizar completamente o processo de configuração de um computador com Windows. Esse é o método recomendado para computadores em seu datacenter ou em outro ambiente de nuvem.

* Siga um procedimento passo a passo para instalar e configurar manualmente a função Hybrid Runbook Worker em sua VM não Azure.

> [!NOTE]
> Para gerenciar a configuração de servidores que dão suporte à função de Hybrid Runbook Worker com a DSC (configuração de estado desejado), você deve adicionar os servidores como nós DSC.

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>Requisitos mínimos para o Windows Hybrid Runbook Worker

Os requisitos mínimos para um Windows Hybrid Runbook Worker são:

* Windows Server 2012 ou posterior
* Windows PowerShell 5,1 ou posterior ([baixar o WMF 5,1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 ou posterior
* Dois núcleos
* 4 GB de RAM
* Porta 443 (saída)

### <a name="network-configuration"></a>Configuração de rede

Para obter mais requisitos de rede para o Hybrid Runbook Worker, consulte [ Configurando sua rede ](automation-hybrid-runbook-worker.md#network-planning).

### <a name="server-onboarding-for-management-with-automation-dsc"></a>Integração do servidor para gerenciamento com o DSC de Automação

Para obter informações sobre servidores de integração para gerenciamento com DSC, consulte [máquinas de integração para gerenciamento pelo Azure DSC de automação](automation-dsc-onboarding.md).

Habilitar a [solução de gerenciamento de atualizações](../operations-management-suite/oms-solution-update-management.md) configura automaticamente qualquer computador Windows que esteja conectado ao seu espaço de trabalho log Analytics como um Hybrid runbook Worker para dar suporte a runbooks incluídos na solução. No entanto, esse trabalhador não está registrado com nenhum grupo de Hybrid Runbook Worker já definido em sua conta de automação.

### <a name="addition-of-the-computer-to-a-hybrid-runbook-worker-group"></a>Adição do computador a um grupo de Hybrid Runbook Worker

Você pode adicionar o computador de trabalho a um grupo de Hybrid Runbook Worker em sua conta de automação. Observe que você deve dar suporte a runbooks de automação contanto que esteja usando a mesma conta para a solução e a associação de grupo de Hybrid Runbook Worker. Essa funcionalidade foi adicionada à versão 7.2.12024.0 do Hybrid Runbook Worker.

## <a name="automated-deployment"></a>Implantação automatizada

No computador de destino, execute as etapas a seguir para automatizar a instalação e a configuração da função de Hybrid Worker do Windows.

### <a name="step-1---download-the-powershell-script"></a>Etapa 1-baixar o script do PowerShell

Baixe o script **New-onpremisehybridworker. ps1** da [Galeria do PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). O download deve ser diretamente do computador que executa a função Hybrid Runbook Worker ou de outro computador em seu ambiente. Depois de baixar o script, copie-o para o seu trabalho. O script **New-onpremisehybridworker. ps1** usa os parâmetros descritos abaixo durante a execução.

| Parâmetro | Status | Descrição |
| --------- | ------ | ----------- |
| `AAResourceGroupName` | Obrigatório | O nome do grupo de recursos associado à sua conta de automação. |
| `AutomationAccountName` | Obrigatório | O nome da sua conta de automação.
| `Credential` | Opcional | As credenciais a serem usadas ao fazer logon no ambiente do Azure. |
| `HybridGroupName` | Obrigatório | O nome de um grupo do Hybrid Runbook Worker que você especifica como um destino para os runbooks que dão suporte a esse cenário. |
| `OMSResourceGroupName` | Opcional | O nome do grupo de recursos do espaço de trabalho do Log Analytics. Se esse grupo de recursos não for especificado, o valor `AAResourceGroupName` de será usado. |
| `SubscriptionID` | Obrigatório | O identificador da assinatura do Azure associada à sua conta de automação. |
| `TenantID` | Opcional | O identificador da organização do locatário associado à sua conta de automação. |
| `WorkspaceName` | Opcional | O nome do espaço de trabalho do Log Analytics. Se você não tiver um espaço de trabalho do Log Analytics, o script cria e configura um. |

> [!NOTE]
> Ao habilitar soluções, a automação do Azure só dá suporte a determinadas regiões para vincular um Log Analytics espaço de trabalho e uma conta de automação. Para obter uma lista dos pares de mapeamento com suporte, confira [mapeamento de região para conta de automação e espaço de trabalho de log Analytics](how-to/region-mappings.md).

### <a name="step-2---open-windows-powershell-command-line-shell"></a>Etapa 2 – abrir o Shell de linha de comando do Windows PowerShell

Abra o **Windows PowerShell** na tela **Iniciar** no modo de administrador.

### <a name="step-3---run-the-powershell-script"></a>Etapa 3 – executar o script do PowerShell

No Shell de linha de comando do PowerShell, navegue até a pasta que contém o script que você baixou. Altere `AutomationAccountName`os valores dos parâmetros, `AAResourceGroupName` `OMSResourceGroupName` `HybridGroupName` `SubscriptionID`,,, e. `WorkspaceName` Em seguida, execute o script.

Você é solicitado a autenticar com o Azure depois de executar o script. Você deve fazer login com uma conta que seja membro da função Administradores da inscrição e co-administrador da assinatura.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionID <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="step-4---install-nuget"></a>Etapa 4-instalar o NuGet

Você será solicitado a concordar em instalar o NuGet e a autenticar com suas credenciais do Azure. Se você não tiver a versão mais recente do NuGet, poderá obtê-la nas [versões de distribuição do NuGet disponíveis](https://www.nuget.org/downloads).

### <a name="step-5---verify-the-deployment"></a>Etapa 5 – verificar a implantação

Depois que o script for concluído, a página Grupos de Trabalhadores Hybrid mostrará o novo grupo e o número de membros. Se for um grupo existente, o número de membros é incrementado. Você pode selecionar o grupo na lista na página grupos de Hybrid Worker e escolher o bloco **Hybrid Workers** . Na página Hybrid Workers, você pode ver cada membro do grupo listado.

## <a name="manual-deployment"></a>Implantação manual

No computador de destino, execute as duas primeiras etapas uma vez para o ambiente de automação. Em seguida, execute as etapas restantes para cada computador de trabalho.

### <a name="step-1---create-a-log-analytics-workspace"></a>Etapa 1-criar um espaço de trabalho Log Analytics

Se você ainda não tiver um espaço de trabalho Log Analytics, examine as [diretrizes de design de Azure monitor log](../azure-monitor/platform/design-logs-deployment.md) antes de criar o espaço de trabalho.

### <a name="step-2---add-the-automation-solution-to-the-log-analytics-workspace"></a>Etapa 2 – adicionar a solução de automação ao espaço de trabalho Log Analytics

A solução de automação adiciona funcionalidade para a automação do Azure, incluindo suporte para o Hybrid Runbook Worker. Quando você adiciona a solução ao seu espaço de trabalho do Log Analytics, ela envia automaticamente para o computador do agente os componentes de trabalho que você instala conforme descrito na próxima etapa.

Para adicionar a solução de automação ao seu espaço de trabalho, execute o seguinte cmdlet do PowerShell.

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="step-3---install-the-log-analytics-agent-for-windows"></a>Etapa 3 – instalar o agente de Log Analytics para Windows

O agente Log Analytics para Windows conecta computadores a um espaço de trabalho Azure Monitor Log Analytics. Quando você instala o agente no seu computador e o conecta ao seu espaço de trabalho, ele baixa automaticamente os componentes necessários para o Hybrid Runbook Worker.

Para instalar o agente no computador, siga as instruções em [conectar computadores Windows para Azure monitor logs](../log-analytics/log-analytics-windows-agent.md). Você poderá repetir esse processo em vários computadores para adicionar vários trabalhos ao ambiente.

Quando o agente tiver se conectado com êxito ao seu espaço de trabalho do Log Analytics após alguns minutos, você poderá executar a consulta a seguir para verificar se ele está enviando dados de pulsação para o espaço de trabalho.

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

Nos resultados da pesquisa, você deve ver os registros de pulsação do computador, indicando que ele está conectado e relatando para o serviço. Por padrão, cada agente encaminha um registro de pulsação para seu espaço de trabalho atribuído. 

Use as etapas a seguir para concluir a instalação e a instalação do agente.

1. Habilite a solução para carregar o computador do agente. Confira [integração de computadores no espaço de trabalho](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-automation-account#onboard-machines-in-the-workspace).
2. Verifique se o agente baixou corretamente a solução de automação. 
3. Para confirmar a versão do Hybrid Runbook Worker, navegue até **C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation** e anote a subpasta **version** .

### <a name="step-4---install-the-runbook-environment-and-connect-to-azure-automation"></a>Etapa 4-instalar o ambiente de runbook e conectar-se à automação do Azure

Quando você configura um agente para relatar a um espaço de trabalho do Log Analytics, a solução de automação `HybridRegistration` envia por push o módulo do `Add-HybridRunbookWorker` PowerShell, que contém o cmdlet. Use este cmdlet para instalar o ambiente de runbook no computador e registrá-lo com a automação do Azure.

Abra uma sessão do PowerShell no modo de Administrador e execute os comandos a seguir para importar o módulo.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Agora, execute `Add-HybridRunbookWorker` o cmdlet usando a sintaxe a seguir.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Você pode obter as informações necessárias para esse cmdlet na página Gerenciar Chaves no portal do Azure. Abra esta página selecionando **chaves** na página configurações na sua conta de automação.

![Página gerenciar chaves](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* Para o `GroupName` parâmetro, use o nome do grupo de Hybrid runbook Worker. Se esse grupo já existir na conta de automação, o computador atual será adicionado a ele. Se esse grupo não existir, será adicionado.
* Para o `EndPoint` parâmetro, use a entrada **URL** na página gerenciar chaves.
* Para o `Token` parâmetro, use a entrada de **chave de acesso primária** na página gerenciar chaves.
* Se necessário, defina o `Verbose` parâmetro para receber detalhes sobre a instalação.

### <a name="step-5----install-powershell-modules"></a>Etapa 5-instalar os módulos do PowerShell

Os Runbooks podem usar qualquer uma das atividades e cmdlets definidos nos módulos instalados em seu ambiente de Automação do Azure. Como esses módulos não são implantados automaticamente em computadores locais, você deve instalá-los manualmente. A exceção é o módulo do Azure. Esse módulo é instalado por padrão e fornece acesso a cmdlets para todos os serviços e atividades do Azure para a automação do Azure.

Como a principal finalidade do recurso de Hybrid Runbook Worker é gerenciar recursos locais, é mais provável que você precise instalar os módulos que dão suporte a esses recursos, `PowerShellGet` particularmente o módulo. Para obter informações sobre a instalação de módulos do Windows PowerShell, consulte [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell).

Os módulos instalados devem estar em um local referenciado pela variável `PSModulePath` de ambiente para que o Hybrid Worker possa importá-los automaticamente. Para obter mais informações, consulte [install modules in PSModulePath](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="next-steps"></a>Próximas etapas

* Para saber como configurar os runbooks para automatizar processos no datacenter local ou em outro ambiente de nuvem, consulte [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Para obter instruções sobre como remover Hybrid runbook Workers, consulte [remover Hybrid runbook Workers da automação do Azure](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Para saber como solucionar problemas de seus Hybrid runbook Workers, consulte [solução de problemas de Hybrid runbook Workers do Windows](troubleshoot/hybrid-runbook-worker.md#windows).
* Para obter etapas adicionais para solucionar problemas de gerenciamento de atualizações, consulte [Gerenciamento de atualizações: solução de problemas](troubleshoot/update-management.md).
