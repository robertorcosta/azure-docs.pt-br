---
title: Visão geral da extensão do Azure Diagnostics
description: Usar o diagnóstico do Azure para depurar, medir o desempenho, monitorar e analisar o tráfego em serviços de nuvem, em máquinas virtuais e no Service Fabric
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 6cb514312db525ffd2ccf9f7b70968daaa94f322
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672371"
---
# <a name="azure-diagnostics-extension-overview"></a>Visão geral da extensão do Azure Diagnostics
A extensão Azure Diagnostics é um [agente no Azure Monitor](agents-overview.md) que coleta dados de monitoramento do sistema operacional convidado de recursos computacionais do Azure, incluindo máquinas virtuais. Este artigo fornece uma visão geral da extensão do Azure Diagnostics, incluindo funcionalidade específica que ele suporta e opções para instalação e configuração. 

> [!NOTE]
> A extensão azure Diagnostics é um dos agentes disponíveis para coletar dados de monitoramento do sistema operacional convidado de recursos computacionais. Consulte [a visão geral dos agentes do Monitor do Azure](agents-overview.md) para obter uma descrição dos diferentes agentes e orientações sobre a seleção dos agentes apropriados para suas necessidades.

## <a name="comparison-to-log-analytics-agent"></a>Comparação com o agente log analytics
O agente Log Analytics no Azure Monitor também pode ser usado para coletar dados de monitoramento do sistema operacional convidado de máquinas virtuais. Você pode optar por usar um ou ambos, dependendo de suas necessidades. Consulte [a visão geral dos agentes do Monitor Do Azure](agents-overview.md) para obter uma comparação detalhada dos agentes do Monitor Do Azure. 

As principais diferenças a considerar são:

