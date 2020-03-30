---
title: Diagnósticos de desempenho em Hiperescala
description: Este artigo descreve como solucionar problemas de desempenho de hiperescala no Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: seo-lt-2019
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: 26bd6ddb9d8255b8e2510133fc4b6aa645f89f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75615057"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>Diagnósticos de solução de problemas de desempenho da SQL Hyperscale

Para solucionar problemas de desempenho em um banco de dados Hyperscale, [as metodologias gerais de ajuste de desempenho](sql-database-monitor-tune-overview.md) no nó de computação do banco de dados Azure SQL são o ponto de partida de uma investigação de desempenho. No entanto, dada a [arquitetura distribuída](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) da Hyperscale, diagnósticos adicionais foram adicionados para ajudar. Este artigo descreve dados de diagnóstico específicos da Hyperscale.

## <a name="log-rate-throttling-waits"></a>Espera-se estrangulamento da taxa de log

Cada nível de serviço do Banco de Dados SQL do Azure tem limites de taxa de geração de log sustais aplicados via [controle de taxa de log](sql-database-resource-limits-database-server.md#transaction-log-rate-governance). Em Hyperscale, o limite de geração de log é atualmente definido como 100 MB/seg, independentemente do nível de serviço. No entanto, há momentos em que a taxa de geração de log na réplica de computação primária tem que ser estrangulada para manter os SLAs de capacidade de recuperação. Esse estrangulamento acontece quando um [servidor de página ou outra réplica de computação](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) está significativamente por trás da aplicação de novos registros de log do serviço Log.

Os seguintes tipos de espera (em [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)) descrevem as razões pelas quais a taxa de log pode ser estrangulada na réplica de computação primária:

|Tipo de Espera    |Descrição                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | Ocorre quando uma taxa de geração de log de log de nó de computação de banco de dados hyperscale está sendo estrangulada devido ao consumo de log atrasado no servidor de página.         |
|RBIO_RG_DESTAGE        | Ocorre quando uma taxa de geração de log de log de nó de banco de dados hyperscale está sendo estrangulada devido ao consumo de log atrasado pelo armazenamento de log de longo prazo.         |
|RBIO_RG_REPLICA        | Ocorre quando uma taxa de geração de log de log de nó de banco de dados Hyperscale está sendo estrangulada devido ao consumo de log atrasado pelas réplicas secundárias legíveis.         |
|RBIO_RG_LOCALDESTAGE   | Ocorre quando uma taxa de geração de log de log de nó de banco de dados Hyperscale está sendo estrangulada devido ao consumo de log atrasado pelo serviço log.         |

## <a name="page-server-reads"></a>Leituras do servidor de página

As réplicas de cálculo não armazenam uma cópia completa do banco de dados localmente. Os dados locais para a réplica de computação são armazenados no buffer pool (na memória) e no cache rbpex (RBPEX) local do Buffer Pool Extension (RBPEX) que é um cache parcial (não-coberto) de páginas de dados. Este cache RBPEX local é dimensionado proporcionalmente ao tamanho da computação e é três vezes a memória do nível de computação. O RBPEX é semelhante ao Buffer Pool, pois possui os dados mais acessados. Cada servidor de página, por outro lado, tem um cache RBPEX de cobertura para a parte do banco de dados que mantém.
 
Quando uma leitura é emitida em uma réplica de computação, se os dados não existirem no Buffer Pool ou no cache RBPEX local, uma chamada de função getPage (pageId, LSN) é emitida e a página é retirada do servidor de página correspondente. As leituras dos servidores de página são leituras remotas e, portanto, são mais lentas do que as leituras do RBPEX local. Ao solucionar problemas de desempenho relacionados ao IO, precisamos ser capazes de dizer quantos IOs foram feitos através de leituras de servidor de página remota relativamente mais lentas.

Vários DMVs e eventos estendidos têm colunas e campos que especificam o número de leituras remotas de um servidor de página, que podem ser comparados com as leituras totais. O armazenamento de consultas também captura leituras remotas como parte das estatísticas de tempo de execução da consulta.

- Colunas para reportar leituras de servidor de página estão disponíveis em DMVs de execução e visualizações de catálogo, tais como:
    - [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
    - [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
    - [sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
    - [sys.dm_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
    - [sys.query_store_runtime_stats](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql/)
- As leituras do servidor de página são adicionadas aos seguintes eventos estendidos:
    - sql_statement_completed
    - sp_statement_completed
    - sql_batch_completed
    - rpc_completed
    - scan_stopped
    - query_store_begin_persist_runtime_stat
    - store_execution_runtime_info de consulta
- ActualPageServerReads/ActualPageServerReadAheads são adicionados ao plano de consulta XML para planos reais. Por exemplo: 

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> Para visualizar esses atributos na janela de propriedades do plano de consulta, é necessário o SSMS 18.3 ou posterior.

## <a name="virtual-file-stats-and-io-accounting"></a>Estatísticas de arquivos virtuais e contabilidade de IO

No Banco de Dados SQL do Azure, o DMF [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) é a principal maneira de monitorar o SQL Server IO. As características de IO em Hyperscale são diferentes devido à sua [arquitetura distribuída.](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) Nesta seção, focamos em IO (leituras e gravações) para arquivos de dados como visto neste DMF. Em Hyperscale, cada arquivo de dados visível neste DMF corresponde a um servidor de página remota. O cache RBPEX mencionado aqui é um cache local baseado em SSD, que é um cache não coberto na réplica de computação.

### <a name="local-rbpex-cache-usage"></a>Uso local do cache RBPEX

O cache RBPEX local existe na réplica de computação, no armazenamento SSD local. Assim, o IO contra esse cache é mais rápido que o IO contra servidores de páginas remotas. Atualmente, [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) em um banco de dados Hyperscale tem uma linha especial relatando o IO contra o cache RBPEX local na réplica de computação. Esta linha tem o valor `database_id` `file_id` de 0 para ambas as colunas. Por exemplo, a consulta abaixo retorna as estatísticas de uso do RBPEX desde a inicialização do banco de dados.

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

Uma proporção de leituras feitas no RBPEX para leituras agregadas feitas em todos os outros arquivos de dados fornece a razão de sucesso de cache RBPEX.

### <a name="data-reads"></a>Os dados são lidos

- Quando as leituras são emitidas pelo mecanismo SQL Server em uma réplica de computação, elas podem ser servidas pelo cache RBPEX local, ou por servidores de páginas remotas ou por uma combinação das duas se lerem várias páginas.
- Quando a réplica de computação lê algumas páginas de um arquivo específico, por exemplo, file_id 1, se esses dados residirem exclusivamente no cache RBPEX local, todos os IO para esta leitura são contabilizados em file_id 0 (RBPEX). Se alguma parte desses dados estiver no cache RBPEX local, e alguma parte estiver em um servidor de página remota, então o IO é contabilizado para file_id 0 para a parte servida do RBPEX, e a parte servida a partir do servidor de página remota é contabilizada para file_id 1. 
- Quando uma réplica de computação solicita uma página em um [LSN](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/) específico de um servidor de página, se o servidor de página não tiver alcançado o LSN solicitado, a leitura na réplica de computação aguardará até que o servidor da página seja alcançado antes que a página seja devolvida à réplica de computação. Para qualquer leitura de um servidor de página na réplica de computação, você verá o tipo de espera PAGEIOLATCH_* se estiver esperando por esse IO. No Hyperscale, esse tempo de espera inclui tanto o tempo para atualizar a página solicitada no servidor de página para o LSN necessário, quanto o tempo necessário para transferir a página do servidor de página para a réplica de computação.
- Leituras grandes, como read-ahead, são muitas vezes feitas usando [leituras "Scatter-Gather".](/sql/relational-databases/reading-pages/) Isso permite leituras de até 4 MB de páginas por vez, consideradas uma única leitura no mecanismo SQL Server. No entanto, quando os dados que estão sendo lidos estão no RBPEX, essas leituras são contabilizadas como várias leituras individuais de 8 KB, uma vez que o buffer pool e o RBPEX sempre usam 8 páginas KB. Como resultado, o número de IOs de leitura vistos contra RBPEX pode ser maior do que o número real de IOs realizados pelo motor.

### <a name="data-writes"></a>Data writes

- A réplica de computação primária não é escrita diretamente para servidores de página. Em vez disso, os registros de log do serviço Log são reproduzidos em servidores de página correspondentes. 
- As gravações que acontecem na réplica de computação são predominantemente escritas para o RBPEX local (file_id 0). Para gravações em arquivos lógicos maiores que 8 KB, ou seja, aqueles feitos usando [Gather-write,](/sql/relational-databases/writing-pages/)cada operação de gravação é traduzida em várias gravações individuais de 8 KB para RBPEX, uma vez que o buffer pool e o RBPEX sempre usam páginas de 8 KB. Como resultado, o número de IOs de gravação vistos contra RBPEX pode ser maior do que o número real de IOs realizados pelo motor.
- Arquivos não-RBPEX, ou arquivos de dados diferentes de file_id 0 que correspondem a servidores de página, também mostram gravações. No nível de serviço Hyperscale, essas gravações são simuladas, porque as réplicas de computação nunca escrevem diretamente em servidores de página. Gravar IOPS e throughput são contabilizados como ocorrem na réplica de computação, mas a latência para arquivos de dados diferentes de file_id 0 não reflete a latência real das gravações do servidor de página.

### <a name="log-writes"></a>Gravações de log

- Na computação primária, uma gravação de registro é contabilizada em file_id 2 de sys.dm_io_virtual_file_stats. Uma gravação de log na computação primária é uma gravação para a zona de pouso de log.
- Os registros de registro não são endurecidos na réplica secundária em um commit. Em Hyperscale, o log é aplicado pelo serviço Log às réplicas secundárias assíncronamente. Como as gravações de log não ocorrem realmente em réplicas secundárias, qualquer contabilidade de IOs de Log nas réplicas secundárias é apenas para fins de rastreamento.

## <a name="data-io-in-resource-utilization-statistics"></a>IO de dados em estatísticas de utilização de recursos

Em um banco de dados não-Hyperscale, os pontos de leitura e gravação combinados de IOPS contra arquivos de dados, em relação `avg_data_io_percent` ao limite de dados de governança de [recursos](/azure/sql-database/sql-database-resource-limits-database-server#resource-governance) IOPS, são relatados em [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) e [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) visualizações, na coluna. O mesmo valor é relatado no portal como _Percentual de Dados IO_. 

Em um banco de dados Hyperscale, esta coluna relata a utilização de Dados IOPS em relação ao `tempdb`limite para armazenamento local apenas em réplica de computação, especificamente IO contra RBPEX e . Um valor de 100% nesta coluna indica que a governança de recursos está limitando o IOPS de armazenamento local. Se isso estiver correlacionado com um problema de desempenho, ajuste a carga de trabalho para gerar menos IO ou aumente o objetivo de serviço de banco de dados para aumentar o [limite](sql-database-vcore-resource-limits-single-databases.md) _de IOPS de_ governança de recursos . Para a governança de recursos do RBPEX lê e grava, o sistema conta iOs individuais de 8 KB, em vez de IOs maiores que podem ser emitidos pelo mecanismo SQL Server. 

O IO de dados contra servidores de páginas remotas não é relatado em visualizações de utilização de recursos ou no portal, mas é relatado no DMF [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) como observado anteriormente.


## <a name="additional-resources"></a>Recursos adicionais

- Para os limites de recursos vCore para um banco de dados único hyperscale, consulte [Limite de nível de serviço hyperscale vCore Limits](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5)
- Para ajuste de desempenho do Banco de Dados Azure SQL, consulte [o desempenho da consulta no Banco de Dados SQL do Azure](sql-database-performance-guidance.md)
- Para ajuste de desempenho usando a Loja de Consulta, consulte [Monitoramento de desempenho usando a loja de consulta](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/)
- Para scripts de monitoramento do Detran, consulte [Monitorando o desempenho do Banco de Dados Azure SQL usando visualizações dinâmicas de gerenciamento](sql-database-monitoring-with-dmvs.md)
