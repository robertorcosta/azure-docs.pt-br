---
title: Implantar um Hybrid Runbook Worker do Windows na Automação do Azure
description: Este artigo informa como implantar um Hybrid Runbook Worker que você pode usar para executar runbooks em computadores baseados no Windows em seu datacenter local ou ambiente de nuvem.
services: automation
ms.subservice: process-automation
ms.date: 06/24/2020
ms.topic: conceptual
ms.openlocfilehash: 634f200280a85ff865741cd03905101ff1e5c19f
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87448053"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Implantar um Windows híbrido Runbook Worker

Você pode usar o recurso Hybrid Runbook Worker da automação do Azure para executar runbooks diretamente no computador que está hospedando a função e em recursos no ambiente para gerenciar esses recursos locais. A automação do Azure armazena e gerencia runbooks e, em seguida, os entrega a um ou mais computadores designados. Este artigo descreve como implantar um Hybrid Runbook Worker em um computador Windows, como remover o trabalho e como remover um grupo de Hybrid Runbook Worker.

Depois de implantar com êxito um trabalhador de runbook, revise [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para saber como configurar seus runbooks para automatizar processos em seu datacenter local ou em outro ambiente de nuvem.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem o seguinte.

### <a name="a-log-analytics-workspace"></a>Um espaço de trabalho Log Analytics

A função Hybrid Runbook Worker depende de um espaço de trabalho de Log Analytics de Azure Monitor para instalar e configurar a função. Você pode criá-lo por meio de [Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace), por meio do [PowerShell](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json), ou no [portal do Azure](../azure-monitor/learn/quick-create-workspace.md).

Se você não tiver um Azure Monitor Log Analytics espaço de trabalho, examine as [diretrizes de design do Azure monitor log](../azure-monitor/platform/design-logs-deployment.md) antes de criar o espaço de trabalho.

Se você tiver um espaço de trabalho, mas ele não estiver vinculado à sua conta de automação, a habilitação de um recurso de automação adicionará funcionalidade para a automação do Azure, incluindo suporte para o Hybrid Runbook Worker. Quando você habilita um dos recursos de automação do Azure em seu espaço de trabalho Log Analytics, especificamente [Gerenciamento de atualizações](update-management/update-mgmt-overview.md) ou [controle de alterações e inventário](change-tracking.md), os componentes de trabalho são enviados automaticamente para o computador do agente.

   Para adicionar o recurso Gerenciamento de Atualizações ao seu espaço de trabalho, execute o seguinte cmdlet do PowerShell:

```powershell-interactive
   Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "Updates" -Enabled $true
```

   Para adicionar o Controle de Alterações e o recurso de inventário ao seu espaço de trabalho, execute o seguinte cmdlet do PowerShell:

```powershell-interactive
   Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "ChangeTracking" -Enabled $true
```

### <a name="log-analytics-agent"></a>Agente do Log Analytics

A função Hybrid Runbook Worker requer o [agente de log Analytics](../azure-monitor/platform/log-analytics-agent.md) para o sistema operacional Windows com suporte.

### <a name="supported-windows-operating-system"></a>Sistema operacional Windows com suporte

As seguintes versões do sistema operacional Windows têm suporte oficial para um Hybrid Runbook Worker do Windows:

* Windows Server 2019
* Windows Server 2016, versão 1709 e 1803
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enterprise (incluindo multissessão) e Pro
* Windows 8 Enterprise e Pro
* Windows 7 SP1

### <a name="minimum-requirements"></a>Requisitos mínimos

Os requisitos mínimos para um Windows Hybrid Runbook Worker são:

* Windows PowerShell 5.1 ou posterior ([baixe o WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 ou posterior
* Dois núcleos
* 4 GB de RAM
* Porta 443 (saída)

### <a name="network-configuration"></a>Configuração de rede

Para obter mais requisitos de rede para o Hybrid Runbook Worker, consulte [ Configurando sua rede ](automation-hybrid-runbook-worker.md#network-planning).

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Adicionando um computador a um grupo de Hybrid Runbook Worker

Você pode adicionar o computador de trabalho a um grupo de Hybrid Runbook Worker em sua conta de automação. Observe que é preciso permitir runbooks de automação contanto que você esteja usando a mesma conta para o recurso de Automação do Azure e a associação ao grupo do Hybrid Runbook Worker. Essa funcionalidade foi adicionada à versão 7.2.12024.0 do Hybrid Runbook Worker.

>[!NOTE]
>Habilitar a automação do Azure [Gerenciamento de atualizações](update-management/update-mgmt-overview.md) configura automaticamente qualquer computador Windows conectado ao seu espaço de trabalho log Analytics como um Hybrid runbook Worker para dar suporte ao gerenciamento de suas atualizações do sistema operacional. No entanto, essa função de trabalho não está registrada em nenhum grupo do Hybrid Runbook Worker já definido em sua conta de Automação.

## <a name="enabling-machines-for-management-with-azure-automation-state-configuration"></a>Habilitando computadores para gerenciamento com a configuração de estado de automação do Azure

Para obter informações sobre como habilitar computadores para gerenciamento com a configuração de estado de automação do Azure, consulte [habilitar máquinas para gerenciamento pela configuração de estado de automação do Azure](automation-dsc-onboarding.md).

> [!NOTE]
> Para gerenciar a configuração de computadores que dão suporte à função de Hybrid Runbook Worker com a DSC (configuração de estado desejado), você deve adicionar os computadores como nós DSC.

## <a name="windows-hybrid-runbook-worker-installation-options"></a>Opções de instalação do Windows Hybrid Runbook Worker

Para instalar e configurar um Windows Hybrid Runbook Worker, você pode usar um dos métodos a seguir.

* Para VMs do Azure, instale o agente Log Analytics para Windows usando a [extensão de máquina virtual para o Windows](../virtual-machines/extensions/oms-windows.md). A extensão instala o agente do Log Analytics em máquinas virtuais do Azure e registra máquinas virtuais em um workspace do Log Analytics existente usando um modelo do Resource Manager ou o PowerShell. Depois que o agente é instalado, a VM pode ser adicionada a um grupo do Hybrid Runbook Worker em sua conta de Automação.

* Para VMs não Azure, instale o agente do Log Analytics para Windows usando as opções de implantação descritas no artigo [conectar computadores Windows ao Azure monitor](../azure-monitor/platform/agent-windows.md) . Você pode repetir esse processo para que vários computadores adicionem vários trabalhadores ao seu ambiente. Depois que o agente é instalado, as VMs podem ser adicionadas a um grupo de Hybrid Runbook Worker em sua conta de automação.

* Use um script do PowerShell fornecido para [automatizar](#automated-deployment) completamente o processo de configuração de um ou mais computadores Windows. Esse é o método recomendado para computadores em seu datacenter ou em outro ambiente de nuvem.

## <a name="automated-deployment"></a>Implantação automatizada

No computador de destino, execute as seguintes etapas para automatizar a instalação e a configuração da função de Hybrid Worker do Windows usando o script do PowerShell **New-OnPremiseHybridWorker.ps1**. O script executa as seguintes etapas:

* Instala os módulos necessários
* Entrar com sua conta do Azure
* Verifica a existência do grupo de recursos e da conta de automação especificados
* Cria referências a atributos de conta de automação
* Cria um Azure Monitor espaço de trabalho Log Analytics se não for especificado
* Habilitar a solução de automação do Azure no espaço de trabalho
* Baixe e instale o agente de Log Analytics para Windows
* Registrar o computador como Hybrid Runbook Worker

### <a name="step-1---download-the-powershell-script"></a>Etapa 1: baixar o script do PowerShell

Baixe o script **New-OnPremiseHybridWorker.ps1** na [Galeria do PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). Depois de baixar o script, copie-o ou execute-o no computador de destino. O script **New-OnPremiseHybridWorker.ps1** usa os parâmetros descritos abaixo durante a execução.

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

No **menu iniciar** , clique **em Iniciar**, digite **PowerShell**, clique com o botão direito do mouse em **Windows PowerShell**e clique em **Executar como administrador**.

### <a name="step-3---run-the-powershell-script"></a>Etapa 3: executar o script do PowerShell

No shell de linha de comando do PowerShell, navegue até a pasta que contém o script que você baixou. Mude os valores dos parâmetros `AutomationAccountName`, `AAResourceGroupName`, `OMSResourceGroupName`, `HybridGroupName`, `SubscriptionID` e `WorkspaceName`. Em seguida, execute o script.

Você é solicitado a autenticar com o Azure depois de executar o script. Faça login com uma conta que seja membro da função Administradores de Assinatura e coadministrador da assinatura.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <nameOfAutomationAccount> -AAResourceGroupName <nameOfResourceGroup>`
-OMSResourceGroupName <nameOfOResourceGroup> -HybridGroupName <nameOfHRWGroup> `
-SubscriptionID <subscriptionId> -WorkspaceName <nameOfLogAnalyticsWorkspace>
```

### <a name="step-4---install-nuget"></a>Etapa 4: instalar o NuGet

É preciso concordar com a instalação do NuGet e autenticar com suas credenciais do Azure. Se você não tiver a versão mais recente do NuGet, poderá baixá-la das [versões de distribuição do NuGet disponíveis](https://www.nuget.org/downloads).

### <a name="step-5---verify-the-deployment"></a>Etapa 5: verificar a implantação

Depois que o script for concluído, a página grupos de Hybrid Worker na sua conta de automação mostrará o novo grupo e o número de membros. Se for um grupo existente, o número de membros é incrementado. Você pode selecionar o grupo na lista d página Grupos do Hybrid Worker e escolher o bloco **Hybrid Workers**. Na página Hybrid Workers, você verá cada membro do grupo listado.

## <a name="manual-deployment"></a>Implantação manual

Para instalar e configurar um Hybrid Runbook Worker do Windows, execute as etapas a seguir.

### <a name="step-1---verify-agent-is-reporting-to-workspace"></a>Etapa 1-verificar se o agente está relatando para o espaço de trabalho

O agente Log Analytics para Windows conecta computadores a um espaço de trabalho Azure Monitor Log Analytics. Quando você instala o agente em seu computador e o conecta ao seu espaço de trabalho, ele baixa automaticamente os componentes necessários para o Hybrid Runbook Worker.

Quando o agente tiver se conectado ao seu workspace do Log Analytics após alguns minutos, você poderá executar a consulta a seguir para verificar se ele está enviando dados de pulsação para o workspace.

```kusto
Heartbeat 
| where Category == "Direct Agent"
| where TimeGenerated > ago(30m)
```

Nos resultados da pesquisa, você deve ver os registros de pulsação do computador, indicando que ele está conectado e relatando para o serviço. Por padrão, cada agente encaminha um registro de pulsação para seu workspace atribuído. Use as etapas a seguir para concluir a instalação e a configuração do agente.

1. Habilite o recurso para adicionar o computador do agente. Para Gerenciamento de Atualizações e VMs do Azure, consulte [habilitar gerenciamento de atualizações de uma conta de automação](update-management/update-mgmt-enable-automation-account.md), [habilitar gerenciamento de atualizações navegando na portal do Azure](update-management/update-mgmt-enable-portal.md), [habilitando gerenciamento de atualizações de um runbook](update-management/update-mgmt-enable-runbook.md)ou [habilitando gerenciamento de atualizações de uma VM do Azure](update-management/update-mgmt-enable-vm.md). Para Controle de Alterações e VMs do Azure, consulte [habilitar VMs do Azure](automation-enable-changes-from-auto-acct.md#enable-azure-vms)e para VMs não Azure consulte [habilitar computadores no espaço de trabalho](automation-enable-changes-from-auto-acct.md#enable-machines-in-the-workspace).

2. Para confirmar a versão do Hybrid Runbook Worker, navegue até `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` e anote a subpasta **versão** .

### <a name="step-2---install-the-runbook-environment-and-connect-to-azure-automation"></a>Etapa 2-instalar o ambiente de runbook e conectar-se à automação do Azure

Quando você configura um agente para se comunicar com um workspace do Log Analytics, o recurso de Automação do Azure envia por push o módulo PowerShell `HybridRegistration`, que contém o cmdlet `Add-HybridRunbookWorker`. Use este cmdlet para instalar o ambiente de runbook no computador e registrá-lo com a automação do Azure.

Abra uma sessão do PowerShell no modo de Administrador e execute os comandos a seguir para importar o módulo.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Agora, execute o cmdlet `Add-HybridRunbookWorker` usando a sintaxe a seguir.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
```

Você pode obter as informações necessárias para os parâmetros `Url` e `Key` a partir da página de **chaves** em sua conta de automação. Selecione **chaves** na seção **configurações de conta** do lado esquerdo da página.

![Página Gerenciar Chaves](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* Para o `Url` parâmetro, copie o valor para **URL**.

* Para o `Key` parâmetro, copie o valor da **chave de acesso primária**.

* Para o parâmetro `GroupName`, use o nome do grupo de Hybrid Runbook Worker. Se esse grupo já existir na conta de automação, o computador atual será adicionado a ele. Se esse grupo não existir, será adicionado.

* Se necessário, defina o parâmetro `Verbose` para receber detalhes sobre a instalação.

### <a name="step-3----install-powershell-modules"></a>Etapa 3-instalar os módulos do PowerShell

Os Runbooks podem usar qualquer uma das atividades e cmdlets definidos nos módulos instalados em seu ambiente de Automação do Azure. Como esses módulos não são implantados automaticamente em computadores locais, você deve instalá-los manualmente. A exceção é o módulo do Azure. Esse módulo é instalado por padrão e fornece acesso a cmdlets para todos os serviços e atividades do Azure para a Automação do Azure.

Como o objetivo principal do Hybrid Runbook Worker é gerenciar recursos locais, você provavelmente precisará instalar os módulos que permitem esses recursos, principalmente o módulo `PowerShellGet`. Para obter informações sobre como instalar módulos do Windows PowerShell, veja [Windows PowerShell](/powershell/scripting/developer/windows-powershell).

Os módulos instalados devem estar em um local referenciado pela variável de ambiente `PSModulePath`, para que o Hybrid Worker possa importá-los automaticamente. Para obter mais informações, veja [Instalar módulos no PSModulePath](/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-windows-machine"></a><a name="remove-windows-hybrid-runbook-worker"></a>Remover o Hybrid Runbook Worker de um computador Windows local

1. No portal do Azure, vá para sua conta de automação.

2. Em **Configurações da conta**, selecione **Chaves** e anote os valores para **URL** e **Chave de acesso primária**.

3. Abra uma sessão do PowerShell no modo de Administrador e execute o comando a seguir com a URL e os valores de chave de acesso primária. Use o parâmetro `Verbose` para obter um log detalhado do processo de remoção. Para remover computadores antigos do grupo do Hybrid Worker, use o parâmetro opcional `machineName`.

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Remover um grupo de Hybrid Worker

Para remover um grupo de Hybrid Runbook Worker, primeiro você precisa remover o Hybrid Runbook Worker de cada computador que seja membro do grupo. Em seguida, execute as seguintes etapas para remover o grupo:

1. Abra a conta de Automação no Portal do Azure.

2. Em **Automação de Processo**, selecione **Grupos de Trabalho Híbrido**. Selecione o grupo que você deseja excluir. A página de propriedades desse grupo é exibida.

   ![Página Propriedades](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Na página de propriedades do grupo selecionado, selecione **Excluir**. Uma mensagem pede para você confirmar esta ação. Selecione **Sim** se tiver certeza de que deseja continuar.

   ![Mensagem de confirmação](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Esse processo pode levar vários segundos para terminar. Você pode acompanhar o progresso em **Notificações** no menu.

## <a name="next-steps"></a>Próximas etapas

* Para saber como configurar os runbooks para automatizar processos no datacenter local ou em outro ambiente de nuvem, consulte [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

* Para saber como solucionar problemas de seus Hybrid Runbook Workers, veja [Solucionar Problemas do Hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#general).
