---
title: Logs de consulta lentos-banco de dados do Azure para MySQL
description: Descreve os logs de consulta lento disponíveis no banco de dados do Azure para MySQL e os parâmetros disponíveis para habilitar diferentes níveis de log.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 9b661a7fa6a7b9f079a3b24d1b83f27118c4bd23
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75745859"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>Logs de consulta lentos no banco de dados do Azure para MySQL
No Banco de Dados do Azure para MySQL, o log de consultas lentas está disponível para os usuários. No entanto, não há suporte para acesso ao log de transação. O log de consultas lentas pode ser usado para identificar gargalos de desempenho para solução de problemas.

Para obter mais informações sobre o log de consultas lentas do MySQL, consulte o manual de referência de MySQL [seção de log de consulta lenta](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

## <a name="access-slow-query-logs"></a>Acessar logs de consulta lentos
Você pode listar e baixar logs de consulta lenta do banco de dados do Azure para MySQL usando o portal do Azure e o CLI do Azure.

No Portal do Azure, selecione o servidor do Banco de Dados do Azure para MySQL. Sob o título **Monitoramento**, selecione a página **Logs do Servidor**.

Para obter mais informações sobre CLI do Azure, consulte [configurar e acessar logs de consulta lentos usando CLI do Azure](howto-configure-server-logs-in-cli.md).

Da mesma forma, você pode canalizar os logs para Azure Monitor usando os logs de diagnóstico. Consulte [abaixo](concepts-server-logs.md#diagnostic-logs) para obter mais informações.

## <a name="log-retention"></a>Retenção de log
Logs estão disponíveis por até sete dias desde a criação deles. Se o tamanho total dos logs disponíveis exceder 7 GB, os arquivos mais antigos serão excluídos até que haja espaço disponível. 

Logs são reciclados a cada 24 horas ou 7 GB, o que ocorrer primeiro.

## <a name="configure-slow-query-logging"></a>Configurar o log de consultas lentas 
Por padrão, o log de consultas lentas está desabilitado. Para habilitá-lo, defina slow_query_log para ON.

Outros parâmetros que você pode ajustar incluem:

- **long_query_time**: se uma consulta demorar mais do que long_query_time (em segundos), essa consulta será registrada. O padrão é 10 segundos.
- **log_slow_admin_statements**: se ON inclui instruções administrativas como ALTER_TABLE e ANALYZE_TABLE nas instruções gravadas para o slow_query_log.
- **log_queries_not_using_indexes**: determina se as consultas que não usam índices são ou não registradas para o slow_query_log
- **log_throttle_queries_not_using_indexes**: este parâmetro limita o número de consultas que não são de índice que podem ser gravadas no log de consultas lentas. Esse parâmetro tem efeito quando log_queries_not_using_indexes está definido como ON.
- **log_output**: se "File", permite que o log de consultas lentas seja gravado no armazenamento do servidor local e em Azure monitor logs de diagnóstico. Se "None", o log de consultas lentas só será gravado em Azure Monitor logs de diagnóstico. 

> [!Note]
> Por `sql_text`, o log será truncado se exceder 2048 caracteres.

Consulte a [documentação de log de consulta lenta](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) MySQL para descrições completas dos parâmetros de log de consultas lentas.

## <a name="diagnostic-logs"></a>Logs de diagnóstico
O Banco de Dados do Azure para MySQL é integrado aos Logs de Diagnóstico do Monitor do Azure. Depois de habilitar os logs de consulta lentos em seu servidor MySQL, você pode optar por que eles sejam emitidos para Azure Monitor logs, hubs de eventos ou armazenamento do Azure. Para saber mais sobre como ativar logs de diagnóstico, consulte o como parte da [documentação registros de diagnóstico](../azure-monitor/platform/platform-logs-overview.md).

> [!IMPORTANT]
> Esse recurso de diagnóstico para logs de servidor só está disponível nos [tipos de preço](concepts-pricing-tiers.md)uso geral e com otimização de memória.

A tabela a seguir descreve o que está em cada log. Dependendo do método de saída, os campos incluídos e a ordem em que aparecem podem variar.

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua ID de locatário |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Carimbo de data/hora quando o log foi gravado, em UTC |
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
| `start_time_t` [UTC] | Horário em que a consulta começou |
| `query_time_s` | Tempo total em segundos que a consulta levou para ser executada |
| `lock_time_s` | Tempo total em segundos em que a consulta foi bloqueada |
| `user_host_s` | Nome de Usuário |
| `rows_sent_s` | Número de linhas enviadas |
| `rows_examined_s` | Número de linhas verificadas |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | Inserir ID |
| `sql_text_s` | Consulta completa |
| `server_id_s` | A ID do servidor |
| `thread_id_s` | ID do thread |
| `\_ResourceId` | URI de recurso |

## <a name="next-steps"></a>Próximas etapas
- [Como configurar e acessar logs de servidor por meio da CLI do Azure](howto-configure-server-logs-in-cli.md).
