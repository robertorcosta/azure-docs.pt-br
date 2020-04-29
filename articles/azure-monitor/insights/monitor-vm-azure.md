---
title: Monitorar máquinas virtuais do Azure com Azure Monitor
description: Descreve como coletar e analisar dados de monitoramento de máquinas virtuais no Azure usando o Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 2cb53d0c88d8c29da2bd8bf52d6536555d56c76e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80283932"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>Monitorando máquinas virtuais do Azure com Azure Monitor
Este artigo descreve como usar Azure Monitor para coletar e analisar dados de monitoramento de máquinas virtuais do Azure para manter sua integridade. As máquinas virtuais podem ser monitoradas quanto à disponibilidade e ao desempenho com Azure Monitor como qualquer [outro recurso do Azure](monitor-azure-resource.md), mas são exclusivas de outros recursos, já que você também precisa monitorar o sistema operacional e as cargas de trabalho convidadas que são executadas nele. 

> [!NOTE]
> Este artigo fornece uma visão geral completa dos conceitos e das opções para monitorar máquinas virtuais no Azure Monitor. Para começar a monitorar suas máquinas virtuais rapidamente sem se concentrar nos conceitos subjacentes, consulte [início rápido: monitorar uma máquina virtual do Azure com Azure monitor](../learn/quick-monitor-azure-vm.md).


## <a name="differences-from-other-azure-resources"></a>Diferenças de outros recursos do Azure
O [monitoramento de recursos do Azure com Azure monitor](monitor-azure-resource.md) descreve os dados de monitoramento gerados pelos recursos do Azure e como você pode usar os recursos do Azure monitor para analisar e alertar sobre esses dados. Você pode coletar e agir nos mesmos dados de monitoramento de máquinas virtuais do Azure com as seguintes diferenças:

