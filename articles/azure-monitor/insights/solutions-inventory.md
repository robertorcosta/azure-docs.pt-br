---
title: Inventário de soluções de monitoramento no Azure | Microsoft Docs
description: As soluções de monitoramento no Azure Monitor são uma coleção de regras de lógica, visualização e aquisição de dados que fornecem métricas centradas em torno de uma área específica do problema.  Este artigo fornece uma lista de soluções de monitoramento disponíveis na Microsoft e detalhes sobre seu método e a frequência de coleta de dados.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/26/2018
ms.openlocfilehash: 7b88d957bce45bf518fc77584f1691de8010459a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77663122"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>Detalhes de inventário e coleta de dados para soluções de monitoramento no Azure
As [soluções de monitoramento](solutions.md) aproveitam os serviços no Azure para fornecer informações adicionais sobre a operação de um determinado aplicativo ou serviço. As soluções de monitoramento geralmente coletam dados de log e fornecem consultas e exibições para analisar os dados coletados. É possível adicionar soluções de monitoramento ao Azure Monitor para qualquer aplicativo e serviço usado. Normalmente, estão disponíveis gratuitamente, mas coletam dados que podem invocar encargos de uso.

Este artigo inclui uma lista de [soluções de montioring](solutions.md) disponíveis da Microsoft com links para sua documentação detalhada.  Ele também fornece informações sobre o método e frequência de coleta de dados no Azure Monitor.  Você pode usar as informações neste artigo para identificar as diferentes soluções disponíveis e para entender o fluxo de dados e os requisitos de conexão para diferentes soluções de monitoramento.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>Lista de soluções de monitoramento

A tabela a seguir lista as [soluções de monitoramento](solutions.md) no Azure fornecidas pela Microsoft. Uma entrada na coluna significa que a solução coleta dados no Azure Monitor usando esse método.  Se uma solução não tiver colunas selecionadas, ela gravará diretamente no Azure Monitor de outro serviço do Azure. Siga cada link para obter a documentação detalhada e mais informações.

As explicações das colunas são as seguintes:

- **Agente de monitoramento da Microsoft** -agente usado no Windows e Linux para executar o pacote de gerenciamento do SCOM e soluções de monitoramento do Azure. Nessa configuração, o agente é conectado diretamente ao Azure Monitor sem estar conectado a um grupo de gerenciamento do Operations Manager. 
- **Operations Manager** - Agente igual ao Microsoft Monitoring Agent. Nessa configuração, ele é [conectado a um grupo de gerenciamento do Operations Manager](../platform/om-agents.md) que está conectado ao Azure Monitor. 
-  **Armazenamento do Microsoft Azure** - A solução coleta dados de uma conta de armazenamento do Azure. 
- **O Operations Manager é necessário?** -Um grupo de gerenciamento de Operations Manager conectado é necessário para a coleta de dados pela solução de monitoramento. 
- **Dados do agente do Operations Manager enviados por meio do grupo de gerenciamento** – se o agente estiver [conectado a um grupo de gerenciamento do SCOM](../platform/om-agents.md), os dados serão enviados ao Azure Monitor do servidor de gerenciamento. Nesse caso, o agente não precisa se conectar diretamente ao Azure Monitor. Se essa caixa não estiver selecionada, os dados serão enviados diretamente do agente para o Azure Monitor, mesmo se o agente estiver conectado a um grupo de gerenciamento do SCOM. Ele precisará ser capaz de se comunicar com o Azure Monitor por meio do [gateway do Log Analytics](../platform/gateway.md).
- **Frequência de coleta** -especifica a frequência com que os dados são coletados pela solução de monitoramento. 



