---
title: Analisar sua carga de trabalho
description: Técnicas para analisar a priorização de consultas para sua carga de trabalho no Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: a382ef2d93f10e69569ecbbed1399f256a7afbb3
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351214"
---
# <a name="analyze-your-workload-in-azure-synapse-analytics"></a>Analise sua carga de trabalho no Azure Synapse Analytics

Técnicas para analisar sua carga de trabalho sql analytics no Azure Synapse Analytics.

## <a name="resource-classes"></a>Classes de recursos

O SQL Analytics fornece classes de recursos para atribuir recursos do sistema a consultas.  Para obter mais informações sobre classes de recursos, consulte [classes de recursos & gerenciamento de carga de trabalho](resource-classes-for-workload-management.md).  As consultas serão aguardadas se a classe de recursos atribuída a uma consulta precisar de mais recursos do que estão disponíveis atualmente.

## <a name="queued-query-detection-and-other-dmvs"></a>Detecção de consulta enfileirada e outros DMVs

Você pode usar o DMV `sys.dm_pdw_exec_requests` para identificar consultas que estão aguardando em uma fila de simultaneidade. As consultas que estão aguardando um slot de simultaneidade terão um status de **suspensas**.

```sql
SELECT  r.[request_id]                           AS Request_ID
,       r.[status]                               AS Request_Status
,       r.[submit_time]                          AS Request_SubmitTime
,       r.[start_time]                           AS Request_StartTime
,       DATEDIFF(ms,[submit_time],[start_time])  AS Request_InitiateDuration_ms
,       r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

As funções de gerenciamento de carga de trabalho podem ser exibidas com `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

A consulta a seguir mostra a qual função cada usuário está atribuído.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

O SQL Analytics tem os seguintes tipos de espera:

* **LocalQueriesConcurrencyResourceType**: consultas que ficam fora da estrutura de slot de simultaneidade. Consultas DMV e funções de sistema, como `SELECT @@VERSION` , são exemplos de consultas de locais.
* **UserConcurrencyResourceType**: consultas que ficam dentro da estrutura de slot de simultaneidade. Consultas em tabelas do usuário final representam exemplos que usariam esse tipo de recurso.
* **DmsConcurrencyResourceType**: esperas resultantes de operações de movimentação de dados.
* **BackupConcurrencyResourceType**: essa espera indica que está sendo feito backup de um banco de dados. O valor máximo para esse tipo de recurso é 1. Se vários backups forem solicitados ao mesmo tempo, os outros serão colocados em fila. Em geral, recomendamos um tempo mínimo entre instantâneos consecutivos de 10 minutos. 

O DMV `sys.dm_pdw_waits` pode ser usado para ver quais recursos uma solicitação está aguardando.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]                                           AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,       SESSION_ID()                                       AS Current_session
,       s.[status]                                         AS Session_status
,       s.[login_name]
,       s.[query_count]
,       s.[client_id]
,       s.[sql_spid]
,       r.[command]                                        AS Request_command
,       r.[label]
,       r.[status]                                         AS Request_status
,       r.[submit_time]
,       r.[start_time]
,       r.[end_compile_time]
,       r.[end_time]
,       DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,       DATEDIFF(ms,r.[start_time],r.[end_compile_time])   AS Request_compile_time_ms
,       DATEDIFF(ms,r.[end_compile_time],r.[end_time])     AS Request_execution_time_ms
,       r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID()
;
```

O `sys.dm_pdw_resource_waits` Detran mostra as informações de espera para uma determinada consulta. O tempo de espera dos recursos mede o tempo de espera para que os recursos sejam fornecidos. Tempo de espera de sinal é o tempo necessário para que os servidores SQL subjacentes programem a consulta para a CPU.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID()
;
```

Você também pode usar o `sys.dm_pdw_resource_waits` DMV calcular quantos slots de simultaneidade foram concedidos.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots
FROM    sys.[dm_pdw_resource_waits]
WHERE   [state]           = 'Granted'
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

O DMV `sys.dm_pdw_wait_stats` pode ser usado para análise de tendências históricas de espera.

```sql
SELECT   w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w
;
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o gerenciamento de usuários de banco de dados e segurança, consulte [Proteger um banco de dados no SQL Analytics](sql-data-warehouse-overview-manage-security.md). Para obter mais informações sobre como classes de recurso maiores podem melhorar a qualidade do índice columnstore clusterizado, consulte [Recriando índices para melhorar a qualidade de segmento](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).
