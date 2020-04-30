---
title: Inventário de soluções de monitoramento no Azure | Microsoft Docs
description: As soluções de monitoramento no Azure Monitor são uma coleção de regras de lógica, visualização e aquisição de dados que fornecem métricas centradas em torno de uma área específica do problema.  Este artigo fornece uma lista de soluções de monitoramento disponíveis na Microsoft e detalhes sobre seu método e a frequência de coleta de dados.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/26/2018
ms.openlocfilehash: b87179c79489bf781619b70b19ca8982f2e38dff
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509475"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>Detalhes de inventário e coleta de dados para soluções de monitoramento no Azure
As [soluções de monitoramento](solutions.md) usam serviços no Azure para obter conhecimento da operação de um determinado aplicativo ou serviço. As soluções de monitoramento geralmente coletam dados de log e fornecem consultas e exibições para analisar os dados coletados. É possível adicionar soluções de monitoramento ao Azure Monitor para qualquer aplicativo e serviço usado. Normalmente, eles estão disponíveis sem custos, mas coletam dados que podem invocar encargos de uso.

Este artigo inclui uma lista de [soluções de monitoramento](solutions.md) disponíveis na Microsoft com links para sua documentação detalhada.  Ele também fornece informações sobre o método e frequência de coleta de dados no Azure Monitor.  Você pode usar as informações neste artigo para identificar as diferentes soluções disponíveis e para entender o fluxo de dados e os requisitos de conexão para diferentes soluções de monitoramento.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>Lista de soluções de monitoramento

A tabela a seguir lista as [soluções de monitoramento](solutions.md) no Azure fornecidas pela Microsoft. Uma entrada na coluna significa que a solução coleta dados no Azure Monitor usando esse método.  Se uma solução não tiver colunas selecionadas, ela gravará diretamente no Azure Monitor de outro serviço do Azure. Siga cada link para obter a documentação detalhada e mais informações.

As explicações das colunas são as seguintes:

- **Agente de monitoramento da Microsoft** -agente usado no Windows e Linux para executar o pacote de gerenciamento do Microsoft System Center – Operations Manager (OM) e soluções de monitoramento do Azure. Nessa configuração, o agente é conectado diretamente ao Azure Monitor sem estar conectado a um grupo de gerenciamento do Operations Manager. 
- **Operations Manager** - Agente igual ao Microsoft Monitoring Agent. Nessa configuração, ele é [conectado a um grupo de gerenciamento do Operations Manager](../platform/om-agents.md) que está conectado ao Azure Monitor. 
-  **Armazenamento do Azure** -a solução coleta dados de uma conta de armazenamento do Azure. 
- **Operations Manager necessário?** -Um grupo de gerenciamento de Operations Manager conectado é necessário para a coleta de dados pela solução de monitoramento. 
- **Operations Manager dados do agente enviados por meio do grupo de gerenciamento** – se o agente estiver [conectado a um grupo de gerenciamento do OM](../platform/om-agents.md), os dados serão enviados para Azure monitor do servidor de gerenciamento. Nesse caso, o agente não precisa se conectar diretamente ao Azure Monitor. Se essa caixa não estiver selecionada, os dados serão enviados do agente diretamente para Azure Monitor mesmo que o agente esteja conectado a um grupo de gerenciamento do OM. Ele precisará ser capaz de se comunicar com o Azure Monitor por meio do [gateway do Log Analytics](../platform/gateway.md).
- **Frequência de coleta** -especifica a frequência com que os dados são coletados pela solução de monitoramento. 



| **Solução de monitoramento** | **Plataforma** | **Agente de monitoramento da Microsoft** | **Agente do Operations Manager** | **Armazenamento do Azure** | **Operations Manager necessário?** | **Dados do agente do Operations Manager enviados por meio do grupo de gerenciamento** | **Frequência de coleta** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Log Analytics de atividades](../platform/activity-log-collect.md) | Azure | | | | | | após a notificação |
| [Avaliação do AD](ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dias |
| [Status de replicação do AD](ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 dias |
| [Integridade do agente](solution-agenthealth.md) | Windows e Linux | &#8226; | &#8226; | | | &#8226; | 1 minuto |
| [Gerenciamento de Alertas](../platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |na chegada |
| [Gerenciamento de alertas](../platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 minuto |
| [Gerenciamento de Alertas](../platform/alert-management-solution.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 minutos |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | N/D |
| [Conector do Application Insights (Preterido)](../platform/app-insights-connector.md) | Azure | | | |  |  | após a notificação |
| [Hybrid Worker de AutomaçãoWorker](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | N/D |
| [Análise de Gateway de Aplicativo do Azure](azure-networking-analytics.md) | Azure |  |  |  |  |  | após a notificação |
| **Solução de monitoramento** | **Plataforma** | **Agente de monitoramento da Microsoft** | **Agente do Operations Manager** | **Armazenamento do Azure** | **Operations Manager necessário?** | **Dados do agente do Operations Manager enviados por meio do grupo de gerenciamento** | **Frequência de coleta** |
| [Análise do Grupo de Segurança de Rede do Azure (preterido)](azure-networking-analytics.md) | Azure |  |  |  |  |  | após a notificação |
| [Azure SQL Analytics (Visualização)](azure-sql.md) | Windows | | | | | | 1 minuto |
| [Backup](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | após a notificação |
| [Capacidade e Desempenho (versão prévia)](capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |na chegada |
| [Controle de alterações](../../automation/change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |[consoante](../../automation/change-tracking.md#change-tracking-and-inventory-data-collection) |
| [Controle de alterações](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[consoante](../../automation/change-tracking.md#change-tracking-and-inventory-data-collection) |
| [Contêineres](containers.md) | Windows e Linux | &#8226; | &#8226; |  |  |  | 3 minutos |
| [Análise do Cofre de Chaves](azure-key-vault.md) |Windows | | | | | |após a notificação |
| [Avaliação de malware](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |por hora |
| [Monitor de Desempenho de Rede](network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | Handshakes TCP a cada cinco segundos; dados enviados a cada três minutos |
| [Análise do Office 365 (visualização)](solution-office-365.md) |Windows | | | | | |após a notificação |
| **Solução de monitoramento** | **Plataforma** | **Agente de monitoramento da Microsoft** | **Agente do Operations Manager** | **Armazenamento do Azure** | **Operations Manager necessário?** | **Dados do agente do Operations Manager enviados por meio do grupo de gerenciamento** | **Frequência de coleta** |
| [Análise do Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 minutos |
| [Mapa do Serviço](service-map.md) | Windows e Linux | &#8226; | &#8226; |  |  |  | 15 s |
| [Avaliação do SQL](sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dias |
| [SurfaceHub](surface-hubs.md) |Windows |&#8226; | | | | |na chegada |
| [Avaliação do System Center Operations Manager (versão prévia)](scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | sete dias |
| [Gerenciamento de atualizações](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |pelo menos duas vezes por dia e 15 minutos após a instalação de uma atualização |
| [Upgrade Readiness](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 dias |
| [Monitoramento do VMware (preterido)](vmware.md) | Linux | &#8226; |  |  |  |  | 3 minutos |
| [Wire Data 2.0 (versão prévia)](wire-data.md) |Windows (2012 R2/8.1 ou posterior) |&#8226; |&#8226; | | | | 1 minuto |




## <a name="next-steps"></a>Próximas etapas
* Saiba como [instalar e usar soluções de monitoramento](solutions.md).
* Saiba como [criar consultas](../log-query/log-query-overview.md) para analisar dados coletados por soluções de monitoramento.
