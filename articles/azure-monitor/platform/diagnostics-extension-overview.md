---
title: Visão geral da extensão do Diagnóstico do Azure
description: Usar o diagnóstico do Azure para depurar, medir o desempenho, monitorar e analisar o tráfego em serviços de nuvem, em máquinas virtuais e no Service Fabric
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/13/2019
ms.openlocfilehash: 1bdefc6b61e4e5cc5b8648880c5fdd8662af1bc1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75395362"
---
# <a name="what-is-azure-diagnostics-extension"></a>O que é a extensão Diagnóstico do Azure
A extensão Diagnóstico do Azure é um agente no Azure que permite a coleta de dados de diagnóstico em um aplicativo implantado. Você pode usar a extensão de diagnóstico de várias fontes diferentes. As que têm suporte no momento são as Funções de Trabalho ou Web do Serviço de Nuvem do Azure (clássico), as Máquinas Virtuais, os conjuntos de dimensionamento de Máquinas Virtuais e o Service Fabric. Outros serviços do Azure têm métodos diferentes de diagnósticos. Consulte [Visão geral do monitoramento no Azure](../../azure-monitor/overview.md).

## <a name="linux-agent"></a>Agente do Linux
Uma [versão do Linux da extensão](../../virtual-machines/extensions/diagnostics-linux.md) está disponível para Máquinas Virtuais que executam o Linux. As estatísticas coletadas e o comportamento variam conforme a versão do Windows.

## <a name="data-you-can-collect"></a>Dados que você pode coletar
A extensão Diagnóstico do Azure pode coletar os seguintes tipos de dados:

| fonte de dados | Description |
| --- | --- |
| Métricas do contador de desempenho |Contadores de desempenho personalizados e do Sistema Operacional |
| Logs de aplicativo |Rastreio de mensagens gravadas pelo seu aplicativo |
| Logs de Eventos do Windows |Informações enviadas ao sistema de log de eventos do Windows |
| Logs do .NET EventSource |Eventos de gravação de código usando a classe [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) do .NET |
| Logs IIS |Informações sobre sites do IIS |
| [Logs do ETW baseados no manifesto](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |Rastreamento de Eventos para eventos do Windows gerados por qualquer processo.(1) |
| Despejos de memória (logs) |Informações sobre o estado do processo se um aplicativo falhar |
| Logs de erros personalizados |Logs criados por seu aplicativo ou serviço |
| Logs de infraestrutura do Diagnóstico do Azure |Informações sobre o próprio Diagnóstico do Azure |

(1) para obter uma lista de provedores ETW, execute `c:\Windows\System32\logman.exe query providers` em uma janela de console no computador do qual você gostaria de coletar informações.

## <a name="data-storage"></a>Armazenamento de dados
A extensão armazena seus dados em uma [conta do Armazenamento do Azure](diagnostics-extension-to-storage.md) especificada.

Você também pode enviá-los para o [Application Insights](../../azure-monitor/app/cloudservices.md). 

Outra opção é transmiti-los para o [Hub de Eventos](../../event-hubs/event-hubs-about.md), que permite o envio dos dados para serviços de monitoramento que não fazem parte do Azure.

Você também tem a opção de envio de dados para o banco de dados de série temporal de métricas do Azure Monitor. Neste momento, esse coletor só é aplicável aos Contadores de Desempenho. Ele permite que você envie os contadores de desempenho como métricas personalizadas. Esse recurso está em Preview. O coletor do Azure Monitor dá suporte a:
* Recuperação de todos os contadores de desempenho enviados para o Azure Monitor por meio de [APIs de métrica do Azure Monitor.](https://docs.microsoft.com/rest/api/monitor/)
* Alertas de todos os contadores de desempenho enviados para o Azure Monitor por meio dos [alertas de métrica](../../azure-monitor/platform/alerts-overview.md) no Azure Monitor
* Tratamento do operador curinga em contadores de desempenho como a dimensão de "Instância" na sua métrica.  Por exemplo, se você tiver coletado o contador “LogicalDisk(\*)/DiskWrites/sec”, seria capaz de filtrar e dividir na dimensão "Instância" para gráfico ou alerta sobre as gravações de disco/s para cada Disco Lógico na VM (C:, por exemplo)

Para saber mais sobre como configurar esse coletor, confira a [documentação do esquema de diagnóstico do Azure.](diagnostics-extension-schema-1dot3.md)

## <a name="costs"></a>Custos
Cada uma das opções acima pode incorrer em custos. Não se esqueça de Pesquisar para evitar faturas inesperadas.  Application Insights, o Hub de eventos e o armazenamento do Azure têm custos separados associados à ingestão e à hora armazenada. Em particular, o armazenamento do Azure manterá todos os dados para sempre, para que você possa desejar limpar os dados mais antigos após um determinado período de tempo para manter os custos inativos.    

## <a name="versioning-and-configuration-schema"></a>Controle de versão e esquema de configuração
Confira [Histórico de versão e esquema do Diagnóstico do Azure](diagnostics-extension-schema.md).


## <a name="next-steps"></a>Próximos passos
Escolha de qual serviço você está tentando coletar diagnósticos e use os seguintes artigos para começar. Use os links gerais de diagnóstico do Azure para obter referências de tarefas específicas.

## <a name="cloud-services-using-azure-diagnostics"></a>Serviços de Nuvem que usam o Diagnóstico do Azure
* Se estiver usando o Visual Studio, veja [Usar o Visual Studio para rastrear um aplicativo dos Serviços de Nuvem](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) para começar. Caso contrário, veja
* [Como monitorar os Serviços de Nuvem que usam o Diagnóstico do Azure](../../cloud-services/cloud-services-how-to-monitor.md)
* [Configurar o Diagnóstico do Azure em um Aplicativo dos Serviços de Nuvem](../../cloud-services/cloud-services-dotnet-diagnostics.md)

Para tópicos mais avançados, veja

* [Uso dos Diagnósticos do Azure com o Application Insights para Serviços de Nuvem](../../azure-monitor/app/cloudservices.md)
* [Rastrear o fluxo de um aplicativo de Serviços de Nuvem com o Diagnóstico do Azure](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Usar o PowerShell para configurar o diagnóstico nos Serviços de Nuvem](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Máquinas virtuais
* Se estiver usando o Visual Studio, veja [Usar o Visual Studio para rastrear Máquinas Virtuais do Azure](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) para começar. Caso contrário, veja
* [Configurar o Diagnóstico do Azure em uma Máquina Virtual do Azure](/azure/virtual-machines/extensions/diagnostics-windows)

Para tópicos mais avançados, veja

* [Usar o PowerShell para configurar o diagnóstico nas Máquinas Virtuais do Azure](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Criar uma máquina virtual do Windows com monitoramento e diagnóstico usando o modelo do Azure Resource Manager](../../virtual-machines/extensions/diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Malha de Serviço
Comece em [Monitorar um aplicativo do Service Fabric](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Muitos outros artigos de diagnóstico do Service Fabric estarão disponíveis na árvore de navegação à esquerda depois que você acessar este artigo.

## <a name="general-articles"></a>Artigos gerais
* Saiba como [usar os Contadores de Desempenho no Diagnóstico do Azure](../../cloud-services/diagnostics-performance-counters.md).
* Caso tenha problemas com o início do diagnóstico ou a localização de seus dados nas tabelas de armazenamento do Azure, confira [Solução de problemas do Diagnóstico do Azure](diagnostics-extension-troubleshooting.md)

