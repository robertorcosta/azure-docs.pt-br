---
title: Monitorar suas implantações do SQL com o SQL insights (versão prévia)
description: Visão geral do SQL insights no Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: b5add466a60bc855e08917d02fecaf60a35deeb1
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889562"
---
# <a name="monitor-your-sql-deployments-with-sql-insights-preview"></a>Monitorar suas implantações do SQL com o SQL insights (versão prévia)
O SQL insights monitora o desempenho e a integridade de suas implantações do SQL.  Ele pode ajudar a fornecer desempenho previsível e disponibilidade de cargas de trabalho vitais que você criou em um back-end do SQL identificando gargalos e problemas de desempenho. O SQL insights armazena seus dados em [logs de Azure monitor](../logs/data-platform-logs.md), o que permite que ele forneça agregação e filtragem poderosas e analise as tendências de dados ao longo do tempo. Você pode exibir esses dados de Azure Monitor nos modos de exibição fornecidos como parte dessa oferta e você pode se aprofundar diretamente nos dados de log para executar consultas e analisar tendências.

O SQL insights não instala nada em suas implantações de IaaS do SQL. Em vez disso, ele usa máquinas virtuais de monitoramento dedicado para coletar dados remotamente para implantações SQL PaaS e SQL IaaS.  O perfil de monitoramento do SQL insights permite que você gerencie os conjuntos de dados a serem coletados com base no tipo de SQL, incluindo o Azure SQL DB, o Azure SQL Instância Gerenciada e o SQL Server em execução em uma máquina virtual do Azure.

## <a name="pricing"></a>Preços

