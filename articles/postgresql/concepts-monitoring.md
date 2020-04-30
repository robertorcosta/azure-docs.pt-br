---
title: Monitorar e ajustar – Banco de Dados do Azure para PostgreSQL – Servidor único
description: Este artigo descreve os recursos de monitoramento e ajuste no banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 99c8d4e7e5263b8a7caf30833a899976d3cc829b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133700"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>Monitorar e ajustar o Banco de Dados do Azure para PostgreSQL – Servidor único
Monitorar os dados dos seus servidores ajuda a solucionar problemas e otimizar sua carga de trabalho. O Banco de Dados do Azure para PostgreSQL oferece várias opções de monitoramento para fornecer insights sobre o comportamento do servidor.

## <a name="metrics"></a>Métricas
O Banco de Dados do Azure para PostgreSQL oferece várias métricas que fornecem informações sobre o comportamento dos recursos compatíveis com o servidor PostgreSQL. Cada métrica é emitida em uma frequência de um minuto e tem até 30 dias de histórico. É possível configurar alertas nas métricas. Para obter diretrizes passo a passo, consulte [How to set up alerts](howto-alert-on-metric.md) (Como configurar alertas). Outras tarefas incluem a configuração de ações automatizadas, execução de análises avançadas e arquivamento de histórico. Para obter mais informações, consulte a [Visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista de métricas
Essas métricas estão disponíveis para o Banco de Dados do Azure para PostgreSQL:

|Métrica|Nome de exibição da métrica|Unidade|Descrição|
|---|---|---|---|
|cpu_percent|Porcentagem de CPU|Porcentagem|O percentual de CPU em uso.|
|memory_percent|Porcentagem de memória|Porcentagem|O percentual de memória em uso.|
|io_consumption_percent|Porcentagem de E/S|Porcentagem|O percentual de E/S em uso.|
|storage_percent|Porcentagem de armazenamento|Porcentagem|O percentual de armazenamento usado fora do máximo do servidor.|
|storage_used|Armazenamento usado|Bytes|A quantidade de armazenamento em uso. O armazenamento usado pelo serviço pode incluir os arquivos de banco de dados, os logs de transação e os logs do servidor.|
|storage_limit|Limite de armazenamento|Bytes|O armazenamento máximo para esse servidor.|
|serverlog_storage_percent|Porcentagem de armazenamento do Log do Servidor|Porcentagem|A porcentagem de armazenamento de log do servidor usada fora do armazenamento de log máximo do servidor.|
|serverlog_storage_usage|Armazenamento do Log do Servidor usado|Bytes|A quantidade de armazenamento de log do servidor em uso.|
|serverlog_storage_limit|Limite de armazenamento do Log do Servidor|Bytes|O armazenamento de log do servidor de máximo para esse servidor.|
|active_connections|Conexões ativas|Contagem|O número de conexões ativas com o servidor.|
|connections_failed|Conexões com falha|Contagem|O número de conexões com falha com o servidor.|
|network_bytes_egress|Saída da rede|Bytes|Rede-Out em conexões ativas.|
|network_bytes_ingress|Entrada na rede|Bytes|Entrada de rede em conexões ativas.|
|backup_storage_used|Backup do Microsoft Azure|Bytes|A quantidade de armazenamento de backup usado.|
|pg_replica_log_delay_in_bytes|Retardo Máximo entre Réplicas|Bytes|A latência em bytes entre o mestre e a réplica mais retardada. Essa métrica está disponível apenas no servidor mestre.|
|pg_replica_log_delay_in_seconds|Retardo da Réplica|Segundos|O tempo desde a última transação reproduzida. Essa métrica está disponível somente para servidores de réplica.|

## <a name="server-logs"></a>Logs do servidor
Você pode habilitar o registro no servidor. Esses logs de recursos podem ser enviados para [Azure monitor logs](../azure-monitor/log-query/log-query-overview.md), hubs de eventos e uma conta de armazenamento. Para saber mais sobre o registro em log, visite a página [logs de servidor](concepts-server-logs.md).

## <a name="query-store"></a>Repositório de Consultas
[Repositório de consultas](concepts-query-store.md) controla o desempenho da consulta ao longo do tempo, incluindo estatísticas de tempo de execução de consulta e eventos de espera. O recurso mantém as informações de desempenho de runtime de consulta em um banco de dados do sistema chamado **azure_sys** sob o esquema query_store. Você pode controlar a coleta e o armazenamento de dados por meio de vários botões de configuração.

## <a name="query-performance-insight"></a>Análise de Desempenho de Consultas
[Análise de Desempenho de Consultas](concepts-query-performance-insight.md) funciona em conjunto com o Repositório de Consultas para fornecer visualizações acessíveis do portal do Azure. Esses gráficos permitem que você identifique as principais consultas que afetam o desempenho. Análise de Desempenho de Consultas é acessível na seção **suporte + solução de problemas** da página do portal do banco de dados do Azure para o servidor PostgreSQL.

## <a name="performance-recommendations"></a>Recomendações de desempenho
O recurso [Recomendações de Desempenho](concepts-performance-recommendations.md) identifica as oportunidades de melhorar o desempenho da carga de trabalho. As recomendações de desempenho fornecem recomendações para a criação de novos índices que têm o potencial de melhorar o desempenho de suas cargas de trabalho. Para produzir recomendações de índice, o recurso leva em consideração várias características do banco de dados, inclusive seu esquema e a carga de trabalho, conforme relatado pelo Repositório de Consultas. Depois de implementar qualquer recomendação de desempenho, os clientes devem testar o desempenho para avaliar o impacto dessas alterações. 

## <a name="planned-maintenance-notification"></a>Notificação de manutenção planejada

As **notificações de manutenção planejada** permitem que você receba alertas para uma manutenção planejada futura para o banco de dados do Azure para PostgreSQL-servidor único. Essas notificações são integradas à manutenção planejada [da integridade do serviço](../service-health/overview.md) e permitem que você exiba toda a manutenção agendada para suas assinaturas em um único local. Ele também ajuda a dimensionar a notificação para os públicos certos para grupos de recursos diferentes, pois você pode ter contatos diferentes responsáveis por diferentes recursos. Você receberá a notificação sobre as próximas 72 horas de manutenção antes do evento.

> [!Note]
> Faremos todas as tentativas de fornecer **notificação de manutenção planejada** 72 horas de aviso para todos os eventos. No entanto, em casos de patches críticos ou de segurança, as notificações podem ser enviadas próximo ao evento ou ser omitidas.

### <a name="to-receive-planned-maintenance-notification"></a>Para receber a notificação de manutenção planejada

1. No [portal](https://portal.azure.com), selecione **Integridade do Serviço**.
2. Na seção **Alertas**, selecione **Alertas de integridade**.
3. Selecione **+ adicionar alerta de integridade do serviço** e preencha os campos.
4. Preencha os campos obrigatórios. 
5. Escolha o **tipo de evento**, selecione **manutenção planejada** ou **selecionar tudo**
6. Em **grupos de ação** , defina como você deseja receber o alerta (obter um email, disparar um aplicativo lógico etc.)  
7. Verifique se habilitar regra após a criação está definido como Sim.
8. Selecione **criar regra de alerta** para concluir o alerta

Para obter etapas detalhadas sobre como criar **alertas de integridade do serviço**, consulte [criar alertas do log de atividades em notificações de serviço](../service-health/alerts-activity-log-service-notifications.md).

> [!IMPORTANT]
> As notificações de manutenção planejada estão atualmente em versão prévia

## <a name="next-steps"></a>Próximas etapas
- Consulte [como configurar alertas](howto-alert-on-metric.md) para obter orientação sobre como criar um alerta em uma métrica.
- Para obter mais informações sobre como acessar e exportar métricas usando o Portal do Azure, a API REST ou a CLI, consulte a [Visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Leia nosso blog sobre [práticas recomendadas para monitorar seu servidor](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/).
