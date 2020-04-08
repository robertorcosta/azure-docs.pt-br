---
title: Limites de recursos do Banco de Dados SQL do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral dos limites de recursos do Banco de Dados SQL do Azure para bancos de dados únicos e pools elásticos. Também fornece informações sobre o que acontece quando esses limites de recursos são atingidos ou excedidos.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 11/19/2019
ms.openlocfilehash: afb30a17d7a1450f169402c18f41ce249415e89d
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804819"
---
# <a name="sql-database-resource-limits-and-resource-governance"></a>Limites de recursos do Banco de Dados SQL e governança de recursos

Este artigo fornece uma visão geral dos limites de recursos do Banco de Dados SQL para um servidor do Banco de Dados SQL que gerencia bancos de dados individuais e pools elásticos. Ele fornece informações sobre o que acontece quando esses limites de recursos são atingidos ou excedidos, e descreve os mecanismos de governança de recursos usados para impor esses limites.

> [!NOTE]
> Para limites de Instância Gerenciada, consulte [Limites de recursos do Banco de Dados SQL para instâncias gerenciadas](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Limites máximos de recursos

| Recurso | Limite |
| :--- | :--- |
| Bancos de dados por servidor | 5.000 |
| Número padrão de servidores por assinatura por região | 20 |
| Número máximo padrão de servidores por assinatura por região | 200 |  
| DTU / cota de eDTU por servidor | 54.000 |  
| Cota de vCore por servidor/instância | 540 |
| Pools de máx por servidor | Limitado pelo número de DTUs ou vCores. Por exemplo, se cada pool tiver 1.000 DTUs, um servidor poderá dar suporte a 54 pools.|
|||

> [!IMPORTANT]
> Conforme o número de bancos de dados vai se aproximando do limite por servidor do Banco de Dados SQL, pode ocorrer o seguinte:
>
> - Aumento de latência nas consultas em execução no banco de dados mestre.  Isso inclui modos de exibição de estatísticas de utilização de recursos, como sys.resource_stats.
> - Aumento de latência nas operações de gerenciamento e pontos de vista do portais de renderização que envolvem a enumeração de bancos de dados no servidor.

> [!NOTE]
> Para obter mais cota DTU/eDTU, cota vCore ou mais servidores do que o valor padrão, envie uma nova solicitação de suporte no portal azure. Para obter mais informações, consulte [Solicitar aumentos de cotas para o Banco de Dados SQL do Azure](quota-increase-request.md).

### <a name="storage-size"></a>Tamanho de armazenamento

Para tamanhos de armazenamento de recursos de bancos de dados únicos, consulte [os limites de recurso baseados em DTU](sql-database-dtu-resource-limits-single-databases.md) ou os limites de recurso [baseados em vCore](sql-database-vcore-resource-limits-single-databases.md) para os limites de tamanho de armazenamento por nível de preço.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>O que acontece quando os limites de recursos do banco de dados são atingidos

### <a name="compute-dtus-and-edtus--vcores"></a>Computação (DTUs e eDTUs/ vCores)

Quando a utilização do cálculo do banco de dados (medida por DTUs e eDTUs, ou vCores) se torna alta, a latência de consultas aumenta e as consultas podem até mesmo tempo. Nessas condições, as consultas podem ser enfileiradas pelo serviço e são fornecidas recursos para execução à medida que os recursos se tornam gratuitos.
Ao encontrar uma utilização alta de computação, as opções de atenuação incluem:

- Aumentar o tamanho da computação do banco de dados ou do pool elástico a fim de fornecer ao banco de dados mais recursos de computação. Consulte [limites de recursos do banco de dados individual em escala](sql-database-single-database-scale.md) e [limites de recursos do pool elástico](sql-database-elastic-pool-scale.md).
- Otimização de consultas para reduzir a utilização de recursos de cada consulta. Para saber mais, consulte [Ajuste/Dicas de consulta](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Armazenamento

Quando o espaço usado do banco de dados atingir o limite de tamanho máximo, inserções e atualizações do banco de dados que aumentam o tamanho dos dados falharão, e os clientes receberão uma [mensagem de erro](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md). As instruções SELECT e DELETE continuam a ter sucesso.

Ao encontrar uma utilização alta de espaço, as opções de atenuação incluem:

- Aumentando o tamanho máximo do banco de dados ou do pool elástico, ou adicionando mais armazenamento. Consulte [limites de recursos do banco de dados individual em escala](sql-database-single-database-scale.md) e [limites de recursos do pool elástico](sql-database-elastic-pool-scale.md).
- Se o banco de dados estiver em um pool elástico, o banco de dados poderá ser movido para fora do pool, de modo que seu espaço de armazenamento não seja compartilhado com outros bancos de dados.
- Encolher um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [Gerenciar espaço no arquivo no banco de dados SQL do Azure](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Sessões e trabalhos (solicitações)

O número máximo de sessões e trabalhadores é determinado pelo nível de serviço e tamanho da computação (DTUs/eDTUs ou vCores). Novas solicitações serão rejeitadas quando os limites de sessão ou de trabalho forem atingidos, e os clientes receberão uma mensagem de erro. Embora o número de conexões disponíveis possa ser controlado pelo aplicativo, o número de trabalhos simultâneos costuma ser mais difícil de ser estimado e controlado. Isso é especialmente verdadeiro durante períodos de pico de carga quando os limites de recursos do banco de dados são atingidos e os trabalhadores se acumulam devido a consultas mais longas, grandes cadeias de bloqueio ou paralelismo de consulta excessiva.

Ao encontrar uma utilização alta de sessão ou trabalho, as opções de atenuação incluem:

- Aumentar a camada de serviço ou o tamanho da computação do banco de dados ou do pool elástico. Consulte [limites de recursos do banco de dados individual em escala](sql-database-single-database-scale.md) e [limites de recursos do pool elástico](sql-database-elastic-pool-scale.md).
- Otimizar consultas para reduzir a utilização de recursos de cada consulta se a causa do aumento da utilização de trabalho for devido à contenção de recursos de computação. Para saber mais, consulte [Ajuste/Dicas de consulta](sql-database-performance-guidance.md#query-tuning-and-hinting).
- Reduzindo a configuração [MAXDOP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) (grau máximo de paralelismo).
- Otimização da carga de trabalho de consulta para reduzir o número de ocorrências e a duração do bloqueio de consultas.

### <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>Consumo de recursos por cargas de trabalho do usuário e processos internos

O consumo de CPU e memória por cargas de trabalho do usuário em cada banco `avg_cpu_percent` `avg_memory_usage_percent` de dados é relatado nas [visualizações sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) e [sys.resource_stats,](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) in e colunas. Para piscinas elásticas, o consumo de recursos em nível de piscina é relatado na exibição [sys.elastic_pool_resource_stats.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) O consumo de CPU da `cpu_percent` carga de trabalho do usuário também é relatado através da métrica Do Monitor do Azure, para [bancos de dados únicos](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases) e [piscinas elásticas](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) no nível do pool.

O Banco de Dados SQL do Azure requer recursos computacionais para implementar recursos essenciais de serviço, como alta disponibilidade e recuperação de desastres, backup e restauração de banco de dados, monitoramento, Query Store, ajuste automático, etc. O sistema reserva uma certa parcela limitada dos recursos globais para esses processos internos usando mecanismos de governança de [recursos,](#resource-governance) disponibilizando o restante dos recursos para cargas de trabalho dos usuários. Às vezes, quando os processos internos não estão usando recursos computacionais, o sistema os disponibiliza para cargas de trabalho do usuário.

O consumo total de CPU e memória por cargas de trabalho do usuário e processos internos na instância do SQL Server hospedando um único banco de dados ou um pool elástico é relatado nas [visualizações sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) e [sys.resource_stats,](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) dentro `avg_instance_cpu_percent` e `avg_instance_memory_percent` colunas. Esses dados também são `sqlserver_process_core_percent` `sqlserver_process_memory_percent` relatados através das métricas do Monitor e do Azure, para [bancos de dados únicos](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases) e [piscinas elásticas](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) no nível da piscina.

Uma divisão mais detalhada do consumo de recursos recentes por meio de cargas de trabalho do usuário e processos internos é relatada nas [visualizações sys.dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) e [sys.dm_resource_governor_workload_groups_history_ex.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database) Para obter detalhes sobre pools de recursos e grupos de carga de trabalho referenciados nessas exibições, consulte [governança de recursos](#resource-governance). Essas visualizações relatam a utilização de recursos por cargas de trabalho do usuário e processos internos específicos nos pools de recursos e grupos de carga de trabalho associados.

No contexto de monitoramento de desempenho e solução de problemas, é importante considerar tanto o consumo`avg_instance_cpu_percent`de`sqlserver_process_core_percent`CPU **do usuário** `avg_cpu_percent`(), `cpu_percent`quanto o **consumo total de CPU** por cargas de trabalho do usuário e processos internos (, ).

**O consumo da CPU do usuário** é calculado como uma porcentagem dos limites de carga de trabalho do usuário em cada objetivo de serviço. **A utilização da CPU do usuário** em 100% indica que a carga de trabalho do usuário atingiu o limite do objetivo de serviço. No entanto, quando o **consumo total da CPU** atinge a faixa de 70-100%, é possível ver o throughput de carga de trabalho do usuário se achatando e a latência da consulta aumentando, mesmo que o consumo de **CPU** relatado permaneça significativamente abaixo de 100%. Isso é mais provável de ocorrer quando se usa objetivos de serviço menores com uma alocação moderada de recursos computacionais, mas cargas de trabalho relativamente intensas do usuário, como em [piscinas elásticas densas](sql-database-elastic-pool-resource-management.md). Isso também pode ocorrer com objetivos de serviço menores quando processos internos exigem temporariamente recursos adicionais, por exemplo, ao criar uma nova réplica do banco de dados.

Quando **o consumo total de CPU** é alto, as opções de mitigação são as mesmas observadas anteriormente e incluem o aumento do objetivo do serviço e/ou otimização da carga de trabalho do usuário.

## <a name="resource-governance"></a>Governança de recursos

Para impor limites de recursos, o Azure SQL Database usa uma implementação de governança de recursos baseada no SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor), modificada e estendida para executar um serviço de banco de dados SQL Server no Azure. Em cada instância do SQL Server no serviço, existem vários pools de recursos e [grupos de carga de trabalho,](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group)com limites de recursos [definidos](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool) em níveis de pool e grupo para fornecer um [banco de dados equilibrado como serviço](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/). A carga de trabalho do usuário e as cargas de trabalho internas são classificadas em pools de recursos separados e grupos de carga de trabalho. A carga de trabalho do usuário nas réplicas secundárias primárias `SloSharedPool1` e legíveis, incluindo geo-replicas, é classificada no pool de recursos e `UserPrimaryGroup.DBId[N]` no grupo de carga de trabalho, onde `N` se reserva o valor de ID do banco de dados. Além disso, existem vários pools de recursos e grupos de carga de trabalho para várias cargas de trabalho internas.

Além de usar o Resource Governor para governar recursos dentro do processo do SQL Server, o Azure SQL Database também usa o Windows [Job Objects](https://docs.microsoft.com/windows/win32/procthread/job-objects) para a governança de recursos de nível de processo e o [FSRM (Windows File Server Resource Manager, gerente de recursos do servidor de arquivos)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) para gerenciamento de cotas de armazenamento.

A governança de recursos do Banco de Dados Azure SQL tem natureza hierárquica. De cima para baixo, os limites são aplicados no nível do Sistema Operacional e no nível de volume de armazenamento usando mecanismos de governança de recursos do sistema operacional e Governador de Recursos, em seguida, no nível de pool de recursos usando O Governador de Recursos e, em seguida, no nível de grupo de carga de trabalho usando O Governador de Recursos. Os limites de governança de recursos em vigor para o banco de dados atual ou pool elástico são divulgados na visão [sys.dm_user_db_resource_governance.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 

### <a name="data-io-governance"></a>Governança de Data IO

A governança do Data IO é um processo no Banco de Dados SQL do Azure usado para limitar a leitura e a gravação de IO físico contra arquivos de dados de um banco de dados. Os limites do IOPS são definidos para cada nível de serviço para minimizar o efeito "vizinho barulhento", para fornecer equidade de alocação de recursos no serviço de vários locatários e para permanecer dentro dos recursos do hardware e armazenamento subjacentes.

Para bancos de dados únicos, os limites do grupo de carga de trabalho são aplicados a todos os IO de armazenamento `tempdb` contra o banco de dados, enquanto os limites do pool de recursos se aplicam a todos os IO de armazenamento contra todos os bancos de dados na mesma instância do SQL Server, incluindo o banco de dados. Para pools elásticos, os limites do grupo de carga de trabalho se aplicam a `tempdb` cada banco de dados no pool, enquanto o limite de pool de recursos se aplica a todo o pool elástico, incluindo o banco de dados, que é compartilhado entre todos os bancos de dados do pool. Em geral, os limites do pool de recursos podem não ser alcançáveis pela carga de trabalho contra um banco de dados (único ou agrupado), porque os limites do grupo de carga de trabalho são menores do que os limites do pool de recursos e limitam o IOPS/throughput mais cedo. No entanto, os limites de pool podem ser atingidos pela carga de trabalho combinada contra vários bancos de dados na mesma instância do SQL Server.

Por exemplo, se uma consulta gera 1000 IOPS sem qualquer governança de recursos IO, mas o limite máximo de IOPS do grupo de carga de trabalho é definido como 900 IOPS, a consulta não será capaz de gerar mais de 900 IOPS. No entanto, se o limite máximo de IOPS do pool de recursos for definido como 1500 IOPS, e o IO total de todos os grupos de carga de trabalho associados ao pool de recursos exceder 1500 IOPS, então o IO da mesma consulta pode ser reduzido abaixo do limite do grupo de trabalho de 900 IOPS.

Os valores iOPS e throughput min/max devolvidos pelo [sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) ato sys/caps atuam como limites/caps, não como garantias. Além disso, a governança de recursos não garante nenhuma latência específica de armazenamento. A melhor latência possível, O IOPS e o throughput para uma determinada carga de trabalho do usuário dependem não apenas dos limites de governança de recursos de IO, mas também da mistura de tamanhos de IO usados e das capacidades do armazenamento subjacente. O SQL Server usa IOs que variam de tamanho entre 512 KB e 4 MB. Para efeitos de iposição de limites iOPS, cada IO é contabilizado independentemente do seu tamanho, com exceção de bancos de dados com arquivos de dados no Azure Storage. Nesse caso, iOs maiores que 256 KB são contabilizados como múltiplos IOs de 256 KB, para se alinhar em relação à contabilidade iO de armazenamento do Azure.

Para os bancos de dados Basic, Standard e General Purpose, `primary_group_max_io` que usam arquivos de dados no Azure Storage, o valor pode não ser alcançável se um banco de dados não tiver arquivos de dados suficientes para fornecer cumulativamente esse número de IOPS, ou se os dados não forem distribuídos uniformemente entre arquivos ou se o nível de desempenho das blobs subjacentes limitar o IOPS/throughput abaixo do limite de governança de recursos. Da mesma forma, com pequenos IOs de `primary_max_log_rate` log gerados por transações freqüentes, o valor pode não ser alcançado por uma carga de trabalho devido ao limite de IOPS na bolha de armazenamento Azure subjacente.

Valores de `avg_data_io_percent` utilização `avg_log_write_percent`de recursos como e , relatados no [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database), e [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) pontos de vista, são calculados como percentuais dos limites máximos de governança de recursos. Portanto, quando fatores que não o limite de governança de recursos iOPS/throughput, é possível ver o IOPS/throughput achatando e as latências aumentando à medida que a carga de trabalho aumenta, embora a utilização de recursos relatada permaneça abaixo de 100%. 

Para ver ler e escrever IOPS, throughput e latência por arquivo de banco de dados, use a função [sys.dm_io_virtual_file_stats().](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) Essa função supera todos os IO do banco de `avg_data_io_percent`dados, incluindo io de fundo que não é contabilizado, mas usa IOPS e throughput do armazenamento subjacente, e pode impactar a latência de armazenamento observada. A função também apresenta latência adicional que pode ser introduzida pela governança `io_stall_queued_read_ms` de `io_stall_queued_write_ms` recursos iO para leituras e gravações, nas colunas e colunas, respectivamente.

### <a name="transaction-log-rate-governance"></a>Governança da taxa de registro de transações

O controle da taxa de registro de transações é um processo no Banco de Dados SQL do Azure usado para limitar altas taxas de ingestão para cargas de trabalho, como inserção em massa, SELECT INTO e compilações de índices. Esses limites são rastreados e aplicados no nível de subsito à taxa de geração de registros de registro, limitando o throughput independentemente de quantas IOs podem ser emitidas contra arquivos de dados.  As taxas de geração de log de transações atualmente são dimensionadas linearmente até um ponto que depende do hardware, com a taxa máxima de log permitida sendo de 96 MB/s com o modelo de compra vCore. 

> [!NOTE]
> Os IOs físicos reais para arquivos de registro de transações não são regidos ou limitados.

As taxas de log são definidas de forma que possam ser alcançadas e sustentadas em uma variedade de cenários, enquanto o sistema global pode manter sua funcionalidade com impacto minimizado na carga do usuário. O controle da taxa de log garante que os backups de log de transação permaneçam em SLAs de capacidade de recuperação publicadas.  Essa governança também evita um atraso excessivo nas réplicas secundárias.

À medida que os registros são gerados, cada operação é avaliada e avaliada para saber se deve ser adiada para manter uma taxa máxima de registro desejada (MB/s por segundo). Os atrasos não são adicionados quando os registros de registro são liberados para armazenamento, em vez disso, a governança da taxa de log é aplicada durante a própria geração de taxa de log.

As taxas reais de geração de log impostas no tempo de execução também podem ser influenciadas por mecanismos de feedback, reduzindo temporariamente as taxas de log permitidas para que o sistema possa se estabilizar. O gerenciamento de espaço de arquivos de registro, evitando correr para fora das condições de espaço de registro e mecanismos de replicação do Grupo de Disponibilidade pode diminuir temporariamente os limites gerais do sistema.

A modelagem do tráfego do governador da taxa de log é superficial através dos seguintes tipos de espera (expostos nas vistas [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) e [sys.dm_os_wait_stats):](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)

| Tipo de Espera | Observações |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Limitação de banco de dados |
| POOL_LOG_RATE_GOVERNOR | Limitação de piscina |
| INSTANCE_LOG_RATE_GOVERNOR | Limitação do nível de instância |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Controle de feedback, replicação física do grupo de disponibilidade no Premium/Business Critical não acompanhando |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Controle de feedback, taxas de limitação para evitar uma condição de espaço fora do registro |
| HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO | Controle de feedback de replicação geogeográfica, limitando a taxa de registro para evitar alta latência de dados e indisponibilidade de geosecundários|
|||

Ao encontrar um limite de taxa de log que esteja dificultando a escalabilidade desejada, considere as seguintes opções:
- Dimensione até um nível de serviço mais alto para obter a taxa máxima de registro de 96 MB/s ou mude para um nível de serviço diferente. O nível de serviço [Hyperscale](sql-database-service-tier-hyperscale.md) fornece taxa de log de 100 MB/s, independentemente do nível de serviço escolhido.
- Se os dados que estão sendo carregados forem transitórios, como a preparação de dados em um processo ETL, eles podem ser carregados em tempdb (que é minimamente registrado). 
- Para cenários analíticos, carregue em uma tabela coberta de columnstore agrupada. Isso reduz a taxa de registro necessária devido à compressão. Essa técnica aumenta a utilização da CPU e só é aplicável a conjuntos de dados que se beneficiam de índices de columnstore agrupados. 

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre limites gerais do Azure, confira [Assinatura do Azure e limites de serviço, cotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Para saber mais sobre DTUs e eDTUs, confira [DTUs e eDTUs](sql-database-purchase-models.md#dtu-based-purchasing-model).
- Para obter informações sobre limites de tamanho de tempdb, consulte [TempDB no Banco de Dados SQL do Azure](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