- As [métricas de plataforma](../platform/data-platform-metrics.md) são coletadas automaticamente para máquinas virtuais, mas apenas para o [host da máquina virtual](#monitoring-data). Você precisa de um agente para coletar dados de desempenho do sistema operacional convidado. 
- As máquinas virtuais não geram [logs de recursos](../platform/platform-logs-overview.md) que fornecem informações sobre as operações que foram executadas em um recurso do Azure. Você usa um agente para coletar dados de log do sistema operacional convidado.
- Você pode criar [configurações de diagnóstico](../platform/diagnostic-settings.md) para uma máquina virtual para enviar métricas de plataforma para outros destinos, como armazenamento e hubs de eventos, mas não pode definir essas configurações de diagnóstico no portal do Azure. 

## <a name="monitoring-data"></a>Dados de monitoramento
As máquinas virtuais no Azure no Azure geram [logs](../platform/data-platform-logs.md) e [métricas](../platform/data-platform-metrics.md) mostrados no diagrama a seguir.

![Visão geral](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>Host da máquina virtual
As máquinas virtuais no Azure geram os seguintes dados para o host da máquina virtual da mesma forma que outros recursos do Azure, conforme descrito em [dados de monitoramento](monitor-azure-resource.md#monitoring-data).

- [Métricas de plataforma](../platform/data-platform-metrics.md) -valores numéricos que são coletados automaticamente em intervalos regulares e descrevem algum aspecto de um recurso em um determinado momento. As métricas de plataforma são coletadas para o host da máquina virtual, mas você precisa da extensão de diagnóstico para coletar métricas para o sistema operacional convidado.
- [Log de atividades](../platform/platform-logs-overview.md) – fornece informações sobre as operações em cada recurso do Azure na assinatura de fora (o plano de gerenciamento). Para uma máquina virtual, isso inclui informações como quando ele foi iniciado e qualquer alteração de configuração.


### <a name="guest-operating-system"></a>Sistema operacional convidado
Para coletar dados do sistema operacional convidado de uma máquina virtual, você precisa de um agente, que é executado localmente em cada máquina virtual e envia dados para Azure Monitor. Vários agentes estão disponíveis para Azure Monitor cada um coletando dados diferentes e gravando dados em locais diferentes. Obtenha uma comparação detalhada dos diferentes agentes em [visão geral dos agentes de Azure monitor](../platform/agents-overview.md). 

- [Agente de log Analytics](../platform/agents-overview.md#log-analytics-agent) – disponível para máquinas virtuais no Azure, outros ambientes de nuvem e locais. Coleta dados para logs de Azure Monitor. Oferece suporte a soluções de Azure Monitor para VMs e monitoramento. Esse é o mesmo agente usado para System Center Operations Manager.
- [Agente de dependência](../platform/agents-overview.md#dependency-agent) – coleta dados sobre os processos em execução na máquina virtual e suas dependências. O depende do agente de Log Analytics para transmitir dados para o Azure e oferece suporte às soluções Azure Monitor para VMs, Mapa do Serviço e Wire Data 2.0.
- [Extensão de diagnóstico do Azure](../platform/agents-overview.md#azure-diagnostics-extension) – disponível somente para máquinas virtuais Azure monitor. Pode coletar dados para vários locais, mas usados principalmente para coletar dados de desempenho de convidado em métricas de Azure Monitor para máquinas virtuais do Windows.
- [Agente Telegraf](../platform/collect-custom-metrics-linux-telegraf.md) – coletar dados de desempenho de VMs Linux para Azure monitor métricas.


## <a name="configuration-requirements"></a>Requisitos de configuração
Para habilitar todos os recursos de Azure Monitor para monitorar uma máquina virtual, você precisa coletar dados de monitoramento do host da máquina virtual e do sistema operacional convidado para [Azure monitor métricas](../platform/data-platform-logs.md) e [logs de Azure monitor](../platform/data-platform-logs.md). A tabela a seguir lista a configuração que deve ser executada para habilitar essa coleção. Você pode optar por não executar todas essas etapas dependendo de seus requisitos específicos.

| Etapa de configuração | Ações concluídas | Recursos habilitados |
|:---|:---|:---|
| Nenhuma configuração | -Métricas de plataforma de host coletadas para métricas.<br>-Log de atividades coletado. | -Métricas Explorer para host.<br>-Alertas de métricas para host.<br>-Alertas do log de atividades. |
| [Habilitar o Azure Monitor para VMs](#enable-azure-monitor-for-vms) | -Agente de Log Analytics instalado.<br>-Agente de dependência instalado.<br>-Dados de desempenho de convidado coletados para logs.<br>-Detalhes de processo e dependência coletados para logs. | -Gráficos de desempenho e pastas de trabalho para dados de desempenho de convidado.<br>-Log de consultas para dados de desempenho de convidado.<br>-Log de alertas para dados de desempenho de convidado.<br>-Mapa de dependências. |
| [Instalar a extensão de diagnóstico e o agente Telegraf](#enable-diagnostics-extension-and-telegraf-agent) | -Dados de desempenho de convidado coletados para métricas. | -Métricas Explorer para convidado.<br>-Alertas de métricas para convidado.  |
| [Configurar Log Analytics espaço de trabalho](#configure-log-analytics-workspace) | -Eventos coletados do convidado. | -Log de consultas para eventos de convidado.<br>-Log de alertas para eventos de convidado. |
| [Criar configuração de diagnóstico para a máquina virtual](#collect-platform-metrics-and-activity-log) | -Métricas de plataforma coletadas para logs.<br>-Log de atividades coletado para logs. | -LOQ consulta as métricas do host.<br>-Log de alertas para métricas do host.<br>-Log de consultas para o log de atividades.

Cada uma dessas etapas de configuração é descrita nas seções a seguir.

### <a name="enable-azure-monitor-for-vms"></a>Habilitar o Azure Monitor para VMs
[Azure monitor para VMs](vminsights-overview.md) é um [insight](insights-overview.md) em Azure monitor que é a principal ferramenta para monitorar máquinas virtuais no Azure monitor. Ele fornece o seguinte valor adicional em relação aos recursos de Azure Monitor padrão.

- Integração simplificada do agente de Log Analytics e do agente de dependência para habilitar o monitoramento de cargas de trabalho e um sistema operacional convidado de máquina virtual. 
- Gráficos predefinidos de desempenho de tendências e pastas de trabalho que permitem que você analise as métricas de desempenho principais do sistema operacional convidado da máquina virtual.
- Mapa de dependências que exibe os processos em execução em cada máquina virtual e os componentes interconectados com outros computadores e fontes externas.

![Azure Monitor para VMs](media/monitor-vm-azure/vminsights-01.png)

![Azure Monitor para VMs](media/monitor-vm-azure/vminsights-02.png)


Habilite Azure Monitor para VMs da opção **insights** no menu de máquina virtual do portal do Azure. Consulte [habilitar Azure monitor para VMs visão geral](vminsights-enable-overview.md) para obter detalhes e outros métodos de configuração.

![Habilitar o Azure Monitor para VMs](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>Configurar Log Analytics espaço de trabalho
O agente de Log Analytics usado pelo Azure Monitor para VMs envia dados para um [espaço de trabalho log Analytics](../platform/data-platform-logs.md#how-is-data-in-azure-monitor-logs-structured). Você pode habilitar a coleta de dados de desempenho adicionais, eventos e outros dados de monitoramento do agente, configurando o espaço de trabalho Log Analytics. Ele só precisa ser configurado uma vez, uma vez que qualquer agente que se conecta ao espaço de trabalho baixará automaticamente a configuração e começará imediatamente a coletar os dados definidos. 

Você pode acessar a configuração do espaço de trabalho diretamente do Azure Monitor para VMs selecionando **configuração do espaço de trabalho** na **introdução**. Clique no nome do espaço de trabalho para abrir o menu.

![Configuração do workspace](media/monitor-vm-azure/workspace-configuration.png)

Selecione **Configurações avançadas** no menu do espaço de trabalho e, em seguida, **dados** para configurar as fontes de dados. Para agentes do Windows, selecione **logs de eventos do Windows** e adicione logs de eventos comuns, como *sistema* e *aplicativo*. Para agentes do Linux, selecione **syslog** e adicione instalações comuns, como *kerning* e *daemon*. Consulte [fontes de dados do agente no Azure monitor](../platform/agent-data-sources.md) para obter uma lista das fontes de dados disponíveis e detalhes sobre como configurá-las. 

![Configurar eventos](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> Você pode configurar os contadores de desempenho a serem coletados da configuração do espaço de trabalho, mas isso pode ser redundante com os contadores de desempenho coletados pelo Azure Monitor para VMs. Azure Monitor para VMs coleta o conjunto mais comum de contadores em uma frequência de uma vez por minuto. Configure somente os contadores de desempenho a serem coletados pelo espaço de trabalho se você quiser coletar contadores que ainda não foram coletados pelo Azure Monitor para VMs ou se você tiver consultas existentes usando dados de desempenho.


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>Habilitar a extensão de diagnóstico e o agente Telegraf
O Azure Monitor para VMs é baseado no agente de Log Analytics que coleta dados em um espaço de trabalho Log Analytics. Isso dá suporte a [vários recursos do Azure monitor](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) , como [consultas de log](../log-query/log-query-overview.md), [alertas de log](../platform/alerts-log.md)e [pastas de trabalho](../platform/workbooks-overview.md). A [extensão de diagnóstico](../platform/diagnostics-extension-overview.md) coleta dados de desempenho do sistema operacional convidado de máquinas virtuais do Windows para o armazenamento do Azure e, opcionalmente, envia dados de desempenho para [Azure monitor métricas](../platform/data-platform-metrics.md). Para máquinas virtuais do Linux, o [agente Telegraf](../platform/collect-custom-metrics-linux-telegraf.md) é necessário para enviar dados para as métricas do Azure.  Isso permite outros recursos do Azure Monitor, como [métricas Explorer](../platform/metrics-getting-started.md) e [alertas de métricas](../platform/alerts-metric.md). Você também pode configurar a extensão de diagnóstico para enviar eventos e dados de desempenho fora do Azure Monitor usando os hubs de eventos do Azure.

Instale a extensão de diagnóstico para uma única máquina virtual do Windows no portal do Azure da opção de **configuração de diagnóstico** no menu da VM. Selecione a opção para habilitar **Azure monitor** na guia **coletores** . Para habilitar a extensão de um modelo ou linha de comando para várias máquinas virtuais, consulte [instalação e configuração](../platform/diagnostics-extension-overview.md#installation-and-configuration). Ao contrário do agente de Log Analytics, os dados a serem coletados são definidos na configuração da extensão em cada máquina virtual.

![Configuração de diagnóstico](media/monitor-vm-azure/diagnostic-setting.png)

Consulte [instalar e configurar o Telegraf](../platform/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf) para obter detalhes sobre como configurar os agentes do telegraf em máquinas virtuais do Linux. A opção de menu de **configuração de diagnóstico** está disponível para Linux, mas só permitirá que você envie dados para o armazenamento do Azure.

### <a name="collect-platform-metrics-and-activity-log"></a>Coletar métricas de plataforma e o log de atividades
Você pode exibir as métricas de plataforma e o log de atividades coletados para cada host de máquina virtual na portal do Azure. Colete esses dados no mesmo espaço de trabalho Log Analytics como Azure Monitor para VMs para analisá-los com os outros dados de monitoramento coletados para a máquina virtual. Essa coleção é configurada com uma [configuração de diagnóstico](../platform/diagnostic-settings.md). Colete o log de atividades com uma [configuração de diagnóstico para a assinatura](../platform/diagnostic-settings.md#create-diagnostic-settings-in-azure-portal).

Colete métricas de plataforma com uma configuração de diagnóstico para a máquina virtual. Ao contrário de outros recursos do Azure, você não pode criar uma configuração de diagnóstico para uma máquina virtual no portal do Azure, mas deve usar [outro método](../platform/diagnostic-settings.md#create-diagnostic-settings-using-powershell). Os exemplos a seguir mostram como coletar métricas para uma máquina virtual usando o PowerShell e a CLI.

```powershell
Set-AzDiagnosticSetting -Name vm-diagnostics -ResourceId "/subscriptions/monitor diagnostic-settings create \
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" -Enabled $true -MetricCategory AllMetrics -workspaceId "/subscriptions/monitor diagnostic-settings create \
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
```

```CLI
az monitor diagnostic-settings create \
--name VM-Diagnostics 
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace

```

## <a name="monitoring-in-the-azure-portal"></a>Monitoramento no portal do Azure 
Depois de configurar a coleta de dados de monitoramento para uma máquina virtual, você tem várias opções para acessá-la no portal do Azure:

- Use o menu **Azure monitor** para acessar dados de todos os recursos monitorados. 
- Use Azure Monitor para VMs para monitorar conjuntos de máquinas virtuais em escala.
- Analise os dados de uma única máquina virtual em seu menu na portal do Azure. A tabela a seguir lista opções diferentes para monitorar o menu de máquinas virtuais.

![Monitoramento no portal do Azure](media/monitor-vm-azure/monitor-menu.png)

| Opção de menu | Descrição |
|:---|:---|
| Visão geral | Exibe as [métricas de plataforma](../platform/data-platform-metrics.md) para o host da máquina virtual. Clique em um grafo para trabalhar com esses dados no [Metrics Explorer](../platform/metrics-getting-started.md). |
| Log de atividades | Entradas do [log de atividades](../platform/activity-log-view.md) filtradas para a máquina virtual atual. |
| Insights | Abre [Azure monitor para VMs](../insights/vminsights-overview.md) com o mapa para a máquina virtual atual selecionada. |
| Alertas | Exibe [alertas](../platform/alerts-overview.md) para a máquina virtual atual.  |
| Métricas | Abra o [Metrics Explorer](../platform/metrics-getting-started.md) com o escopo definido para a máquina virtual atual. |
| Configurações de Diagnóstico | Habilite e configure a [extensão de diagnóstico](../platform/diagnostics-extension-overview.md) para a máquina virtual atual. |
| Recomendações do Assistente | Recomendações para a máquina virtual atual do [Azure Advisor](/azure/advisor/). |
| Logs | Abra [log Analytics](../log-query/log-query-overview.md#what-is-log-analytics) com o [escopo](../log-query/scope.md) definido para a máquina virtual atual. |
| Monitor de conexão | Abra o [Monitor de conexão do observador de rede](../../network-watcher/connection-monitor-preview.md) para monitorar conexões entre a máquina virtual atual e outras máquinas virtuais. |


## <a name="analyzing-metric-data"></a>Analisando dados de métrica
Você pode analisar métricas para máquinas virtuais usando o Metrics Explorer abrindo **métricas** no menu da máquina virtual. Consulte [introdução ao Azure Metrics Explorer](../platform/metrics-getting-started.md) para obter detalhes sobre como usar essa ferramenta. 

Há dois namespaces usados por máquinas virtuais para métricas:

| Namespace | Descrição |
|:---|:---|
| Host de Máquina Virtual | Métricas do host coletadas automaticamente para todas as máquinas virtuais do Azure. Lista detalhada de métricas em [Microsoft. Compute/virtualMachines](../platform/metrics-supported.md#microsoftcomputevirtualmachines). |
| Convidado de máquina virtual | Métricas do sistema operacional convidado coletadas de máquinas virtuais com a extensão de diagnóstico instaladas e configuradas para enviar para Azure Monitor coletor. |

![Métricas](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>Analisando dados de log
As máquinas virtuais do Azure coletarão os dados a seguir para Azure Monitor logs. 

Azure Monitor para VMs habilita a coleta de um conjunto predeterminado de contadores de desempenho que são gravados na tabela *InsightsMetrics* . Essa é a mesma tabela usada por [Azure monitor para contêineres](container-insights-overview.md). 

| Fonte de dados | Requisitos | Tabelas |
|:---|:---|:---|
| Azure Monitor para VMs | Habilite em cada máquina virtual. | InsightsMetrics<br>VMBoundPort<br>VMComputer<br>VMConnection<br>VMProcess<br>Consulte [como consultar logs de Azure monitor para VMs](vminsights-log-search.md) para obter detalhes. |
| Log de atividades | Configuração de diagnóstico para a assinatura. | AzureActivity |
| Métricas do host | Configuração de diagnóstico para a máquina virtual. | AzureMetrics |
| Fontes de dados do sistema operacional convidado | Habilite o Log Analytics Agent e configure as fontes de dados. | Consulte a documentação para cada fonte de dados. |


> [!NOTE]
> Os dados de desempenho coletados pelo agente de Log Analytics são gravados na tabela *perf* enquanto Azure monitor para VMs o coletarão na tabela *InsightsMetrics* . Esses são os mesmos dados, mas as tabelas têm uma estrutura diferente. Se você tiver consultas existentes com base no *desempenho*, será necessário reescrever para usar o *InsightsMetrics*.


## <a name="alerts"></a>Alertas
[Alertas](../platform/alerts-overview.md) no Azure monitor notificá-lo proativamente quando condições importantes forem encontradas nos dados de monitoramento e potencialmente iniciar uma ação, como iniciar um aplicativo lógico ou chamar um webhook. As regras de alerta definem a lógica usada para determinar quando um alerta deve ser criado. Azure Monitor coleta os dados usados pelas regras de alerta, mas você precisa criar regras para definir condições de alerta em sua assinatura do Azure.

As seções a seguir descrevem os tipos de regras de alerta e recomendações sobre quando você deve usar cada um. Essa recomendação se baseia na funcionalidade e no custo do tipo de regra de alerta. Para obter detalhes sobre os preços de alertas, consulte [preços de Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).


### <a name="activity-log-alert-rules"></a>Regras de alerta do log de atividades
[As regras de alerta do log de atividades](../platform/alerts-activity-log.md) são acionadas quando uma entrada que corresponde a critérios específicos é criada no log de atividades. Eles não têm nenhum custo, portanto, devem ser sua primeira opção se a lógica necessária estiver no log de atividades. 

O recurso de destino para alertas do log de atividades pode ser uma máquina virtual específica, todas as máquinas virtuais em um grupo de recursos ou todas as máquinas virtuais em uma assinatura.

Por exemplo, crie um alerta se uma máquina virtual crítica for parada selecionando desligar a *máquina virtual* para o nome do sinal.

![Alerta do log de atividades](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>Regras de alerta de métrica
[As regras de alerta de métrica](../platform/alerts-metric.md) são acionadas quando um valor de métrica excede um limite. Você pode definir um valor de limite específico ou permitir que Azure Monitor determine dinamicamente um limite com base em dados históricos.  Use alertas de métrica sempre que possível com dados de métrica, pois eles custam menos e são mais responsivos do que as regras de alerta de log. Eles também estão com monitoração de estado, o que significa que eles se resolverão quando a métrica cair abaixo do limite.

O recurso de destino para alertas do log de atividades pode ser uma máquina virtual específica ou todas as máquinas virtuais em um grupo de recursos.

Por exemplo, para criar um alerta quando o processador de uma máquina virtual exceder um valor específico, crie uma regra de alerta de métrica usando a *porcentagem de CPU* como o tipo de sinal. Defina um valor de limite específico ou permita que Azure Monitor defina um limite dinâmico. 

![Alerta de métrica](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>Alertas de registro
[As regras de alerta de log](../platform/alerts-log.md) são acionadas quando os resultados de uma consulta de log agendada correspondem a determinados critérios. Os alertas de consulta de log são os mais caros e menos responsivos das regras de alerta, mas têm acesso aos dados mais diversos e podem executar uma lógica complexa que não pode ser executada pelas outras regras de alerta. 

O recurso de destino para uma consulta de log é um espaço de trabalho Log Analytics. Filtrar computadores específicos na consulta.

Por exemplo, para criar um alerta que verifica se alguma máquina virtual em um grupo de recursos específico está offline, use a consulta a seguir, que retorna um registro para cada computador que perdeu uma pulsação nos últimos dez minutos. Use um limite de 1 que é acionado se pelo menos um computador tiver uma pulsação perdida.

```kusto
Heartbeat
| where TimeGenerated < ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![Alerta de log](media/monitor-vm-azure/log-alert-01.png)

Para criar um alerta se um número excessivo de logons com falha tiver ocorrido em qualquer máquina virtual do Windows na assinatura, use a consulta a seguir que retorna um registro para cada evento de logon com falha na última hora. Use um limite definido para o número de logons com falha que você permitirá. 

```kusto
Event
| where TimeGenerated < ago(1hr)
| where EventID == 4625
```

![Alerta de log](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
System Center Operations Manager (SCOM) fornece monitoramento granular de cargas de trabalho em máquinas virtuais. Consulte o [Guia de monitoramento de nuvem](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/monitor/) para obter uma comparação de plataformas de monitoramento e estratégias diferentes para implementação.

Se você tiver um ambiente existente do SCOM que pretende continuar usando, você poderá integrá-lo com Azure Monitor para fornecer funcionalidade adicional. O agente de Log Analytics usado pelo Azure Monitor é o mesmo usado para o SCOM para que você monitore as máquinas virtuais para ambos. Você ainda precisa adicionar o agente para Azure Monitor para VMs e configurar o espaço de trabalho para coletar dados adicionais, conforme especificado acima, mas as máquinas virtuais podem continuar a executar seus pacotes de gerenciamento existentes em um ambiente do SCOM sem modificação.

Os recursos de Azure Monitor que aumentam os recursos existentes do SCOM incluem o seguinte:

- Use Log Analytics para analisar interativamente seus dados de desempenho e de log.
- Use alertas de log para definir condições de alerta em várias máquinas virtuais e usando tendências de longo prazo que não são possíveis usando alertas no SCOM.   

Consulte [conectar Operations Manager ao Azure monitor](../platform/om-agents.md) para obter detalhes sobre como conectar seu grupo de gerenciamento do SCOM existente ao seu espaço de trabalho do log Analytics.


## <a name="next-steps"></a>Próximas etapas

* [Saiba como analisar dados em logs de Azure Monitor usando consultas de log.](../log-query/get-started-queries.md)
* [Saiba mais sobre alertas usando métricas e logs em Azure Monitor.](../platform/alerts-overview.md)

