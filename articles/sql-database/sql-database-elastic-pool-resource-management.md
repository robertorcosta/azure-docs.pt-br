---
title: Gestão de recursos em piscinas elásticas densas | Microsoft Docs
description: Gerencie recursos de computação em pools elásticos Azure SQL com muitos bancos de dados.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: carlrab
ms.date: 03/13/2019
ms.openlocfilehash: 014f9edca1706c39930c6e48bb64cd8873bcace9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473720"
---
# <a name="resource-management-in-dense-elastic-pools"></a>Gerenciamento de recursos em pools elásticos densos

Os [grupos elásticos](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool) do Banco de Dados Azure SQL são uma solução econômica para gerenciar muitos bancos de dados com uso variável de recursos. Todos os bancos de dados em um pool elástico compartilham a mesma alocação de recursos, como CPU, memória, threads do trabalhador, espaço de armazenamento, tempdb, na suposição de que **apenas um subconjunto de bancos de dados no pool usará recursos computacionais a qualquer momento.** Essa suposição permite que as piscinas elásticas sejam econômicas. Em vez de pagar por todos os recursos que cada banco de dados individual poderia precisar, os clientes pagam por um conjunto muito menor de recursos, compartilhados entre todos os bancos de dados do pool.

## <a name="resource-governance"></a>Governança de recursos

O compartilhamento de recursos exige que o sistema controle cuidadosamente o uso de recursos para minimizar o efeito "vizinho barulhento", onde um banco de dados com alto consumo de recursos afeta outras bases de dados no mesmo pool elástico. Ao mesmo tempo, o sistema deve fornecer recursos suficientes para recursos como alta disponibilidade e recuperação de desastres (HADR), backup e restauração, monitoramento, Query Store, ajuste automático, etc. para funcionar de forma confiável.

