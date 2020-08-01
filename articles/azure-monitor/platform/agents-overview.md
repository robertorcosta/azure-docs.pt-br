---
title: Visão geral agentes de monitoramento do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral detalhada dos agentes do Azure disponíveis que oferecem suporte ao monitoramento de máquinas virtuais hospedadas no Azure ou no ambiente híbrido.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: c6aea3be5782c967c5816a1e40dc5443306671b3
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87445289"
---
# <a name="overview-of-azure-monitor-agents"></a>Visão geral dos agentes de Azure Monitor

Máquinas virtuais e outros recursos de computação exigem um agente para coletar dados de monitoramento para medir o desempenho e a disponibilidade de seu sistema operacional convidado e cargas de trabalho. Este artigo descreve os agentes usados pelo Azure Monitor e ajuda a determinar qual é necessário para atender aos requisitos para seu ambiente específico.

> [!NOTE]
> Atualmente, Azure Monitor tem vários agentes devido a uma consolidação recente de Azure Monitor e Log Analytics. Embora possa haver sobreposição em seus recursos, cada um tem recursos exclusivos. Dependendo dos seus requisitos, você pode precisar de um ou mais agentes em suas máquinas virtuais. 

Você pode ter um conjunto específico de requisitos que não podem ser atendidos completamente com um único agente para uma determinada máquina virtual. Por exemplo, talvez você queira usar alertas de métrica que exijam a extensão de diagnóstico do Azure, mas também deseja aproveitar a funcionalidade de Azure Monitor para VMs que requer o agente de Log Analytics e o agente de dependência. Em casos como esse, você pode usar vários agentes, e esse é um cenário comum para clientes que exigem funcionalidade de cada um.

## <a name="summary-of-agents"></a>Resumo dos agentes

As tabelas a seguir fornecem uma comparação rápida dos agentes de Azure Monitor para Windows e Linux. Mais detalhes sobre cada um são fornecidos na seção abaixo. 

### <a name="windows-agents"></a>Agentes do Windows

| | Diagnóstico<br>extensão (WAD) | Log Analytics<br>agente | Dependência<br>agente |
|:---|:---|:---|:---|
| **Ambientes com suporte** | Azure | Azure<br>Outra nuvem<br>Local | Azure<br>Outra nuvem<br>Local | 
| **Requisitos do agente**  | Nenhum | Nenhum | Requer agente de Log Analytics |
| **Dados coletados** | Logs de eventos<br>eventos ETW<br>Desempenho<br>Logs baseados em arquivo<br>Logs IIS<br>Logs do aplicativo .NET<br>Despejos de memória<br>Logs de diagnóstico do agente | Logs de eventos<br>Desempenho<IIS logs><br>Logs baseados em arquivo<br>Insights e soluções<br>Outros serviços | Detalhes e dependências do processo<br>Métricas de conexão de rede |
| **Dados enviados para** | Armazenamento do Azure<br>Métricas do Azure Monitor<br>Hub de evento | Logs do Azure Monitor | Logs do Azure Monitor |


### <a name="linux-agents"></a>Agentes do Linux

| | Diagnóstico<br>extensão (LAD) | Telegraf<br>agente | Log Analytics<br>agente | Dependência<br>agente |
|:---|:---|:---|:---|:---|
| **Ambientes com suporte** | Azure | Azure<br>Outra nuvem<br>Local | Azure<br>Outra nuvem<br>Local | Azure<br>Outra nuvem<br>Local |
| **Requisitos do agente**  | Nenhum | Nenhum | Nenhum | Requer agente de Log Analytics |
| **Dados coletados** | syslog<br>Desempenho | Desempenho | syslog<br>Desempenho| Detalhes e dependências do processo<br>Métricas de conexão de rede |
| **Dados enviados para** | Armazenamento do Azure<br>Hub de evento | Métricas do Azure Monitor | Logs do Azure Monitor | Logs do Azure Monitor |

## <a name="log-analytics-agent"></a>Agente do Log Analytics

O [agente de log Analytics](log-analytics-agent.md) coleta dados de monitoramento do sistema operacional convidado e cargas de trabalho de máquinas virtuais no Azure, outros provedores de nuvem e locais. Ele coleta dados em um espaço de trabalho Log Analytics. O agente de Log Analytics é o mesmo agente usado pelo System Center Operations Manager, e você pode usar computadores de agente multihome para se comunicar com o grupo de gerenciamento e Azure Monitor simultaneamente. Esse agente também é exigido por determinadas informações e soluções no Azure Monitor.


> [!NOTE]
> O agente de Log Analytics para Windows é geralmente conhecido como Microsoft Monitoring Agent (MMA). O agente de Log Analytics para Linux é geralmente conhecido como agente do OMS.



Use o agente de Log Analytics se você precisar:

