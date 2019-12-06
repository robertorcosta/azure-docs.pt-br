---
title: Entender a integridade do Linux Hybrid Runbook Worker no Azure Gerenciamento de Atualizações
description: Saiba como solucionar problemas com o Hybrid Runbook Worker no Linux que oferece suporte a Gerenciamento de Atualizações.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 924c2fd176b5b8e45352d616d226f484e814450d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849252"
---
# <a name="understand-the-linux-hybrid-runbook-worker-health-in-update-management"></a>Entenda a integridade do Linux Hybrid Runbook Worker no Gerenciamento de Atualizações

Pode haver vários motivos pelos quais o computador não mostra o status **Pronto** no Gerenciamento de Atualizações. No Gerenciamento de Atualizações, você pode verificar a integridade de um agente de Hybrid Runbook Worker para determinar o problema subjacente. Este artigo discute como executar a solução de problemas para computadores do Azure por meio do portal do Azure e de computadores não Azure no [cenário offline](#troubleshoot-offline).

A lista a seguir é composta pelos três estados de preparação em que um computador pode estar:

* **Pronto** -a Hybrid runbook Worker é implantada e foi vista pela última vez há menos de 1 hora.
* **Desconectado** -a Hybrid runbook Worker é implantada e foi vista pela última vez há uma hora.
* **Não configurado** -a Hybrid runbook Worker não foi encontrada ou não terminou a integração.

> [!NOTE]
> Pode haver um pequeno atraso entre o que o portal do Azure mostra e o estado atual do computador.

## <a name="start-the-troubleshooter"></a>Iniciar a solução de problemas

Para computadores do Azure, ao clicar no link **Solução de Problemas** na coluna **Preparação do Agente de Atualização** no portal, a página **Solucionar Problemas do Agente de Atualização** será iniciada. Para computadores não Azure, o link o leva a este artigo. Consulte as instruções offline para solucionar problemas em um computador não Azure.

![vm list page](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> As verificações exigem que a VM esteja em execução. Se a VM não estiver em execução, você verá um botão para **Iniciar a VM**.

Na página **Solucionar problemas do Agente de Atualização**, clique em **Executar Verificações** para iniciar a solução de problemas. A solução de problemas usa o [comando executar](../../virtual-machines/linux/run-command.md) para executar um script no computador para verificar as dependências. Quando a solução de problemas é concluída, retorna o resultado das verificações.

![Página Solucionar problemas](../media/update-agent-issues-linux/troubleshoot-page.png)

Após a conclusão, os resultados são retornados na janela. As seções de verificação fornecem informações sobre o que cada verificação está procurando.

![Página Atualizar verificações de agente](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Verificações de pré-requisitos

### <a name="operating-system"></a>Sistema operacional

A verificação do sistema operacional verifica se o Hybrid Runbook Worker está executando um dos seguintes sistemas operacionais:

|Sistema operacional  |Notas  |
|---------|---------|
|CentOS 6 (x86/x64) e 7 (x64)      | Os agentes do Linux devem ter acesso a um repositório de atualização. O patch baseado em classificação requer que o yum retorne dados de segurança que o CentOS não possui.         |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Os agentes do Linux devem ter acesso a um repositório de atualização.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Os agentes do Linux devem ter acesso a um repositório de atualização.        |
|Ubuntu 14.04 LTS, 16.04 LTS e 18.04 LTS (x86/x64)      |Os agentes do Linux devem ter acesso a um repositório de atualização.         |

## <a name="monitoring-agent-service-health-checks"></a>Monitoramento das verificações de integridade de serviço do agente

### <a name="log-analytics-agent"></a>Agente do log Analytics

Essa verificação garante que o agente do Log Analytics para Linux esteja instalado. Para obter instruções sobre como instalar, consulte [Instalar o agente para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
).

### <a name="log-analytics-agent-status"></a>Status do agente de Log Analytics

Essa verificação garante que o agente do Log Analytics para Linux esteja em execução. Se o agente não estiver em execução, será possível executar o comando a seguir para tentar reiniciá-lo. Para saber mais sobre como solucionar problemas do agente, confira [Linux Hybrid Runbook worker troubleshooting](hybrid-runbook-worker.md#linux) (Solução de problemas de trabalho do Hybrid Runbook do Linux)

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Hospedagem múltipla

Essa verificação determina se o agente está se comunicando com vários workspaces. Não há suporte para hospedagem múltipla pelo Gerenciamento de Atualizações.

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

Essa verificação verifica se o agente de Log Analytics para Linux tem o pacote Hybrid Runbook Worker. Este pacote é necessário para o Gerenciamento de Atualizações trabalhar.

### <a name="hybrid-runbook-worker-status"></a>Status do Hybrid Runbook Worker

Essa verificação garante que o Hybrid Runbook Worker esteja em execução no computador. Os processos a seguir devem estar presentes se o Hybrid Runbook Worker estiver sendo executado corretamente. Para obter mais informações, consulte [solução de problemas do agente do Log Analytics para Linux](hybrid-runbook-worker.md#oms-agent-not-running).

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Verificações de conectividade

### <a name="general-internet-connectivity"></a>Conectividade com a Internet geral

Essa verificação garante que o computador tenha acesso à internet.

### <a name="registration-endpoint"></a>Ponto de extremidade de registro

Essa verificação determina se o Hybrid Runbook Worker pode se comunicar corretamente com a automação do Azure com o espaço de trabalho Log Analytics.

As configurações de proxy e firewall devem permitir que o agente do Hybrid Runbook Worker se comunique com o ponto de extremidade de registro. Para obter uma lista de endereços e portas a serem abertas, confira [Planejamento de rede para Hybrid Workers](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>Ponto de extremidade de operações

Essa verificação determina se o agente pode se comunicar corretamente com o Serviço de Dados de Runtime do Trabalho.

As configurações de proxy e firewall devem permitir que o agente do Hybrid Runbook Worker se comunique com o Serviço de Dados de Runtime do Trabalho. Para obter uma lista de endereços e portas a serem abertas, confira [Planejamento de rede para Hybrid Workers](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="log-analytics-endpoint-1"></a>Ponto de extremidade do Log Analytics 1

Essa verificação verifica se o seu computador tem acesso aos pontos de extremidade necessários para o agente do Log Analytics.

### <a name="log-analytics-endpoint-2"></a>Ponto de extremidade do Log Analytics 2

Essa verificação verifica se o seu computador tem acesso aos pontos de extremidade necessários para o agente do Log Analytics.

### <a name="log-analytics-endpoint-3"></a>Ponto de extremidade do Log Analytics 3

Essa verificação verifica se o seu computador tem acesso aos pontos de extremidade necessários para o agente do Log Analytics.

## <a name="troubleshoot-offline"></a>Solucionar problemas offline

Você pode usar a solução de problemas offline em um Hybrid Runbook Worker executando o script localmente. O script de python [update_mgmt_health_check.py](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6) pode ser encontrados no Script Center. Um exemplo da saída desse script é mostrado no seguinte exemplo:

```output
Debug: Machine Information:   Static hostname: LinuxVM2
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 00000000000000000000000000000000
           Boot ID: 00000000000000000000000000000000
    Virtualization: microsoft
  Operating System: Ubuntu 16.04.5 LTS
            Kernel: Linux 4.15.0-1025-azure
      Architecture: x86-64


Passed: Operating system version is supported

Passed: Microsoft Monitoring agent is installed

Debug: omsadmin.conf file contents:
        WORKSPACE_ID=00000000-0000-0000-0000-000000000000
        AGENT_GUID=00000000-0000-0000-0000-000000000000
        LOG_FACILITY=local0
        CERTIFICATE_UPDATE_ENDPOINT=https://00000000-0000-0000-0000-000000000000.oms.opinsights.azure.com/ConfigurationService.Svc/RenewCertificate
        URL_TLD=opinsights.azure.com
        DSC_ENDPOINT=https://scus-agentservice-prod-1.azure-automation.net/Accou            nts/00000000-0000-0000-0000-000000000000/Nodes\(AgentId='00000000-0000-0000-0000-000000000000'\)
        OMS_ENDPOINT=https://00000000-0000-0000-0000-000000000000.ods.opinsights            .azure.com/OperationalData.svc/PostJsonDataItems
        AZURE_RESOURCE_ID=/subscriptions/00000000-0000-0000-0000-000000000000/re            sourcegroups/myresourcegroup/providers/microsoft.compute/virtualmachines/linuxvm            2
        OMSCLOUD_ID=0000-0000-0000-0000-0000-0000-00
        UUID=00000000-0000-0000-0000-000000000000


Passed: Microsoft Monitoring agent is running

Passed: Machine registered with log analytics workspace:['00000000-0000-0000-0000-000000000000']

Passed: Hybrid worker package is present

Passed: Hybrid worker is running

Passed: Machine is connected to internet

Passed: TCP test for {scus-agentservice-prod-1.azure-automation.net} (port 443)             succeeded

Passed: TCP test for {eus2-jobruntimedata-prod-su1.azure-automation.net} (port 4            43) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.ods.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.oms.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {ods.systemcenteradvisor.com} (port 443) succeeded

```

## <a name="next-steps"></a>Próximos passos

Para solucionar problemas adicionais com os Hybrid runbook Workers, consulte [solução de problemas-Hybrid runbook Workers](hybrid-runbook-worker.md).
