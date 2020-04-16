---
title: O que é monitorado pelo Azure Monitor
description: Referência de todos os serviços e outros recursos monitorados pelo Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2020
ms.openlocfilehash: 27503defa23b2a35e767156139228aa2e177652f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405174"
---
# <a name="what-is-monitored-by-azure-monitor"></a>O que é monitorado pelo Azure Monitor?
Este artigo descreve os diferentes aplicativos e serviços que são monitorados pelo Azure Monitor. 

## <a name="insights-and-core-solutions"></a>Insights e soluções fundamentais
Os insights e soluções principais são considerados parte do Azure Monitor e seguem os contratos de suporte e nível de serviço para o Azure. Eles são suportados em todas as regiões do Azure onde o Azure Monitor está disponível.

### <a name="insights"></a>Insights

Os insights fornecem uma experiência de monitoramento personalizada para determinados aplicativos e serviços. Eles coletam e analisam tanto logs quanto métricas.

| Insight | Descrição |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Serviço APM (Extensible Application Performance Management, gerenciamento de desempenho de aplicativos extensíveis) para monitorar seu aplicativo web ao vivo em qualquer plataforma. |
| [Azure Monitor para Contêineres](insights/container-insights-overview.md) | Monitora o desempenho das cargas de trabalho de contêineres implantadas em instâncias de contêineres do Azure ou em clusters Kubernetes gerenciados hospedados no Azure Kubernetes Service (AKS). |
| [Monitor Azure para Cosmos DB (visualização)](insights/cosmosdb-insights-overview.md) | Fornece uma visão do desempenho geral, falhas, capacidade e saúde operacional de todos os seus recursos do Azure Cosmos DB em uma experiência interativa unificada. |
| [Monitor de Redes Do Azure (visualização)](insights/network-insights-overview.md) | Fornece uma visão abrangente da saúde e métricas para todos os recursos da sua rede. O recurso avançado de pesquisa ajuda a identificar dependências de recursos, permitindo cenários como identificar recursos que hospedam seu site, simplesmente pesquisando o nome do seu site. |
[Monitor do Azure para Grupos de Recursos (visualização)](insights/resource-group-insights.md) |  Trietrie e diagnostice quaisquer problemas que seus recursos individuais encontrem, ao mesmo tempo em que oferece contexto quanto à saúde e desempenho do grupo de recursos como um todo. |
| [Monitor do Azure para Armazenamento (visualização)](insights/storage-insights-overview.md) | Fornece um monitoramento abrangente de suas contas de armazenamento do Azure, fornecendo uma visão unificada do desempenho, capacidade e disponibilidade dos serviços de armazenamento do Azure. |
| [Monitor Azure para VMs](insights/container-insights-overview.md) | Monitora suas máquinas virtuais Azure (VM) e conjuntos de escala de máquinas virtuais em escala. Ele analisa o desempenho e a integridade das VMs do Windows e do Linux e monitora os processos e as dependências de outros recursos e processos externos. |

### <a name="core-solutions"></a>Soluções fundamentais

As soluções são baseadas em consultas de log e visualizações personalizadas para um determinado aplicativo ou serviço. Eles coletam e analisam apenas os registros e estão sendo preteridos ao longo do tempo em favor de insights.

| Solução | Descrição |
|:---|:---|
| [Agente de saúde](insights/solution-agenthealth.md) | Analise a saúde e configuração dos agentes do Log Analytics. |
| [Gerenciamento de alertas](platform/alert-management-solution.md) | Analise os alertas coletados do Gerente de Operações do Centro de Sistemas, Nagios ou Zabbix. |
| [Mapa de Serviço](insights/service-map.md) | Descobre automaticamente os componentes do aplicativo nos sistemas Windows e Linux e mapeia a comunicação entre os serviços. |



## <a name="azure-services"></a>Serviços do Azure
A tabela a seguir lista os serviços do Azure e os dados coletados no Azure Monitor. 