* Colete logs e dados de desempenho de máquinas virtuais ou físicas fora do Azure. 
* Envie dados para um espaço de trabalho Log Analytics para aproveitar os recursos com suporte dos [logs de Azure monitor](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) , como [consultas de log](../log-query/log-query-overview.md).
* Use [Azure monitor para VMs](../insights/vminsights-overview.md) que permite monitorar suas máquinas virtuais em escala e monitora seus processos e dependências em outros recursos e processos externos.  
* Gerencie a segurança de suas máquinas virtuais usando a [central de segurança do Azure](../../security-center/security-center-intro.md) ou o [Azure Sentinel](../../sentinel/overview.md).
* Use o [Gerenciamento de atualizações da automação do Azure](../../automation/update-management/update-mgmt-overview.md), a [configuração de estado da automação do azure](../../automation/automation-dsc-overview.md)ou a [controle de alterações de automação do Azure e o inventário](../../automation/change-tracking.md) para fornecer gerenciamento abrangente de suas VMs do Azure
* Use [soluções](../monitor-reference.md#insights-and-core-solutions) diferentes para monitorar um determinado serviço ou aplicativo.

As limitações do agente de Log Analytics incluem:

- Não é possível enviar dados para Azure Monitor métricas, armazenamento do Azure ou hubs de eventos do Azure.

## <a name="azure-diagnostics-extension"></a>Extensão de diagnóstico do Azure

A [extensão diagnóstico do Azure](diagnostics-extension-overview.md) coleta dados de monitoramento do sistema operacional convidado e cargas de trabalho de máquinas virtuais do Azure e outros recursos de computação. Ele coleta principalmente dados no armazenamento do Azure, mas também permite que você defina coletores de dados para também enviar dados a outros destinos, como Azure Monitor métricas e hubs de eventos do Azure.

Use a extensão de diagnóstico do Azure se você precisar:

- Envie dados para o armazenamento do Azure para arquivamento ou para analisá-los com ferramentas como [Gerenciador de armazenamento do Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
- Envie dados para [Azure monitor métricas](data-platform-metrics.md) para analisá-lo com o [Metrics Explorer](metrics-getting-started.md) e aproveitar os recursos como [alertas de métrica](./alerts-metric-overview.md) quase em tempo real e [dimensionamento automático](autoscale-overview.md) (somente Windows).
- Envie dados para ferramentas de terceiros usando [hubs de eventos do Azure](diagnostics-extension-stream-event-hubs.md).
- Colete o [diagnóstico de inicialização](../../virtual-machines/troubleshooting/boot-diagnostics.md) para investigar problemas de inicialização da VM.

As limitações da extensão de diagnóstico do Azure incluem:

- Só pode ser usado com recursos do Azure.
- Capacidade limitada de enviar dados para Azure Monitor logs.

## <a name="telegraf-agent"></a>Agente Telegraf

O [agente InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md) é usado para coletar dados de desempenho de computadores Linux para Azure monitor métricas.

Use o agente do Telegraf se você precisar:

* Envie dados para [Azure monitor métricas](data-platform-metrics.md) para analisá-lo com o [Metrics Explorer](metrics-getting-started.md) e aproveitar os recursos, como [alertas de métrica](./alerts-metric-overview.md) quase em tempo real e [dimensionamento automático](autoscale-overview.md) (somente Linux). 



## <a name="dependency-agent"></a>Agente de dependência

O agente de dependência coleta dados descobertos sobre processos em execução na máquina virtual e dependências de processo externo. 

Use o Dependency Agent se você precisar:

* Use o recurso de mapa [Azure monitor para VMs](../insights/vminsights-overview.md) ou a solução de [mapa do serviço](../insights/service-map.md) .

Considere o seguinte ao usar o Dependency Agent:

- O agente de dependência requer que o agente de Log Analytics seja instalado na mesma máquina virtual.
- Em VMs do Linux, o agente de Log Analytics deve ser instalado antes da extensão de diagnóstico do Azure.

## <a name="extensions-compared-to-agents"></a>Extensões comparadas aos agentes

A extensão de Log Analytics para [Windows](../../virtual-machines/extensions/oms-windows.md) e [Linux](../../virtual-machines/extensions/oms-linux.md) instala o agente de log Analytics em máquinas virtuais do Azure. A extensão de dependência Azure Monitor para [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) e [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) instala o Dependency Agent em máquinas virtuais do Azure. Esses são os mesmos agentes descritos acima, mas permitem que você os gerencie por meio de [extensões de máquina virtual](../../virtual-machines/extensions/overview.md). Você deve usar extensões para instalar e gerenciar os agentes sempre que possível.


## <a name="next-steps"></a>Próximas etapas

Obtenha mais detalhes sobre cada um dos agentes no seguinte:

- [Visão geral do agente de Log Analytics](log-analytics-agent.md)
- [Visão geral da extensão de Diagnóstico do Azure](diagnostics-extension-overview.md)
- [Coletar métricas personalizadas para uma VM do Linux com o Agente InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md)

