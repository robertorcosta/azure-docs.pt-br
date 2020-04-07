---
title: Monitore a carga de trabalho - Portal Azure
description: Monitore o Synapse SQL usando o portal Azure
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 0658a775e40c1fc433c7c2e1d853493544e74ee4
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743207"
---
# <a name="monitor-workload---azure-portal"></a>Monitore a carga de trabalho - Portal Azure

Este artigo descreve como usar o portal Azure para monitorar sua carga de trabalho. Isso inclui a configuração do Azure Monitor Logs para investigar as tendências de execução de consultas e carga de trabalho usando análises de log para [Synapse SQL](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure: Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Pool SQL: Estaremos coletando logs para um pool SQL. Se você não tiver um pool SQL provisionado, consulte as instruções em [Criar um pool SQL](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="create-a-log-analytics-workspace"></a>Criar um espaço de trabalho do Log Analytics

Navegue até a lâmina de navegação para espaços de trabalho do Log Analytics e crie um espaço de trabalho

![Workspaces do Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![Adicionar espaço de trabalho do Analytics](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![Adicionar espaço de trabalho do Analytics](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

Para obter mais detalhes sobre os espaços de trabalho, visite a [seguinte documentação](../../azure-monitor/learn/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace).

## <a name="turn-on-diagnostic-logs"></a>Ativar registros de diagnóstico

Configure as configurações de diagnóstico para emitir logs do pool SQL. Os logs consistem em visualizações de telemetria equivalentes aos DMVs de solução de problemas de desempenho mais usados. Atualmente, as seguintes visualizações são suportadas:

- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

![Habilitar logs de diagnóstico](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

Os logs podem ser emitidos para o Azure Storage, Stream Analytics ou Log Analytics. Para este tutorial, selecione Log Analytics.

![Especificar logs](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Executar consultas contra o Log Analytics

Navegue até o seu espaço de trabalho do Log Analytics, onde você pode fazer o seguinte:

- Analise logs usando consultas de log e salve consultas para reutilização
- Salvar consultas para reutilização
- Criar alertas de log
- Resultados da consulta de pinos para um painel

Para obter detalhes sobre os recursos das consultas de log, visite a [seguinte documentação](../../azure-monitor/log-query/query-language.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

![Editor de espaço de trabalho do Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![Consultas de espaço de trabalho do Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Consultas de registro de amostra

```Kusto
//List all queries
AzureDiagnostics
| where Category contains "ExecRequests"
| project TimeGenerated, StartTime_t, EndTime_t, Status_s, Command_s, ResourceClass_s, duration=datetime_diff('millisecond',EndTime_t, StartTime_t)
```

```Kusto
//Chart the most active resource classes
AzureDiagnostics
| where Category contains "ExecRequests"
| where Status_s == "Completed"
| summarize totalQueries = dcount(RequestId_s) by ResourceClass_s
| render barchart
```

```Kusto
//Count of all queued queries
AzureDiagnostics
| where Category contains "waits"
| where Type_s == "UserConcurrencyResourceType"
| summarize totalQueuedQueries = dcount(RequestId_s)
```

## <a name="next-steps"></a>Próximas etapas

Agora que você configurou e configurou os registros do monitor do Azure, [personalize os dashboards do Azure](../../azure-portal/azure-portal-dashboards.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) para compartilhar em sua equipe.