| **Solução de monitoramento** | **Plataforma** | **Microsoft Monitoring Agent** | **Agente do Operations Manager** | **Armazenamento do Azure** | **O Operations Manager é necessário?** | **Dados do agente do Operations Manager enviados por meio do grupo de gerenciamento** | **Frequência de coleta** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Análise do Log de Atividades](../platform/activity-log-collect.md) | Azure | | | | | | após a notificação |
| [Avaliação do AD](ad-assessment.md) |Portal |&#8226; |&#8226; | | |&#8226; |7 dias |
| [Status de replicação do AD](ad-replication-status.md) |Portal |&#8226; |&#8226; | | |&#8226; |5 dias |
| [Integridade do Agente](solution-agenthealth.md) | Windows e Linux | &#8226; | &#8226; | | | &#8226; | 1 minuto |
| [Gerenciamento de Alertas](../platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |na chegada |
| [Gerenciamento de Alertas](../platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 minuto |
| [Gerenciamento de Alertas](../platform/alert-management-solution.md) (Operations Manager) |Portal | |&#8226; | |&#8226; |&#8226; |3 minutos |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | n/d |
| [Conector do Application Insights (Preterido)](../platform/app-insights-connector.md) | Azure | | | |  |  | após a notificação |
| [Hybrid Worker de Automação](../../automation/automation-hybrid-runbook-worker.md) | Portal | &#8226; | &#8226; |  |  |  | n/d |
| [Análise de Gateway de Aplicativo do Azure](azure-networking-analytics.md) | Azure |  |  |  |  |  | após a notificação |
| **Solução de monitoramento** | **Plataforma** | **Microsoft Monitoring Agent** | **Agente do Operations Manager** | **Armazenamento do Azure** | **O Operations Manager é necessário?** | **Dados do agente do Operations Manager enviados por meio do grupo de gerenciamento** | **Frequência de coleta** |
| [Análise do Grupo de Segurança de Rede do Azure (preterido)](azure-networking-analytics.md) | Azure |  |  |  |  |  | após a notificação |
| [Azure SQL Analytics (Visualização)](azure-sql.md) | Portal | | | | | | 1 minuto |
| [Backup](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | após a notificação |
| [Capacidade e Desempenho (versão prévia)](capacity-performance.md) |Portal |&#8226; |&#8226; | | |&#8226; |na chegada |
| [Controle de alterações](../../automation/change-tracking.md) |Portal |&#8226; |&#8226; | | |&#8226; |[consoante](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Controle de alterações](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[consoante](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Contêineres](containers.md) | Windows e Linux | &#8226; | &#8226; |  |  |  | 3 minutos |
| [Análise do Cofre de Chaves](azure-key-vault.md) |Portal | | | | | |após a notificação |
| [Avaliação de malware](../../security-center/security-center-install-endpoint-protection.md) |Portal |&#8226; |&#8226; | | |&#8226; |por hora |
| [Monitor de Desempenho de Rede](network-performance-monitor.md) | Portal | &#8226; | &#8226; |  |  |  | Handshakes TCP a cada cinco segundos; dados enviados a cada três minutos |
| [Análise do Office 365 (versão prévia)](solution-office-365.md) |Portal | | | | | |após a notificação |
| **Solução de monitoramento** | **Plataforma** | **Microsoft Monitoring Agent** | **Agente do Operations Manager** | **Armazenamento do Azure** | **O Operations Manager é necessário?** | **Dados do agente do Operations Manager enviados por meio do grupo de gerenciamento** | **Frequência de coleta** |
| [Análise do Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Portal | | |&#8226; | | |5 minutos |
| [Mapa do Serviço](service-map.md) | Windows e Linux | &#8226; | &#8226; |  |  |  | 15 s |
| [Avaliação do SQL](sql-assessment.md) |Portal |&#8226; |&#8226; | | |&#8226; |7 dias |
| [SurfaceHub](surface-hubs.md) |Portal |&#8226; | | | | |na chegada |
| [Avaliação do System Center Operations Manager (versão prévia)](scom-assessment.md) | Portal | &#8226; | &#8226; |  |  | &#8226; | sete dias |
| [Gerenciamento de atualizações](../../automation/automation-update-management.md) | Portal |&#8226; |&#8226; | | |&#8226; |pelo menos, 2 vezes por dia e 15 minutos após a instalação de uma atualização |
| [Preparação para atualização](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Portal | &#8226; |  |  |  |  | 2 dias |
| [Monitoramento do VMware (preterido)](vmware.md) | Linux | &#8226; |  |  |  |  | 3 minutos |
| [Wire Data 2.0 (versão prévia)](wire-data.md) |Windows (2012 R2/8.1 ou posterior) |&#8226; |&#8226; | | | | 1 minuto |




## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}
* Saiba como [instalar e usar soluções de monitoramento](solutions.md).
* Saiba como [criar consultas](../log-query/log-query-overview.md) para analisar dados coletados por soluções de monitoramento.
