---
title: Visão geral agentes de monitoramento do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral detalhada dos agentes do Azure disponíveis que oferecem suporte ao monitoramento de máquinas virtuais hospedadas no Azure ou no ambiente híbrido.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: a7e6a3a299df8112fe4fbcf457516894c1766b8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249081"
---
# <a name="overview-of-azure-monitor-agents"></a>Visão geral dos agentes do Monitor do Azure

Máquinas virtuais e outros recursos computacionais exigem que um agente colete dados de monitoramento para medir o desempenho e a disponibilidade de seu sistema operacional convidado e cargas de trabalho. Este artigo descreve os agentes usados pelo Azure Monitor e ajuda você a determinar quais você precisa atender aos requisitos para o seu ambiente específico.

> [!NOTE]
> Atualmente, o Azure Monitor possui vários agentes devido à recente consolidação do Azure Monitor e do Log Analytics. Embora possa haver sobreposição em suas características, cada um tem capacidades únicas. Dependendo de suas necessidades, você pode precisar de um ou mais dos agentes em suas máquinas virtuais. 

Você pode ter um conjunto específico de requisitos que não podem ser completamente cumpridos com um único agente para uma determinada máquina virtual. Por exemplo, você pode querer usar alertas métricos que exigem extensão de diagnóstico do Azure, mas também deseja aproveitar a funcionalidade do Monitor Azure para VMs, que requer o agente Log Analytics e o agente Dependency. Em casos como este, você pode usar vários agentes, e este é um cenário comum para clientes que exigem funcionalidade de cada um.

## <a name="summary-of-agents"></a>Resumo dos agentes

As tabelas a seguir fornecem uma comparação rápida dos agentes do Monitor Azure para Windows e Linux. Mais detalhes sobre cada um são fornecidos na seção abaixo. 

### <a name="windows-agents"></a>Agentes do Windows

| | Diagnósticos<br>extensão (WAD) | Log Analytics<br>agente | Dependência<br>agente |
|:---|:---|:---|:---|
| Ambientes suportados | Azure | Azure<br>Outra nuvem<br>Local | Azure<br>Outra nuvem<br>Local | 
| Requisitos do agente  | Nenhum | Nenhum | Requer agente de análise de log |
| Dados coletados | Logs de eventos<br>eventos ETW<br>Desempenho<br>Logs baseados em arquivos<br>Logs IIS<br>Logs de aplicativos .NET<br>Despejos de memória<br>Registros de diagnóstico de agentes | Logs de eventos<br>Desempenho<IIS logs><br>Logs baseados em arquivos<br>Insights e soluções<br>Outros serviços | Detalhes e dependências do processo<br>Métricas de conexão de rede |
| Dados enviados para | Armazenamento do Azure<br>Métricas do Monitor Azure<br>Hub de evento | Logs do Azure Monitor | Logs do Azure Monitor |


### <a name="linux-agents"></a>Agentes do Linux

| | Diagnósticos<br>extensão (LAD) | Telegraf<br>agente | Log Analytics<br>agente | Dependência<br>agente |
|:---|:---|:---|:---|:---|
| Ambientes suportados | Azure | Azure<br>Outra nuvem<br>Local | Azure<br>Outra nuvem<br>Local | Azure<br>Outra nuvem<br>Local |
| Requisitos do agente  | Nenhum | Nenhum | Nenhum | Requer agente de análise de log |
| Dados coletados | syslog<br>Desempenho | Desempenho | syslog<br>Desempenho| Detalhes e dependências do processo<br>Métricas de conexão de rede |
| Dados enviados para | Armazenamento do Azure<br>Hub de evento | Métricas do Monitor Azure | Logs do Azure Monitor | Logs do Azure Monitor |

## <a name="log-analytics-agent"></a>Agente do Log Analytics

O [agente log analytics](log-analytics-agent.md) coleta dados de monitoramento do sistema operacional convidado e cargas de trabalho de máquinas virtuais no Azure, outros provedores de nuvem e on-premises. Ele coleta dados em um espaço de trabalho do Log Analytics. O agente Log Analytics é o mesmo agente usado pelo System Center Operations Manager, e você pode obter computadores de agentes multihome para se comunicar com seu grupo de gerenciamento e o Azure Monitor simultaneamente. Este agente também é exigido por certos insights e soluções no Azure Monitor.


> [!NOTE]
> O agente log analytics para Windows é frequentemente referido como Microsoft Monitoring Agent (MMA). O agente log analytics para Linux é frequentemente referido como agente OMS.



Use o agente Log Analytics se necessário:

