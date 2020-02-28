---
title: Visão geral da extensão de Diagnóstico do Azure
description: Usar o diagnóstico do Azure para depurar, medir o desempenho, monitorar e analisar o tráfego em serviços de nuvem, em máquinas virtuais e no Service Fabric
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 6cb514312db525ffd2ccf9f7b70968daaa94f322
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77672371"
---
# <a name="azure-diagnostics-extension-overview"></a>Visão geral da extensão de Diagnóstico do Azure
Diagnóstico do Azure extensão é um [agente no Azure monitor](agents-overview.md) que coleta dados de monitoramento do sistema operacional convidado dos recursos de computação do Azure, incluindo máquinas virtuais. Este artigo fornece uma visão geral da extensão de Diagnóstico do Azure, incluindo funcionalidades específicas às quais ele dá suporte e opções para instalação e configuração. 

> [!NOTE]
> A extensão de Diagnóstico do Azure é um dos agentes disponíveis para coletar dados de monitoramento do sistema operacional convidado dos recursos de computação. Consulte [visão geral dos agentes de Azure monitor](agents-overview.md) para obter uma descrição dos diferentes agentes e diretrizes sobre como selecionar os agentes apropriados para seus requisitos.

## <a name="comparison-to-log-analytics-agent"></a>Comparação com o agente de Log Analytics
O agente de Log Analytics no Azure Monitor também pode ser usado para coletar dados de monitoramento do sistema operacional convidado das máquinas virtuais. Você pode optar por usar um ou ambos, dependendo de seus requisitos. Consulte [visão geral dos agentes de Azure monitor](agents-overview.md) para obter uma comparação detalhada dos agentes de Azure monitor. 

As principais diferenças a serem consideradas são:

