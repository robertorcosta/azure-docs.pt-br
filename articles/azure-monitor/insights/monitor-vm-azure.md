---
title: Monitore máquinas virtuais do Azure com o Monitor Azure
description: Descreve como coletar e analisar dados de monitoramento de máquinas virtuais no Azure usando o Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 2cb53d0c88d8c29da2bd8bf52d6536555d56c76e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283932"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>Monitoramento de máquinas virtuais do Azure com monitor azure
Este artigo descreve como usar o Azure Monitor para coletar e analisar dados de monitoramento de máquinas virtuais do Azure para manter sua saúde. As máquinas virtuais podem ser monitoradas para disponibilidade e desempenho com o Azure Monitor como qualquer [outro recurso do Azure,](monitor-azure-resource.md)mas são exclusivas de outros recursos, já que você também precisa monitorar o funcionamento e o sistema dos hóspedes e as cargas de trabalho que o executam nele. 

> [!NOTE]
> Este artigo fornece uma visão completa dos conceitos e opções para monitorar máquinas virtuais no Azure Monitor. Para começar a monitorar suas máquinas virtuais rapidamente sem focar nos conceitos subjacentes, consulte [Quickstart: Monitore uma máquina virtual Do Zure com o Monitor Azure](../learn/quick-monitor-azure-vm.md).


## <a name="differences-from-other-azure-resources"></a>Diferenças em relação a outros recursos do Azure
[O monitoramento dos recursos do Azure com o Azure Monitor](monitor-azure-resource.md) descreve os dados de monitoramento gerados pelos recursos do Azure e como você pode usar os recursos do Azure Monitor para analisar e alertar sobre esses dados. Você pode coletar e agir nos mesmos dados de monitoramento de máquinas virtuais do Azure com as seguintes diferenças:

