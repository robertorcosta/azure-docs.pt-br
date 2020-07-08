---
title: incluir arquivo
description: incluir arquivo
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2019
ms.author: cynthn
ms.openlocfilehash: feea9696316723a2750be6fc1e13001224320324
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81865618"
---
Com o crescimento significativo das VMs hospedadas no Azure, é importante identificar problemas de desempenho e integridade que afetam os aplicativos e serviços de infraestrutura aos quais eles dão suporte. O monitoramento básico é fornecido por padrão com o Azure pelos tipos de métrica uso de CPU, utilização de disco, utilização de memória e tráfego de rede coletado pelo hipervisor de host. Dados de métrica e de log adicionais podem ser coletados usando [extensões](../articles/virtual-machines/windows/extensions-features.md) para configurar o diagnóstico em suas VMs a partir do sistema operacional convidado.

Para detectar e ajudar a diagnosticar problemas de desempenho e integridade com os componentes do sistema operacional convidado, baseados em .NET ou aplicativos Web Java em execução na VM, Azure Monitor fornece monitoramento centralizado com recursos abrangentes, como Azure Monitor para VMs e Application Insights.

## <a name="diagnostics-and-metrics"></a>Diagnóstico e métrica 

Você pode configurar e monitorar a coleta de [dados de diagnóstico](https://docs.microsoft.com/cli/azure/vm/diagnostics) usando [métricas](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) no portal do Azure, a CLI do Azure, o Azure PowerShell e programando APIs (Interfaces de Programação de Aplicativos). Por exemplo, você pode:

- **Observar as métricas básicas para a VM.** Na tela Visão geral no portal do Azure, as métricas básicas mostradas incluem uso da CPU, uso de rede, total de bytes de disco e operações de disco por segundo.

- **Habilitar a coleta de diagnóstico de inicialização e exibi-lo usando o portal do Azure.** Ao colocar sua própria imagem no Azure ou iniciar uma das imagens da plataforma, muitas razões podem contribuir para que uma VM fique em um estado não inicializável. Você pode habilitar o diagnóstico de inicialização facilmente quando cria uma máquina virtual clicando em **Habilitado** no Diagnóstico de Inicialização na seção Monitoramento da tela Configurações.

    Durante a inicialização das VMs, o agente de diagnóstico de inicialização captura a saída de inicialização e a armazena no Armazenamento do Azure. Esses dados podem ser usados para solucionar problemas de inicialização da VM. Os diagnósticos de inicialização não são habilitados automaticamente quando você cria uma VM nas ferramentas de linha de comando. Antes de habilitar o diagnóstico de inicialização, é necessário criar uma conta de armazenamento para armazenar os logs de inicialização. Se você habilitar o diagnóstico de inicialização no portal do Azure, uma conta de armazenamento será criada automaticamente.

    Se você não habilitar o diagnóstico de inicialização quando a VM for criada, poderá habilitá-la mais tarde usando a [CLI do Azure](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), o [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic) ou um [modelo do Azure Resource Manager](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Habilitar a coleta de dados de diagnóstico do sistema operacional convidado.** Quando você cria uma máquina virtual, tem a oportunidade de habilitar o diagnóstico do sistema operacional convidado na tela de configurações. Quando você habilita a coleta de dados de diagnóstico, a [extensão IaaSDiagnostics para Linux](../articles/virtual-machines/linux/diagnostic-extension.md) ou a [extensão IaSDiagnostics para Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) é adicionada à VM, o que permite que você colete dados de disco, CPU e memória adicionais.

    Usando os dados de diagnóstico coletados, você pode configurar o dimensionamento automático das VMs. Você também pode configurar [logs de Azure monitor](../articles/azure-monitor/platform/data-platform-logs.md) para armazenar os dados e configurar alertas para que você saiba quando o desempenho não está certo.

## <a name="alerts"></a>Alertas

Você pode criar [alertas](../articles/azure-monitor/platform/alerts-overview.md) com base em métricas de desempenho específicas. Exemplos dos problemas que podem ser avisados incluem quando o uso médio da CPU excede determinado limite ou o espaço em disco livre disponível fica abaixo de determinado valor. Os alertas podem ser configurados no [portal do Azure](../articles/azure-monitor/platform/alerts-metric.md#create-with-azure-portal), usando [modelos de Azure Resource Manager](../articles/azure-monitor/platform/alerts-metric-create-templates.md)ou [CLI do Azure](../articles/azure-monitor/platform/alerts-metric.md#with-azure-cli).

## <a name="azure-service-health"></a>Integridade do Serviço do Azure

A [Integridade de Serviço do Azure](../articles/service-health/service-health-overview.md) dá suporte e diretrizes personalizadas em caso de problemas nos serviços do Azure, e ajuda você a se preparar para manutenção futura planejada. A Integridade de Serviço do Azure alerta você e suas equipes usando notificações flexíveis e direcionadas.

## <a name="azure-resource-health"></a>Azure Resource Health

O [Azure Resource Health](../articles/service-health/resource-health-overview.md) ajuda você a diagnosticar e a obter suporte quando um problema com o Azure afeta seus recursos. Ele informa sobre a integridade atual e anterior de seus recursos e ajuda a reduzir os problemas. O Resource Health fornece suporte técnico quando você precisa de ajuda com problemas de serviço do Azure.

## <a name="azure-activity-log"></a>Log de Atividades do Azure

O [log de atividades do Azure](../articles/azure-monitor/platform/platform-logs-overview.md) é um log de assinatura que fornece informações sobre eventos no nível da assinatura que ocorreram no Azure. O log inclui um intervalo de dados, de dados operacionais do Azure Resource Manager para atualizações em eventos de Integridade do Serviço. Você pode clicar em Log de Atividades no portal do Azure para exibir o log da sua VM.

Algumas coisas que você pode fazer com o log de atividades incluem:

- Criar um [alerta em um evento do log de atividades](../articles/azure-monitor/platform/platform-logs-overview.md).
- [Transmita-o para um hub de eventos](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) para ingestão por um serviço de terceiros ou uma solução de análise personalizada, como Power bi.
- Analise-o em Power BI usando o [pacote de conteúdo do Power bi](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Salvá-lo em uma conta de armazenamento](../articles/azure-monitor/platform/archive-activity-log.md) para inspeção manual ou arquivamento. Você pode especificar o tempo de retenção (em dias) usando o Perfil de Log.

Você também pode acessar os dados de log de atividade usando o [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), a [CLI do Azure](https://docs.microsoft.com/cli/azure/monitor) ou as [APIs REST do Monitor](https://docs.microsoft.com/rest/api/monitor/).

[Os logs de recursos do Azure](../articles/azure-monitor/platform/platform-logs-overview.md) são logs emitidos por sua VM que fornecem dados avançados e frequentes sobre sua operação. Os logs de recursos diferem do log de atividades, fornecendo informações sobre as operações que foram executadas na VM.

Algumas coisas que você pode fazer com os logs de diagnóstico incluem:

- [Salve-os em uma conta de armazenamento](../articles/azure-monitor/platform/archive-diagnostic-logs.md) para auditoria ou inspeção manual. Você pode especificar o tempo de retenção (em dias) usando as Configurações de Diagnóstico do Recurso.
- [Transmita-os para os hubs de eventos](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md) para ingestão por um serviço de terceiros ou uma solução de análise personalizada, como Power bi.
- Analise-os com [log Analytics](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Monitoramento avançado

Para obter visibilidade do aplicativo ou serviço com suporte dos conjuntos de dimensionamento de máquinas virtuais e VM do Azure, identificação de problemas com o sistema operacional convidado ou carga de trabalho em execução na VM para entender se ele está afetando a disponibilidade ou o desempenho do aplicativo, ou é um problema com o aplicativo, habilite [Azure monitor para VMs](../articles/azure-monitor/insights/vminsights-overview.md) e [Application insights](../articles/azure-monitor/app/app-insights-overview.md).

O Azure Monitor para VMs monitora suas máquinas virtuais (VM) do Azure em escala analisando o desempenho e a integridade de suas VMs Windows e Linux, incluindo os diferentes processos e dependências interconectadas em outros recursos e processos externos que ele descobre. Ele inclui vários gráficos de tendências de desempenho para ajudar durante a investigação de problemas e avaliar a capacidade de suas VMs. O mapa de dependências mostra computadores monitorados e não monitorados, conexões de rede com falha e ativas entre processos e esses computadores e mostra gráficos de tendência com métricas de conexão de rede padrão. Combinado com Application Insights, você monitora seu aplicativo e captura a telemetria, como solicitações HTTP, exceções, etc., para que você possa correlacionar os problemas entre as VMs e seu aplicativo. Configure [Azure monitor alertas](../articles/azure-monitor/platform/alerts-overview.md) para alertá-lo sobre condições importantes detectadas por meio de monitoramento de dados coletados pelo Azure monitor para VMs.

## <a name="next-steps"></a>Próximas etapas

- Siga as etapas em [Monitorar uma Máquina Virtual Windows com o Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) ou [Monitorar uma Máquina Virtual Linux com a CLI do Azure](../articles/virtual-machines/linux/tutorial-monitoring.md).

- Saiba mais sobre as melhores práticas de [Monitoramento e Diagnóstico](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
