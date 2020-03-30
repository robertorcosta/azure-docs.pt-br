---
title: Fontes de dados no Azure Monitor | Microsoft Docs
description: Descreve os dados disponíveis para monitorar a integridade e desempenho de seus recursos do Azure e dos aplicativos executados neles.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/19/2019
ms.openlocfilehash: d1c0652844556b545cf0617032d21b80dd67d198
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479834"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Fontes de dados de monitoramento para o Azure Monitor
O Azure Monitor é baseado em uma [plataforma comum de dados de monitoramento](data-platform.md) que inclui [Logs](data-platform-logs.md) e [Metrics](data-platform-metrics.md). A coleta de dados nesta plataforma permite que dados de vários recursos sejam analisados em conjunto usando um conjunto comum de ferramentas no Azure Monitor. Os dados de monitoramento também podem ser enviados para outros locais para suportar determinados cenários, e alguns recursos podem ser enviados para outros locais antes que possam ser coletados em Logs ou Métricas.

Este artigo descreve as diferentes fontes de dados de monitoramento coletados pelo Azure Monitor, além dos dados de monitoramento criados pelos recursos do Azure. Os links são fornecidos para informações detalhadas sobre a configuração necessária para coletar esses dados em diferentes locais.

## <a name="application-tiers"></a>Camadas de aplicativo

Fontes de dados de monitoramento de aplicativos Do Zure podem ser organizadas em níveis, sendo os níveis mais altos o seu próprio aplicativo e os níveis inferiores sendo componentes da plataforma Azure. O método de acesso aos dados de cada camada varia. Os níveis de aplicação são resumidos na tabela abaixo, e as fontes de dados de monitoramento em cada camada são apresentadas nas seções a seguir. Consulte [os locais de monitoramento de dados no Azure](data-locations.md) para obter uma descrição de cada localização de dados e como você pode acessar seus dados.


![Níveis de monitoramento](../media/overview/overview.png)


### <a name="azure"></a>Azure
A tabela a seguir descreve brevemente os níveis de aplicação específicos do Azure. Siga o link para obter mais detalhes sobre cada um das seções abaixo.

