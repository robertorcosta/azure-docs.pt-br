---
title: Implantar um Hybrid Runbook Worker do Linux na Automação do Azure
description: Este artigo informa como instalar um Hybrid Runbook Worker de automação do Azure para executar runbooks em computadores baseados em Linux em seu datacenter local ou ambiente de nuvem.
services: automation
ms.subservice: process-automation
ms.date: 06/24/2020
ms.topic: conceptual
ms.openlocfilehash: 7f19aec65ed2616d757718116ac948473dd4b0ed
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87448019"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Implantar o Hybrid Runbook Worker do Linux

Você pode usar o recurso Hybrid Runbook Worker da automação do Azure para executar runbooks diretamente no computador que está hospedando a função e em recursos no ambiente para gerenciar esses recursos locais. O Linux Hybrid Runbook Worker executa runbooks como um usuário especial que pode ser elevado para executar comandos que precisam de elevação. A automação do Azure armazena e gerencia runbooks e, em seguida, os entrega a um ou mais computadores designados. Este artigo descreve como instalar o Hybrid Runbook Worker em um computador Linux, como remover o trabalho e como remover um grupo do Hybrid Runbook Worker.

Depois de implantar com êxito um trabalhador de runbook, revise [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para saber como configurar seus runbooks para automatizar processos em seu datacenter local ou em outro ambiente de nuvem.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem o seguinte:

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

A função Hybrid Runbook Worker requer o [agente de log Analytics](../azure-monitor/platform/log-analytics-agent.md) para o sistema operacional Linux com suporte.

### <a name="supported-linux-operating-systems"></a>Sistemas operacionais Linux com suporte

O recurso Hybrid Runbook Worker dá suporte para as distribuições a seguir:

* Amazon Linux 2012.09 a 2015.09 (x86/x64)
* CentOS Linux 5, 6 e 7 (x86/x64)
* Oracle Linux 5, 6 e 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 e 7 (x86/x64)
* Debian GNU/Linux 6, 7 e 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS e 18.04 (x86/x64)
* SUSE Linux Enterprise Server 11 e 12 (x86/x64)

### <a name="minimum-requirements"></a>Requisitos mínimos

Os requisitos mínimos para um Hybrid Runbook Worker do Linux são:

* Dois núcleos
* 4 GB de RAM
* Porta 443 (saída)

| **Pacote necessário** | **Descrição** | **Versão mínima**|
|--------------------- | --------------------- | -------------------|
|Glibc |Biblioteca GNU C| 2.5-12 |
|Openssl| Bibliotecas OpenSSL | 1.0 (há suporte para TLS 1.1 e TLS 1.2)|
|Curl | cliente Web cURL | 7.15.5|
|Python-ctypes | O Python 2.x é obrigatório |
|PAM | Módulos de autenticação conectáveis|
| **Pacotes opcionais** | **Descrição** | **Versão mínima**|
| PowerShell Core | Para executar runbooks do PowerShell, o PowerShell Core precisa ser instalado. Consulte [Instalar PowerShell Core no Linux](/powershell/scripting/install/installing-powershell-core-on-linux) para saber como instalá-lo. | 6.0.0 |

## <a name="supported-runbook-types"></a>Tipos de runbook com suporte

Os Hybrid runbook Workers do Linux dão suporte a um conjunto limitado de tipos de runbook na automação do Azure, e eles são descritos na tabela a seguir.

|Tipo de runbook | Com suporte |
|-------------|-----------|
|Python 2 |Yes |
|PowerShell |Sim<sup>1</sup> |
|Fluxo de trabalho do PowerShell |No |
|Gráfico |No |
|Fluxo de Trabalho Gráfico do PowerShell |No |

<sup>1</sup> Os runbooks do PowerShell exigem que o PowerShell Core seja instalado no computador Linux. Consulte [Instalar PowerShell Core no Linux](/powershell/scripting/install/installing-powershell-core-on-linux) para saber como instalá-lo.

## <a name="install-a-linux-hybrid-runbook-worker"></a>Instalar um Hybrid Runbook Worker do Linux

Para instalar e configurar um Hybrid Runbook Worker do Linux, execute as etapas a seguir.

1. Implante o agente de Log Analytics no computador de destino.

    * Para VMs do Azure, instale o agente de Log Analytics para Linux usando a [extensão de máquina virtual para Linux](../virtual-machines/extensions/oms-linux.md). A extensão instala o agente de Log Analytics em máquinas virtuais do Azure e registra as máquinas virtuais em um espaço de trabalho Log Analytics existente usando um modelo Azure Resource Manager ou o CLI do Azure. Depois que o agente é instalado, a VM pode ser adicionada a um grupo do Hybrid Runbook Worker em sua conta de Automação.

    * Para VMs não Azure, instale o agente de Log Analytics para Linux usando as opções de implantação descritas no artigo [conectar computadores Linux ao Azure monitor](../azure-monitor/platform/agent-linux.md) . Você pode repetir esse processo para que vários computadores adicionem vários trabalhadores ao seu ambiente. Depois que o agente é instalado, as VMs podem ser adicionadas a um grupo de Hybrid Runbook Worker em sua conta de automação.

    > [!NOTE]
    > Para gerenciar a configuração de computadores que dão suporte à função de Hybrid Runbook Worker com a DSC (configuração de estado desejado), você deve adicionar os computadores como nós DSC.

    > [!NOTE]
    > A conta de [nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) com as permissões sudo correspondentes deve estar presente durante a instalação do Hybrid Worker do Linux. Se você tentar instalar o trabalho e a conta não estiver presente ou não tiver as permissões apropriadas, a instalação falhará.

2. Verifique se o agente está relatando para o espaço de trabalho.

    O agente de Log Analytics para Linux conecta computadores a um espaço de trabalho Azure Monitor Log Analytics. Quando você instala o agente em seu computador e o conecta ao seu espaço de trabalho, ele baixa automaticamente os componentes necessários para o Hybrid Runbook Worker.

    Quando o agente tiver se conectado ao seu workspace do Log Analytics após alguns minutos, você poderá executar a consulta a seguir para verificar se ele está enviando dados de pulsação para o workspace.

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    Nos resultados da pesquisa, você deve ver os registros de pulsação do computador, indicando que ele está conectado e relatando para o serviço. Por padrão, cada agente encaminha um registro de pulsação para seu workspace atribuído.

3. Execute o comando a seguir para adicionar o computador a um grupo de Hybrid runbook Worker, especificando os valores para os parâmetros `-w` , `-k` , `-g` e `-e` .

    Você pode obter as informações necessárias para parâmetros `-k` e `-e` na página **chaves** em sua conta de automação. Selecione **chaves** na seção **configurações de conta** do lado esquerdo da página.

    ![Página Gerenciar Chaves](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * Para o `-e` parâmetro, copie o valor para **URL**.

    * Para o `-k` parâmetro, copie o valor da **chave de acesso primária**.

    * Para o `-g` parâmetro, especifique o nome do grupo de Hybrid runbook Worker ao qual o novo Hybrid runbook Worker do Linux deve ingressar. Se esse grupo já existir na conta de automação, o computador atual será adicionado a ele. Se esse grupo não existir, ele será criado com esse nome.

    * Para o `-w` parâmetro, especifique sua ID de espaço de trabalho log Analytics.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

4. Depois que o comando for concluído, a página grupos de Hybrid Worker na sua conta de automação mostrará o novo grupo e o número de membros. Se este for um grupo existente, o número de membros será incrementado. Você pode selecionar o grupo na lista na página Grupos do Hybrid Worker e selecionar o bloco **Hybrid Workers**. Na página Hybrid Workers, você verá cada membro do grupo listado.

    > [!NOTE]
    > Se você estiver usando a extensão de máquina virtual Log Analytics para Linux para uma VM do Azure, é recomendável definir `autoUpgradeMinorVersion` `false` como as versões de atualização automática podem causar problemas com o Hybrid runbook Worker. Para saber como atualizar a extensão manualmente, confira [Implantação da CLI do Azure](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turn-off-signature-validation"></a>Desativar a validação de assinatura

Por padrão, o Linux Hybrid Runbook Workers exige validação de assinatura. Se você executar um runbook não assinado em um trabalho, verá um erro `Signature validation failed`. Para desativar a validação de assinatura, execute o comando a seguir. Substitua o segundo parâmetro pela ID do workspace do Log Analytics.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-linux-machine"></a><a name="remove-linux-hybrid-runbook-worker"></a>Remover o Hybrid Runbook Worker de um computador Linux local

Você pode usar o comando `ls /var/opt/microsoft/omsagent` no Hybrid Runbook Worker para obter a ID do workspace. É criada uma pasta nomeada com a ID do workspace.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Esse script não remove o agente de Log Analytics para Linux do computador. Ele remove apenas a funcionalidade e a configuração da função Hybrid Runbook Worker.

## <a name="remove-a-hybrid-worker-group"></a>Remover um grupo de Hybrid Worker

Para remover um grupo de Hybrid Runbook Worker de computadores Linux, use as mesmas etapas de um grupo do Hybrid Worker do Windows. Confira [Remover um grupo do Hybrid Worker](automation-windows-hrw-install.md#remove-a-hybrid-worker-group).

## <a name="next-steps"></a>Próximas etapas

* Para saber como configurar os runbooks para automatizar processos no datacenter local ou em outro ambiente de nuvem, consulte [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

* Para saber como solucionar problemas de seus Hybrid Runbook Workers, confira [Solucionar problemas do Hybrid Runbook Worker – Linux](troubleshoot/hybrid-runbook-worker.md#linux).
