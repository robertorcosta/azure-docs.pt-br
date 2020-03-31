---
title: Monitoramento - Banco de dados Azure para MySQL
description: Este artigo descreve as métricas de monitoramento e alertas para o Banco de Dados do Azure para MySQL, incluindo CPU, armazenamento e estatísticas de conexão.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 2de04bbb1523151ac566b78bf99eba34c437fccd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537084"
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
Você pode ativar o registro lento de consulta e auditoria em seu servidor. Esses registros também estão disponíveis através do Azure Diagnostic Logs nos registros do Monitor do Azure, hubs de eventos e conta de armazenamento. Para saber mais sobre o registro, visite os [registros de auditoria](concepts-audit-logs.md) e os artigos de [logs de consulta lenta.](concepts-server-logs.md)

## <a name="query-store"></a>Repositório de Consultas
[O Query Store](concepts-query-store.md) é um recurso que mantém o controle do desempenho da consulta ao longo do tempo, incluindo estatísticas de tempo de execução de consulta e eventos de espera. O recurso persiste em consultar informações de desempenho em tempo de execução no esquema **mysql.** Você pode controlar a coleta e o armazenamento de dados por meio de vários botões de configuração.

## <a name="query-performance-insight"></a>Análise de Desempenho de Consultas
[Análise de Desempenho de Consultas](concepts-query-performance-insight.md) funciona em conjunto com o Repositório de Consultas para fornecer visualizações acessíveis do portal do Azure. Esses gráficos permitem que você identifique as principais consultas que afetam o desempenho. O Query Performance Insight está acessível na seção **Desempenho Inteligente** do banco de dados Do Azure para a página do portal do servidor MySQL.

## <a name="performance-recommendations"></a>Recomendações de desempenho
O recurso [Recomendações de Desempenho](concepts-performance-recommendations.md) identifica as oportunidades de melhorar o desempenho da carga de trabalho. As recomendações de desempenho fornecem recomendações para criar novos índices que tenham o potencial de melhorar o desempenho de suas cargas de trabalho. Para produzir recomendações de índice, o recurso leva em consideração várias características do banco de dados, inclusive seu esquema e a carga de trabalho, conforme relatado pelo Repositório de Consultas. Depois de implementar qualquer recomendação de desempenho, os clientes devem testar o desempenho para avaliar o impacto dessas alterações.

## <a name="planned-maintenance-notification"></a>Notificação de manutenção planejada

**Notificações de manutenção planejadas** permitem que você receba alertas para a próxima manutenção planejada no seu Banco de Dados Azure para MySQL. Essas notificações são integradas à manutenção planejada [do Service Health](../service-health/overview.md) e permitem que você visualize toda a manutenção programada para suas assinaturas em um só lugar. Também ajuda a dimensionar a notificação para os públicos certos para diferentes grupos de recursos, pois você pode ter contatos diferentes responsáveis por diferentes recursos. Você receberá a notificação sobre a próxima manutenção 72 horas antes do evento.

> [!Note]
> Faremos todas as tentativas de fornecer **notificação de manutenção planejada** de 72 horas para todos os eventos. No entanto, em casos de patches críticos ou de segurança, as notificações podem ser enviadas mais perto do evento ou serem omitidas.

### <a name="to-receive-planned-maintenance-notification"></a>Para receber notificação de manutenção planejada

1. No [portal](https://portal.azure.com), selecione **Integridade do Serviço**.
2. Na seção **Alertas**, selecione **Alertas de integridade**.
3. Selecione **+ Adicione alerta de saúde** do serviço e preencha os campos.
4. Preencha os campos necessários. 
5. Escolha o **tipo de evento,** selecione **Manutenção planejada** ou **Selecione tudo**
6. Em **Grupos de Ação** definem como você gostaria de receber o alerta (obter um e-mail, acionar um aplicativo lógico etc.)  
7. Certifique-se de que a regra Ativar após a criação está definida como Sim.
8. Selecione **Criar regra de alerta** para completar seu alerta

Para obter **etapas detalhadas**sobre como criar alertas de saúde do serviço, consulte [Criar alertas de registro de atividade sustais sobre notificações de serviços](../service-health/alerts-activity-log-service-notifications.md).

> [!IMPORTANT]
> Notificações de manutenção planejadas estão atualmente em pré-visualização

## <a name="next-steps"></a>Próximas etapas
- Consulte [Como configurar alertas](howto-alert-on-metric.md) para obter orientação sobre como criar um alerta em uma métrica.
- Para obter mais informações sobre como acessar e exportar métricas usando o Portal do Azure, a API REST ou a CLI, consulte a [Visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Leia nosso blog sobre [práticas recomendadas para monitorar seu servidor](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-mysql-monitoring/).
