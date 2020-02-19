---
title: Hybrid Runbook Worker do Windows de Automação do Azure
description: Este artigo fornece informações sobre a instalação de um Trabalhador de Runbook Híbrido de Automação do Azure que você pode usar para executar runbooks em computadores baseados no Windows em seu datacenter ou ambiente de nuvem local.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: a6d2e2d912f176a88dc993803d750e37cff1acb6
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443645"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Implantar um Windows híbrido Runbook Worker

É possível usar o recurso Hybrid Runbook Worker da Automação do Azure para executar os runbooks diretamente no computador que hospeda a função e os recursos do ambiente para gerenciar esses recursos locais. Os runbooks são armazenados e gerenciados na Automação do Azure e entregues a um ou mais computadores designados. Este artigo descreve como implantar o Hybrid Runbook Worker em um computador Windows.

Depois de implantar com êxito um trabalhador de runbook, revise [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para saber como configurar seus runbooks para automatizar processos em seu datacenter local ou em outro ambiente de nuvem.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Instalação e configuração do Windows Hybrid Runbook Worker

Para instalar e configurar um Hybrid Runbook Worker do Windows, você pode usar um dos métodos a seguir.

* Para VMs do Azure, você instala o agente do Log Analytics para Windows usando a [extensão da máquina virtual para Windows](../virtual-machines/extensions/oms-windows.md). A extensão instala o agente de Log Analytics em máquinas virtuais do Azure e registra as máquinas virtuais em um espaço de trabalho Log Analytics existente usando um modelo de Azure Resource Manager ou com o PowerShell. Depois que o agente for instalado, a VM poderá ser adicionada a um grupo de Hybrid Runbook Worker em sua conta de automação seguindo a **etapa 4** na seção [implantação manual](#manual-deployment) abaixo.

* Use um runbook de automação para automatizar completamente o processo de configuração de um computador com Windows. Esse é o método recomendado para computadores em seu datacenter ou em outro ambiente de nuvem.

* Siga um procedimento passo a passo para instalar e configurar manualmente a função Hybrid Runbook Worker em sua VM não Azure.

> [!NOTE]
> Para gerenciar a configuração de seus servidores que suportam a função de executante de caderno híbrido com DSC (Configuração de estado desejado), você precisa adicioná-los como nós DSC.

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

Para obter mais informações sobre servidores de integração para gerenciamento com o DSC, consulte [Máquinas de integração para gerenciamento pelo DSC de automação do Azure](automation-dsc-onboarding.md).
Se você habilitar a [solução gerenciamento de atualizações](../operations-management-suite/oms-solution-update-management.md), qualquer computador Windows conectado ao seu espaço de trabalho log Analytics será configurado automaticamente como um Hybrid runbook Worker para dar suporte a runbooks incluídos nesta solução. No entanto, ele não está registrado em nenhum grupo de Hybrid Worker já definido em sua conta de automação. 

### <a name="adding-the-computer-to-a-hybrid-runbook-worker-group"></a>Adicionando o computador a um grupo de Hybrid Runbook Worker

O computador pode ser adicionado a um grupo de executável de manual híbrido em sua conta de automação para oferecer suporte a registros de execução de automação, desde que você esteja usando a mesma conta para a solução e a associação de grupo de trabalhador de executável híbrido. Essa funcionalidade foi adicionada à versão 7.2.12024.0 do Hybrid Runbook Worker.

## <a name="automated-deployment"></a>Implantação automatizada

No computador de destino, execute as etapas a seguir para automatizar a instalação e a configuração da função de Hybrid Worker do Windows.

### <a name="1-download-the-powershell-script"></a>1. baixar o script do PowerShell

Faça o download do script New-OnPremiseHybridWorker.ps1 da [Galeria do PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker) diretamente do computador que executa a função de executor de manual híbrido ou de outro computador em seu ambiente. Copie o script para o trabalhador. O script New-OnPremiseHybridWorker.ps1 requer os seguintes parâmetros durante a execução:

   * *AAResourceGroupName* (obrigatório): o nome do grupo de recursos associado à sua conta de automação.
   * *OMSResourceGroupName* (opcional): o nome do grupo de recursos do espaço de trabalho do Log Analytics. Se este grupo de recursos não for especificado, *AAResourceGroupName* será usado.
   * *SubscriptionId* (obrigatório): a ID da assinatura do Azure na qual sua conta de automação está.
   * *Tenantid* (opcional): o identificador da organização do locatário associado à sua conta de automação.
   * *WorkspaceName* (opcional): o nome do espaço de trabalho do Log Analytics. Se você não tiver um espaço de trabalho do Log Analytics, o script cria e configura um.
   * *AutomationAccountName* (obrigatório): o nome da sua conta de automação.
   * *HybridGroupName* (obrigatório): o nome de um grupo de executável de manual híbrido que você especifica como um destino para os runbooks que suportam esse cenário.
   * *Credencial* (opcional): as credenciais a serem usadas ao fazer logon no ambiente do Azure.
  
   > [!NOTE]
   > Ao habilitar soluções, somente determinadas regiões têm suporte para vincular um espaço de trabalho do Log Analytics e uma Conta de Automação.
   >
   > Para obter uma lista dos pares de mapeamento com suporte, confira [mapeamento de região para conta de automação e espaço de trabalho de log Analytics](how-to/region-mappings.md).

### <a name="2-open-windows-powershell-command-line-shell"></a>2. abrir Shell de linha de comando do Windows PowerShell

Abra o **Windows PowerShell** na tela **Iniciar** no modo de administrador.

### <a name="3-run-the-powershell-script"></a>3. executar o script do PowerShell

No Shell de linha de comando do PowerShell, navegue até a pasta que contém o script que você baixou. Altere os valores para os parâmetros *-AutomationAccountName*, *-AAResourceGroupName*, *-OMSResourceGroupName*, *-HybridGroupName*, *-ScriptioncriptionId* e *-WorkspaceName*. Em seguida, execute o script.

Você é solicitado a autenticar com o Azure depois de executar o script. Você deve entrar com uma conta que seja membro da função Administradores de assinatura e coadministrador da assinatura.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="4-install-nuget"></a>4. instalar o NuGet

Você será solicitado a concordar em instalar o NuGet e a autenticar com suas credenciais do Azure. Se você não tiver a versão mais recente do NuGet, poderá obtê-la nas [versões de distribuição do NuGet disponíveis](https://www.nuget.org/downloads).

### <a name="5-verify-the-deployment"></a>5. verificar a implantação

Depois que o script for concluído, a página **Grupos de Trabalhadores Hybrid** mostrará o novo grupo e o número de membros. Se for um grupo existente, o número de membros é incrementado. Você pode selecionar o grupo na lista na página **grupos de Hybrid Worker** e selecionar o bloco **Hybrid Workers** . Na página **Hybrid Workers**, você verá cada membro do grupo listado.

## <a name="manual-deployment"></a>Implantação manual

No computador de destino, execute as duas primeiras etapas uma vez para o ambiente de automação. Em seguida, execute as etapas restantes para cada computador de trabalho.

### <a name="1-create-a-log-analytics-workspace"></a>1. criar um espaço de trabalho Log Analytics

Se você ainda não tiver um espaço de trabalho Log Analytics, primeiro examine as [diretrizes de design Azure monitor log](../azure-monitor/platform/design-logs-deployment.md) antes de criar um espaço de trabalho.

### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. Adicione a solução de automação ao espaço de trabalho Log Analytics

A solução de automação adiciona funcionalidade para automação do Azure, incluindo suporte para Hybrid Runbook Worker. Quando você adiciona a solução ao seu espaço de trabalho do Log Analytics, ele envia automaticamente os componentes de trabalho para o computador do agente que você instalará na próxima etapa.

Para adicionar a solução de **automação** ao seu espaço de trabalho, execute o seguinte cmdlet do PowerShell.

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="3-install-the-log-analytics-agent-for-windows"></a>3. instalar o agente de Log Analytics para Windows

O agente Log Analytics para Windows conecta computadores a um espaço de trabalho Azure Monitor Log Analytics. Quando você instala o agente no seu computador e o conecta ao seu espaço de trabalho, ele baixa automaticamente os componentes necessários para Hybrid Runbook Worker.

Para instalar o agente no computador, siga as instruções em [conectar computadores Windows para Azure monitor logs](../log-analytics/log-analytics-windows-agent.md). Você poderá repetir esse processo em vários computadores para adicionar vários trabalhos ao ambiente.

Quando o agente tiver se conectado com êxito ao seu espaço de trabalho do Log Analytics após alguns minutos, você poderá executar a consulta a seguir para verificar se ele está enviando dados de pulsação para o espaço de trabalho:

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

Nos resultados da pesquisa, você deve ver os registros de pulsação do computador, indicando que ele está conectado e relatando para o serviço. Por padrão, cada agente encaminha um registro de pulsação para seu espaço de trabalho atribuído. Você pode verificar se o agente baixou corretamente a solução de automação quando ela tem uma pasta chamada AzureAutomationFiles em C:\Program Files\Microsoft Monitoring Agent\Agent. Para confirmar a versão do Hybrid Runbook Worker, navegue até C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ e observe a subpasta \\*version* .

### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. instalar o ambiente de runbook e conectar-se à automação do Azure

Quando você configura um agente para relatar a um espaço de trabalho Log Analytics, a solução de automação envia por push o módulo HybridRegistration do PowerShell, que contém o cmdlet **Add-HybridRunbookWorker** . Use este cmdlet para instalar o ambiente de runbook no computador e registrá-lo com a automação do Azure.

Abra uma sessão do PowerShell no modo de Administrador e execute os comandos a seguir para importar o módulo.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Em seguida, execute o cmdlet **Add-HybridRunbookWorker** usando a sintaxe a seguir.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Você pode obter as informações necessárias para esse cmdlet na página "gerenciar chaves" no portal do Azure. Abra esta página selecionando a opção **Chaves** na página **Configurações** na sua conta de automação.

![Página "Gerenciar Chaves"](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* Para o parâmetro *GroupName* , use o nome do grupo de Hybrid runbook Worker. Se esse grupo já existir na conta de automação, o computador atual será adicionado a ele. Se esse grupo não existir, será adicionado.
* Para o parâmetro de *ponto de extremidade* , use a entrada **URL** na página gerenciar chaves.
* Para o parâmetro de *token* , use a entrada de **chave de acesso primária** na página gerenciar chaves.

Para receber informações detalhadas sobre a instalação, use a opção *-Verbose* com **Add-HybridRunbookWorker**.

### <a name="5-install-powershell-modules"></a>5. instalar módulos do PowerShell

Os runbooks podem usar qualquer uma das atividades e cmdlets definidos nos módulos instalados no ambiente de Automação do Azure. Esses módulos não são implantados automaticamente em computadores locais, portanto, você deve instalá-los manualmente. A exceção é o módulo do Azure. Esse módulo é instalado por padrão e fornece acesso a cmdlets para todos os serviços e atividades do Azure para a automação do Azure.

Como a principal finalidade do recurso de Hybrid Runbook Worker é gerenciar recursos locais, é mais provável que você precise instalar os módulos que dão suporte a esses recursos, especialmente o módulo PowerShellGet. Para obter informações sobre a instalação de módulos do Windows PowerShell, consulte [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell).

Os módulos instalados devem estar em um local referenciado pela variável de ambiente PSModulePath para que o Hybrid Worker possa importá-los automaticamente. Para obter mais informações, consulte [install modules in PSModulePath](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="next-steps"></a>Próximas etapas

* Para saber como configurar os runbooks para automatizar processos no datacenter local ou em outro ambiente de nuvem, consulte [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Para obter instruções sobre como remover os Hybrid Runbook Workers, consulte [Remover Hybrid Runbook Workers da Automação do Azure](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Para saber como solucionar problemas de seus Trabalhadores de Runbook Híbridos, consulte [Solucionando problemas de trabalhadores de runbook híbrido do Windows](troubleshoot/hybrid-runbook-worker.md#windows)
* Para obter etapas adicionais sobre como solucionar problemas com o Gerenciamento de Atualizações, consulte [Gerenciamento de Atualizações: solução de problemas](troubleshoot/update-management.md).
