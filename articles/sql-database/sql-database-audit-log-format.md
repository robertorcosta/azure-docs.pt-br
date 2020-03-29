---
title: Formato do log de auditoria
description: Entenda como os registros de auditoria do Banco de Dados SQL são estruturados.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 01/03/2019
ms.openlocfilehash: 13746b86eed75055ceb5203afafb2d27a78ce1d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722077"
---
# <a name="sql-database-audit-log-format"></a>Formato de log de auditoria de banco de dados SQL

[A auditoria do Banco de Dados Do Azure SQL](sql-database-auditing.md) rastreia eventos do banco de dados e os grava em um registro de auditoria em sua conta de armazenamento Do Zure ou os envia para o Event Hub ou log analytics para processamento e análise a jusante.

## <a name="naming-conventions"></a>Convenções de nomenclatura

### <a name="blob-audit"></a>Auditoria Blob

Os registros de auditoria armazenados no armazenamento `sqldbauditlogs` Blob são armazenados em um contêiner nomeado na conta Azure Storage. A hierarquia do diretório dentro do `<ServerName>/<DatabaseName>/<AuditName>/<Date>/`recipiente é do formulário . O formato blob `<CreationTime>_<FileNumberInSession>.xel`filename `CreationTime` é , `hh_mm_ss_ms` onde `FileNumberInSession` está no formato UTC, e é um índice em execução no caso de logs de sessão abrangem vários arquivos Blob.

Por exemplo, `Database1` para `Server1` banco de dados a seguir é um caminho válido possível:

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

[Réplicas somente leitura](sql-database-read-scale-out.md) Os registros de auditoria são armazenados no mesmo recipiente. A hierarquia do diretório dentro do `<ServerName>/<DatabaseName>/<AuditName>/<Date>/RO/`recipiente é do formulário . O nome do arquivo Blob compartilha o mesmo formato. Os registros de auditoria de réplicas somente leitura são armazenados no mesmo recipiente.


### <a name="event-hub"></a>Hub de evento

