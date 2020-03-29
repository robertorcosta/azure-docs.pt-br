---
title: Logs de auditoria - Banco de dados Azure para MySQL
description: Descreve os registros de auditoria disponíveis no Banco de Dados Azure para MySQL e os parâmetros disponíveis para habilitar os níveis de registro.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/19/2020
ms.openlocfilehash: b42f0d7a8146f7f2b313959273abd22303c89a60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062553"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Logs de auditoria no banco de dados Do Zure para MySQL

No Banco de Dados Azure para MySQL, o registro de auditoria está disponível para os usuários. O registro de auditoria pode ser usado para rastrear atividades em nível de banco de dados e é comumente usado para conformidade.

> [!IMPORTANT]
> A funcionalidade do registro de auditoria está atualmente em visualização.

## <a name="configure-audit-logging"></a>Configurar o registro de auditoria

Por padrão, o registro de auditoria está desativado. Para habilitá-lo, defina-o. `audit_log_enabled`

Outros parâmetros que você pode ajustar incluem:

- `audit_log_events`: controla os eventos a serem registrados. Veja abaixo a tabela para eventos específicos de auditoria.
- `audit_log_include_users`: Usuários mySQL a serem incluídos para registro. O valor padrão para este parâmetro está vazio, o que incluirá todos os usuários para registro. Isso tem maior `audit_log_exclude_users`prioridade sobre . O comprimento máximo do parâmetro é de 512 caracteres.
> [!Note]
> `audit_log_include_users`tem maior `audit_log_exclude_users`prioridade sobre . Por exemplo, `audit_log_include_users`  =  `demouser` `audit_log_exclude_users`  =  `demouser`se e , o usuário será `audit_log_include_users` incluído nos registros de auditoria porque tem maior prioridade.
- `audit_log_exclude_users`: Usuários mySQL a serem excluídos do registro. O comprimento máximo do parâmetro é de 512 caracteres.

> [!Note]
> Para `sql_text`, log será truncado se exceder 2048 caracteres.

| **Evento** | **Descrição** |
|---|---|
| `CONNECTION` | - Iniciação de conexão (bem sucedida ou sem sucesso) <br> - Reautenticação do usuário com diferentes usuários/senha durante a sessão <br> - Término de conexão |
| `DML_SELECT`| Consultas SELECT |
| `DML_NONSELECT` | CONSULTAS INSERT/DELETE/UPDATE |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Consultas como "DROP DATABASE" |
| `DCL` | Consultas como "GRANT PERMISSION" |
| `ADMIN` | Consultas como "STATUS DE SHOW" |
| `GENERAL` | Tudo em DML_SELECT, DML_NONSELECT, DML, DDL, DCL e ADMIN |
| `TABLE_ACCESS` | - Disponível apenas para MySQL 5.7 <br> - Tabela de declarações de leitura, como SELECT ou INSERT INTO ... Selecione <br> - Tabela de exclusão de declarações, como DELETE ou TRUNCATE TABLE <br> - Instruções de inserção de mesa, como INSERT ou REPLACE <br> - Declarações de atualização de tabela, como UPDATE |

## <a name="access-audit-logs"></a>Acesse os logs de auditoria

Os registros de auditoria são integrados aos registros de diagnóstico do Monitor Azure. Depois de habilitar registros de auditoria no seu servidor MySQL, você pode emiti-los para logs do Monitor Do Azure, Hubs de Eventos ou Armazenamento Azure. Para saber mais sobre como ativar os registros de diagnóstico no portal Azure, consulte o artigo do [portal de registro de auditoria](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Schemas de registros de diagnóstico

As seções a seguir descrevem o que é saída por logs de auditoria MySQL com base no tipo de evento. Dependendo do método de saída, os campos incluídos e a ordem em que aparecem podem variar.

### <a name="connection"></a>Conexão

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua ID de locatário |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Carimbo de data/hora quando o log foi gravado, em UTC |
| `Type` | Tipo do log. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID para a assinatura a que o servidor pertence |
| `ResourceGroup` | Nome do grupo de recursos ao qual o servidor pertence |
| `ResourceProvider` | Nome do provedor de recursos. Sempre `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de recurso |
| `Resource` | Nome do servidor |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nome do servidor |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` `CHANGE USER` ,(disponível apenas para MySQL 5.7) |
| `connection_id_d` | ID de conexão exclusivo gerado pelo MySQL |
| `host_s` | Em branco |
| `ip_s` | Endereço IP da conexão do cliente ao MySQL |
| `user_s` | Nome do usuário executando a consulta |
| `db_s` | Nome do banco de dados conectado a |
| `\_ResourceId` | URI de recurso |

### <a name="general"></a>Geral

O esquema abaixo se aplica aos tipos de eventos GENERAL, DML_SELECT, DML_NONSELECT, DML, DDL, DCL e ADMIN.

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua ID de locatário |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Carimbo de data/hora quando o log foi gravado, em UTC |
| `Type` | Tipo do log. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID para a assinatura a que o servidor pertence |
| `ResourceGroup` | Nome do grupo de recursos ao qual o servidor pertence |
| `ResourceProvider` | Nome do provedor de recursos. Sempre `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de recurso |
| `Resource` | Nome do servidor |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nome do servidor |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR` `RESULT` ,(disponível apenas para MySQL 5.6) |
| `event_time` | Tempo de início da consulta no carimbo de data-hora da UTC |
| `error_code_d` | Código de erro se a consulta falhar. `0`significa que nenhum erro |
| `thread_id_d` | ID do segmento que executou a consulta |
| `host_s` | Em branco |
| `ip_s` | Endereço IP da conexão do cliente ao MySQL |
| `user_s` | Nome do usuário executando a consulta |
| `sql_text_s` | Texto de consulta completa |
| `\_ResourceId` | URI de recurso |

### <a name="table-access"></a>Acesso à tabela

> [!NOTE]
> Os logs de acesso à tabela são apenas saída satisfaz o MySQL 5.7.

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua ID de locatário |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Carimbo de data/hora quando o log foi gravado, em UTC |
| `Type` | Tipo do log. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID para a assinatura a que o servidor pertence |
| `ResourceGroup` | Nome do grupo de recursos ao qual o servidor pertence |
| `ResourceProvider` | Nome do provedor de recursos. Sempre `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de recurso |
| `Resource` | Nome do servidor |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nome do servidor |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE` ou `DELETE` |
| `connection_id_d` | ID de conexão exclusivo gerado pelo MySQL |
| `db_s` | Nome do banco de dados acessado |
| `table_s` | Nome da tabela acessada |
| `sql_text_s` | Texto de consulta completa |
| `\_ResourceId` | URI de recurso |

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analisar logs no Azure Monitor Logs

Uma vez que seus registros de auditoria sejam canalizados para logs do Monitor do Azure através de registros de diagnóstico, você pode realizar uma análise mais aprofundada de seus eventos auditados. Abaixo estão algumas consultas de exemplo para ajudá-lo a começar. Certifique-se de atualizar o abaixo com o nome do servidor.

- Listar eventos GERAIS em um servidor específico

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "general_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last 
    ```

- Liste eventos CONNECTION em um servidor específico

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "connection_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ```

- Resumir eventos auditados em um servidor específico

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- Gráfico a distribuição do tipo de evento de auditoria em um servidor específico

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- Liste eventos auditados em todos os servidores MySQL com logs de diagnóstico habilitados para registros de auditoria

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>Próximas etapas

- [Como configurar registros de auditoria no portal Azure](howto-configure-audit-logs-portal.md)