* Colete registros e dados de desempenho de máquinas virtuais ou físicas fora do Azure. 
* Envie dados para um espaço de trabalho do Log Analytics para aproveitar os recursos suportados pelo [Azure Monitor Logs,](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) como [consultas de log](../log-query/log-query-overview.md).
* Use [o Monitor Azure para VMs](../insights/vminsights-overview.md) que permite monitorar suas máquinas virtuais em escala e monitora seus processos e dependências em outros recursos e processos externos..  
* Gerencie a segurança de suas máquinas virtuais usando [o Azure Security Center](../../security-center/security-center-intro.md) ou o [Azure Sentinel](../../sentinel/overview.md).
* Use [o gerenciamento de atualização de automação do Azure,](../../automation/automation-update-management.md) [configuração do estado de automação do Azure](../../automation/automation-dsc-overview.md)ou rastreamento e inventário de [alterações de automação do Azure](../../automation/change-tracking.md) para fornecer gerenciamento abrangente de suas VMs Azure
* Use [diferentes soluções](../monitor-reference.md#insights-and-core-solutions) para monitorar um determinado serviço ou aplicativo.

As limitações do agente log analytics incluem:

- Não é possível enviar dados para o Azure Monitor Metrics, Azure Storage ou Azure Event Hubs.

## <a name="azure-diagnostics-extension"></a>Extensão de diagnóstico do Azure

A [extensão Azure Diagnostics](diagnostics-extension-overview.md) coleta dados de monitoramento do sistema operacional convidado e cargas de trabalho de máquinas virtuais do Azure e outros recursos de computação. Ele coleta principalmente dados no Azure Storage, mas também permite definir sinks de dados para também enviar dados para outros destinos, como Azure Monitor Metrics e Azure Event Hubs.

Use a extensão de diagnóstico do Azure se necessário:

- Envie dados para o Azure Storage para arquivamento ou para analisá-los com ferramentas como [o Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
- Envie dados para [o Azure Monitor Metrics](data-platform-metrics.md) para analisá-lo com o explorador de [métricas](metrics-getting-started.md) e para aproveitar recursos como [alertas métricos próximos](../../azure-monitor/platform/alerts-metric-overview.md) em tempo real e [autoescala](autoscale-overview.md) (somente Windows).
- Envie dados para ferramentas de terceiros usando [o Azure Event Hubs](diagnostics-extension-stream-event-hubs.md).
- Coletar [diagnósticos de inicialização](../../virtual-machines/troubleshooting/boot-diagnostics.md) para investigar problemas de inicialização da VM.

As limitações da extensão de diagnósticos do Azure incluem:

- Só pode ser usado com recursos do Azure.
- Capacidade limitada de enviar dados para o Azure Monitor Logs.

## <a name="telegraf-agent"></a>Agente telegraf

O [agente InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md) é usado para coletar dados de desempenho de computadores Linux para o Azure Monitor Metrics.

Use o agente Telegraf se precisar:

* Envie dados para [o Azure Monitor Metrics](data-platform-metrics.md) para analisá-lo com o explorador de [métricas](metrics-getting-started.md) e para aproveitar recursos como [alertas métricos próximos](../../azure-monitor/platform/alerts-metric-overview.md) em tempo real e [autoescala](autoscale-overview.md) (somente Linux). 



## <a name="dependency-agent"></a>Agente de dependência

O agente dependency coleta dados descobertos sobre processos em execução na máquina virtual e dependências de processos externos. 

Use o agente de dependência se precisar:

* Use o recurso [Mapa Azure Monitor para VMs](../insights/vminsights-overview.md) ou a solução [Mapa de Serviço.](../insights/service-map.md)

Considere o seguinte ao usar o agente de dependência:

- O agente Dependency requer que o agente Log Analytics seja instalado na mesma máquina virtual.
- Nas VMs Linux, o agente Log Analytics deve ser instalado antes da Extensão de Diagnóstico do Azure.

## <a name="extensions-compared-to-agents"></a>Extensões em relação aos agentes

A extensão Log Analytics para [Windows](../../virtual-machines/extensions/oms-windows.md) e [Linux](../../virtual-machines/extensions/oms-linux.md) instala o agente Log Analytics em máquinas virtuais do Azure. A extensão Azure Monitor Dependency para [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) e [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) instala o agente Dependency em máquinas virtuais do Azure. Estes são os mesmos agentes descritos acima, mas permitem gerenciá-los através de [extensões de máquinas virtuais](../../virtual-machines/extensions/overview.md). Você deve usar extensões para instalar e gerenciar os agentes sempre que possível.


## <a name="next-steps"></a>Próximas etapas

Obtenha mais detalhes sobre cada um dos agentes a seguir:

- [Visão geral do agente log analytics](log-analytics-agent.md)
- [Visão geral da extensão do Azure Diagnostics](diagnostics-extension-overview.md)
- [Coletar métricas personalizadas para uma VM do Linux com o Agente InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md)