O Banco de Dados SQL do Azure alcança esses objetivos usando vários mecanismos de governança de recursos, incluindo o Windows [Job Objects](https://docs.microsoft.com/windows/win32/procthread/job-objects) para a governança de recursos de nível de processo, o [FSRM (Windows File Server Resource Manager, gerente de recursos do servidor de arquivos)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) para gerenciamento de cotas de armazenamento e uma versão modificada e estendida do SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) para implementar a governança de recursos dentro de cada instância do SQL Server em execução no Banco de Dados SQL do Azure.

O objetivo principal das piscinas elásticas é ser econômico. Por essa razão, o sistema permite intencionalmente que os clientes criem piscinas _densas,_ ou seja, pools com o número de bancos de dados se aproximando ou no máximo permitido, mas com uma alocação moderada de recursos computacionais. Pela mesma razão, o sistema não reserva todos os recursos potencialmente necessários para seus processos internos, mas permite o compartilhamento de recursos entre processos internos e cargas de trabalho do usuário.

Essa abordagem permite que os clientes usem piscinas elásticas densas para obter desempenho adequado e grandes economias de custos. No entanto, se a carga de trabalho contra muitos bancos de dados em um pool denso for suficientemente intensa, a contenção de recursos se torna significativa. A contenção de recursos reduz o desempenho da carga de trabalho do usuário e pode impactar negativamente os processos internos.

> [!IMPORTANT]
> Em piscinas densas com muitos bancos de dados ativos, pode não ser viável aumentar o número de bancos de dados no pool até os máximos documentados para pools elásticos [DTU](sql-database-dtu-resource-limits-elastic-pools.md) e [vCore.](sql-database-vcore-resource-limits-elastic-pools.md)
> 
> O número de bancos de dados que podem ser colocados em pools densos sem causar problemas de contenção de recursos e desempenho depende do número de bancos de dados ativos simultaneamente e do consumo de recursos por cargas de trabalho do usuário em cada banco de dados. Esse número pode mudar com o tempo à medida que as cargas de trabalho do usuário mudam.

Quando a contenção de recursos ocorre em um pool densamente lotado, os clientes podem escolher uma ou mais das seguintes ações para atenuá-lo:
- Ajuste a carga de trabalho da consulta para reduzir o consumo de recursos ou espalhar o consumo de recursos em vários bancos de dados ao longo do tempo.
- Reduza a densidade do pool movendo alguns bancos de dados para outro pool ou tornando-os bancos de dados autônomos.
- Dimensione o pool para obter mais recursos.

Para obter sugestões sobre como implementar as duas [últimas](#operational-recommendations) ações, consulte recomendações operacionais mais tarde neste artigo. A redução da contenção de recursos beneficia tanto as cargas de trabalho do usuário quanto os processos internos, e permite que o sistema mantenha de forma confiável o nível de serviço esperado.

## <a name="monitoring-resource-consumption"></a>Monitoramento do consumo de recursos

Para evitar a degradação do desempenho devido à contenção de recursos, os clientes que usam pools elásticos densos devem monitorar proativamente o consumo de recursos e tomar medidas oportunas se o aumento da contenção de recursos começar a afetar as cargas de trabalho. O monitoramento contínuo é importante porque o uso de recursos em um pool muda ao longo do tempo, devido a mudanças na carga de trabalho do usuário, mudanças nos volumes e distribuição de dados, alterações na densidade do pool e alterações no serviço de banco de dados Do Azure SQL. 

O Banco de Dados SQL do Azure fornece várias métricas relevantes para este tipo de monitoramento. Exceder o valor médio recomendado para cada métrica indica disputa de recursos no pool, e deve ser abordado usando uma das ações mencionadas anteriormente.

|Nome da métrica|Descrição|Valor médio recomendado| 
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|Utilização da CPU do processo SQL Server associada a um pool elástico, medida pelo sistema operacional subjacente. Disponível no [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) view em cada banco de dados e `master` no [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) view no banco de dados. Essa métrica também é emitida para o Azure Monitor, onde é [nomeada](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `sqlserver_process_core_percent`, e pode ser visualizada no portal Azure. Este valor é o mesmo para cada banco de dados no mesmo pool elástico.|Abaixo de 70%. Picos curtos ocasionais de até 90% podem ser aceitáveis.|
|`max_worker_percent`|[Utilização da linha]( https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide) do trabalhador. Fornecido para cada banco de dados no pool, bem como para o pool em si. Existem diferentes limites no número de threads do trabalhador no nível do banco de dados e, no nível do pool, recomenda-se, portanto, monitorar essa métrica em ambos os níveis. Disponível no [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) view em cada banco de dados e `master` no [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) view no banco de dados. Essa métrica também é emitida para o Azure Monitor, onde é [nomeada](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `workers_percent`, e pode ser visualizada no portal Azure.|Abaixo de 80%. Picos de até 100% farão com que tentativas de conexão e consultas falhem.|
|`avg_data_io_percent`|Utilização do IOPS para ler e escrever IO físico. Fornecido para cada banco de dados no pool, bem como para o pool em si. Existem diferentes limites sobre o número de IOPS no nível do banco de dados e, no nível do pool, recomenda-se, portanto, monitorar essa métrica em ambos os níveis. Disponível no [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) view em cada banco de dados e `master` no [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) view no banco de dados. Essa métrica também é emitida para o Azure Monitor, onde é [nomeada](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `physical_data_read_percent`, e pode ser visualizada no portal Azure.|Abaixo de 80%. Picos curtos ocasionais até 100% podem ser aceitáveis.|
|`avg_log_write_percent`|Utilização de throughput para iO de gravação de registro de transações. Fornecido para cada banco de dados no pool, bem como para o pool em si. Existem diferentes limites no throughput de log no nível do banco de dados e, no nível do pool, recomenda-se, portanto, monitorar essa métrica em ambos os níveis. Disponível no [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) view em cada banco de dados e `master` no [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) view no banco de dados. Essa métrica também é emitida para o Azure Monitor, onde é [nomeada](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `log_write_percent`, e pode ser visualizada no portal Azure. Quando esta métrica estiver próxima de 100%, todas as modificações do banco de dados (INSERT, UPDATE, DELETE, Merge statements, SELECT ... EM, INSERÇÃO A GRANEL, etc.) será mais lento.|Abaixo de 90%. Picos curtos ocasionais até 100% podem ser aceitáveis.|
|`oom_per_second`|A taxa de erros fora da memória (OOM) em um pool elástico, que é um indicador de pressão de memória. Disponível na vista [sys.dm_resource_governor_resource_pools_history_ex.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database?view=azuresqldb-current) Consulte [Exemplos](#examples) para obter uma consulta de amostra para calcular essa métrica.|0|
|`avg_storage_percent`|Utilização do espaço de armazenamento no nível da piscina elástica. Disponível no [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) `master` no banco de dados. Essa métrica também é emitida para o Azure Monitor, onde é [nomeada](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `storage_percent`, e pode ser visualizada no portal Azure.|Abaixo de 80%. Pode se aproximar 100% para pools sem crescimento de dados.|
|`tempdb_log_used_percent`|Utilização do espaço `tempdb` de registro de transações no banco de dados. Embora objetos temporários criados em um banco de dados não `tempdb` sejam visíveis em outros bancos de dados no mesmo pool elástico, é um recurso compartilhado para todos os bancos de dados no mesmo pool. Uma transação longa `tempdb` ou ociosa iniciada a partir de um banco de dados no pool pode consumir uma grande parte do registro de transações e causar falhas em consultas em outros bancos de dados no mesmo pool. Disponível na vista [sys.dm_db_log_space_usage.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql) Essa métrica também é emitida para o Azure Monitor, e pode ser visualizada no portal Azure. Consulte [Exemplos](#examples) para uma consulta de exemplo para retornar o valor atual desta métrica.|Abaixo de 50%. Picos ocasionais de até 80% são aceitáveis.|
|||

Além dessas métricas, o Azure SQL Database fornece uma visão que retorna os limites reais de governança de recursos, bem como visualizações adicionais que retornam estatísticas de utilização de recursos no nível do pool de recursos e no nível do grupo de carga de trabalho.

|Nome da exibição|Descrição|  
|-----------------|--------------------------------|  
|[sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|Retorna configurações reais e configurações de capacidade usadas por mecanismos de governança de recursos no banco de dados atual ou pool elástico.|
|[sys.dm_resource_governor_resource_pools](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|Retorna informações sobre o estado atual do pool de recursos, a configuração atual de pools de recursos e estatísticas cumulativas do pool de recursos.|
|[sys.dm_resource_governor_workload_groups](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|Retorna estatísticas cumulativas do grupo de carga de trabalho e a configuração atual do grupo de carga de trabalho. Essa exibição pode ser acompanhada com sys.dm_resource_governor_resource_pools na coluna para obter informações sobre o `pool_id` pool de recursos.|
|[sys.dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)|Retorna as estatísticas de utilização do pool de recursos para os últimos 32 minutos. Cada linha representa um intervalo de 20 segundos. As `delta_` colunas retornam a mudança em cada estatística durante o intervalo.|
|[sys.dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)|Retorna as estatísticas de utilização do grupo de carga de trabalho para os últimos 32 minutos. Cada linha representa um intervalo de 20 segundos. As `delta_` colunas retornam a mudança em cada estatística durante o intervalo.|
|||

Essas visualizações podem ser usadas para monitorar a utilização de recursos e solucionar problemas de contenção de recursos em quase tempo real. A carga de trabalho do usuário nas réplicas secundárias primárias `SloSharedPool1` e legíveis, incluindo geo-replicas, é classificada no pool de recursos e `UserPrimaryGroup.DBId[N]` no grupo de carga de trabalho, onde `N` se reserva o valor de ID do banco de dados.

Além de monitorar a utilização atual de recursos, os clientes que usam pools densos podem manter dados históricos de utilização de recursos em um armazenamento de dados separado. Esses dados podem ser usados em análises preditivas para gerenciar proativamente a utilização de recursos com base em tendências históricas e sazonais.

## <a name="operational-recommendations"></a>Recomendações operacionais

**Deixe espaço suficiente para a cabeça de recursos**. Se ocorrer contenção de recursos e degradação de desempenho, a mitigação pode envolver a movimentação de alguns bancos de dados para fora do pool elástico afetado ou a ampliação do pool, como observado anteriormente. No entanto, essas ações exigem recursos adicionais de computação para serem concluídas. Em particular, para pools Premium e Business Critical, essas ações exigem a transferência de todos os dados para os bancos de dados que estão sendo movidos, ou para todos os bancos de dados no pool elástico se o pool for ampliado. A transferência de dados é uma operação de longa duração e com uso intensivo de recursos. Se o pool já estiver alta pressão de recursos, a operação mitigadora em si irá degradar ainda mais o desempenho. Em casos extremos, pode não ser possível resolver a contenção de recursos via movimento de banco de dados ou escala de pool porque os recursos necessários não estão disponíveis. Neste caso, reduzir temporariamente a carga de trabalho de consulta no pool elástico afetado pode ser a única solução.

Os clientes que usam pools densos devem monitorar de perto as tendências de utilização de recursos como descrito anteriormente e tomar medidas mitigadoras enquanto as métricas permanecem dentro das faixas recomendadas e ainda há recursos suficientes no pool elástico.

A utilização dos recursos depende de vários fatores que mudam ao longo do tempo para cada banco de dados e cada pool elástico. Alcançar uma relação ótimo de preço/desempenho em piscinas densas requer monitoramento e reequilíbrio contínuos, ou seja, mover bancos de dados de pools mais utilizados para pools menos utilizados e criar novos pools conforme necessário para acomodar o aumento da carga de trabalho.

**Não mova bancos de dados "quentes".** Se a contenção de recursos no nível do pool for causada principalmente por um pequeno número de bancos de dados altamente utilizados, pode ser tentador mover esses bancos de dados para um pool menos utilizado, ou torná-los bancos de dados autônomos. No entanto, fazer isso enquanto um banco de dados permanece altamente utilizado não é recomendado, pois a operação de movimentação irá degradar ainda mais o desempenho, tanto para o banco de dados que está sendo movido, quanto para todo o pool. Em vez disso, espere até que a alta utilização diminua ou mova bancos de dados menos utilizados em vez de aliviar a pressão de recursos no nível do pool. Mas mover bancos de dados com utilização muito baixa não proporciona nenhum benefício neste caso, porque não reduz materialmente a utilização de recursos no nível do pool.

**Crie novos bancos de dados em um pool de "quarentena".** Em cenários onde novas bases de dados são criadas com freqüência, como aplicativos usando o modelo de inquilino por banco de dados, há o risco de que um novo banco de dados colocado em um pool elástico existente consuma inesperadamente recursos significativos e afete outros bancos de dados e processos internos na piscina. Para mitigar esse risco, crie um pool separado de "quarentena" com ampla alocação de recursos. Use este pool para novos bancos de dados com padrões de consumo de recursos ainda desconhecidos. Uma vez que um banco de dados tenha ficado neste pool para um ciclo de negócios, como uma semana ou um mês, e seu consumo de recursos for conhecido, ele pode ser movido para um pool com capacidade suficiente para acomodar esse uso adicional de recursos.

**Evite servidores lógicos excessivamente densos.** O Banco de Dados SQL do Azure [suporta](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server) até 5000 bancos de dados por servidor lógico. Os clientes que usam piscinas elásticas com milhares de bancos de dados podem considerar colocar vários pools elásticos em um único servidor, com o número total de bancos de dados até o limite suportado. No entanto, servidores lógicos com milhares de bancos de dados criam desafios operacionais. As operações que requerem enumerar todos os bancos de dados em um servidor, por exemplo, visualizando bancos de dados no portal, serão mais lentas. Erros operacionais, como modificação incorreta de logins de nível de servidor ou regras de firewall, afetarão um número maior de bancos de dados. A exclusão acidental do servidor lógico exigirá assistência do Microsoft Support para recuperar bancos de dados no servidor excluído e causará uma paralisação prolongada para todos os bancos de dados afetados.

Recomendamos limitar o número de bancos de dados por servidor lógico a um número menor do que o máximo suportado. Em muitos cenários, usar até 1000-2000 bancos de dados por servidor é ótimo. Para reduzir a probabilidade de exclusão acidental do servidor, recomendamos colocar um [bloqueio de exclusão](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) no servidor lógico ou em seu grupo de recursos.

No passado, certos cenários envolvendo a movimentação de bancos de dados dentro, fora ou entre piscinas elásticas no mesmo servidor lógico eram mais rápidos do que ao mover bancos de dados entre servidores lógicos. Atualmente, todos os movimentos de banco de dados são executados na mesma velocidade, independentemente da fonte e do servidor lógico de destino.

## <a name="examples"></a>Exemplos

### <a name="monitoring-memory-utilization"></a>Monitorando a utilização da memória

Esta consulta calcula `oom_per_second` a métrica de cada pool de recursos, nos últimos 32 minutos. Esta consulta pode ser executada em qualquer banco de dados em um pool elástico.

```
SELECT pool_id,
       name AS resource_pool_name,
       IIF(name LIKE 'SloSharedPool%' OR name LIKE 'UserPool%', 'user', 'system') AS resource_pool_type,
       SUM(CAST(delta_out_of_memory_count AS decimal))/(SUM(duration_ms)/1000.) AS oom_per_second
FROM sys.dm_resource_governor_resource_pools_history_ex
GROUP BY pool_id, name
ORDER BY pool_id;
```
### <a name="monitoring-tempdb-log-space-utilization"></a>Monitoramento `tempdb` da utilização do espaço do registro

Esta consulta retorna o valor `tempdb_log_used_percent` atual da métrica. Esta consulta pode ser executada em qualquer banco de dados em um pool elástico.

```
SELECT used_log_space_in_percent AS tempdb_log_used_percent
FROM tempdb.sys.dm_db_log_space_usage;
```

## <a name="next-steps"></a>Próximas etapas

* Para uma introdução a piscinas elásticas, consulte [piscinas elásticas que ajudam você a gerenciar e dimensionar vários bancos de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool).
* Para obter informações sobre afinação de cargas de trabalho de consulta para reduzir a utilização de recursos, consulte [Monitoramento e ajuste,]( https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-tuning-index)monitoramento [e ajuste de desempenho](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview).
