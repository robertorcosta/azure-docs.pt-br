---
title: Implantar um Hybrid Runbook Worker do Windows na Automação do Azure
description: Este artigo mostra como implantar um Hybrid Runbook Worker para executar runbooks em computadores baseados em Windows no seu datacenter local ou ambiente de nuvem.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 8a21f6a58e8dc657d3b60aac33661504363072e2
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84309907"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Implantar um Windows híbrido Runbook Worker

É possível usar o recurso Hybrid Runbook Worker da Automação do Azure para executar os runbooks diretamente no computador que hospeda a função e os recursos do ambiente para gerenciar esses recursos locais. A Automação do Azure armazena e gerencia runbooks e os entrega a um ou mais computadores atribuídos. Este artigo descreve como implantar o Hybrid Runbook Worker em um computador Windows.

Depois de implantar com êxito um trabalhador de runbook, revise [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para saber como configurar seus runbooks para automatizar processos em seu datacenter local ou em outro ambiente de nuvem.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Instalação e configuração do Hybrid Runbook Worker do Windows

Para instalar e configurar um Windows Hybrid Runbook Worker, você pode usar um dos métodos a seguir.

* Para VMs do Azure, instale o agente Log Analytics para Windows usando a [extensão de máquina virtual para o Windows](../virtual-machines/extensions/oms-windows.md). A extensão instala o agente do Log Analytics em máquinas virtuais do Azure e registra máquinas virtuais em um workspace do Log Analytics existente usando um modelo do Resource Manager ou o PowerShell. Depois que o agente é instalado, a VM pode ser adicionada a um grupo do Hybrid Runbook Worker em sua conta de Automação. Veja as etapas 3 e 4 na seção [Implantação Manual](#manual-deployment).

* Use um runbook de Automação do Azure para automatizar completamente o processo de configuração de um computador com Windows. Esse é o método recomendado para computadores em seu datacenter ou em outro ambiente de nuvem.

* Siga um procedimento passo a passo para instalar e configurar manualmente a função Hybrid Runbook Worker em sua VM não Azure.

> [!NOTE]
> Para gerenciar a configuração de servidores compatíveis com a função Hybrid Runbook Worker com DSC (Desired State Configuration), é preciso adicioná-los como nós DSC.

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>Requisitos mínimos para um Hybrid Runbook Worker do Windows

Os requisitos mínimos para um Windows Hybrid Runbook Worker são:

* Windows Server 2012 ou posterior
* Windows PowerShell 5.1 ou posterior ([baixe o WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 ou posterior
* Dois núcleos
* 4 GB de RAM
* Porta 443 (saída)

### <a name="network-configuration"></a>Configuração de rede

Para obter mais requisitos de rede para o Hybrid Runbook Worker, consulte [ Configurando sua rede ](automation-hybrid-runbook-worker.md#network-planning).

### <a name="enabling-servers-for-management-with-azure-automation-state-configuration"></a>Habilitar servidores para gerenciamento com State Configuration da Automação do Azure

Para obter mais informações sobre como habilitar servidores com State Configuration da Automação do Azure, veja [Habilitar computadores para gerenciamento pelo State Configuration da Automação do Azure](automation-dsc-onboarding.md).

Se você habilitar o [Gerenciamento de Atualizações](automation-update-management.md) da Automação do Azure, qualquer computador Windows que esteja conectado ao seu workspace do Log Analytics como um Hybrid Runbook Worker será automaticamente configurado para permitir atualizações de runbook. No entanto, essa função de trabalho não está registrada em nenhum grupo do Hybrid Runbook Worker já definido em sua conta de Automação.

### <a name="addition-of-the-computer-to-a-hybrid-runbook-worker-group"></a>Adição do computador a um grupo do Hybrid Runbook Worker

É possível adicionar o computador da função de trabalho a um grupo do Hybrid Runbook Worker em sua conta de Automação. Observe que é preciso permitir runbooks de automação contanto que você esteja usando a mesma conta para o recurso de Automação do Azure e a associação ao grupo do Hybrid Runbook Worker. Essa funcionalidade foi adicionada à versão 7.2.12024.0 do Hybrid Runbook Worker.

## <a name="automated-deployment"></a>Implantação automatizada

No computador desejado, execute as etapas a seguir para automatizar a instalação e a configuração da função do Windows Hybrid Worker.

### <a name="step-1---download-the-powershell-script"></a>Etapa 1: baixar o script do PowerShell

Baixe o script **New-OnPremiseHybridWorker.ps1** na [Galeria do PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). O download deve ser feito diretamente no computador que executa a função Hybrid Runbook Worker ou em outro computador em seu ambiente. Depois de baixar o script, copie-o para a sua função de trabalho. O script **New-OnPremiseHybridWorker.ps1** usa os parâmetros descritos abaixo durante a execução.

| Parâmetro | Status | Descrição |
| --------- | ------ | ----------- |
| `AAResourceGroupName` | Obrigatório | O nome do grupo de recursos associado à sua conta de automação. |
| `AutomationAccountName` | Obrigatório | O nome da sua conta de automação.
| `Credential` | Opcional | As credenciais a serem usadas ao fazer logon no ambiente do Azure. |
| `HybridGroupName` | Obrigatório | O nome de um grupo do Hybrid Runbook Worker que você especifica como um destino para os runbooks que dão suporte a esse cenário. |
| `OMSResourceGroupName` | Opcional | O nome do grupo de recursos do espaço de trabalho do Log Analytics. Se esse grupo de recursos não for especificado, o valor de `AAResourceGroupName` será usado. |
| `SubscriptionID` | Obrigatório | O identificador da assinatura do Azure associada à sua conta de Automação. |
| `TenantID` | Opcional | O identificador da organização do locatário associado à sua conta de Automação. |
| `WorkspaceName` | Opcional | O nome do espaço de trabalho do Log Analytics. Se você não tiver um espaço de trabalho do Log Analytics, o script cria e configura um. |

> [!NOTE]
> Ao habilitar recursos, a Automação do Azure permite que somente determinadas regiões vinculem um workspace do Log Analytics e uma conta de Automação. Para obter uma lista dos pares de mapeamento compatíveis, confira [Mapeamento de região para conta da Automação e workspace do Log Analytics](how-to/region-mappings.md).

### <a name="step-2---open-windows-powershell-command-line-shell"></a>Etapa 2: abrir o shell de linha de comando do Windows PowerShell

No modo Administrador, abra o **Windows PowerShell** na tela **Iniciar**.

### <a name="step-3---run-the-powershell-script"></a>Etapa 3: executar o script do PowerShell

No shell de linha de comando do PowerShell, navegue até a pasta que contém o script que você baixou. Mude os valores dos parâmetros `AutomationAccountName`, `AAResourceGroupName`, `OMSResourceGroupName`, `HybridGroupName`, `SubscriptionID` e `WorkspaceName`. Em seguida, execute o script.

Você é solicitado a autenticar com o Azure depois de executar o script. Faça login com uma conta que seja membro da função Administradores de Assinatura e coadministrador da assinatura.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionID <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="step-4---install-nuget"></a>Etapa 4: instalar o NuGet

É preciso concordar com a instalação do NuGet e autenticar com suas credenciais do Azure. Se você não tiver a versão mais recente do NuGet, poderá obtê-la em [versões de distribuição do NuGet disponíveis](https://www.nuget.org/downloads).

### <a name="step-5---verify-the-deployment"></a>Etapa 5: verificar a implantação

Depois que o script for concluído, a página Grupos de Hybrid Worker mostrará o novo grupo e o número de membros. Se for um grupo existente, o número de membros é incrementado. Você pode selecionar o grupo na lista d página Grupos do Hybrid Worker e escolher o bloco **Hybrid Workers**. Na página Hybrid Workers, você verá cada membro do grupo listado.

## <a name="manual-deployment"></a>Implantação manual

No computador de destino, execute as duas primeiras etapas uma vez para o ambiente de Automação. Em seguida, execute as etapas restantes para cada computador de trabalho.

### <a name="step-1---create-a-log-analytics-workspace"></a>Etapa 1: criar um workspace do Log Analytics

Se você ainda não tiver um workspace do Log Analytics, examine as [diretrizes de design do Log do Azure Monitor](../azure-monitor/platform/design-logs-deployment.md) antes de criar o workspace.

### <a name="step-2---add-an-azure-automation-feature-to-the-log-analytics-workspace"></a>Etapa 2: adicionar um recurso de Automação do Azure ao workspace do Log Analytics

Um recurso da Automação acrescenta funcionalidades à Automação do Azure, incluindo compatibilidade com Hybrid Runbook Worker. Quando você habilita um recurso de Automação do Azure em seu workspace do Log Analytics, os componentes da função de trabalho são enviados automaticamente para o computador do agente.

Para adicionar o recurso de Automação do Azure, por exemplo, Gerenciamento de Atualizações, ao seu workspace, execute o seguinte cmdlet do PowerShell:

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
```

### <a name="step-3---install-the-log-analytics-agent-for-windows"></a>Etapa 3: instalar o agente do Log Analytics para Windows

O agente do Log Analytics para Windows conecta computadores a um workspace do Log Analytics do Azure Monitor. Quando você instala o agente no computador e o conecta ao seu espaço de trabalho, ele baixa automaticamente os componentes necessários para o Hybrid Runbook Worker.

Para instalar o agente no computador, siga as instruções em [Conectar computadores Windows aos logs do Azure Monitor](../log-analytics/log-analytics-windows-agent.md). Você poderá repetir esse processo em vários computadores para adicionar vários trabalhos ao ambiente.

Quando o agente tiver se conectado ao seu workspace do Log Analytics após alguns minutos, você poderá executar a consulta a seguir para verificar se ele está enviando dados de pulsação para o workspace.

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

Nos resultados de pesquisa, você deverá ver os registros de pulsação para o computador, indicando que está conectado e comunicando-se com o serviço. Por padrão, cada agente encaminha um registro de pulsação para seu workspace atribuído. Use as etapas a seguir para concluir a instalação e a configuração do agente.

1. Habilite o recurso para adicionar o computador do agente. Veja [Habilitar computadores no workspace](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-automation-account#onboard-machines-in-the-workspace).
2. Verifique se o agente baixou corretamente o recurso de Automação do Azure. 
3. Para confirmar a versão do Hybrid Runbook Worker, procure **C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation** e veja a subpasta **version**.

### <a name="step-4---install-the-runbook-environment-and-connect-to-azure-automation"></a>Etapa 4: instalar o ambiente de runbook e conectá-lo à Automação do Azure

Quando você configura um agente para se comunicar com um workspace do Log Analytics, o recurso de Automação do Azure envia por push o módulo PowerShell `HybridRegistration`, que contém o cmdlet `Add-HybridRunbookWorker`. É possível usar esse cmdlet para instalar o ambiente do runbook no computador e registrá-lo na Automação do Azure.

Abra uma sessão do PowerShell no modo de Administrador e execute os comandos a seguir para importar o módulo.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Agora, execute o cmdlet `Add-HybridRunbookWorker` usando a sintaxe a seguir.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Você pode obter as informações necessárias sobre esse cmdlet na página Gerenciar Chaves no portal do Azure. Abra esta página selecionando **Chaves** na página Configurações na sua conta de Automação.

![Página Gerenciar Chaves](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* Para o parâmetro `GroupName`, use o nome do grupo de Hybrid Runbook Worker. Se esse grupo já existir na conta de automação, o computador atual será adicionado a ele. Se esse grupo não existir, será adicionado.
* Para o parâmetro `EndPoint`, use a entrada **URL** na página Gerenciar Chaves.
* Para o parâmetro `Token`, use a entrada **CHAVE DE ACESSO PRIMÁRIA** na página Gerenciar Chaves.
* Se necessário, defina o parâmetro `Verbose` para receber detalhes sobre a instalação.

### <a name="step-5----install-powershell-modules"></a>Etapa 5: instalar módulos do PowerShell

Os Runbooks podem usar qualquer uma das atividades e cmdlets definidos nos módulos instalados em seu ambiente de Automação do Azure. Como esses módulos não são implantados automaticamente em computadores locais, você deve instalá-los manualmente. A exceção é o módulo do Azure. Esse módulo é instalado por padrão e fornece acesso a cmdlets para todos os serviços e atividades do Azure para a Automação do Azure.

Como o objetivo principal do Hybrid Runbook Worker é gerenciar recursos locais, você provavelmente precisará instalar os módulos que permitem esses recursos, principalmente o módulo `PowerShellGet`. Para obter informações sobre como instalar módulos do Windows PowerShell, veja [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell).

Os módulos instalados devem estar em um local referenciado pela variável de ambiente `PSModulePath`, para que o Hybrid Worker possa importá-los automaticamente. Para obter mais informações, veja [Instalar módulos no PSModulePath](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-windows-computer"></a><a name="remove-windows-hybrid-runbook-worker"></a>Remover o Hybrid Runbook Worker de um computador Windows local

1. No portal do Azure, vá para sua conta de automação.
2. Em **Configurações da conta**, selecione **Chaves** e anote os valores para **URL** e **Chave de acesso primária**.

3. Abra uma sessão do PowerShell no modo de Administrador e execute o comando a seguir com a URL e os valores de chave de acesso primária. Use o parâmetro `Verbose` para obter um log detalhado do processo de remoção. Para remover computadores antigos do grupo do Hybrid Worker, use o parâmetro opcional `machineName`.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Remover um grupo de Hybrid Worker

Para remover um grupo do Hybrid Runbook Worker, você primeiro precisa remover o Hybrid Runbook Worker de cada computador que seja membro do grupo. Em seguida, execute as seguintes etapas para remover o grupo:

1. Abra a conta de Automação no Portal do Azure.
2. Em **Automação de Processo**, selecione **Grupos de Trabalho Híbrido**. Selecione o grupo que você deseja excluir. A página de propriedades desse grupo é exibida.

   ![Página Propriedades](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Na página de propriedades do grupo selecionado, selecione **Excluir**. Uma mensagem pede para você confirmar esta ação. Selecione **Sim** se tiver certeza de que deseja continuar.

   ![Mensagem de confirmação](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Esse processo pode levar vários segundos para terminar. Você pode acompanhar o progresso em **Notificações** no menu.

## <a name="next-steps"></a>Próximas etapas

* Para saber como configurar os runbooks para automatizar processos no datacenter local ou em outro ambiente de nuvem, consulte [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Para saber como solucionar problemas de seus Hybrid Runbook Workers, veja [Solucionar Problemas do Hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#general).
