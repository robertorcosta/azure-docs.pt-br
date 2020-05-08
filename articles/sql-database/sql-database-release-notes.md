---
title: Notas de versão
description: Saiba mais sobre os novos recursos e aprimoramentos no serviço de banco de dados SQL do Azure e na documentação do banco de dados SQL do Azure
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: sstein
ms.openlocfilehash: aa8d1634c015f338053a4d167db34ef0b5a83505
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801681"
---
# <a name="sql-database-release-notes"></a>Notas de versão do banco de dados SQL

Este artigo lista os recursos do banco de dados SQL que estão atualmente em visualização pública. Para atualizações e aprimoramentos do banco de dados SQL, consulte [atualizações do serviço de banco de dados SQL](https://azure.microsoft.com/updates/?product=sql-database). Para obter atualizações e aprimoramentos em outros serviços do Azure, consulte [atualizações de serviço](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Recursos na visualização pública

### <a name="single-database"></a>[Banco de dados individual](#tab/single-database)

| Recurso | Detalhes |
| ---| --- |
| Novas gerações de hardware das séries Fsv2 e M| Para obter informações, consulte [gerações de hardware](sql-database-service-tiers-vcore.md#hardware-generations).|
| Recuperação de banco de dados acelerada com bancos de dados individuais e pools elásticos | Para obter informações, consulte [recuperação de banco de dados acelerada](sql-database-accelerated-database-recovery.md).|
|Distinção de contagem aproximada|Para obter informações, veja [contagem aproximada distinta](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Modo de lote no repositório de armazenamento (no nível de compatibilidade 150)|Para obter informações, consulte [modo de lote no repositório de armazenamento](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Descoberta e classificação de dados  |Para saber mais, confira [banco de dados SQL do Azure e SQL data warehouse a descoberta de Data & classificação](sql-database-data-discovery-and-classification.md).|
| Trabalhos de banco de dados elástico | Para obter informações, consulte [criar, configurar e gerenciar trabalhos elásticos](elastic-jobs-overview.md). |
| Consultas elásticas | Para obter informações, consulte [visão geral de consulta elástica](sql-database-elastic-query-overview.md). |
| Transações elásticas | [Transações distribuídas entre bancos de dados na nuvem](sql-database-elastic-transactions-overview.md). |
|Comentários de concessão de memória (modo de linha) (em nível de compatibilidade 150)|Para obter informações, consulte [comentários de concessão de memória (modo de linha)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Editor de consultas no portal do Azure |Para obter informações, consulte [usar o editor de consultas SQL do portal do Azure para se conectar e consultar dados](sql-database-connect-query-portal.md).|
| Serviços de R/Machine Learning com bancos de dados individuais e pools elásticos |Para obter informações, consulte [serviços de Machine Learning no banco de dados SQL do Azure](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
|Análise de SQL|Para obter informações, consulte [análise de SQL do Azure](../azure-monitor/insights/azure-sql.md).|
|Compilação adiada da variável de tabela (no nível de compatibilidade 150)|Para obter informações, consulte [compilação adiada da variável de tabela](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instance"></a>[Instância Gerenciada](#tab/managed-instance)

| Recurso | Detalhes |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Pools da instância</a> | Uma maneira conveniente e econômica de migrar instâncias SQL menores para a nuvem. |
| <a href="https://aka.ms/managed-instance-aadlogins">Entidades de segurança de nível de instância do Azure AD Server (logons)</a> | Crie logons no nível de servidor usando a instrução <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Create login from external Provider</a> . |
| [Replicação transacional](sql-database-managed-instance-transactional-replication.md) | Replique as alterações de suas tabelas em outros bancos de dados colocados em instâncias gerenciadas, bancos de dados individuais ou instâncias de SQL Server, ou atualize suas tabelas quando algumas linhas forem alteradas em outras instâncias gerenciadas ou SQL Server instância. Para obter informações, consulte [Configurar replicação em um banco de dados de instância gerenciada do banco de dados SQL do Azure](replication-with-sql-database-managed-instance.md). |
| Detecção de ameaças |Para obter informações, consulte [Configurar a detecção de ameaças na instância gerenciada do banco de dados SQL do Azure](sql-database-managed-instance-threat-detection.md).|
| Retenção de backup de longo prazo | Para obter informações, consulte [Configurar retenção de backup de longo prazo na instância gerenciada do banco de dados SQL do Azure](sql-database-managed-instance-long-term-backup-retention-configure.md), que está atualmente em visualização pública limitada. | 

---

## <a name="managed-instance---new-features-and-known-issues"></a>Instância gerenciada-novos recursos e problemas conhecidos

### <a name="managed-instance-h2-2019-updates"></a>Atualizações 2019 de instância gerenciada H2

- [Configuração de sub-rede auxiliada por serviço](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) Uma maneira segura e conveniente de gerenciar a configuração de sub-rede em que você controla o tráfego de dados enquanto a instância gerenciada garante o fluxo ininterrupto do tráfego de gerenciamento
- [A TDE (Transparent Data Encryption) com Bring your own Key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) habilita o cenário de BYOK (traga sua própria chave) para proteção de dados em repouso e permite que as organizações separem as tarefas de gerenciamento para chaves e dados.
- Os [grupos de failover automático](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) permitem replicar todos os bancos de dados da instância primária para uma instância secundária em outra região.
- Configure o comportamento da instância gerenciada com [sinalizadores de rastreamento globais](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/).

### <a name="managed-instance-h1-2019-updates"></a>Instâncias gerenciadas H1 2019 atualizações

Os seguintes recursos estão habilitados no modelo de implantação de instância gerenciada em H1 2019:
  - Suporte para assinaturas com <a href="https://aka.ms/sql-mi-visual-studio-subscribers">crédito mensal do Azure para assinantes do Visual Studio</a> e [limites regionais](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)maiores.
  - Suporte para <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 e SharePoint 2019 </a> e <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central </a>
  - Crie instâncias com <a href="https://aka.ms/managed-instance-collation">agrupamento de nível de servidor</a> e <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">zona de tempo</a> de sua escolha.
  - As instâncias gerenciadas agora são protegidas com o <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">firewall interno</a>.
  - Configure as instâncias para usar [pontos de extremidade públicos](sql-database-managed-instance-public-endpoint-configure.md), conexão de [substituição de proxy](sql-database-connectivity-architecture.md#connection-policy) para obter melhor desempenho de rede, <a href="https://aka.ms/four-cores-sql-mi-update">4 VCores na geração de hardware Gen5</a> ou <a href="https://aka.ms/managed-instance-configurable-backup-retention">configurar a retenção de backup de até 35 dias para a</a> restauração pontual. A [retenção de backup de longo prazo](sql-database-long-term-retention.md#managed-instance-support) (até 10 anos) está atualmente em visualização pública limitada.  
  - As novas funcionalidades permitem que você <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">restaure geograficamente seu banco de dados para outro Data Center usando o PowerShell</a>, [renomeie o banco de dados](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), [exclua o cluster virtual](sql-database-managed-instance-delete-virtual-cluster.md).
  - A nova função de [colaborador de instância](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) interna permite a conformidade de SOD (separação de imposto) com princípios de segurança e conformidade com os padrões corporativos.
  - A instância gerenciada está disponível nas seguintes regiões do Azure governamental para GA (US Gov Texas US Gov Arizona), bem como em Norte da China 2 e Leste da China 2. Ele também está disponível nas seguintes regiões públicas: Austrália Central, Austrália Central 2, sul do Brasil, sul da França, EAU Central, Norte dos EAU, norte da África do Sul, oeste da África do Sul.

### <a name="known-issues"></a>Problemas conhecidos

|Problema  |Data de descoberta  |Status  |Data de resolução  |
|---------|---------|---------|---------|
|[O Agent não responde na modificação, desabilitação ou habilitação de trabalhos existentes](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|Maio de 2020|Mitigado automaticamente| |
|[Permissões no grupo de recursos não aplicadas a Instância Gerenciada](#permissions-on-resource-group-not-applied-to-managed-instance)|Fevereiro de 2020|Tem solução alternativa| |
|[Limitação de failover manual por meio do portal para grupos de failover](#limitation-of-manual-failover-via-portal-for-failover-groups)|Jan 2020|Tem solução alternativa| |
|[As funções do SQL Agent precisam de permissões de execução explícitas para logons não sysadmin](#in-memory-oltp-memory-limits-are-not-applied)|Dec 2019|Tem solução alternativa| |
|[Os trabalhos do SQL Agent podem ser interrompidos pela reinicialização do processo do agente](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Dec 2019|Resolvido|Mar de 2020|
|[Não há suporte para logons e usuários do AAD no SSDT](#aad-logins-and-users-are-not-supported-in-ssdt)|2019 de novembro|Sem solução| |
|[Os limites de memória OLTP na memória não são aplicados](#in-memory-oltp-memory-limits-are-not-applied)|Outubro de 2019|Tem solução alternativa| |
|[Erro incorreto retornado ao tentar remover um arquivo que não está vazio](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|Outubro de 2019|Tem solução alternativa| |
|[Alterar a camada de serviço e criar operações de instância são bloqueadas pela restauração de banco de dados em andamento](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Setembro de 2019|Tem solução alternativa| |
|[Resource Governor na camada de serviço Comercialmente Crítico talvez precise ser reconfigurada após o failover](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Setembro de 2019|Tem solução alternativa| |
|[As caixas de diálogo de Service Broker entre bancos de dados devem ser reinicializadas após a atualização da camada de serviço](#cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade)|2019 de agosto|Tem solução alternativa| |
|[Não há suporte para a impessoa de tipos de logon do Azure AD](#impersonification-of-azure-ad-login-types-is-not-supported)|Julho de 2019|Sem solução| |
|[@queryparâmetro sem suporte no sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|Abril de 2019|Sem solução| |
|[A replicação transacional deve ser reconfigurada após o failover geográfico](#transactional-replication-must-be-reconfigured-after-geo-failover)|Mar de 2019|Sem solução| |
|[O banco de dados temporário é usado durante a operação de restauração](#temporary-database-is-used-during-restore-operation)||Tem solução alternativa| |
|[A estrutura TEMPDB e o conteúdo são recriados](#tempdb-structure-and-content-is-re-created)||Sem solução| |
|[Excedendo o espaço de armazenamento com arquivos de banco de dados pequenos](#exceeding-storage-space-with-small-database-files)||Tem solução alternativa| |
|[Valores de GUID mostrados em vez de nomes de banco de dados](#guid-values-shown-instead-of-database-names)||Tem solução alternativa| |
|[Os logs de erros não são persistentes](#error-logs-arent-persisted)||Sem solução| |
|[O escopo da transação em dois bancos de dados na mesma instância não tem suporte](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Tem solução alternativa|Mar de 2020|
|[Os módulos CLR e os servidores vinculados às vezes não podem fazer referência a um endereço IP local](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Tem solução alternativa| |
|Consistência do banco de dados não verificada usando DBCC CHECKDB após restaurar banco de dados do armazenamento de BLOBs do Azure.| |Resolvido|2019 de novembro|
|A restauração de banco de dados pontual da camada de Comercialmente Crítico para Uso Geral não terá sucesso se o banco de dados de origem contiver objetos OLTP na memória.| |Resolvido|Outubro de 2019|
|Database Mail recurso com servidores de email externos (não Azure) usando conexão segura| |Resolvido|Outubro de 2019|
|Bancos de dados independentes sem suporte na instância gerenciada| |Resolvido|2019 de agosto|

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>O Agent não responde na modificação, desabilitação ou habilitação de trabalhos existentes

Em determinadas circunstâncias, modificar um trabalho existente, desabilitar ou habilitá-lo pode fazer com que o agente deixe de responder. O problema é mitigado automaticamente após a detecção, resultando na reinicialização do processo do agente.

### <a name="permissions-on-resource-group-not-applied-to-managed-instance"></a>Permissões no grupo de recursos não aplicadas à instância gerenciada

Instância Gerenciada função de RBAC do colaborador quando aplicada a um grupo de recursos (RG) não é aplicada a Instância Gerenciada e não tem efeito.

**Solução alternativa**: Configure instância gerenciada função colaborador para usuários no nível de assinatura.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Limitação de failover manual por meio do portal para grupos de failover

Se o grupo de failover se estender entre instâncias em diferentes assinaturas ou grupos de recursos do Azure, o failover manual não poderá ser iniciado a partir da instância primária no grupo de failover.

**Solução alternativa**: Inicie o failover por meio do portal da instância geográfica-secundária.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>As funções do SQL Agent precisam de permissões de execução explícitas para logons não sysadmin

Se os logons não-sysadmin forem adicionados a qualquer uma das [funções de banco de dados fixas do SQL Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles), existe um problema no qual as permissões de execução explícitas precisam ser concedidas aos procedimentos armazenados mestres para que esses logons funcionem. Se esse problema for encontrado, a mensagem de erro "a permissão de execução foi negada no objeto <object_name> (Microsoft SQL Server, erro: 229)" será mostrada.

**Solução alternativa**: depois de adicionar logons a uma das funções de banco de dados fixas do SQL Agent: SQLAgentUserRole, SQLAgentReaderRole ou SQLAgentOperatorRole, para cada um dos logons adicionados a essas funções, execute o script T-SQL abaixo para conceder explicitamente permissões de execução aos procedimentos armazenados listados.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>Os trabalhos do SQL Agent podem ser interrompidos pela reinicialização do processo do agente

**(Resolvido em março de 2020)** O SQL Agent cria uma nova sessão toda vez que o trabalho é iniciado, aumentando gradualmente o consumo de memória. Para evitar atingir o limite de memória interna que bloquearia a execução de trabalhos agendados, o processo do agente será reiniciado quando seu consumo de memória atingir o limite. Isso pode resultar na interrupção da execução de trabalhos em execução no momento da reinicialização.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>Os limites de memória OLTP na memória não são aplicados

Comercialmente Crítico camada de serviço não aplicará corretamente [os limites máximos de memória para objetos com otimização de memória](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space) em alguns casos. A instância gerenciada pode permitir que a carga de trabalho use mais memória para operações OLTP na memória, o que pode afetar a disponibilidade e a estabilidade da instância. As consultas OLTP na memória que estão atingindo os limites podem não falhar imediatamente. Esse problema será corrigido em breve. As consultas que usam mais memória OLTP na memória falharão mais cedo se atingirem os [limites](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space).

**Solução alternativa:** [monitore o uso de armazenamento OLTP na memória](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring) usando [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) para garantir que a carga de trabalho não esteja usando mais do que a memória disponível. Aumente os limites de memória que dependem do número de vCores ou Otimize sua carga de trabalho para usar menos memória.

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Erro incorreto retornado ao tentar remover um arquivo que não está vazio

SQL Server/Instância Gerenciada [não permitir que o usuário descarte um arquivo que não esteja vazio](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Se você tentar remover um arquivo de dados não vazio usando `ALTER DATABASE REMOVE FILE` a instrução, o erro `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` não será retornado imediatamente. Instância Gerenciada continuará tentando descartar o arquivo e a operação falhará após 30 min `Internal server error`com.

**Solução alternativa**: Remova o conteúdo do arquivo usando `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` o comando. Se esse for o único arquivo no grupo de arquivos, você precisará excluir dados da tabela ou da partição associada a esse grupo de arquivos antes de reduzir o arquivo e, opcionalmente, carregar esses dados em outra tabela/partição.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Alterar a camada de serviço e criar operações de instância são bloqueadas pela restauração de banco de dados em andamento

A `RESTORE` instrução contínua, o processo de migração do serviço de migração de dados e a restauração pontual interna bloquearão a atualização da camada de serviço ou o redimensionamento da instância existente e a criação de novas instâncias, até que o processo de restauração seja concluído. O processo de restauração bloqueará essas operações nas instâncias gerenciadas e nos pools de instância na mesma sub-rede em que o processo de restauração está em execução. As instâncias em pools de instâncias não são afetadas. Criar ou alterar as operações da camada de serviço não falharão ou tempo limite-eles continuarão quando o processo de restauração for concluído ou cancelado.

**Solução alternativa**: Aguarde até que o processo de restauração seja concluído ou cancele o processo de restauração se a operação de criação ou atualização da camada de serviço tiver prioridade mais alta.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Resource Governor na camada de serviço Comercialmente Crítico talvez precise ser reconfigurada após o failover

[Resource governor](/sql/relational-databases/resource-governor/resource-governor) recurso que permite limitar os recursos atribuídos à carga de trabalho do usuário pode classificar incorretamente alguma carga de trabalho do usuário após o failover ou a alteração da camada de serviço iniciada pelo usuário (por exemplo, a alteração do tamanho máximo do armazenamento de instância vCore ou máximo).

**Solução alternativa**: `ALTER RESOURCE GOVERNOR RECONFIGURE` execute periodicamente ou como parte do trabalho do SQL Agent que executa a tarefa SQL quando a instância for iniciada se você estiver usando [resource governor](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>As caixas de diálogo de Service Broker entre bancos de dados devem ser reinicializadas após a atualização da camada de serviço

As caixas de diálogo de Service Broker de banco de dados cruzado deixarão de entregar as mensagens para os serviços em outros bancos de dados após a operação de alteração da camada de serviço. As mensagens **não são perdidas** e podem ser encontradas na fila do remetente. Qualquer alteração de tamanho de armazenamento de instância ou vCores em Instância Gerenciada, `service_broke_guid` fará com que o valor na exibição [Sys. databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) seja alterado para todos os bancos de dados. Qualquer `DIALOG` criado usando a instrução [BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) que referencie os agentes de serviço em outro banco de dados interromperá a entrega de mensagens ao serviço de destino.

**Solução alternativa:** Pare qualquer atividade que use conversas de caixa de diálogo Service Broker de banco de dados antes de atualizar a camada de serviço e reinicializá-la após. Se houver mensagens restantes que não são entregues após a alteração da camada de serviço, leia as mensagens da fila de origem e reenvie-as para a fila de destino.

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>Não há suporte para a impessoa de tipos de logon do Azure AD

Não há `EXECUTE AS USER` suporte `EXECUTE AS LOGIN` para a representação usando ou das seguintes entidades de segurança do AAD:
-    Usuários com alias do AAD. O erro a seguir é retornado nesse caso `15517`.
- Logons e usuários do AAD com base em aplicativos do AAD ou entidades de serviço. Os erros a seguir são retornados nesse caso `15517` e `15406`.

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@queryparâmetro sem suporte no sp_send_db_mail

O `@query` parâmetro no procedimento de [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) não funciona.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>A replicação transacional deve ser reconfigurada após o failover geográfico

Se a replicação transacional estiver habilitada em um banco de dados em um grupo de failover automático, o administrador da instância gerenciada deverá limpar todas as publicações no antigo primário e reconfigurá-los no novo primário após a ocorrência de um failover para outra região. Consulte [replicação](sql-database-managed-instance-transact-sql-information.md#replication) para obter mais detalhes.

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>Não há suporte para logons e usuários do AAD no SSDT

SQL Server Data Tools não dão suporte total a logons e usuários do Azure Active Directory.

### <a name="temporary-database-is-used-during-restore-operation"></a>O banco de dados temporário é usado durante a operação de restauração

Quando um banco de dados estiver restaurando em Instância Gerenciada, o serviço de restauração criará primeiro um banco de dados vazio com o nome desejado para alocar o nome na instância. Após algum tempo, esse banco de dados será descartado e a restauração do banco de dados real será iniciada. O banco de dados que está no estado de *restauração* temporário terá um valor de GUID aleatório em vez de nome. O nome temporário será alterado para o nome desejado especificado na `RESTORE` instrução quando o processo de restauração for concluído. Na fase inicial, o usuário pode acessar o banco de dados vazio e, até mesmo, criar tabelas ou carrega-los. Esse banco de dados temporário será Descartado quando o serviço de restauração iniciar a segunda fase.

**Solução alternativa**: não acesse o banco de dados que você está restaurando até ver que a restauração foi concluída.

### <a name="tempdb-structure-and-content-is-re-created"></a>A estrutura TEMPDB e o conteúdo são recriados

O `tempdb` banco de dados sempre é dividido em 12 arquivos de data e a estrutura do arquivo não pode ser alterada. O tamanho máximo por arquivo não pode ser alterado e novos arquivos não podem ser adicionados `tempdb`ao. `Tempdb`é sempre recriado como um banco de dados vazio quando a instância inicia ou faz failover, e quaisquer alterações feitas `tempdb` no não serão preservadas.

### <a name="exceeding-storage-space-with-small-database-files"></a>Excedendo o espaço de armazenamento com arquivos de banco de dados pequenos

`CREATE DATABASE`as `ALTER DATABASE ADD FILE`instruções, `RESTORE DATABASE` e podem falhar porque a instância pode alcançar o limite de armazenamento do Azure.

Cada instância gerenciada Uso Geral tem até 35 TB de armazenamento reservado para o espaço em disco Premium do Azure. Cada arquivo de banco de dados é colocado em um disco físico separado. Tamanhos de disco podem ser 128 GB, 256 GB, 512 GB, 1 TB ou 4 TB. O espaço não utilizado no disco não é cobrado, mas a soma total dos tamanhos de disco Premium do Azure não pode exceder 35 TB. Em alguns casos, uma instância gerenciada que não precisa de 8 TB no total pode exceder o limite de 35 TB do Azure no tamanho do armazenamento devido à fragmentação interna.

Por exemplo, uma instância gerenciada Uso Geral pode ter um arquivo grande que seja de 1,2 TB de tamanho colocado em um disco de 4 TB. Ele também pode ter 248 arquivos com tamanho de 1 GB cada um colocado em discos separados de 128 GB. Neste exemplo:

- O tamanho do armazenamento em disco total alocado é de 1 x 4 TB + 248 x 128 GB = 35 TB.
- O total de espaço reservado para os bancos de dados na instância é de 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Este exemplo ilustra que, em determinadas circunstâncias, devido a uma distribuição específica de arquivos, uma instância gerenciada pode alcançar o limite de 35 TB reservado para um disco Premium do Azure conectado quando você talvez não o espere.

Neste exemplo, os bancos de dados existentes continuam funcionando e podem crescer sem qualquer problema, desde que novos arquivos não sejam adicionados. Novos bancos de dados não podem ser criados ou restaurados porque não há espaço suficiente para novas unidades de disco, mesmo que o tamanho total de todos os bancos de dados não atinja o limite de tamanho da instância. O erro retornado nesse caso não é claro.

Você pode [identificar o número de arquivos restantes](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) usando exibições do sistema. Se você atingir esse limite, tente [esvaziar e excluir alguns dos arquivos menores usando a instrução DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) ou alterne para a [camada comercialmente crítico, que não tem esse limite](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>Valores de GUID mostrados em vez de nomes de banco de dados

Várias entradas de exibições do sistema, contadores de desempenho, mensagens de erro, XEvents e logs de erros exibem identificadores do banco de dados GUID em vez dos nomes reais do banco de dados. Não confie nesses identificadores GUID porque eles são substituídos por nomes de banco de dados reais no futuro.

**Solução alternativa**: Use a exibição sys. databases para resolver o nome de banco de dados real do nome do banco de dados físico, especificado na forma de identificadores de banco de dados GUID

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>Os logs de erros não são persistentes

Os logs de erros que estão disponíveis na instância gerenciada não são mantidos e seu tamanho não é incluído no limite máximo de armazenamento. Os logs de erros poderão ser apagados automaticamente se ocorrer um failover. Pode haver lacunas no histórico do log de erros porque Instância Gerenciada foi movido várias vezes em várias máquinas virtuais.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>O escopo da transação em dois bancos de dados na mesma instância não tem suporte

**(Resolvido em março de 2020)** A `TransactionScope` classe no .net não funcionará se duas consultas forem enviadas a dois bancos de dados dentro da mesma instância no mesmo escopo de transação:

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

**Solução alternativa (não é necessário desde março de 2020):** Use [SqlConnection. ChangeDatabase (String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) para usar outro banco de dados em um contexto de conexão em vez de usar duas conexões.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Os módulos CLR e os servidores vinculados às vezes não podem fazer referência a um endereço IP local

Os módulos CLR colocados em uma instância gerenciada e servidores vinculados ou consultas distribuídas que referenciam uma instância atual às vezes não podem resolver o IP de uma instância local. Esse é um problema temporário.

**Solução alternativa:** Use conexões de contexto em um módulo CLR, se possível.

## <a name="updates"></a>Atualizações

Para obter uma lista de atualizações e melhorias do banco de dados SQL, consulte [atualizações do serviço de banco de dados SQL](https://azure.microsoft.com/updates/?product=sql-database).

Para obter atualizações e aprimoramentos em todos os serviços do Azure, consulte [atualizações de serviço](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Contribuir para o conteúdo

Para contribuir com a documentação do banco de dados SQL do Azure, consulte o [Guia do colaborador do docs](https://docs.microsoft.com/contribute/).
