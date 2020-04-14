---
title: Registros de consulta lenta - Banco de dados Azure para MySQL
description: Descreve os registros de consulta lenta disponíveis no Banco de Dados Azure para MySQL e os parâmetros disponíveis para habilitar diferentes níveis de registro.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: f834ba3355d362e59e2e44f37eca0560b9bf4d7a
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271974"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>Logs de consulta lenta saqueados no Banco de Dados Do Azure para MySQL
No Banco de Dados do Azure para MySQL, o log de consultas lentas está disponível para os usuários. No entanto, não há suporte para acesso ao log de transação. O log de consultas lentas pode ser usado para identificar gargalos de desempenho para solução de problemas.

Para obter mais informações sobre o log de consultas lentas do MySQL, consulte o manual de referência de MySQL [seção de log de consulta lenta](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

## <a name="configure-slow-query-logging"></a>Configure o registro de consulta lenta 
Por padrão, o log de consultas lentas está desabilitado. Para habilitá-lo, defina-o. `slow_query_log` Isso pode ser habilitado usando o portal Azure ou a Cli do Azure. 

Outros parâmetros que você pode ajustar incluem:

- **long_query_time**: se uma consulta demorar mais do que long_query_time (em segundos), essa consulta será registrada. O padrão é 10 segundos.
- **log_slow_admin_statements**: se ON inclui instruções administrativas como ALTER_TABLE e ANALYZE_TABLE nas instruções gravadas para o slow_query_log.
- **log_queries_not_using_indexes**: determina se as consultas que não usam índices são ou não registradas para o slow_query_log
- **log_throttle_queries_not_using_indexes**: este parâmetro limita o número de consultas que não são de índice que podem ser gravadas no log de consultas lentas. Esse parâmetro tem efeito quando log_queries_not_using_indexes está definido como ON.
- **log_output:** se "Arquivo", permite que o registro de consulta lenta seja gravado tanto no armazenamento do servidor local quanto nos logs de diagnóstico do Monitor do Azure. Se "Nenhum", o registro de consulta lenta será gravado apenas no Azure Monitor Diagnostics Logs. 

> [!IMPORTANT]
> Se suas tabelas não estiverem `log_queries_not_using_indexes` `log_throttle_queries_not_using_indexes` indexadas, a definição dos parâmetros e dos parâmetros para ON poderá afetar o desempenho do MySQL, uma vez que todas as consultas em execução contra essas tabelas não indexadas serão gravadas no registro de consulta lenta.<br><br>
> Se você planeja registrar consultas lentas por um longo período `log_output` de tempo, recomenda-se definir como "Nenhum". Se definido como "Arquivo", esses logs serão gravados no armazenamento do servidor local e podem afetar o desempenho do MySQL. 

Consulte a [documentação de log de consulta lenta](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) MySQL para descrições completas dos parâmetros de log de consultas lentas.

## <a name="access-slow-query-logs"></a>Acesse registros de consulta lenta
Existem duas opções para acessar logs de consulta lenta no Azure Database for MySQL: armazenamento de servidor local ou Logs de diagnóstico do Monitor do Azure. Isto é `log_output` definido usando o parâmetro.

Para armazenamento de servidor local, você pode listar e baixar registros de consulta lenta usando o portal Azure ou o Cli Do Zure. No portal Azure, navegue até o seu servidor no portal Azure. Sob o título **Monitoramento**, selecione a página **Logs do Servidor**. Para obter mais informações sobre o Azure CLI, consulte [Configurar e acessar registros de consulta lenta usando o Azure CLI](howto-configure-server-logs-in-cli.md). 

O Azure Monitor Diagnostic Logs permite que você canalize registros de consulta lentas para Log Analytics (Log Analytics), Azure Storage ou Event Hubs. Veja [abaixo](concepts-server-logs.md#diagnostic-logs) para mais informações.

## <a name="local-server-storage-log-retention"></a>Retenção de registro de armazenamento de servidor local
Ao fazer login no armazenamento local do servidor, os logs ficam disponíveis por até sete dias a partir de sua criação. Se o tamanho total dos logs disponíveis exceder 7 GB, os arquivos mais antigos serão excluídos até que haja espaço disponível.

Logs são reciclados a cada 24 horas ou 7 GB, o que ocorrer primeiro.

> [!Note]
> A retenção de log acima não se aplica a logs que são canalizados usando logs de diagnóstico do Monitor Azure. Você pode alterar o período de retenção dos sumidouros de dados que estão sendo emitidos para (ex. Armazenamento Azure).

## <a name="diagnostic-logs"></a>Logs de diagnóstico
O Banco de Dados do Azure para MySQL é integrado aos Logs de Diagnóstico do Monitor do Azure. Depois de habilitar logs de consulta lenta no seu servidor MySQL, você pode optar por emiti-los em registros do Monitor do Azure, hubs de eventos ou armazenamento azure. Para saber mais sobre como ativar logs de diagnóstico, consulte o como parte da [documentação registros de diagnóstico](../azure-monitor/platform/platform-logs-overview.md).

A tabela a seguir descreve o que está em cada log. Dependendo do método de saída, os campos incluídos e a ordem em que aparecem podem variar.

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua ID de locatário |
| `SourceSystem` | `Azure` |
| `TimeGenerated`[UTC] | Carimbo de data/hora quando o log foi gravado, em UTC |
| `Type` | Tipo do log. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID para a assinatura a que o servidor pertence |
| `ResourceGroup` | Nome do grupo de recursos ao qual o servidor pertence |
| `ResourceProvider` | Nome do provedor de recursos. Sempre `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de recurso |
| `Resource` | Nome do servidor |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Nome do servidor |
| `start_time_t`[UTC] | Horário em que a consulta começou |
| `query_time_s` | Tempo total em segundos a consulta levou para executar |
| `lock_time_s` | Tempo total em segundos a consulta foi bloqueada |
| `user_host_s` | Nome de Usuário |
| `rows_sent_s` | Número de linhas enviadas |
| `rows_examined_s` | Número de linhas verificadas |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | Inserir ID |
| `sql_text_s` | Consulta completa |
| `server_id_s` | ID do servidor |
| `thread_id_s` | ID do thread |
| `\_ResourceId` | URI de recurso |

> [!Note]
> Para `sql_text`, log será truncado se exceder 2048 caracteres.

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analisar logs no Azure Monitor Logs

Uma vez que seus registros de consulta lenta sejam canalizados para logs do Monitor do Azure através de Registros de Diagnóstico, você pode realizar uma análise adicional de suas consultas lentas. Abaixo estão algumas consultas de exemplo para ajudá-lo a começar. Certifique-se de atualizar o abaixo com o nome do servidor.

- Consultas com mais de 10 segundos em um servidor específico

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- Liste as 5 consultas mais longas em um servidor específico

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- Resumir consultas lentas pelo tempo mínimo, máximo, médio e de consulta de desvio padrão em um servidor específico

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- Gráfico a distribuição lenta de consulta em um servidor específico

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- Exibir consultas com mais de 10 segundos em todos os servidores MySQL com logs de diagnóstico ativados

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Próximas etapas
- [Como configurar registros de consulta lenta do portal Azure](howto-configure-server-logs-in-portal.md)
- [Como configurar registros de consulta lenta do Azure CLI](howto-configure-server-logs-in-cli.md).