- A extensão azure diagnostics pode ser usada apenas com máquinas virtuais Azure. O agente Log Analytics pode ser usado com máquinas virtuais no Azure, outras nuvens e no local.
- A extensão do Azure Diagnostics envia dados para o Azure Storage, [Azure Monitor Metrics](data-platform-metrics.md) (somente Windows) e Event Hubs. O agente Log Analytics coleta dados para [o Azure Monitor Logs](data-platform-logs.md).
- O agente Log Analytics é necessário para [soluções,](../monitor-reference.md#insights-and-core-solutions) [Monitor Azure para VMs](../insights/vminsights-overview.md)e outros serviços, como [o Azure Security Center.](/azure/security-center/)

## <a name="costs"></a>Custos
Não há custo para o Azure Diagnostic Extension, mas você pode incorrer em encargos pelos dados ingeridos. Verifique os preços do [Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) para o destino onde você está coletando dados.

## <a name="data-collected"></a>Dados coletados
As tabelas a seguir listam os dados que podem ser coletados pela extensão de diagnósticos do Windows e Linux.

### <a name="windows-diagnostics-extension-wad"></a>Extensão de diagnóstico do Windows (WAD)

| fonte de dados | Descrição |
| --- | --- |
| Logs de Eventos do Windows   | Eventos do registro de eventos do Windows. |
| Contadores de desempenho | Valores numéricos medindo o desempenho de diferentes aspectos do sistema operacional e cargas de trabalho. |
| Logs IIS             | Informações de uso para sites iis em execução no sistema operacional convidado. |
| Logs de aplicativo     | Trace mensagens escritas pelo seu aplicativo. |
| Logs do .NET EventSource |Eventos de gravação de código usando a classe [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) do .NET |
| [Logs do ETW baseados no manifesto](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |Rastreamento de eventos para eventos do Windows gerados por qualquer processo. |
| Despejos de memória (logs)   | Informações sobre o estado do processo se um aplicativo falhar. |
| Logs baseados em arquivos    | Logs criados por seu aplicativo ou serviço. |
| Registros de diagnóstico de agentes | Informações sobre o próprio Azure Diagnostics. |


### <a name="linux-diagnostics-extension-lad"></a>Extensão de diagnóstico sél (LAD)

| fonte de dados | Descrição |
| --- | --- |
| syslog | Eventos enviados para o sistema de registro de eventos Linux.   |
| Contadores de desempenho  | Valores numéricos medindo o desempenho de diferentes aspectos do sistema operacional e cargas de trabalho. |
| Arquivos de log | Entradas enviadas para um registro baseado em arquivos.  |

## <a name="data-destinations"></a>Destinos de dados
A extensão Azure Diagnostic para Windows e Linux sempre coleta dados em uma conta do Azure Storage. Consulte [Instalar e configurar a extensão de diagnósticodo Windows Azure (WAD)](diagnostics-extension-windows-install.md) e usar o Linux Diagnostic Extension para monitorar [métricas e logs](../../virtual-machines/extensions/diagnostics-linux.md) de uma lista de tabelas e blobs específicas onde esses dados são coletados.

Configure um ou mais *sinks de dados* para enviar dados para outros destinos adicionais. As seções a seguir listam os dissipadores disponíveis para a extensão de diagnósticos do Windows e Linux.

### <a name="windows-diagnostics-extension-wad"></a>Extensão de diagnóstico do Windows (WAD)

| Destino | Descrição |
|:---|:---|
| Métricas do Monitor Azure | Colete dados de desempenho para o Azure Monitor Metrics. Consulte [Enviar métricas do Sistema Operacional convidado para o banco de dados métrico do Azure Monitor](collect-custom-metrics-guestos-resource-manager-vm.md).  |
| Hubs de Eventos | Use o Azure Event Hubs para enviar dados fora do Azure. Veja [os dados do Streaming Azure Diagnostics para hubs de eventos](diagnostics-extension-stream-event-hubs.md) |
| Blobs do Armazenamento do Azure | Escreva para dados para blobs no Azure Storage, além de tabelas. |
| Application Insights | Colete dados de aplicativos em execução em sua VM para Insights de Aplicativos para se integrar a outros monitoramentos de aplicativos. Consulte [Enviar dados de diagnóstico para insights de aplicativos](diagnostics-extension-to-application-insights.md). |

Você também pode coletar dados WAD do armazenamento em um espaço de trabalho do Log Analytics para analisá-los com o Azure Monitor Logs, embora o agente log analytics seja normalmente usado para essa funcionalidade. Ele pode enviar dados diretamente para um espaço de trabalho do Log Analytics e suporta soluções e insights que fornecem funcionalidade adicional.  Consulte [os registros de diagnóstico do Collect Azure no Azure Storage](diagnostics-extension-logs.md). 


### <a name="linux-diagnostics-extension-lad"></a>Extensão de diagnóstico sél (LAD)
Lad grava dados em tabelas no Azure Storage. Suporta as pias na tabela a seguir.

| Destino | Descrição |
|:---|:---|
| Hubs de Eventos | Use o Azure Event Hubs para enviar dados fora do Azure. |
| Blobs do Armazenamento do Azure | Escreva para dados para blobs no Azure Storage, além de tabelas. |
| Métricas do Monitor Azure | Instale o agente Telegraf, além do LAD. Consulte [Coletar métricas personalizadas para um VM Linux com o agente InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md).


## <a name="installation-and-configuration"></a>Instalação e configuração
A extensão Diagnóstico é implementada como uma [extensão de máquina virtual](../../virtual-machines/extensions/overview.md) no Azure, por isso suporta as mesmas opções de instalação usando modelos de Gerenciador de recursos, PowerShell e CLI. Consulte [extensões e recursos de máquinas virtuais para](../../virtual-machines/extensions/features-windows.md) [extensões e recursos de máquinas virtuais para Linux para](../../virtual-machines/extensions/features-linux.md) obter detalhes gerais sobre a instalação e manutenção de extensões de máquinas virtuais.

Você também pode instalar e configurar a extensão de diagnóstico do Windows e Linux no portal Azure em **configurações de Diagnóstico** na seção **Monitoramento** do menu da máquina virtual.

Consulte os artigos a seguir para obter detalhes sobre a instalação e configuração da extensão de diagnóstico para Windows e Linux.

- [Instale e configure a extensão de diagnósticodo Windows Azure (WAD)](diagnostics-extension-windows-install.md)
- [Use a Extensão de Diagnóstico Linux para monitorar as métricas e os logs](../../virtual-machines/extensions/diagnostics-linux.md)

## <a name="other-documentation"></a>Outra documentação

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Funções web e trabalhadora do Azure Cloud Service (clássico)
- [Introdução ao Monitoramento de Serviço de Nuvem](../../cloud-services/cloud-services-how-to-monitor.md)
- [Habilitando o Diagnóstico do Azure nos Serviços de Nuvem do Azure](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [Insights de aplicativos para serviços de nuvem do Azure](../app/cloudservices.md)<br>[Rastrear o fluxo de um aplicativo de Serviços de Nuvem com o Diagnóstico do Azure](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [Monitorar e diagnosticar serviços em uma configuração de desenvolvimento de computador local](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>Próximas etapas


* Saiba como [usar os Contadores de Desempenho no Diagnóstico do Azure](../../cloud-services/diagnostics-performance-counters.md).
* Caso tenha problemas com o início do diagnóstico ou a localização de seus dados nas tabelas de armazenamento do Azure, confira [Solução de problemas do Diagnóstico do Azure](diagnostics-extension-troubleshooting.md)