- A extensão Diagnóstico do Azure pode ser usada somente com máquinas virtuais do Azure. O agente de Log Analytics pode ser usado com máquinas virtuais no Azure, em outras nuvens e no local.
- A extensão de Diagnóstico do Azure envia dados para o armazenamento do Azure, [métricas de Azure monitor](data-platform-metrics.md) (somente Windows) e hubs de eventos. O agente de Log Analytics coleta dados para [Azure monitor logs](data-platform-logs.md).
- O agente de Log Analytics é necessário para [soluções](../monitor-reference.md#insights-and-core-solutions), [Azure monitor para VMs](../insights/vminsights-overview.md)e outros serviços, como a [central de segurança do Azure](/azure/security-center/).

## <a name="costs"></a>Custos
Não há nenhum custo para a extensão de diagnóstico do Azure, mas você pode incorrer em encargos pelos dados ingeridos. Verifique [Azure monitor preço](https://azure.microsoft.com/pricing/details/monitor/) para o destino em que você está coletando dados.

## <a name="data-collected"></a>Dados coletados
As tabelas a seguir listam os dados que podem ser coletados pela extensão de diagnóstico do Windows e do Linux.

### <a name="windows-diagnostics-extension-wad"></a>Extensão de diagnóstico do Windows (WAD)

| Fonte de Dados | Descrição |
| --- | --- |
| Logs de Eventos do Windows   | Eventos do log de eventos do Windows. |
| Contadores de desempenho | Valores numéricos que medem o desempenho de diferentes aspectos do sistema operacional e das cargas de trabalho. |
| Logs IIS             | Informações de uso para sites do IIS em execução no sistema operacional convidado. |
| Logs de aplicativo     | Rastrear mensagens gravadas pelo seu aplicativo. |
| Logs do .NET EventSource |Eventos de gravação de código usando a classe [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) do .NET |
| [Logs do ETW baseados no manifesto](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |Rastreamento de eventos para eventos do Windows gerados por qualquer processo. |
| Despejos de memória (logs)   | Informações sobre o estado do processo se um aplicativo falhar. |
| Logs baseados em arquivo    | Logs criados por seu aplicativo ou serviço. |
| Logs de diagnóstico do agente | Informações sobre Diagnóstico do Azure si mesma. |


### <a name="linux-diagnostics-extension-lad"></a>Extensão de diagnóstico do Linux (LAD)

| Fonte de Dados | Descrição |
| --- | --- |
| Syslog | Eventos enviados para o sistema de log de eventos do Linux.   |
| Contadores de desempenho  | Valores numéricos que medem o desempenho de diferentes aspectos do sistema operacional e das cargas de trabalho. |
| Arquivos de log | Entradas enviadas a um log baseado em arquivo.  |

## <a name="data-destinations"></a>Destinos de dados
A extensão de diagnóstico do Azure para Windows e Linux sempre coleta dados em uma conta de armazenamento do Azure. Consulte [instalar e configurar a extensão de diagnóstico do Windows Azure (wad)](diagnostics-extension-windows-install.md) e [usar a extensão de diagnóstico do Linux para monitorar métricas e logs](../../virtual-machines/extensions/diagnostics-linux.md) para obter uma lista de tabelas e blobs específicos em que esses dados são coletados.

Configure um ou mais *coletores de dados* para enviar dados a outros destinos adicionais. As seções a seguir listam os coletores disponíveis para a extensão de diagnóstico do Windows e do Linux.

### <a name="windows-diagnostics-extension-wad"></a>Extensão de diagnóstico do Windows (WAD)

| Destino | Descrição |
|:---|:---|
| Métricas de Azure Monitor | Coletar dados de desempenho para Azure Monitor métricas. Consulte [Enviar métricas do sistema operacional convidado para o banco de dados de métrica Azure monitor](collect-custom-metrics-guestos-resource-manager-vm.md).  |
| Hubs de Eventos | Use os hubs de eventos do Azure para enviar dados fora do Azure. Consulte [streaming de dados de diagnóstico do Azure para os hubs de eventos](diagnostics-extension-stream-event-hubs.md) |
| Blobs do Armazenamento do Azure | Gravar em dados em BLOBs no armazenamento do Azure, além de tabelas. |
| Application Insights | Coletar dados de aplicativos em execução em sua VM para Application Insights para integrar com outro monitoramento de aplicativo. Consulte [enviar dados de diagnóstico para Application insights](diagnostics-extension-to-application-insights.md). |

Você também pode coletar dados de WAD do armazenamento em um espaço de trabalho Log Analytics para analisá-los com logs de Azure Monitor, embora o agente de Log Analytics seja normalmente usado para essa funcionalidade. Ele pode enviar dados diretamente para um espaço de trabalho Log Analytics e oferece suporte a soluções e informações que fornecem funcionalidade adicional.  Consulte [coletar logs de diagnóstico do Azure do armazenamento do Azure](diagnostics-extension-logs.md). 


### <a name="linux-diagnostics-extension-lad"></a>Extensão de diagnóstico do Linux (LAD)
LAD grava dados em tabelas no armazenamento do Azure. Ele dá suporte aos coletores na tabela a seguir.

| Destino | Descrição |
|:---|:---|
| Hubs de Eventos | Use os hubs de eventos do Azure para enviar dados fora do Azure. |
| Blobs do Armazenamento do Azure | Gravar em dados em BLOBs no armazenamento do Azure, além de tabelas. |
| Métricas de Azure Monitor | Instale o agente do Telegraf, além do LAD. Consulte [coletar métricas personalizadas para uma VM Linux com o agente InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md).


## <a name="installation-and-configuration"></a>Instalação e configuração
A extensão de diagnóstico é implementada como uma [extensão de máquina virtual](../../virtual-machines/extensions/overview.md) no Azure, portanto, ela dá suporte às mesmas opções de instalação usando modelos do Resource Manager, PowerShell e CLI. Consulte [extensões e recursos de máquina virtual para Windows](../../virtual-machines/extensions/features-windows.md) e [recursos e extensões de máquina virtual para Linux](../../virtual-machines/extensions/features-linux.md) para obter detalhes gerais sobre como instalar e manter extensões de máquina virtual.

Você também pode instalar e configurar a extensão de diagnóstico do Windows e Linux no portal do Azure em **configurações de diagnóstico** na seção **monitoramento** do menu da máquina virtual.

Consulte os artigos a seguir para obter detalhes sobre como instalar e configurar a extensão de diagnóstico para Windows e Linux.

- [Instalar e configurar a extensão de diagnóstico do Windows Azure (WAD)](diagnostics-extension-windows-install.md)
- [Usar a extensão de diagnóstico do Linux para monitorar as métricas e os logs](../../virtual-machines/extensions/diagnostics-linux.md)

## <a name="other-documentation"></a>Outra documentação

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Funções Web e de trabalho do serviço de nuvem do Azure (clássico)
- [Introdução ao monitoramento do serviço de nuvem](../../cloud-services/cloud-services-how-to-monitor.md)
- [Habilitando Diagnóstico do Azure nos serviços de nuvem do Azure](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [Application Insights para serviços de nuvem do Azure](../app/cloudservices.md)<br>[Rastrear o fluxo de um aplicativo de Serviços de Nuvem com o Diagnóstico do Azure](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [Monitorar e diagnosticar serviços em uma configuração de desenvolvimento de computador local](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}


* Saiba como [usar os Contadores de Desempenho no Diagnóstico do Azure](../../cloud-services/diagnostics-performance-counters.md).
* Caso tenha problemas com o início do diagnóstico ou a localização de seus dados nas tabelas de armazenamento do Azure, confira [Solução de problemas do Diagnóstico do Azure](diagnostics-extension-troubleshooting.md)