- [As métricas da plataforma](../platform/data-platform-metrics.md) são coletadas automaticamente para máquinas virtuais, mas apenas para o [host de máquina virtual.](#monitoring-data) Você precisa de um agente para coletar dados de desempenho do sistema operacional convidado. 
- As máquinas virtuais não geram [registros de recursos](../platform/platform-logs-overview.md) que fornecem informações sobre operações que foram realizadas dentro de um recurso do Azure. Você usa um agente para coletar dados de registro do sistema operacional convidado.
- Você pode criar [configurações de diagnóstico](../platform/diagnostic-settings.md) para uma máquina virtual enviar métricas de plataforma para outros destinos, como centros de armazenamento e eventos, mas não pode configurar essas configurações de diagnóstico no portal Azure. 

## <a name="monitoring-data"></a>Dados de monitoramento
Máquinas virtuais no Azure no Azure geram [logs](../platform/data-platform-logs.md) e [métricas mostrados](../platform/data-platform-metrics.md) no diagrama a seguir.

![Visão geral](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>Host de máquina virtual
As máquinas virtuais no Azure geram os seguintes dados para a máquina virtual hospedar o mesmo que outros recursos do Azure, conforme descrito nos [dados de monitoramento](monitor-azure-resource.md#monitoring-data).

- [Métricas da plataforma](../platform/data-platform-metrics.md) - Valores numéricos que são coletados automaticamente em intervalos regulares e descrevem algum aspecto de um recurso em um determinado momento. As métricas da plataforma são coletadas para o host da máquina virtual, mas você precisa da extensão de diagnóstico para coletar métricas para o sistema operacional convidado.
- [Registro de atividades](../platform/platform-logs-overview.md) - Fornece informações sobre as operações em cada recurso do Azure na assinatura de fora (o plano de gerenciamento). Para uma máquina virtual, isso inclui informações como quando foi iniciada e qualquer alteração de configuração.


### <a name="guest-operating-system"></a>Sistema operacional convidado
Para coletar dados do sistema operacional convidado de uma máquina virtual, você precisa de um agente, que é executado localmente em cada máquina virtual e envia dados para o Azure Monitor. Vários agentes estão disponíveis para o Azure Monitor com cada um coletando dados diferentes e escrevendo dados para diferentes locais. Obtenha uma comparação detalhada dos diferentes agentes no [Overview dos agentes do Monitor Do Azure](../platform/agents-overview.md). 

- [Agente log analytics -](../platform/agents-overview.md#log-analytics-agent) Disponível para máquinas virtuais no Azure, outros ambientes em nuvem e no local. Coleta dados para o Azure Monitor Logs. Suporta Monitor Azure para VMs e soluções de monitoramento. Este é o mesmo agente usado para o Gerente de Operações do Centro de Sistemas.
- [Agente de dependência](../platform/agents-overview.md#dependency-agent) - Coleta dados sobre os processos em execução na máquina virtual e suas dependências. Conta com o agente Log Analytics para transmitir dados no Azure e oferece suporte ao Azure Monitor para vMs, mapa de serviço e soluções Wire Data 2.0.
- [Extensão Azure Diagnostic](../platform/agents-overview.md#azure-diagnostics-extension) - Disponível apenas para máquinas virtuais do Azure Monitor. Pode coletar dados para vários locais, mas usado principalmente para coletar dados de desempenho de hóspedes em métricas do Monitor Azure para máquinas virtuais windows.
- [Agente Telegraf](../platform/collect-custom-metrics-linux-telegraf.md) - Colete dados de desempenho de VMs Linux em Métricas do Monitor Azure.


## <a name="configuration-requirements"></a>Requisitos de configuração
Para habilitar todos os recursos do Azure Monitor para monitorar uma máquina virtual, você precisa coletar dados de monitoramento do host da máquina virtual e do sistema operacional convidado tanto para o [Azure Monitor Metrics](../platform/data-platform-logs.md) quanto [para o Azure Monitor Logs](../platform/data-platform-logs.md). A tabela a seguir lista a configuração que deve ser executada para habilitar essa coleta. Você pode optar por não executar todas essas etapas, dependendo de seus requisitos específicos.

| Etapa de configuração | Ações concluídas | Recursos habilitados |
|:---|:---|:---|
| Nenhuma configuração | - Métricas da plataforma host coletadas em Métricas.<br>- Registro de atividade coletado. | - Explorador de métricas para hospedeiro.<br>- Alertas métricos para host.<br>- Alertas de registro de atividades. |
| [Habilitar o Azure Monitor para VMs](#enable-azure-monitor-for-vms) | - Agente log analytics instalado.<br>- Agente de dependência instalado.<br>- Dados de desempenho de hóspedes coletados no Logs.<br>- Detalhes de processo e dependência coletados em Logs. | - Gráficos de desempenho e livros de trabalho para dados de desempenho de hóspedes.<br>- Registre consultas para dados de desempenho de hóspedes.<br>- Registre alertas para dados de desempenho dos hóspedes.<br>- Mapa de dependência. |
| [Instale a extensão de diagnóstico e o agente de telegraf](#enable-diagnostics-extension-and-telegraf-agent) | - Dados de desempenho de hóspedes coletados em Métricas. | - Explorador de métricas para convidado.<br>- Alertas métricos para o hóspede.  |
| [Configurar o espaço de trabalho do Log Analytics](#configure-log-analytics-workspace) | - Eventos coletados de convidados. | - Consultas de registro para eventos de hóspedes.<br>- Alertas de registro para eventos de hóspedes. |
| [Criar configuração de diagnóstico para máquina virtual](#collect-platform-metrics-and-activity-log) | - Métricas da plataforma coletadas em Logs.<br>- Registro de atividades coletado em Logs. | - Consultas de Loq para métricas de host.<br>- Registre alertas para métricas de host.<br>- Registre consultas para registro de atividades.

Cada uma dessas etapas de configuração é descrita nas seguintes seções.

### <a name="enable-azure-monitor-for-vms"></a>Habilitar o Azure Monitor para VMs
[O Azure Monitor for VMs](vminsights-overview.md) é uma [visão do](insights-overview.md) Azure Monitor, que é a principal ferramenta para monitorar máquinas virtuais no Azure Monitor. Ele fornece o seguinte valor adicional sobre os recursos padrão do Azure Monitor.

- O onboarding simplificado do agente log analytics e do agente de dependência para permitir o monitoramento de um sistema operacional e cargas de trabalho de hóspedes de máquina virtual. 
- Gráficos de desempenho de tendências pré-definidos e livros de trabalho que permitem analisar métricas de desempenho principais do sistema operacional convidado da máquina virtual.
- Mapa de dependência que exibe processos em execução em cada máquina virtual e os componentes interconectados com outras máquinas e fontes externas.

![Azure Monitor para VMs](media/monitor-vm-azure/vminsights-01.png)

![Azure Monitor para VMs](media/monitor-vm-azure/vminsights-02.png)


Habilite o Monitor Azure para VMs a partir da opção **Insights** no menu de máquina virtual do portal Azure. Consulte [Ativar o Monitor Azure para vMs para obter detalhes](vminsights-enable-overview.md) e outros métodos de configuração.

![Habilitar o Azure Monitor para VMs](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>Configurar o espaço de trabalho do Log Analytics
O agente log analytics usado pelo Azure Monitor para VMs envia dados para um [espaço de trabalho do Log Analytics](../platform/data-platform-logs.md#how-is-data-in-azure-monitor-logs-structured). Você pode habilitar a coleta de dados adicionais de desempenho, eventos e outros dados de monitoramento do agente, configurando o espaço de trabalho do Log Analytics. Ele só precisa ser configurado uma vez, já que qualquer agente que se conectar ao espaço de trabalho irá baixar automaticamente a configuração e começar imediatamente a coletar os dados definidos. 

Você pode acessar a configuração para o espaço de trabalho diretamente do Monitor Azure para VMs selecionando a **configuração workspace** a partir do **Get Started**. Clique no nome do espaço de trabalho para abrir seu menu.

![Configuração do workspace](media/monitor-vm-azure/workspace-configuration.png)

Selecione **Configurações avançadas** no menu do espaço de trabalho e, em seguida, **Dados** para configurar fontes de dados. Para agentes do Windows, selecione **registros de eventos do Windows** e adicione registros de eventos comuns, como *sistema* e *aplicativo.* Para agentes Linux, selecione **Syslog** e adicione instalações comuns como *kern* e *daemon*. Consulte [as fontes de dados do Agente no Azure Monitor](../platform/agent-data-sources.md) para obter uma lista das fontes de dados disponíveis e detalhes sobre a configuração. 

![Configurar eventos](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> Você pode configurar contadores de desempenho a serem coletados na configuração do espaço de trabalho, mas isso pode ser redundante com os contadores de desempenho coletados pelo Azure Monitor para VMs. O Monitor Azure para VMs coleta o conjunto mais comum de contadores a uma frequência de uma vez por minuto. Apenas configure contadores de desempenho a serem coletados pelo espaço de trabalho se você quiser coletar contadores ainda não coletados pelo Azure Monitor para VMs ou se você tiver consultas existentes usando dados de desempenho.


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>Habilitar extensão de diagnósticos e agente Telegraf
O Azure Monitor for VMs é baseado no agente Log Analytics que coleta dados em um espaço de trabalho do Log Analytics. Isso suporta [vários recursos do Azure Monitor,](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) como consultas de [log,](../log-query/log-query-overview.md) [alertas de log](../platform/alerts-log.md)e [pastas de trabalho.](../platform/workbooks-overview.md) A [extensão de diagnóstico](../platform/diagnostics-extension-overview.md) coleta dados de desempenho do sistema operacional convidado de máquinas virtuais Windows para o Azure Storage e envia opcionalmente dados de desempenho para o [Azure Monitor Metrics](../platform/data-platform-metrics.md). Para máquinas virtuais Linux, o [agente Telegraf](../platform/collect-custom-metrics-linux-telegraf.md) é obrigado a enviar dados para o Azure Metrics.  Isso permite que outros recursos do Azure Monitor, como [o explorador de métricas](../platform/metrics-getting-started.md) e [alertas métricos.](../platform/alerts-metric.md) Você também pode configurar a extensão de diagnóstico para enviar eventos e dados de desempenho fora do Azure Monitor usando o Azure Event Hubs.

Instale a extensão de diagnóstico para uma única máquina virtual do Windows no portal Azure a partir da opção **de configuração Diagnósticos** no menu VM. Selecione a opção para ativar **o Monitor Azure** na guia **Sinks.** Para habilitar a extensão de um modelo ou linha de comando para várias máquinas virtuais, consulte [Instalação e configuração](../platform/diagnostics-extension-overview.md#installation-and-configuration). Ao contrário do agente Log Analytics, os dados a serem coletados são definidos na configuração para a extensão em cada máquina virtual.

![Configuração de diagnóstico](media/monitor-vm-azure/diagnostic-setting.png)

Consulte [Instalar e configurar o Telegraf](../platform/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf) para obter detalhes sobre a configuração dos agentes Telegraf em máquinas virtuais Linux. A opção de menu **de configuração diagnóstico** está disponível para Linux, mas só permitirá enviar dados para o armazenamento do Azure.

### <a name="collect-platform-metrics-and-activity-log"></a>Coletar métricas da plataforma e log de atividades
Você pode visualizar as métricas da plataforma e o registro de atividade coletados para cada host de máquina virtual no portal Azure. Colete esses dados no mesmo espaço de trabalho do Log Analytics que o Azure Monitor para VMs para analisá-los com os outros dados de monitoramento coletados para a máquina virtual. Esta coleção é configurada com uma [configuração de diagnóstico](../platform/diagnostic-settings.md). Colete o registro de atividades com uma [configuração de diagnóstico para a assinatura](../platform/diagnostic-settings.md#create-diagnostic-settings-in-azure-portal).

Colete métricas da plataforma com uma configuração de diagnóstico para a máquina virtual. Ao contrário de outros recursos do Azure, você não pode criar uma configuração de diagnóstico para uma máquina virtual no portal Azure, mas deve usar [outro método](../platform/diagnostic-settings.md#create-diagnostic-settings-using-powershell). Os exemplos a seguir mostram como coletar métricas para uma máquina virtual usando tanto powershell quanto CLI.

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
Uma vez configurada coleta de dados de monitoramento para uma máquina virtual, você tem várias opções para acessá-lo no portal azure:

- Use o **menu Do Monitor do Azure** para acessar dados de todos os recursos monitorados. 
- Use o Monitor Azure para VMs para monitorar conjuntos de máquinas virtuais em escala.
- Analise dados para uma única máquina virtual de seu menu no portal Azure. A tabela abaixo lista diferentes opções para monitorar o menu de máquinas virtuais.

![Monitoramento no portal do Azure](media/monitor-vm-azure/monitor-menu.png)

| Opção de menu | Descrição |
|:---|:---|
| Visão geral | Exibe [métricas](../platform/data-platform-metrics.md) da plataforma para o host de máquina virtual. Clique em um gráfico para trabalhar com esses dados no [metrics explorer](../platform/metrics-getting-started.md). |
| Log de atividades | [Entradas de registro](../platform/activity-log-view.md) de atividade filtradas para a máquina virtual atual. |
| Insights | Abre [o Monitor Azure para VMs](../insights/vminsights-overview.md) com o mapa para a máquina virtual atual selecionada. |
| Alertas | Visualiza [alertas](../platform/alerts-overview.md) para a máquina virtual atual.  |
| Métricas | Abra [o explorador de métricas](../platform/metrics-getting-started.md) com o escopo definido para a máquina virtual atual. |
| Configurações de Diagnóstico | Habilite e configure [a extensão de diagnóstico](../platform/diagnostics-extension-overview.md) para a máquina virtual atual. |
| Recomendações do Assistente | Recomendações para a máquina virtual atual do [Azure Advisor](/azure/advisor/). |
| Logs | Abra [o Log Analytics](../log-query/log-query-overview.md#what-is-log-analytics) com o [escopo](../log-query/scope.md) definido para a máquina virtual atual. |
| Monitor de conexão | [Monitor de conexão de observador de rede aberto](../../network-watcher/connection-monitor-preview.md) para monitorar conexões entre a máquina virtual atual e outras máquinas virtuais. |


## <a name="analyzing-metric-data"></a>Analisando dados métricos
Você pode analisar métricas para máquinas virtuais usando o metrics explorer abrindo **Métricas** do menu da máquina virtual. Consulte [Como começar com o Azure Metrics Explorer](../platform/metrics-getting-started.md) para obter detalhes sobre o uso desta ferramenta. 

Existem dois namespaces usados por máquinas virtuais para métricas:

| Namespace | Descrição |
|:---|:---|
| Host de Máquina Virtual | As métricas do host são coletadas automaticamente para todas as máquinas virtuais do Azure. Lista detalhada de métricas no [Microsoft.Compute/virtualMachines](../platform/metrics-supported.md#microsoftcomputevirtualmachines). |
| Convidado de máquina virtual | Métricas do sistema operacional de hóspedes coletadas de máquinas virtuais com extensão de diagnóstico instalada e configurada para enviar ao dissipador do Monitor Do Azure. |

![Métricas](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>Analisando dados de log
As máquinas virtuais do Azure coletarão os seguintes dados para o Azure Monitor Logs. 

O Monitor Azure para VMs permite a coleta de um conjunto predeterminado de contadores de desempenho que estão gravados na tabela *InsightsMetrics.* Esta é a mesma tabela usada pelo [Azure Monitor for Containers](container-insights-overview.md). 

| Fonte de dados | Requisitos | Tabelas |
|:---|:---|:---|
| Azure Monitor para VMs | Habilite em cada máquina virtual. | InsightsMetrics<br>Vmboundport<br>VMComputer<br>VMConnection<br>VMProcess<br>Consulte [Como consultar os registros do Azure Monitor para obter detalhes.](vminsights-log-search.md) |
| Log de atividades | Configuração de diagnóstico para a assinatura. | AzureActivity |
| Métricas do host | Configuração de diagnóstico para a máquina virtual. | AzureMetrics |
| Fontes de dados do sistema operacional convidado | Habilite o agente log analytics e configure fontes de dados. | Consulte a documentação de cada fonte de dados. |


> [!NOTE]
> Os dados de desempenho coletados pelo agente log analytics são escritos na tabela *Perf,* enquanto o Monitor Azure para VMs os coletará na tabela *InsightsMetrics.* São os mesmos dados, mas as tabelas têm uma estrutura diferente. Se você tiver consultas existentes com base no *Perf,* a vontade de ser reescrita será reescrita para usar *o InsightsMetrics*.


## <a name="alerts"></a>Alertas
[Alertas](../platform/alerts-overview.md) no Azure Monitor notificam-no proativamente quando condições importantes são encontradas em seus dados de monitoramento e potencialmente iniciam uma ação, como iniciar um App Logic ou chamar um webhook. As regras de alerta definem a lógica usada para determinar quando um alerta deve ser criado. O Azure Monitor coleta os dados usados pelas regras de alerta, mas você precisa criar regras para definir condições de alerta em sua assinatura do Azure.

As seções a seguir descrevem os tipos de regras de alerta e recomendações sobre quando você deve usar cada um. Esta recomendação é baseada na funcionalidade e custo do tipo de regra de alerta. Para obter detalhes sobre os preços dos alertas, consulte [os preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).


### <a name="activity-log-alert-rules"></a>Regras de alerta do log de atividades
[As regras de alerta de registro de atividade](../platform/alerts-activity-log.md) são acionadas quando uma entrada que corresponde a critérios específicos é criada no registro de atividades. Eles não têm custo, então eles devem ser sua primeira escolha se a lógica que você precisa está no registro de atividades. 

O recurso de destino para alertas de registro de atividades pode ser uma máquina virtual específica, todas as máquinas virtuais em um grupo de recursos ou todas as máquinas virtuais em uma assinatura.

Por exemplo, crie um alerta se uma máquina virtual crítica for interrompida selecionando a *Máquina Virtual power off* para o nome do sinal.

![Alerta do log de atividades](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>Regras de alerta de métrica
[As regras de alerta métrica](../platform/alerts-metric.md) saem quando um valor métrico excede um limite. Você pode definir um valor de limite específico ou permitir que o Azure Monitor determine dinamicamente um limite com base em dados históricos.  Use alertas métricos sempre que possível com dados métricos, pois eles custam menos e são mais responsivos do que as regras de alerta de log. Eles também são imponentes, o que significa que eles vão resolver-se quando a métrica cair abaixo do limiar.

O recurso de destino para alertas de registro de atividades pode ser uma máquina virtual específica ou todas as máquinas virtuais em um grupo de recursos.

Por exemplo, para criar um alerta quando o processador de uma máquina virtual exceder um valor específico, crie uma regra de alerta métrica usando *cpu porcentagem* como o tipo de sinal. Defina um valor de limite específico ou permita que o Azure Monitor defina um limite dinâmico. 

![Alerta métrico](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>Alertas de registro
[As regras de alerta](../platform/alerts-log.md) de log são acionadas quando os resultados de uma consulta de log agendada correspondem a determinados critérios. Os alertas de consulta de log são os mais caros e menos responsivos das regras de alerta, mas eles têm acesso aos dados mais diversos e podem executar lógicacomplexa que não pode ser executada pelas outras regras de alerta. 

O recurso de destino de uma consulta de log é um espaço de trabalho do Log Analytics. Filtrar para computadores específicos na consulta.

Por exemplo, para criar um alerta que verifique se alguma máquina virtual em um determinado grupo de recursos estiver offline, use a seguinte consulta que retorna um registro para cada computador que perdeu um batimento cardíaco nos últimos dez minutos. Use um limiar de 1 que é acionado se pelo menos um computador tiver um batimento cardíaco perdido.

```kusto
Heartbeat
| where TimeGenerated < ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![Alerta de log](media/monitor-vm-azure/log-alert-01.png)

Para criar um alerta se um número excessivo de logons com falha ocorreu em qualquer máquina virtual do Windows na assinatura, use a seguinte consulta que retorna um registro para cada evento de logon com falha na última hora. Use um limite definido para o número de logons com falha que você permitirá. 

```kusto
Event
| where TimeGenerated < ago(1hr)
| where EventID == 4625
```

![Alerta de log](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
O System Center Operations Manager (SCOM) fornece monitoramento granular de cargas de trabalho em máquinas virtuais. Consulte o [Guia de Monitoramento](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/monitor/) em Nuvem para comparação de plataformas de monitoramento e diferentes estratégias para implementação.

Se você tem um ambiente SCOM existente que você pretende continuar usando, você pode integrá-lo com o Azure Monitor para fornecer funcionalidade adicional. O agente log analytics usado pelo Azure Monitor é o mesmo usado para o SCOM para que você tenha monitorado máquinas virtuais enviar dados para ambos. Você ainda precisa adicionar o agente ao Monitor Azure para VMs e configurar o espaço de trabalho para coletar dados adicionais conforme especificado acima, mas as máquinas virtuais podem continuar a executar seus pacotes de gerenciamento existentes em um ambiente SCOM sem modificação.

Os recursos do Azure Monitor que aumentam os recursos existentes do SCOM incluem o seguinte:

- Use o Log Analytics para analisar interativamente seus dados de log e desempenho.
- Use alertas de log para definir condições de alerta em várias máquinas virtuais e usando tendências de longo prazo que não são possíveis usando alertas no SCOM.   

Consulte [O Connect Operations Manager no Azure Monitor](../platform/om-agents.md) para obter detalhes sobre como conectar seu grupo de gerenciamento SCOM existente ao espaço de trabalho do Log Analytics.


## <a name="next-steps"></a>Próximas etapas

* [Saiba como analisar dados nos registros do Azure Monitor usando consultas de log.](../log-query/get-started-queries.md)
* [Saiba mais sobre alertas usando métricas e logs no Azure Monitor.](../platform/alerts-overview.md)

