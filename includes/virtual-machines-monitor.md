---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2019
ms.author: cynthn
ms.openlocfilehash: fbc6889507e58c4721597a1108337fcb1f8756a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75752466"
---
Com o crescimento significativo das VMs hospedadas no Azure, é importante identificar problemas de desempenho e saúde que impactam aplicativos e serviços de infraestrutura que eles suportam. O monitoramento básico é entregue por padrão com o Azure pelos tipos métricos de uso da CPU, utilização do disco, utilização da memória e tráfego de rede coletadopelo hipervisor host. Dados métricos e de registro adicionais podem ser coletados usando [extensões](../articles/virtual-machines/windows/extensions-features.md) para configurar diagnósticos em suas VMs do sistema operacional convidado.

Para detectar e ajudar a diagnosticar problemas de desempenho e saúde com o sistema operacional convidado, componentes de aplicativos web baseados em .NET ou Java em execução dentro da VM, o Azure Monitor oferece monitoramento centralizado com recursos abrangentes, como o Monitor Azure para VMs e Insights de aplicativos.

## <a name="diagnostics-and-metrics"></a>Diagnóstico e métrica 

Você pode configurar e monitorar a coleta de [dados de diagnóstico](https://docs.microsoft.com/cli/azure/vm/diagnostics) usando [métricas](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) no portal do Azure, a CLI do Azure, o Azure PowerShell e programando APIs (Interfaces de Programação de Aplicativos). Por exemplo, você pode:

- **Observar as métricas básicas para a VM.** Na tela Visão geral no portal do Azure, as métricas básicas mostradas incluem uso da CPU, uso de rede, total de bytes de disco e operações de disco por segundo.

- **Habilitar a coleta de diagnóstico de inicialização e exibi-lo usando o portal do Azure.** Ao colocar sua própria imagem no Azure ou iniciar uma das imagens da plataforma, muitas razões podem contribuir para que uma VM fique em um estado não inicializável. Você pode habilitar o diagnóstico de inicialização facilmente quando cria uma máquina virtual clicando em **Habilitado** no Diagnóstico de Inicialização na seção Monitoramento da tela Configurações.

    Durante a inicialização das VMs, o agente de diagnóstico de inicialização captura a saída de inicialização e a armazena no Armazenamento do Azure. Esses dados podem ser usados para solucionar problemas de inicialização da VM. Os diagnósticos de inicialização não são habilitados automaticamente quando você cria uma VM nas ferramentas de linha de comando. Antes de habilitar o diagnóstico de inicialização, é necessário criar uma conta de armazenamento para armazenar os logs de inicialização. Se você habilitar o diagnóstico de inicialização no portal do Azure, uma conta de armazenamento será criada automaticamente.

    Se você não habilitar o diagnóstico de inicialização quando a VM for criada, poderá habilitá-la mais tarde usando a [CLI do Azure](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), o [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic) ou um [modelo do Azure Resource Manager](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Habilitar a coleta de dados de diagnóstico do sistema operacional convidado.** Quando você cria uma máquina virtual, tem a oportunidade de habilitar o diagnóstico do sistema operacional convidado na tela de configurações. Quando você habilita a coleta de dados de diagnóstico, a [extensão IaaSDiagnostics para Linux](../articles/virtual-machines/linux/diagnostic-extension.md) ou a [extensão IaSDiagnostics para Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) é adicionada à VM, o que permite que você colete dados de disco, CPU e memória adicionais.

    Usando os dados de diagnóstico coletados, você pode configurar o dimensionamento automático das VMs. Você também pode configurar [o Azure Monitor Logs](../articles/azure-monitor/platform/data-platform-logs.md) para armazenar os dados e configurar alertas para avisar quando o desempenho não está certo.

## <a name="alerts"></a>Alertas

Você pode criar alertas com base em [métricas](../articles/azure-monitor/platform/alerts-overview.md) específicas de desempenho. Exemplos dos problemas que podem ser avisados incluem quando o uso médio da CPU excede determinado limite ou o espaço em disco livre disponível fica abaixo de determinado valor. Os alertas podem ser configurados no [portal Azure,](../articles/azure-monitor/platform/alerts-metric.md#create-with-azure-portal)usando [modelos do Azure Resource Manager](../articles/azure-monitor/platform/alerts-metric-create-templates.md)ou a Cli do [Azure.](../articles/azure-monitor/platform/alerts-metric.md#with-azure-cli)

## <a name="azure-service-health"></a>Integridade do Serviço do Azure

A [Integridade de Serviço do Azure](../articles/service-health/service-health-overview.md) dá suporte e diretrizes personalizadas em caso de problemas nos serviços do Azure, e ajuda você a se preparar para manutenção futura planejada. A Integridade de Serviço do Azure alerta você e suas equipes usando notificações flexíveis e direcionadas.

## <a name="azure-resource-health"></a>Azure Resource Health

O [Azure Resource Health](../articles/service-health/resource-health-overview.md) ajuda você a diagnosticar e a obter suporte quando um problema com o Azure afeta seus recursos. Ele informa sobre a integridade atual e anterior de seus recursos e ajuda a reduzir os problemas. O Resource Health fornece suporte técnico quando você precisa de ajuda com problemas de serviço do Azure.

## <a name="azure-activity-log"></a>Log de Atividades do Azure

O [Azure Activity Log](../articles/azure-monitor/platform/platform-logs-overview.md) é um registro de assinatura que fornece informações sobre eventos de nível de assinatura que ocorreram no Azure. O log inclui um intervalo de dados, de dados operacionais do Azure Resource Manager para atualizações em eventos de Integridade do Serviço. Você pode clicar em Log de Atividades no portal do Azure para exibir o log da sua VM.

Algumas coisas que você pode fazer com o log de atividades incluem:

- Crie um [alerta em um evento do Registro de Atividades](../articles/azure-monitor/platform/platform-logs-overview.md).
- [Transmita-o a um Event Hub](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) para ingestão por um serviço de terceiros ou uma solução de análise personalizada, como o Power BI.
- Analise-o no Power BI usando o [pacote de conteúdo Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Salvá-lo em uma conta de armazenamento](../articles/azure-monitor/platform/archive-activity-log.md) para inspeção manual ou arquivamento. Você pode especificar o tempo de retenção (em dias) usando o Perfil de Log.

Você também pode acessar os dados de log de atividade usando o [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), a [CLI do Azure](https://docs.microsoft.com/cli/azure/monitor) ou as [APIs REST do Monitor](https://docs.microsoft.com/rest/api/monitor/).

[Os logs de recursos do Azure](../articles/azure-monitor/platform/platform-logs-overview.md) são registros emitidos pela Sua VM que fornecem dados ricos e frequentes sobre sua operação. Os registros de recursos diferem do registro de atividades, fornecendo informações sobre as operações realizadas dentro da VM.

Algumas coisas que você pode fazer com os logs de diagnóstico incluem:

- [Salve-os em uma conta de armazenamento](../articles/azure-monitor/platform/archive-diagnostic-logs.md) para auditoria ou inspeção manual. Você pode especificar o tempo de retenção (em dias) usando as Configurações de Diagnóstico do Recurso.
- [Transmita-os aos Hubs de Eventos](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md) para ingestão por um serviço de terceiros ou uma solução de análise personalizada, como o Power BI.
- Analise-os com [o Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Monitoramento avançado

Para a visibilidade do aplicativo ou serviço suportado pelos conjuntos de escala de VM e máquinavirtual do Azure VM, identificação de problemas com o Sistema Operacional convidado ou carga de trabalho em execução na VM para entender se está afetando a disponibilidade ou o desempenho do aplicativo, ou se é um problema com o aplicativo, habilite tanto o [Monitor Azure para VMs](../articles/azure-monitor/insights/vminsights-overview.md) quanto [insights de aplicativos.](../articles/azure-monitor/app/app-insights-overview.md)

O Azure Monitor for VMs monitora suas máquinas virtuais do Azure (VM) em escala, analisando o desempenho e a saúde de seus VMs Windows e Linux, incluindo os diferentes processos e dependências interconectadas de outros recursos e processos externos que ele Descobre. Ele inclui vários gráficos de desempenho de tendência para ajudar durante a investigação de problemas e avaliar a capacidade de suas VMs. O mapa de dependência mostra máquinas monitoradas e não monitoradas, conexões de rede falhadas e ativas entre processos e essas máquinas e mostra gráficos de tendências com métricas de conexão de rede padrão. Combinado com o Application Insights, você monitora seu aplicativo e captura telemetria, como solicitações HTTP, exceções, etc. para que você possa correlacionar problemas entre as VMs e seu aplicativo. Configure [os alertas do Azure Monitor](../articles/azure-monitor/platform/alerts-overview.md) para alertá-lo sobre condições importantes detectadas a partir de dados de monitoramento coletados pelo Monitor Azure para VMs.

## <a name="next-steps"></a>Próximas etapas

- Siga as etapas em [Monitorar uma Máquina Virtual Windows com o Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) ou [Monitorar uma Máquina Virtual Linux com a CLI do Azure](../articles/virtual-machines/linux/tutorial-monitoring.md).

- Saiba mais sobre as melhores práticas de [Monitoramento e Diagnóstico](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