| Camada | Descrição | Método de coleta |
|:---|:---|:---|
| [Inquilino Azure](#azure-tenant) | Dados sobre a operação de serviços do Azure no nível de locatário como Azure Active Directory. | Exibir dados AAD no portal ou configurar a coleta no Azure Monitor usando uma configuração de diagnóstico de inquilino. |
| [Assinatura do Azure](#azure-subscription) | Dados relacionados à saúde e gerenciamento de serviços de cross-resource em sua assinatura do Azure, como Gerenciador de Recursos e Saúde de Serviços. | Exibir no portal ou configurar a coleta no Azure Monitor usando um perfil de log. |
| [Recursos do Azure](#azure-resources) |  Dados sobre a operação e o desempenho de cada recurso do Azure. | Métricas coletadas automaticamente, visualização no Metrics Explorer.<br>Configure as configurações de diagnóstico para coletar logs no Monitor Azure.<br>Soluções de monitoramento e Insights disponíveis para monitoramento mais detalhado para tipos de recursos específicos. |

### <a name="azure-other-cloud-or-on-premises"></a>Azul, outra nuvem ou no local 
A tabela a seguir descreve brevemente os níveis de aplicativo que podem estar no Azure, em outra nuvem ou no local. Siga o link para obter mais detalhes sobre cada um das seções abaixo.

| Camada | Descrição | Método de coleta |
|:---|:---|:---|
| [Sistema operacional (convidado)](#operating-system-guest) | Dados sobre o sistema operacional sobre recursos computacionais. | Instale o agente Log Analytics para coletar fontes de dados do cliente no azure Monitor e agente de dependência para coletar dependências que suportam o Monitor Azure para VMs.<br>Para máquinas virtuais Azure, instale o Azure Diagnostic Extension para coletar logs e métricas no Azure Monitor. |
| [Código de aplicação](#application-code) | Dados sobre o desempenho e funcionalidade do aplicativo e código real, incluindo traços de desempenho, registros de aplicativos e telemetria do usuário. | Instrumentalifique seu código para coletar dados no Application Insights. |
| [Fontes personalizadas](#custom-sources) | Dados de serviços externos ou outros componentes ou dispositivos. | Colete dados de log ou métricas no Azure Monitor de qualquer cliente REST. |

## <a name="azure-tenant"></a>Locatário do Azure
A telemetria relacionada ao seu locatário do Azure é coletada de serviços de todos os locatários como o Azure Active Directory.

![Coleção de locatário do Azure](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Logs de Auditoria do Azure Active Directory
Os [relatórios do Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) contêm o histórico de atividade de entrada e a trilha de auditoria das alterações feitas em um locatário específico. 

| Destino | Descrição | Referência |
|:---|:---|:---|
| Logs do Azure Monitor | Configure os logs Ad do Azure a serem coletados no Azure Monitor para analisá-los com outros dados de monitoramento. | [Integrar logs do Azure AD com os logs do Azure Monitor (versão prévia)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Armazenamento do Azure | Exportar logs Azure AD para armazenamento Azure para arquivamento. | [Tutorial: Arquivar logs do Azure AD em uma conta de armazenamento do Azure (versão prévia)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Hub de evento | Stream Azure AD logs para outros locais usando Hubs de eventos. | [Tutorial: Stream Azure Active Directory logs em um hub de eventos Azure (visualização)](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md). |



## <a name="azure-subscription"></a>Assinatura do Azure
Telemetria relacionada à saúde e funcionamento da sua assinatura do Azure.

![Assinatura do Azure](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Log de Atividades do Azure 
O [registro de atividade do Azure](platform-logs-overview.md) inclui registros de saúde do serviço, juntamente com registros de quaisquer alterações de configuração feitas nos recursos da sua assinatura do Azure. O Log de Atividades está disponível para todos os recursos do Azure e representa a exibição _externa_ desses recursos.

| Destino | Descrição | Referência |
|:---|:---|
| Log de atividades | O registro Atividade é coletado em seu próprio armazenamento de dados que você pode visualizar no menu do Monitor do Azure ou usar para criar alertas de registro de atividade. | [Consultar o login de Atividade no portal do Azure](activity-log-view.md#azure-portal) |
| Logs do Azure Monitor | Configure os Logs do Monitor do Azure para coletar o registro de atividades para analisá-lo com outros dados de monitoramento. | [Coletar e analisar os logs de atividades do Azure no espaço de trabalho do Log Analytics no Azure Monitor](activity-log-collect.md) |
| Armazenamento do Azure | Exporte o registro de atividade supérde para arquivamento. | [Registro de atividade de arquivamento](resource-logs-collect-storage.md)  |
| Hubs de Eventos | Transmita o registro de atividades para outros locais usando hubs de eventos | [Stream Activity log to Event Hub](resource-logs-stream-event-hubs.md). |

### <a name="azure-service-health"></a>Integridade do Serviço do Azure
[A Integridade do Serviço do Azure](../../service-health/service-health-overview.md) fornece informações sobre a integridade dos serviços do Azure na assinatura dos quais o aplicativo e os recursos dependem.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Log de atividades<br>Logs do Azure Monitor | Os registros do Service Health são armazenados no registro de atividades do Azure, para que você possa visualizá-los no portal do Azure ou executar quaisquer outras atividades que você possa executar com o registro atividade. | [Exibir as notificações de integridade do serviço usando o portal do Azure](service-notifications.md) |


## <a name="azure-resources"></a>Recursos do Azure
Métricas e registros de recursos fornecem informações sobre a operação _interna_ dos recursos do Azure. Eles estão disponíveis para a maioria dos serviços do Azure, e soluções de monitoramento e insights coletam dados adicionais para serviços específicos.

![Coleção de recursos do Azure](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>Métricas da plataforma 
A maioria dos serviços do Azure enviará [métricas de plataforma](data-platform-metrics.md) que refletem seu desempenho e operação diretamente para o banco de dados de métricas. As [métricas específicas variam para cada tipo de recurso](metrics-supported.md). 

| Destino | Descrição | Referência |
|:---|:---|:---|
| Métricas do Monitor Azure | As métricas da plataforma serão escritas no banco de dados de métricas do Azure Monitor sem configuração. Acesse as métricas da plataforma do Metrics Explorer.  | [Introdução ao Azure Metrics Explorer](metrics-getting-started.md)<br>[Métricas compatíveis com o Azure Monitor](metrics-supported.md) |
| Logs do Azure Monitor | Copiar métricas da plataforma para Logs para tendências e outras análises usando o Log Analytics. | [Direcionar o diagnóstico do Azure para o Log Analytics](resource-logs-collect-workspace.md) |
| Hubs de Eventos | Transmitir métricas para outros locais usando hubs de eventos. |[Stream Azure monitorando dados para um hub de eventos para consumo por uma ferramenta externa](stream-monitoring-data-event-hubs.md) |

### <a name="resource-logs"></a>Logs de recursos
[Os registros de recursos](platform-logs-overview.md) fornecem insights sobre a operação _interna_ de um recurso do Azure.  Os logs de recursos são criados automaticamente, mas você deve criar uma configuração de diagnóstico para especificar um destino para que eles sejam coletados para cada recurso.

Os requisitos de configuração e o conteúdo dos registros de recursos variam de acordo com o tipo de recurso, e nem todos os serviços ainda os criam. Consulte [serviços, esquemas e categorias suportadas para registros de recursos do Azure](diagnostic-logs-schema.md) para obter detalhes sobre cada serviço e links para procedimentos de configuração detalhados. Se o serviço não estiver listado neste artigo, esse serviço não criará atualmente registros de recursos.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Logs do Azure Monitor | Envie registros de recursos para o Azure Monitor Logs para análise com outros dados de log coletados. | [Colete logins de recursos do Azure no espaço de trabalho do Log Analytics no Azure Monitor](resource-logs-collect-storage.md) |
| Armazenamento | Envie registros de recursos para o Azure Storage para arquivamento. | [Arquive os registros de recursos do Azure](resource-logs-collect-workspace.md) |
| Hubs de Eventos | Transmita registros de recursos para outros locais usando hubs de eventos. |[Stream Azure registra logs de recursos para um hub de eventos](resource-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>Sistema operacional (convidado)
Recursos de computação no Azure, em outras nuvens e localmente têm um sistema operacional convidado para monitorar. Com a instalação de um ou mais agentes, você pode reunir telemetria do hóspede no Azure Monitor para analisá-lo com as mesmas ferramentas de monitoramento que os próprios serviços do Azure.

![Coleta de recursos de computação do Azure](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Extensão de diagnóstico do Azure
Habilitar a extensão Azure Diagnostics para máquinas Virtuais Do Azure permite coletar logs e métricas do sistema operacional convidado dos recursos de computação do Azure, incluindo Funções Web e Operárias do Azure Cloud Service (clássica), Máquinas Virtuais, máquina virtual conjuntos de escala, e Malha de Serviço.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Armazenamento | A extensão de diagnóstico do Azure sempre é escrita em uma conta do Azure Storage. | [Instale e configure a extensão de diagnósticodo Windows Azure (WAD)](diagnostics-extension-windows-install.md)<br>[Use a Extensão de Diagnóstico Linux para monitorar as métricas e os logs](../../virtual-machines/extensions/diagnostics-linux.md) |
| Métricas do Monitor Azure | Quando você configura a Extensão de Diagnóstico para coletar contadores de desempenho, eles são gravados no banco de dados de métricas do Azure Monitor. | [Envie métricas do Sistema Operacional convidado para a loja métrica do Azure Monitor usando um modelo de Gerenciador de recursos para uma máquina virtual do Windows](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Hubs de Eventos | Configure a Extensão Diagnóstico para transmitir os dados para outros locais usando hubs de eventos.  | [Streaming de dados do Azure Diagnostics usando hubs de eventos](diagnostics-extension-stream-event-hubs.md)<br>[Use a Extensão de Diagnóstico Linux para monitorar as métricas e os logs](../../virtual-machines/extensions/diagnostics-linux.md) |
| Logs de insights de aplicativos | Colete logs e contadores de desempenho do recurso de computação que suporta seu aplicativo para ser analisado com outros dados do aplicativo. | [Enviar dados de diagnóstico do Serviço de Nuvem, da máquina virtual ou do Service Fabric ao Application Insights](diagnostics-extension-to-application-insights.md) |


### <a name="log-analytics-agent"></a>Agente do Log Analytics 
Instale o agente Log Analytics para monitoramento e gerenciamento abrangentes de suas máquinas virtuais Windows ou Linux. A máquina virtual pode estar em execução no Azure, em outra nuvem ou localmente.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Logs do Azure Monitor | O agente log analytics conecta-se ao Azure Monitor diretamente ou através do System Center Operations Manager e permite coletar dados de fontes de dados que você configura ou de soluções de monitoramento que fornecem insights adicionais sobre aplicativos funcionando na máquina virtual. | [Fontes de dados do agente no Azure Monitor](agent-data-sources.md)<br>[Conecte o gerente de operações ao Monitor Azure](om-agents.md) |
| Armazenamento em VM | O Azure Monitor for VMs usa o agente Log Analytics para armazenar informações sobre o estado de saúde em um local personalizado. Para obter mais informações, confira a próxima seção.  |


### <a name="azure-monitor-for-vms"></a>Azure Monitor para VMs 
[O Azure Monitor for VMs](../insights/vminsights-overview.md) oferece uma experiência de monitoramento personalizada para máquinas virtuais, fornecendo recursos além da funcionalidade principal do Azure Monitor. Ele requer um Agente de Dependência em máquinas virtuais Windows e Linux que se integre ao agente log analytics para coletar dados descobertos sobre processos em execução na máquina virtual e dependências de processos externos.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Logs do Azure Monitor | Armazena dados sobre processos e dependências do agente. | [Usando o Mapa do Monitor Do Azure para VMs (visualização) para entender os componentes do aplicativo](../insights/vminsights-maps.md) |



## <a name="application-code"></a>Código do aplicativo
O monitoramento detalhado de aplicativos no Azure Monitor é feito com [o Application Insights,](https://docs.microsoft.com/azure/application-insights/) que coleta dados de aplicativos em execução em uma variedade de plataformas. O aplicativo pode estar em execução no Azure, em outra nuvem ou localmente.

![Coleta de dados do aplicativo](media/data-sources/applications.png)


### <a name="application-data"></a>Dados do aplicativo
Quando você habilita o Application Insights para um aplicativo por meio da instalação de um pacote de instrumentação, ele coleta as métricas e logs relacionados ao desempenho e à operação do aplicativo. O Application Insights armazena os dados coletados na mesma plataforma de dados do Azure Monitor usada por outras fontes de dados. Ele inclui ferramentas extensas para analisar esses dados, mas você também pode analisá-los com dados de outras fontes usando ferramentas como Metrics Explorer e Log Analytics.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Logs do Azure Monitor | Dados operacionais sobre seu aplicativo, incluindo visualizações de página, solicitações de aplicativos, exceções e rastreamentos. | [Analisar dados de log no Azure Monitor](../log-query/log-query-overview.md) |
|                    | Informações de dependência entre componentes do aplicativo para suportar mapa de aplicativos e correlação de telemetria. | [Correlação de telemetria no Application Insights](../app/correlation.md) <br> [Mapa do aplicativo](../app/app-map.md) |
|            | Resultados de testes de disponibilidade que testam a disponibilidade e a capacidade de resposta de sua aplicação de diferentes locais na Internet pública. | [Monitorar a disponibilidade e a capacidade de resposta de qualquer site da Web](../app/monitor-web-app-availability.md) |
| Métricas do Monitor Azure | O Application Insights coleta métricas descrevendo o desempenho e o funcionamento do aplicativo, além de métricas personalizadas que você define em seu aplicativo no banco de dados de métricas do Azure Monitor. | [Métricas baseadas em log e pré-agregadas no Application Insights](../app/pre-aggregated-metrics-log-metrics.md)<br>[API do Application Insights para métricas e eventos personalizados](../app/api-custom-events-metrics.md) |
| Armazenamento do Azure | Envie dados do aplicativo para o Azure Storage para arquivamento. | [Exportar telemetria do Application Insights](../app/export-telemetry.md) |
|            | Os detalhes dos testes de disponibilidade são armazenados no Azure Storage. Use o Application Insights no portal Azure para baixar para análise local. Os resultados dos testes de disponibilidade são armazenados no Azure Monitor Logs. | [Monitorar a disponibilidade e a capacidade de resposta de qualquer site da Web](../app/monitor-web-app-availability.md) |
|            | Os dados de rastreamento do profiler são armazenados no Azure Storage. Use o Application Insights no portal Azure para baixar para análise local.  | [Aplicativos de produção de perfil no Azure com o Application Insights](../app/profiler-overview.md) 
|            | Os dados de snapshot de depuração capturados para um subconjunto de exceções são armazenados no Azure Storage. Use o Application Insights no portal Azure para baixar para análise local.  | [Como os instantâneos funcionam](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>Monitoramento de soluções e insights
[Soluções de monitoramento](../insights/solutions.md) e [Insights](../insights/insights-overview.md) coletam dados para fornecer insights adicionais sobre a operação de um determinado serviço ou aplicativo. Eles podem abordar recursos em diferentes níveis de aplicativos e até mesmo em vários níveis.

### <a name="monitoring-solutions"></a>Soluções de monitoramento

| Destino | Descrição | Referência
|:---|:---|:---|
| Logs do Azure Monitor | As soluções de monitoramento coletam dados nos registros do Azure Monitor, onde podem ser analisados usando a linguagem de consulta ou [visualizações](view-designer.md) que normalmente estão incluídas na solução. | [Detalhes da coleta de dados para soluções de monitoramento no Azure](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>Azure Monitor para contêineres
[O Azure Monitor para contêineres](../insights/container-insights-overview.md) oferece uma experiência de monitoramento personalizada para [o Azure Kubernetes Service (AKS)](/azure/aks/). Ele coleta dados adicionais sobre esses recursos descritos na tabela a seguir.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Logs do Azure Monitor | Armazena dados de monitoramento para AKS, incluindo inventário, registros e eventos. Os dados métricos também são armazenados em Logs, a fim de aproveitar sua funcionalidade de análise no portal. | [Compreender o desempenho de cluster do AKS com o Azure Monitor para contêineres](../insights/container-insights-analyze.md) |
| Métricas do Monitor Azure | Os dados métricos são armazenados no banco de dados métrico para impulsionar a visualização e os alertas. | [Exibir métricas de contêiner no explorador de métricas](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Serviço de Kubernetes do Azure | Fornece acesso direto aos registros de contêineres do Azure Kubernetes Service (AKS) (stdout/stderror), eventos e métricas de pod no portal. | [Como visualizar registros, eventos e métricas de pod do Kubernetes em tempo real](../insights/container-insights-livedata-overview.md) |

### <a name="azure-monitor-for-vms"></a>Azure Monitor para VMs
[O Monitor Azure para VMs](../insights/vminsights-overview.md) oferece uma experiência personalizada para monitorar máquinas virtuais. Uma descrição dos dados coletados pelo Azure Monitor para VMs está incluída na seção [Sistema Operacional (convidado)](#operating-system-guest) acima.

## <a name="custom-sources"></a>Fontes personalizadas
Além das camadas padrão de um aplicativo, você precisa monitorar outros recursos que têm a telemetria que não pode ser coletada com outras fontes de dados. Para esses recursos, escreva esses dados em Métricas ou Logs usando uma API do Monitor do Azure.

![Coleção personalizada](media/data-sources/custom.png)

| Destino | Método | Descrição | Referência |
|:---|:---|:---|:---|
| Logs do Azure Monitor | API do Coletor de Dados | Colete dados de log de qualquer cliente REST e armazene no espaço de trabalho do Log Analytics. | [Enviar dados de log para o Azure Monitor com a API do Coletor de Dados HTTP (visualização pública)](data-collector-api.md) |
| Métricas do Monitor Azure | API de métricas personalizadas | Coletar dados métricos de qualquer cliente REST e armazenar no banco de dados de métricas do Azure Monitor. | [Enviar métricas personalizadas de um recurso do Azure para o repositório de métricas do Azure Monitor usando uma API REST](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Outros serviços
Outros serviços no Azure escrevem dados para a plataforma de dados do Azure Monitor. Isso permite analisar os dados coletados por esses serviços com dados coletados pelo Azure Monitor e aproveitar as mesmas ferramentas de análise e visualização.

| Serviço | Destino | Descrição | Referência |
|:---|:---|:---|:---|
| [Centro de Segurança Azure](/azure/security-center/) | Logs do Azure Monitor | O Azure Security Center armazena os dados de segurança coletados em um espaço de trabalho do Log Analytics que permite que ele seja analisado com outros dados de log coletados pelo Azure Monitor.  | [Coleta de dados na Central de Segurança do Azure](../../security-center/security-center-enable-data-collection.md) |
| [Sentinela Azure](/azure/sentinel/) | Logs do Azure Monitor | O Azure Sentinel armazena os dados coletados de diferentes fontes de dados em um espaço de trabalho do Log Analytics, o que permite que ele seja analisado com outros dados de log coletados pelo Azure Monitor.  | [Conectar fontes de dados](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [tipos de dados de monitoramento coletados pelo Azure Monitor](data-platform.md) e como exibir e analisar esses dados.
- Liste os [diferentes locais onde os recursos do Azure armazenam dados](data-locations.md) e como você pode acessá-los. 
