---
title: Solução de problemas problemas do agente de atualização do Linux no Gerenciamento de Atualização de Automação do Azure
description: Saiba como solucionar problemas e resolver problemas com o agente de atualização do Linux Windows usando a solução Update Management.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: bba1c7e89a9c3bb1c9aa1567e36dd71a40f14636
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679068"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>Solucionar problemas do agente de atualização do Linux

Pode haver muitas razões para sua máquina não estar aparecendo tão pronta (saudável) no Gerenciamento de Atualizações. No Update Management, você pode verificar a saúde de um agente Híbrido Runbook Worker para determinar o problema subjacente. Este artigo discute como executar o solucionador de problemas para máquinas Azure a partir do portal Azure e máquinas não-Azure no [cenário offline](#troubleshoot-offline). 

A lista a seguir é composta pelos três estados de preparação em que um computador pode estar:

* Pronto - O Híbrido Runbook Worker foi implantado e foi visto pela última vez há menos de 1 hora.
* Desconectado - O Híbrido Runbook Worker foi implantado e foi visto pela última vez há mais de 1 hora.
* Não configurado - O Hybrid Runbook Worker não foi encontrado ou não terminou o onboarding.

> [!NOTE]
> Pode haver um pequeno atraso entre o que o portal Azure mostra e o estado atual de uma máquina.

## <a name="start-the-troubleshooter"></a>Iniciar a solução de problemas

Para computadores do Azure, ao clicar no link **Solução de Problemas** na coluna **Preparação do Agente de Atualização** no portal, a página Solucionar Problemas do Agente de Atualização será iniciada. Para máquinas não-Azure, o link traz você para este artigo. Consulte as instruções off-line para solucionar problemas de uma máquina não-Azure.

![vm list page](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> As verificações exigem que a VM esteja em execução. Se a VM não estiver em execução, você será apresentado com um botão **Iniciar o VM.**

Na página Solucionar problemas do Agente de Atualização, clique em **Executar Verificações** para iniciar a solução de problemas. O solucionador de problemas usa [o comando Executar](../../virtual-machines/linux/run-command.md) para executar um script na máquina para verificar as dependências. Quando a solução de problemas é concluída, retorna o resultado das verificações.

![Página Solucionar problemas](../media/update-agent-issues-linux/troubleshoot-page.png)

Após a conclusão, os resultados são retornados na janela. As seções de verificação fornecem informações sobre o que cada verificação está procurando.

![Página Atualizar verificações de agente](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Verificações de pré-requisitos

### <a name="operating-system"></a>Sistema operacional

A verificação do sistema operacional verifica se o Hybrid Runbook Worker está executando um dos seguintes sistemas operacionais:

|Sistema operacional  |Observações  |
|---------|---------|
|CentOS 6 (x86/x64) e 7 (x64)      | Os agentes do Linux devem ter acesso a um repositório de atualização. O patch baseado em classificação requer que o yum retorne dados de segurança que o CentOS não possui.         |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Os agentes do Linux devem ter acesso a um repositório de atualização.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Os agentes do Linux devem ter acesso a um repositório de atualização.        |
|Ubuntu 14.04 LTS, 16.04 LTS e 18.04 LTS (x86/x64)      |Os agentes do Linux devem ter acesso a um repositório de atualização.         |

## <a name="monitoring-agent-service-health-checks"></a>Monitoramento das verificações de integridade de serviço do agente

### <a name="log-analytics-agent"></a>Agente do Log Analytics

Esta verificação garante que o agente Log Analytics para Linux esteja instalado. Para obter instruções sobre como instalar, consulte [Instalar o agente para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
).

### <a name="log-analytics-agent-status"></a>Status do agente do Log Analytics

Esta verificação garante que o agente log analytics para Linux esteja sendo executado. Se o agente não estiver em execução, será possível executar o comando a seguir para tentar reiniciá-lo. Para saber mais sobre como solucionar problemas do agente, confira [Linux Hybrid Runbook worker troubleshooting](hybrid-runbook-worker.md#linux) (Solução de problemas de trabalho do Hybrid Runbook do Linux)

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Hospedagem múltipla

Essa verificação determina se o agente está se comunicando com vários workspaces. Não há suporte para hospedagem múltipla pelo Gerenciamento de Atualizações.

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

Esta verificação verifica se o agente Log Analytics para Linux tem o pacote Hybrid Runbook Worker. Este pacote é necessário para o Gerenciamento de Atualizações trabalhar.

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

Esta verificação determina se o Hybrid Runbook Worker pode se comunicar adequadamente com o Azure Automation no espaço de trabalho do Log Analytics.

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

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Solução de problemas offline

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

## <a name="next-steps"></a>Próximas etapas

Para solucionar problemas adicionais com seus trabalhadores híbridos do runbook, consulte [Troubles - Hybrid Runbook Workers](hybrid-runbook-worker.md).