- Métricas - O serviço coleta automaticamente métricas no Azure Monitor Metrics. 
- Logs - O serviço suporta configurações de diagnóstico que podem coletar logs e métricas da plataforma para o Azure Monitor Logs.
- Insight - Há um insight disponível para o serviço que proporciona uma experiência de monitoramento personalizada para o serviço.

| Serviço | Métricas | Logs | Insight | Observações |
|:---|:---|:---|:---|:---|
|Active Directory | Não | Sim | [Sim](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | Não | Não | Não |  |
|Active Directory Domain Services | Não | Sim | Não |  |
|Log de atividades | Não | Sim | Não | |
|Proteção Avançada contra Ameaças | Não | Não | Não |  |
|Supervisor | Não | Não | Não |  |
|AI Builder | Não | Não | Não |  |
|Serviços de análise | Sim | Sim | Não |  |
|API para FHIR | Não | Não | Não |  |
|Gerenciamento de API | Sim | Sim | Não |  |
|Serviço de Aplicativo | Sim | Sim | Não |  |
|AppConfig | Não | Não | Não |  |
|Gateway de Aplicativo | Sim | Sim | Não |  |
|Serviço de Attestation | Não | Não | Não |  |
|Automação | Sim | Sim | Não |  |
|Gerente de Serviços Azure (RDFE) | Não | Não | Não |  |
|Backup | Não | Sim | Não |  |
|Bastion | Não | Não | Não |  |
|Lote | Sim | Sim | Não |  |
|Lote AI | Não | Não | Não |  |
|Blockchain Service | Não | Sim | Não |  |
|Blueprints | Não | Não | Não |  |
|Serviço de Bot | Não | Não | Não |  |
|Serviços de Nuvem | Sim | Sim | Não | Agente necessário para monitorar o sistema operacional convidado e fluxos de trabalho.  |
|Cloud Shell | Não | Não | Não |  |
|Serviços Cognitivos | Sim | Sim | Não |  |
|Instâncias de Contêiner | Sim | Não | Não |  |
|Registro de Contêiner | Sim | Sim | Não |  |
|CDN (Rede de Distribuição de Conteúdo) | Não | Sim | Não |  |
|Cosmos DB | Sim | Sim | [Sim](insights/cosmosdb-insights-overview.md) |  |
|Gerenciamento de Custos | Não | Não | Não |  |
|Data Box | Não | Não | Não |  |
|Catálogo de dados Gen2 | Não | Não | Não |  |
|Data Explorer | Sim | Sim | Não |  |
|Data Factory | Sim | Sim | Não |  |
|Fábrica de Dados v2 | Não | Sim | Não |  |
|Data Share | Não | Não | Não |  |
|Banco de Dados para MariaDB | Sim | Sim | Não |  |
|Banco de Dados para MySQL | Sim | Sim | Não |  |
|Banco de Dados para PostgreSQL | Sim | Sim | Não |  |
|Serviço de Migração de Banco de Dados | Não | Não | Não |  |
|Databricks | Não | Sim | Não |  |
|Proteção contra DDoS | Sim | Sim | Não |  |
|DevOps | Não | Não | Não |  |
|DNS | Sim | Não | Não |  |
|Nomes de domínio | Não | Não | Não |  |
|DPS | Não | Não | Não |  |
|Dinâmica 365 Engajamento do Cliente | Não | Não | Não |  |
|Dinâmica 365 Finanças e Operações | Não | Não | Não |  |
|Grade de Eventos | Sim | Não | Não |  |
|Hubs de Eventos | Sim | Sim | Não |  |
|ExpressRoute | Sim | Sim | Não |  |
|Firewall | Sim | Sim | Não |  |
|Front Door | Sim | Sim | Não |  |
|Funções | Sim | Sim | Não |  |
|HDInsight | Não | Sim | Não |  |
|HPC Cache | Não | Não | Não |  |
|Proteção de Informações | Não | Sim | Não |  |
|Intune | Não | Sim | Não |  |
|Central da IoT | Não | Não | Não |  |
|Hub IoT | Sim | Sim | Não |  |
|Key Vault | Sim | Sim | Não |  |
|AKS (Serviço do Kubernetes) | Não | Não | [Sim](insights/container-insights-overview.md)  |  |
|Load Balancer | Sim | Sim | Não |  |
|Aplicativos Lógicos | Sim | Sim | Não |  |
|Serviço do Machine Learning | Não | Não | Não |  |
|Aplicativos gerenciados  | Não | Não | Não |  |
|Mapas  | Não | Não | Não |  |
|Serviços de mídia | Sim | Sim | Não |  |
|Microsoft Flow | Não | Não | Não |  |
|Área de Trabalho Gerenciada da Microsoft | Não | Não | Não |  |
|Microsoft PowerApps | Não | Não | Não |  |
|Microsoft Social Engagement | Não | Não | Não |  |
|Microsoft Stream | Sim | Sim | Não |  |
|Migrar | Não | Não | Não |  |
|Autenticação Multifator | Não | Sim | Não |  |
|Observador de Rede | Sim | Sim | Não |  |
|Hubs de Notificação | Sim | Não | Não |  |
|Conjuntos de dados em aberto | Não | Não | Não |  |
|Política | Não | Não | Não |  |
|Power BI | Sim | Sim | Não |  |
|Power BI Embedded | Não | Não | Não |  |
|Link Privado | Não | Não | Não |  |
|Plataforma de comunicação do Projeto Spool | Não | Não | Não |  |
|Red Hat OpenShift | Não | Não | Não |  |
|Cache Redis | Sim | Sim | Não |  |
|Resource Graph | Não | Não | Não |  |
|Gerenciador de Recursos | Não | Não | Não |  |
|Pesquisa de Varejo – por Bing | Não | Não | Não |  |
|Search | Sim | Sim | Não |  |
|Barramento de Serviço | Sim | Sim | Não |  |
|Service Fabric | Não | Sim | Não | Agente necessário para monitorar o sistema operacional convidado e fluxos de trabalho.  |
|Portal de Inscrição | Não | Não | Não |  |
|Site Recovery | Não | Sim | Não |  |
|Serviço de Nuvem da Primavera | Não | Não | Não |  |
|SQL Data Warehouse | Sim | Sim | Não |  |
|Banco de Dados SQL | Sim | Sim | Não |  |
|SQL Server Stretch Database | Sim | Sim | Não |  |
|Pilha | Não | Não | Não |  |
|Armazenamento | Sim | Não | [Sim](insights/storage-insights-overview.md) |  |
|Cache de armazenamento | Não | Não | Não |  |
|Serviços de sincronização de armazenamento | Não | Não | Não |  |
|Stream Analytics | Sim | Sim | Não |  |
|Time Series Insights | Sim | Sim | Não |  |
|TINA | Não | Não | Não |  |
|Gerenciador de Tráfego | Sim | Sim | Não |  |
|Impressão Universal | Não | Não | Não |  |
|Conjuntos de Dimensionamento de Máquinas Virtuais | Não | Sim | [Sim](insights/vminsights-overview.md) | Agente necessário para monitorar o sistema operacional convidado e fluxos de trabalho. |
|Máquinas Virtuais | Sim | Sim | [Sim](insights/vminsights-overview.md) | Agente necessário para monitorar o sistema operacional convidado e fluxos de trabalho. |
|Rede Virtual | Sim | Sim | [Sim](insights/network-insights-overview.md) |  |
|Rede Virtual - Registros de fluxo nsg | Não | Sim | Não |  |
|Gateway de VPN | Sim | Sim | Não |  |
|Área de Trabalho Virtual do Windows | Não | Não | Não |  |


## <a name="product-integrations"></a>Integrações de produtos
Os serviços e soluções na tabela a seguir armazenam seus dados em um espaço de trabalho do Log Analytics para que possam ser analisados com outros dados de log coletados pelo Azure Monitor.

| Produto/Serviço | Descrição |
|:---|:---|
| [Automação do Azure](/azure/automation/) | Gerencie atualizações do sistema operacional e acompanhe as alterações nos computadores Windows e Linux. Consulte [O gerenciamento de rastreamento e](../automation/change-tracking.md) atualização de [alterações](../automation/automation-update-management.md). |
| [Proteção de informações do Azure](https://docs.microsoft.com/azure/information-protection/) | Classifique e proteja opcionalmente documentos e e-mails. Consulte [o relatório Central para proteção de informações do Azure](https://docs.microsoft.com/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports). |
| [Central de Segurança do Azure](/azure/security-center/) | Coletar e analisar eventos de segurança e realizar análises de ameaças. Ver [coleta de dados no Azure Security Center](/azure/security-center/security-center-enable-data-collection) |
| [Azure Sentinel](/azure/sentinel/) | Conecta-se a diferentes fontes, incluindo o Office 365 e o Amazon Web Services Cloud Trail. Consulte [Conectar fontes de dados](/azure/sentinel/connect-data-sources). |
| [Análise do Cofre de Chaves](insights/azure-key-vault.md) | Analise os logs de eventos do Azure Key VaultEvent. |
| [Microsoft Intune](https://docs.microsoft.com/intune/) | Crie uma configuração de diagnóstico para enviar logs para o Azure Monitor. Consulte [Enviar dados de log para armazenamento, hubs de eventos ou análises de log no Intune (visualização)](https://docs.microsoft.com/intune/fundamentals/review-logs-using-azure-monitor).  |
| Rede  | [Monitor de desempenho de rede](insights/network-performance-monitor.md) - Monitore a conectividade e o desempenho da rede para os pontos finais de serviço e aplicativos.<br>[Gateway de aplicativo Do Azure](insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) - Analise logs e métricas do Gateway de aplicativos do Azure.<br>[Traffic Analytics](/azure/network-watcher/traffic-analytics) - Analisa os registros de fluxo do Grupo de Segurança de Rede de Observadores de Rede (NSG) para fornecer insights sobre o fluxo de tráfego em sua nuvem Azure. |
| [Office 365](insights/solution-office-365.md) | Monitore o ambiente do Office 365. Versão atualizada com onboarding melhorado disponível através do Azure Sentinel. |
| [Análise de SQL](insights/azure-sql.md) | Monitore o desempenho de bancos de dados SQL do Azure, pools elásticos e instâncias gerenciadas em escala e em várias assinaturas. |
| [Hub de Superfície](insights/surface-hubs.md) | Acompanhe a saúde e o uso de dispositivos Surface Hub. |
| [System Center Operations Manager](https://docs.microsoft.com/system-center/scom) | Coletar dados dos agentes do Gerente de Operações conectando seu grupo de gerenciamento ao Azure Monitor. Consulte [O connect operations manager do Azure Monitor](platform/om-agents.md)<br> Avalie o risco e a saúde do grupo de gerenciamento do Gerente de Operações do Centro de Sistemas com a solução [de Avaliação do Gerente de Operações.](insights/scom-assessment.md) |
| [Salas de Equipes da Microsoft](https://docs.microsoft.com/microsoftteams/room-systems/azure-monitor-deploy) | Gerenciamento integrado e de ponta a ponta dos dispositivos Microsoft Teams Rooms. |
| [Visual Studio App Center](https://docs.microsoft.com/appcenter/) | Construa, teste e distribua aplicativos e monitore seu status e uso. Consulte [Comece a analisar seu aplicativo móvel com o App Center e o Application Insights](learn/mobile-center-quickstart.md). |
| Windows | [Conformidade com o Windows Update](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started) - Avalie as atualizações da área de trabalho do Windows.<br>[Desktop Analytics](https://docs.microsoft.com/configmgr/desktop-analytics/overview) - Integra-se ao Gerenciador de Configuração para fornecer insights e inteligência para tomar decisões mais informadas sobre a prontidão de atualização de seus clientes Windows. |



## <a name="other-solutions"></a>Outras soluções
Outras soluções estão disponíveis para monitorar diferentes aplicativos e serviços, mas o desenvolvimento ativo parou e eles podem não estar disponíveis em todas as regiões. Eles são cobertos pelo contrato de nível de nível de ingestão de dados do Azure Log Analytics.

| Solução | Descrição |
|:---|:---|
| [Verificação de saúde do Diretório Ativo](insights/ad-assessment.md) | Avalie o risco e a saúde de seus ambientes de Diretório Ativo. |
| [Status de replicação do Diretório Ativo](insights/ad-replication-status.md) | Monitora regularmente o ambiente do Active Directory para obter falhas de replicação. |
| [Análise de log de atividades](platform/activity-log-view.md#azure-portal) | Exibir entradas do Registro de Atividades. |
| [DNS Analytics (visualização)](insights/dns-analytics.md) | Coleta, analisa e correlaciona registros de análise e auditoria do Windows DNS e outros dados relacionados de seus servidores DNS. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Colete, visualize e analise as métricas de saúde e desempenho do sistema cloud foundry, em várias implantações. |
| [Contêineres](insights/containers.md) | Exibir e gerenciar hosts de contêineres Docker e Windows. |
| [Avaliações sob demanda](https://docs.microsoft.com/services-hub/health/getting_started_with_on_demand_assessments) | Avalie e otimize a disponibilidade, a segurança e o desempenho de seus ambientes de tecnologia Microsoft no local, híbridos e em nuvem. |
| [Verificação de saúde SQL](insights/sql-assessment.md) | Avalie o risco e a saúde de seus ambientes sql server.  |
| [Dados durante a transmissão](insights/wire-data.md) | Dados consolidados de rede e desempenho coletados de computadores conectados ao Windows e Linux com o agente Log Analytics. |

## <a name="third-party-integration"></a>Integração de terceiros

| Solução | Descrição |
|:---|:---|
| [ITSM](platform/itsmc-overview.md) | O ITSMC (Conector de Gerenciamento de Serviços de TI) permite que você conecte o Azure e um produto/serviço de ITSM (Gerenciamento de Serviços de TI) compatível.  |


## <a name="resources-outside-of-azure"></a>Recursos fora do Azure
O Azure Monitor pode coletar dados de recursos fora do Azure usando os métodos listados na tabela a seguir.

| Recurso | Método |
|:---|:---|
| Aplicativos | Monitore aplicativos web fora do Azure usando o Application Insights. Veja [o que é o Application Insights?](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). |
| Máquinas virtuais | Use o agente Log Analytics para coletar dados do sistema operacional convidado de máquinas virtuais em outros ambientes de nuvem ou no local. Consulte [Coletar dados de log com o agente Log Analytics](platform/log-analytics-agent.md). |
| Cliente de API REST | ApIs separadas estão disponíveis para gravar dados no Azure Monitor Logs and Metrics de qualquer cliente da API REST. Consulte [Enviar dados de log para o Azure Monitor com a API http de coletor de dados](platform/data-collector-api.md) para logs e enviar [métricas personalizadas para um recurso do Azure Para o armazenamento de métricas do Azure Monitor usando uma API REST](platform/metrics-store-custom-rest-api.md) for Metrics. |



## <a name="next-steps"></a>Próximas etapas

- Leia mais sobre a [plataforma de dados do Azure Monitor, que armazena os logs e métricas coletados por insights e soluções.](platform/data-platform.md)
- Complete um [tutorial sobre o monitoramento de um recurso do Azure](learn/tutorial-resource-logs.md).
- Complete um [tutorial sobre como escrever uma consulta de log para analisar dados no Azure Monitor Logs](learn/tutorial-resource-logs.md).
- Complete um [tutorial sobre a criação de um gráfico de métricas para analisar dados no Azure Monitor Metrics](learn/tutorial-metrics-explorer.md).

 