Os eventos de auditoria são escritos no namespace e no hub de eventos que foi definido durante a configuração de auditoria, e são capturados no corpo de eventos [Apache Avro](https://avro.apache.org/) e armazenados usando a formatação JSON com codificação UTF-8. Para ler os logs de auditoria, você pode usar as [Ferramentas Avro](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) ou ferramentas similares que processam esse formato.

### <a name="log-analytics"></a>Log Analytics

Os eventos de auditoria são gravados no espaço de `AzureDiagnostics` trabalho do `SQLSecurityAuditEvents`Log Analytics definido durante a configuração de auditoria, na tabela com a categoria . Para obter informações úteis adicionais sobre o idioma e os comandos de pesquisa do Log Analytics, consulte [Referência de pesquisa do Log Analytics](../log-analytics/log-analytics-log-search.md).

## <a name="audit-log-fields"></a><a id="subheading-1"></a>Campos de registro de auditoria

| Nome (Blob) | Nome (Hubs de eventos/Análise de Log) | Descrição | Tipo de blob | Hubs de eventos/tipo de análise de log |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | ID da ação | varchar(4) | string |
| action_name | action_name_s | Nome da ação | N/D | string |
| additional_information | additional_information_s | Qualquer informação adicional sobre o evento, armazenada como XML | nvarchar(4000) | string |
| affected_rows | affected_rows_d | Número de linhas afetadas pela consulta | BIGINT | INT |
| application_name | application_name_s| Nome do aplicativo cliente | nvarchar(128) | string |
| audit_schema_version | audit_schema_version_d | Sempre 1 | INT | INT |
| class_type | class_type_s | Tipo de entidade auditável em que a auditoria ocorre | varchar(2) | string |
| class_type_desc | class_type_description_s | Descrição da entidade auditável em que a auditoria ocorre | N/D | string |
| client_ip | client_ip_s | IP de origem do aplicativo cliente | nvarchar(128) | string |
| connection_id | N/D | ID da conexão no servidor | GUID | N/D |
| data_sensitivity_information | data_sensitivity_information_s | Tipos de informações e etiquetas de sensibilidade retornados pela consulta auditada, com base nas colunas classificadas no banco de dados. Saiba mais sobre [a descoberta e classificação de dados do Banco de Dados Azure SQL](sql-database-data-discovery-and-classification.md) | nvarchar(4000) | string |
| database_name | database_name_s | O contexto do banco de dados em que a ação ocorreu | sysname | string |
| database_principal_id | database_principal_id_d | ID do contexto do usuário do banco de dados em que a ação é realizada | INT | INT |
| database_principal_name | database_principal_name_s | Nome do contexto do usuário do banco de dados no qual a ação é realizada | sysname | string |
| duration_milliseconds | duration_milliseconds_d | Duração da execução da consulta em milissegundos | BIGINT | INT |
| event_time | event_time_t | Data e hora em que a ação auditável é acionada | datetime2 | DATETIME |
| host_name | N/D | Nome do host do cliente | string | N/D |
| is_column_permission | is_column_permission_s | Sinalizador que indica se esta é uma permissão no nível de coluna. 1 = verdadeiro, 0 = falso | bit | string |
| N/D | is_server_level_audit_s | Bandeira indicando se esta auditoria está no nível do servidor | N/D | string |
| object_id | object_id_d | A ID da entidade na qual a auditoria ocorreu. Isso inclui: objetos de servidor, bancos de dados, objetos de banco de dados e objetos de esquema. 0 se a entidade for o próprio servidor ou se a auditoria não for realizada em um nível de objeto | INT | INT |
| object_name | object_name_s | O nome da entidade na qual a auditoria ocorreu. Isso inclui: objetos de servidor, bancos de dados, objetos de banco de dados e objetos de esquema. 0 se a entidade for o próprio servidor ou se a auditoria não for realizada em um nível de objeto | sysname | string |
| permission_bitmask | permission_bitmask_s | Quando aplicável, mostra as permissões concedidas, negadas ou revogadas | varbinary(16) | string |
| response_rows | response_rows_d | Número de linhas retornadas no conjunto de resultados | BIGINT | INT |
| schema_name | schema_name_s | O contexto do esquema no qual a ação aconteceu. NULL para auditorias que ocorrem fora de um esquema | sysname | string |
| N/D | securable_class_type_s | Objeto securável que mapeia a class_type sendo auditado | N/D | string |
| sequence_group_id | sequence_group_id_g | Identificador exclusivo | varbinary | GUID |
| sequence_number | sequence_number_d | Rastreia a seqüência de registros dentro de um único registro de auditoria que era muito grande para caber no buffer de gravação para auditorias | INT | INT |
| server_instance_name | server_instance_name_s | Nome da instância do servidor onde ocorreu a auditoria | sysname | string |
| server_principal_id | server_principal_id_d | ID do contexto de login em que a ação é realizada | INT | INT |
| server_principal_name | server_principal_name_s | Login atual | sysname | string |
| server_principal_sid | server_principal_sid_s | SID de login atual | varbinary | string |
| session_id | session_id_d | ID da sessão em que o evento ocorreu | SMALLINT | INT |
| session_server_principal_name | session_server_principal_name_s | Diretor do servidor para sessão | sysname | string |
| instrução | statement_s | Declaração T-SQL que foi executada (se houver) | nvarchar(4000) | string |
| succeeded | succeeded_s | Indica se a ação que desencadeou o evento foi bem sucedida. Para eventos que não sejam login e lote, isso só informa se a verificação de permissão foi bem sucedida ou não, não a operação. 1 = sucesso, 0 = falha | bit | string |
| target_database_principal_id | target_database_principal_id_d | O banco de dados principal no qual a operação GRANT/DENY/REVOKE é executada. 0 se não for aplicável | INT | INT |
| target_database_principal_name | target_database_principal_name_s | Usuário alvo de ação. NULO se não aplicável | string | string |
| target_server_principal_id | target_server_principal_id_d | Diretor de Servidor que o GRANT/operação do DENY/REVOKE é executada em. Retorna 0 se não aplicável | INT | INT |
| target_server_principal_name | target_server_principal_name_s | Login de ação alvo. NULO se não aplicável | sysname | string |
| target_server_principal_sid | target_server_principal_sid_s | SID de login de alvo. NULO se não aplicável | varbinary | string |
| transaction_id | transaction_id_d | Somente sql server (a partir de 2016) - 0 para Azure SQL DB | BIGINT | INT |
| user_defined_event_id | user_defined_event_id_d | O id de evento definido pelo usuário passou como um argumento para sp_audit_write. NULL para eventos do sistema (padrão) e não zero para evento definido pelo usuário. Para obter mais informações, consulte [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | SMALLINT | INT |
| user_defined_information | user_defined_information_s | As informações definidas pelo usuário passaram como um argumento para sp_audit_write. NULL para eventos do sistema (padrão) e não zero para evento definido pelo usuário. Para obter mais informações, consulte [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar(4000) | string |

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [a auditoria do Banco de Dados Azure SQL](sql-database-auditing.md).
