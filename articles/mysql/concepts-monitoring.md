---
title: Monitoramento no Banco de Dados do Azure para MySQL
description: Este artigo descreve as métricas de monitoramento e alertas para o Banco de Dados do Azure para MySQL, incluindo CPU, armazenamento e estatísticas de conexão.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: df03f8ba0e522aacd305b6337e506f53e309660a
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384037"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Monitoramento no Banco de Dados do Azure para MySQL
Monitorar os dados dos seus servidores ajuda a solucionar problemas e otimizar sua carga de trabalho. O Banco de Dados do Azure para MySQL oferece várias métricas que fornecem insights sobre o comportamento do seu servidor.

## <a name="metrics"></a>Métricas
Todas as métricas do Azure têm uma frequência de um minuto e cada uma delas fornece 30 dias de histórico. É possível configurar alertas nas métricas. Para obter diretrizes passo a passo, consulte [How to set up alerts](howto-alert-on-metric.md) (Como configurar alertas). Outras tarefas incluem a configuração de ações automatizadas, execução de análises avançadas e arquivamento de histórico. Para obter mais informações, consulte a [Visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista de métricas
Essas métricas estão disponíveis para o Banco de Dados do Azure para MySQL:

|Métrica|Nome de exibição da métrica|Unidade|Descrição|
|---|---|---|---|
|cpu_percent|Porcentagem de CPU|Porcentagem|O percentual de CPU em uso.|
|memory_percent|Porcentagem de memória|Porcentagem|O percentual de memória em uso.|
|io_consumption_percent|Porcentagem de E/S|Porcentagem|O percentual de E/S em uso.|
|storage_percent|Porcentagem de armazenamento|Porcentagem|O percentual de armazenamento usado fora do máximo do servidor.|
|storage_used|Armazenamento usado|Bytes|A quantidade de armazenamento em uso. O armazenamento usado pelo serviço pode incluir os arquivos de banco de dados, os logs de transação e os logs do servidor.|
|serverlog_storage_percent|Porcentagem de armazenamento do Log do Servidor|Porcentagem|A porcentagem de armazenamento de log do servidor usada fora do armazenamento de log máximo do servidor.|
|serverlog_storage_usage|Armazenamento do Log do Servidor usado|Bytes|A quantidade de armazenamento de log do servidor em uso.|
|serverlog_storage_limit|Limite de armazenamento do Log do Servidor|Bytes|O armazenamento de log do servidor de máximo para esse servidor.|
|storage_limit|Limite de armazenamento|Bytes|O armazenamento máximo para esse servidor.|
|active_connections|Conexões ativas|Contagem|O número de conexões ativas com o servidor.|
|connections_failed|Conexões com falha|Contagem|O número de conexões com falha com o servidor.|
|seconds_behind_master|Retardo de replicação em segundos|Contagem|O número de segundos que o servidor de réplica está atrasando contra o servidor mestre.|
|network_bytes_egress|Saída da rede|Bytes|Rede-Out em conexões ativas.|
|network_bytes_ingress|Entrada na rede|Bytes|Entrada de rede em conexões ativas.|
|backup_storage_used|Backup do Microsoft Azure|Bytes|A quantidade de armazenamento de backup usado.|

## <a name="server-logs"></a>Logs do servidor
You can enable slow query and audit logging on your server. These logs are also available through Azure Diagnostic Logs in Azure Monitor logs, Event Hubs, and Storage Account. To learn more about logging, visit the [audit logs](concepts-audit-logs.md) and [slow query logs](concepts-server-logs.md) articles.

## <a name="query-store"></a>Repositório de Consultas
[Query Store](concepts-query-store.md) is a feature that keeps track of query performance over time including query runtime statistics and wait events. The feature persists query runtime performance information in the **mysql** schema. Você pode controlar a coleta e o armazenamento de dados por meio de vários botões de configuração.

## <a name="query-performance-insight"></a>Informações de desempenho de consulta
[Análise de Desempenho de Consultas](concepts-query-performance-insight.md) funciona em conjunto com o Repositório de Consultas para fornecer visualizações acessíveis do portal do Azure. Esses gráficos permitem que você identifique as principais consultas que afetam o desempenho. Query Performance Insight is accessible in the **Intelligent Performance** section of your Azure Database for MySQL server's portal page.

## <a name="performance-recommendations"></a>Recomendações de desempenho
O recurso [Recomendações de Desempenho](concepts-performance-recommendations.md) identifica as oportunidades de melhorar o desempenho da carga de trabalho. Performance Recommendations provides you with recommendations for creating new indexes that have the potential to improve the performance of your workloads. Para produzir recomendações de índice, o recurso leva em consideração várias características do banco de dados, inclusive seu esquema e a carga de trabalho, conforme relatado pelo Repositório de Consultas. Depois de implementar qualquer recomendação de desempenho, os clientes devem testar o desempenho para avaliar o impacto dessas alterações.

## <a name="service-health"></a>Integridade do serviço
[Azure Service health](../service-health/overview.md) provides a view of all service health notifications in your subscription. You can set up Service Health alerts to notify you via your preferred communication channels when there are issues or changes that may affect the Azure services and regions you use.

You can view scheduled maintenance events for Azure Database for MySQL by using the **planned maintenance** event type. To learn how to create **service health alerts**, visit the [Create activity log alerts on service notifications](../service-health/alerts-activity-log-service-notifications.md) article.

> [!IMPORTANT]
> The planned maintenance notifications is available in preview for EAST US and UK South only.

## <a name="next-steps"></a>Próximos passos
- Consulte [Como configurar alertas](howto-alert-on-metric.md) para obter orientação sobre como criar um alerta em uma métrica.
- Para obter mais informações sobre como acessar e exportar métricas usando o Portal do Azure, a API REST ou a CLI, consulte a [Visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Leia nosso blog sobre [práticas recomendadas para monitorar seu servidor](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-mysql-monitoring/).