Não há custo direto para o SQL insights, mas você é cobrado por sua atividade no espaço de trabalho Log Analytics. Com base no preço que é publicado na [página de preços Azure monitor](https://azure.microsoft.com/pricing/details/monitor/), o SQL insights é cobrado por:

- Dados ingeridos de agentes e armazenados no espaço de trabalho.
- Regras de alerta com base em dados de log.
- Notificações enviadas de regras de alerta.

O tamanho do log varia de acordo com os comprimentos da cadeia de caracteres dos dados coletados e pode aumentar com a quantidade de atividade do banco de dado. 

## <a name="supported-versions"></a>Versões com suporte 
O SQL insights dá suporte às seguintes versões do SQL Server:

- SQL Server 2012 e mais recente

O SQL insights dá suporte a SQL Server em execução nos seguintes ambientes:

- Banco de Dados SQL do Azure
- Instância Gerenciada do Azure SQL
- VMs do SQL do Azure
- VMs do Azure

O SQL insights não tem suporte ou suporte limitado para o seguinte:

- Não há suporte atualmente para SQL Server em execução em máquinas virtuais fora do Azure.
- Pools elásticos do banco de dados SQL do Azure: suporte limitado durante a visualização pública. Terá suporte total na disponibilidade geral.  
- Implantações sem SQL Server do Azure: como o DR geográfico ativo, os agentes de monitoramento atuais impedirão a implantação sem servidor de entrar em suspensão. Isso pode causar maior que os custos esperados de implantações sem servidor.  
- Secundários legíveis: atualmente, somente os tipos de implantação com um único ponto de extremidade secundário legível (Comercialmente Crítico ou hiperescala) terão suporte. Quando as implantações de hiperescala dão suporte a réplicas nomeadas, poderemos dar suporte a vários pontos de extremidade secundários legíveis para um único banco de dados lógico.  
- Diretórios ativos do Azure: atualmente, damos suporte apenas a logons do SQL para o agente de monitoramento. Planejamos dar suporte a diretórios ativos do Azure em uma versão futura e não há suporte atual para autenticação de VM do SQL usando diretórios ativos em um controlador de domínio do bespoke.  


## <a name="open-sql-insights"></a>Abrir o SQL insights
Abra o SQL insights selecionando **SQL (visualização)** na seção **insights** do menu **Azure monitor** na portal do Azure. Clique em um bloco para carregar a experiência para o tipo de SQL que você está monitorando.

:::image type="content" source="media/sql-insights/portal.png" alt-text="Informações sobre o SQL no portal do Azure.":::


## <a name="enable-sql-insights"></a>Habilitar insights do SQL 
Consulte [habilitar insights do SQL](sql-insights-enable.md) para o procedimento detalhado para habilitar o SQL insights, além de etapas para solução de problemas.


## <a name="data-collected-by-sql-insights"></a>Dados coletados pelo SQL insights

O SQL insights dá suporte apenas ao método remoto de monitoramento de SQL. Não instalamos nenhum agente nas VMs que estão executando o SQL Server. Uma ou mais VMs de monitoramento dedicadas são necessárias, que usamos para coletar dados remotamente de seus recursos do SQL. 

Cada uma dessas VMs de monitoramento terá o [agente de Azure monitor](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview) instalado nelas, juntamente com a extensão de WLI (análise de informações de carga de trabalho). 

A extensão WLI inclui o agente de [Telegraf](https://www.influxdata.com/time-series-platform/telegraf/)de código-fonte aberto.  Usamos [regras de coleta de dados](https://docs.microsoft.com/azure/azure-monitor/agents/data-collection-rule-overview) para configurar o [plug-in de entrada do SqlServer](https://www.influxdata.com/integration/microsoft-sql-server/) para especificar os dados a serem COLETAdos do BD SQL do azure, azure SQL instância gerenciada e SQL Server em execução em uma VM do Azure. 

As tabelas a seguir resumem o seguinte:

- Nome da consulta no plug-in do SqlServer Telegraf
- Exibições gerenciadas dinâmicas que as chamadas de consulta
- Namespace os dados aparecem sob na tabela *InsighstMetrics*
- Se os dados são coletados por padrão
- Com que frequência os dados são coletados por padrão
 
Você pode modificar quais consultas são executadas e a frequência de coleta de dados ao criar seu perfil de monitoramento. 

### <a name="azure-sql-db-data"></a>Dados do BD SQL do Azure 

| Nome da consulta | DMV | Namespace | Habilitado por padrão | Frequência de coleta padrão |
|:---|:---|:---|:---|:---|
| AzureSQLDBWaitStats |  sys.dm_db_wait_stats | sqlserver_azuredb_waitstats | No | NA |
| AzureSQLDBResourceStats | sys.dm_db_resource_stats | sqlserver_azure_db_resource_stats | Yes | 60 segundos |
| AzureSQLDBResourceGovernance | sys.dm_user_db_resource_governance | sqlserver_db_resource_governance | Yes | 60 segundos |
| AzureSQLDBDatabaseIO | sys.dm_io_virtual_file_stats<br>sys.database_files<br>tempdb.sys .database_files | sqlserver_database_io | Yes | 60 segundos |
| AzureSQLDBServerProperties | sys.dm_os_job_object<br>sys.database_files<br>sistema. bancos<br>sistema. [database_service_objectives] | sqlserver_server_properties | Yes | 60 segundos |
| AzureSQLDBOsWaitstats | sys.dm_os_wait_stats | sqlserver_waitstats | Yes | 60 segundos |
| AzureSQLDBMemoryClerks | sys.dm_os_memory_clerks | sqlserver_memory_clerks | Yes | 60 segundos |
| AzureSQLDBPerformanceCounters | sys.dm_os_performance_counters<br>sys.databases | sqlserver_performance | Yes | 60 segundos |
| AzureSQLDBRequests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | sqlserver_requests | No | NA |
| AzureSQLDBSchedulers | sys.dm_os_schedulers | sqlserver_schedulers | No | NA  |

### <a name="azure-sql-managed-instance-data"></a>Dados de instância gerenciada do SQL do Azure 

| Nome da consulta | DMV | Namespace | Habilitado por padrão | Frequência de coleta padrão |
|:---|:---|:---|:---|:---|
| AzureSQLMIResourceStats | sys.server_resource_stats | sqlserver_azure_db_resource_stats | Yes | 60 segundos |
| AzureSQLMIResourceGovernance | sys.dm_instance_resource_governance | sqlserver_instance_resource_governance | Yes | 60 segundos |
| AzureSQLMIDatabaseIO | sys.dm_io_virtual_file_stats<br>sys.master_files | sqlserver_database_io | Yes | 60 segundos |
| AzureSQLMIServerProperties | sys.server_resource_stats | sqlserver_server_properties | Yes | 60 segundos |
| AzureSQLMIOsWaitstats | sys.dm_os_wait_stats | sqlserver_waitstats | Yes | 60 segundos |
| AzureSQLMIMemoryClerks | sys.dm_os_memory_clerks | sqlserver_memory_clerks | Yes | 60 segundos |
| AzureSQLMIPerformanceCounters | sys.dm_os_performance_counters<br>sys.databases | sqlserver_performance | Yes | 60 segundos |
| AzureSQLMIRequests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | sqlserver_requests | No | NA |
| AzureSQLMISchedulers | sys.dm_os_schedulers | sqlserver_schedulers | No | NA |

### <a name="sql-server-data"></a>Dados do SQL Server

| Nome da consulta | DMV | Namespace | Habilitado por padrão | Frequência de coleta padrão |
|:---|:---|:---|:---|:---|
| SQLServerPerformanceCounters | sys.dm_os_performance_counters | sqlserver_performance | Yes | 60 segundos |
| SQLServerWaitStatsCategorized | sys.dm_os_wait_stats | sqlserver_waitstats | Yes | 60 segundos | 
| SQLServerDatabaseIO | sys.dm_io_virtual_file_stats<br>sys.master_files | sqlserver_database_io | Yes | 60 segundos |
| Sqlserverproperties | sys.dm_os_sys_info | sqlserver_server_properties | Yes | 60 segundos |
| SQLServerMemoryClerks | sys.dm_os_memory_clerks | sqlserver_memory_clerks | Yes | 60 segundos |
| SQLServerSchedulers | sys.dm_os_schedulers | sqlserver_schedulers | No | NA |
| SQLServerRequests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | sqlserver_requests | No | NA |
| SQLServerVolumeSpace | sys.master_files | sqlserver_volume_space | Yes | 60 segundos |
| SQLServerCpu | sys.dm_os_ring_buffers | sqlserver_cpu | Yes | 60 segundos |
| SQLServerAvailabilityReplicaStates | sys.dm_hadr_availability_replica_states<br>sys.availability_replicas<br>sys.availability_groups<br>sys.dm_hadr_availability_group_states | sqlserver_hadr_replica_states | | 60 segundos |
| SQLServerDatabaseReplicaStates | sys.dm_hadr_database_replica_states<br>sys.availability_replicas | sqlserver_hadr_dbreplica_states | | 60 segundos |




## <a name="next-steps"></a>Próximas etapas

Consulte [habilitar insights do SQL](sql-insights-enable.md) para o procedimento detalhado para habilitar o SQL insights.
Consulte as [perguntas](../faq.md#sql-insights-preview) frequentes sobre as perguntas frequentes sobre o SQL insights.
