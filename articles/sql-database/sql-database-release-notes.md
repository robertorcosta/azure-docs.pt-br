---
title: Notas de versão
description: Saiba mais sobre os novos recursos e aprimoramentos no serviço de Banco de Dados SQL do Azure e na documentação do banco de dados SQL do Azure
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: sstein
ms.openlocfilehash: 3e5069c779cee0700bff6b2236f3cd36547fd623
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659608"
---
# <a name="sql-database-release-notes"></a>Notas de lançamento do Banco de Dados SQL

Este artigo lista os recursos do banco de dados SQL que estão atualmente em visualização pública. Para atualizações e aprimoramentos do Banco de Dados SQL, consulte [atualizações do serviço de Banco de Dados SQL](https://azure.microsoft.com/updates/?product=sql-database). Para encontrar atualizações e melhorias para outros serviços do Azure, confira [Atualizações de serviço](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Inclui versão prévia pública

### <a name="single-database"></a>[Banco de dados individual](#tab/single-database)

| Recurso | Detalhes |
| ---| --- |
| Novas gerações de hardware das séries Fsv2 e M| Para obter informações, consulte [Gerações de hardware](sql-database-service-tiers-vcore.md#hardware-generations).|
| Recuperação acelerada do banco de dados com pools elásticos e bancos de dados individuais | Para obter mais informações, confira [Recuperação acelerada de banco de dados](sql-database-accelerated-database-recovery.md).|
|Distinção de contagem aproximada|Para obter mais informações, consulte [Distinção de contagem aproximada](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Modo de lote no Rowstore (abaixo do nível de compatibilidade 150)|Para obter mais informações, confira [Modo de lote em Rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Descoberta e classificação de dados  |Para obter mais informações, [Descoberta e classificação do Banco de Dados SQL do Azure e do SQL Data Warehouse](sql-database-data-discovery-and-classification.md).|
| Trabalhos de banco de dados elástico | Para obter mais informações, consulte [Criar, configurar e gerenciar trabalhos elásticos](elastic-jobs-overview.md). |
| Consultas elásticas | Para obter mais informações, consulte [Visão geral de consulta elástica](sql-database-elastic-query-overview.md). |
| Transações elásticas | [Transações distribuídas entre bancos de dados na nuvem](sql-database-elastic-transactions-overview.md). |
|Comentários de concessão de memória (modo de linha) (abaixo de nível de compatibilidade 150)|Para obter mais informações, confira [Comentários sobre concessão de memória (modo de linha)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Editor de consultas no Portal do Azure |Para obter mais informações, confira como [Usar o editor de consultas SQL do portal do Azure para se conectar e consultar dados](sql-database-connect-query-portal.md).|
| Serviços de R / Machine Learning com bancos de dados individuais e pools elásticos |Para obter mais informações, consulte o artigo [Serviços de Machine Learning no Banco de Dados SQL do Azure](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
|Análise de SQL|Para obter mais informações, consulte o artigo [Análise de SQL do Azure](../azure-monitor/insights/azure-sql.md).|
|Compilação adiada da variável de tabela (abaixo do nível de compatibilidade 150)|Para obter mais informações, consulte o artigo [Compilação adiada de variável da tabela](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instance"></a>[Instância Gerenciada](#tab/managed-instance)

| Recurso | Detalhes |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Pools da instância</a> | Uma maneira conveniente e econômica de migrar instâncias do SQL menores para a nuvem. |
| <a href="https://aka.ms/managed-instance-aadlogins">Entidades de segurança do servidor do Azure AD (logons) de nível de entidade</a> | Crie logons no nível do servidor usando a instrução<a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CRIAR LOGON DO PROVEDOR EXTERNO</a>. |
| [Replicação transacional](sql-database-managed-instance-transactional-replication.md) | Replique as alterações de suas tabelas em outros bancos de dados colocados em instâncias gerenciadas, bancos de dados individuais ou instâncias de SQL Server, ou atualize suas tabelas quando algumas linhas forem alteradas em outras instâncias gerenciadas ou instâncias do SQL Server. Para obter mais informações, consulte [Configurar a replicação em um banco de dados de instância gerenciada do Banco de Dados SQL do Azure](replication-with-sql-database-managed-instance.md). |
| Detecção de ameaças |Para obter mais informações, consulte o artigo [Configurar a detecção de ameaças na instância gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-threat-detection.md).|
| Retenção de backup de longo prazo | Para obter mais informações, consulte [Configurar a retenção de backup de longo prazo na instância gerenciada do banco de dados SQL do Azure](sql-database-managed-instance-long-term-backup-retention-configure.md), que está atualmente em visualização pública limitada. | 

---

## <a name="managed-instance---new-features-and-known-issues"></a>Instância gerenciada - novos recursos e problemas conhecidos

### <a name="managed-instance-h2-2019-updates"></a>Atualizações do segundo semestre de 2019 da instância gerenciada

- [A configuração de sub-rede auxiliada por serviço](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) Uma maneira segura e conveniente de gerenciar a configuração de sub-rede em que você controla o tráfego de dados enquanto a instância gerenciada garante o fluxo ininterrupto de tráfego de gerenciamento
- [A TDE (Transparent Data Encryption) com Bring Your Own Key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) permite o cenário de BYOK (traga sua própria chave) para proteção de dados em repouso e permite que as organizações separem as tarefas de gerenciamento para chaves e dados.
- [Grupos de failover automático](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) permitem que você replique todos os bancos de dados da instância primária para uma instância secundária em outra região.
- Configure o comportamento da instância gerenciada com [sinalizadores de rastreamento globais](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/).

### <a name="managed-instance-h1-2019-updates"></a>Atualizações do primeiro semestre de 2019 da instância gerenciada

Os seguintes recursos estão habilitados no modelo de implantação de instância gerenciada em H1 2019:
  - Suporte para assinaturas com <a href="https://aka.ms/sql-mi-visual-studio-subscribers"> Crédito mensal do Azure para assinantes do Visual Studio </a> e maiores [limites regionais](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
  - Suporte para <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 e SharePoint 2019 </a> e <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central </a>
  - Crie instâncias com a <a href="https://aka.ms/managed-instance-collation">ordenação em nível de servidor</a> e <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">fuso horário</a>de sua escolha.
  - Instâncias gerenciadas são protegidas com <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">firewall interno</a>.
  - Configure as instâncias para usar [pontos de extremidade públicos](sql-database-managed-instance-public-endpoint-configure.md), [substituição de proxy](sql-database-connectivity-architecture.md#connection-policy) conexão para obter melhor desempenho de rede, <a href="https://aka.ms/four-cores-sql-mi-update"> 4 vCores na geração de hardware Gen5</a> ou <a href="https://aka.ms/managed-instance-configurable-backup-retention">Configure a retenção de backup de até 35 dias</a> para a restauração pontual. [A retenção de backup de longo prazo](sql-database-long-term-retention.md#managed-instance-support) (até 10 anos) está atualmente em visualização pública limitada.  
  - As novas funcionalidades permitem que você <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">faça a restauração geográfica de seu banco de dados para outro data center usando o PowerShell</a>, [renomeie o banco de dados](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/) e [excluir o cluster virtual](sql-database-managed-instance-delete-virtual-cluster.md).
  - A nova [função de colaborador de instância](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) interna permite a conformidade de SoD (separação de imposto) com princípios de segurança e conformidade com os padrões corporativos.
  - A instância gerenciada está disponível nas seguintes regiões do Azure Governamental para GA (US Gov Texas, US Gov Arizona), bem como Norte da China 2 e Leste da China 2. Ele também está disponível nas seguintes regiões públicas: Austrália Central, Austrália Central 2, sul do Brasil, sul da França, EAU Central, Norte dos EAU, norte da África do Sul, oeste da África do Sul.

### <a name="known-issues"></a>Problemas conhecidos

|Problema  |Data descoberta  |Status  |Data resolvida  |
|---------|---------|---------|---------|
|[A restauração do backup manual sem soma de verificação pode falhar](#restoring-manual-backup-without-checksum-might-fail)|Maio de 2020|Tem solução alternativa| |
|[O agente não responde após a modificação, desabilitação ou habilitação de trabalhos existentes](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|Maio de 2020|Reduzido automaticamente| |
|[Permissões no grupo de recursos não aplicadas à Instância Gerenciada](#permissions-on-resource-group-not-applied-to-managed-instance)|Fev 2020|Tem solução alternativa| |
|[Limitação de failover manual por meio do portal para grupos de failover](#limitation-of-manual-failover-via-portal-for-failover-groups)|Jan 2020|Tem solução alternativa| |
|[As funções do SQL Agent precisam de permissões EXECUTE explícitas para logons não sysadmin](#in-memory-oltp-memory-limits-are-not-applied)|Dez 2019|Tem solução alternativa| |
|[Os trabalhos do SQL Agent podem ser interrompidos pela reinicialização do processo do agente](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Dez 2019|Resolvido|Mar 2020|
|[Não há suporte para logons e usuários do AAD no SSDT](#aad-logins-and-users-are-not-supported-in-ssdt)|Nov 2019|Sem solução| |
|[Os limites de memória OLTP na memória não são aplicados](#in-memory-oltp-memory-limits-are-not-applied)|Out 2019|Tem solução alternativa| |
|[Erro incorreto retornado ao tentar remover um arquivo que não está vazio](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|Out 2019|Tem solução alternativa| |
|[A alteração da camada de serviço e a criação de operações de instância são bloqueadas pela restauração de banco de dados em andamento](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Set 2019|Tem solução alternativa| |
|[O Resource Governor na camada de serviço comercialmente crítico talvez precise ser reconfigurado após o failover](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Set 2019|Tem solução alternativa| |
|[As caixas de diálogo de Service Broker entre bancos de dados devem ser reinicializadas após a atualização da camada de serviço](#cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade)|Ago 2019|Tem solução alternativa| |
|[Não há suporte para a representação de tipos de logon do Azure AD](#impersonification-of-azure-ad-login-types-is-not-supported)|Jul 2019|Sem solução| |
|[@query parâmetro sem suporte no sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|Abr 2019|Sem solução| |
|[A replicação transacional deve ser reconfigurada após o failover geográfico](#transactional-replication-must-be-reconfigured-after-geo-failover)|Mar 2019|Sem solução| |
|[O banco de dados temporário é usado durante a operação de restauração](#temporary-database-is-used-during-restore-operation)||Tem solução alternativa| |
|[A estrutura TEMPDB e o conteúdo são recriados](#tempdb-structure-and-content-is-re-created)| |Sem solução| |
|[Excedendo o espaço de armazenamento com arquivos de banco de dados pequenos](#exceeding-storage-space-with-small-database-files)| |Tem solução alternativa| |
|[Valores de GUID mostrados em vez de nomes de banco de dados](#guid-values-shown-instead-of-database-names) ||Tem solução alternativa| |
|[Os logs de erros não são persistentes](#error-logs-arent-persisted)||Sem solução| |
|[Os módulos CLR e os servidores vinculados às vezes não podem fazer referência ao endereço IP local](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)| |Tem solução alternativa| |
|[O escopo de transação em dois bancos de dados dentro da mesma instância não é compatível](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)| |Resolvido|Mar 2020|
|Consistência do banco de dados não verificada usando DBCC CHECKDB após restaurar banco de dados do Armazenamento de blobs do Azure.| |Resolvido|Nov 2019|
|A restauração de banco de dados pontual da camada comercialmente crítica para Uso Geral não terá sucesso se o banco de dados de origem contiver objetos OLTP na memória.| |Resolvido|Out 2019|
|Recurso de Database Mail com servidores de email externos (não Azure) usando conexão segura| |Resolvido|Out 2019|
|Bancos de dados contidos não têm suporte na instância gerenciada| |Resolvido|Ago 2019|

### <a name="restoring-manual-backup-without-checksum-might-fail"></a>A restauração do backup manual sem CHECKSUM pode falhar

Em determinadas circunstâncias, o backup manual de bancos de dados feito na instância gerenciada sem CHECKSUM pode falhar ao ser restaurado. Nesse caso, tente restaurar o backup novamente até que ele seja bem-sucedido.

**Solução alternativa**: Faça backups manuais de bancos de dados na instância gerenciada com CHECKSUM habilitada.

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>O agente não responde após a modificação, desabilitação ou habilitação de trabalhos existentes

Em determinadas circunstâncias, modificar um trabalho existente, desabilitar ou habilitá-lo pode fazer com que o agente deixe de responder. O problema é mitigado automaticamente após a detecção, resultando na reinicialização do processo do agente.

### <a name="permissions-on-resource-group-not-applied-to-managed-instance"></a>Permissões no grupo de recursos não aplicadas à Instância Gerenciada

Quando aplicada a um grupo de recursos (RG), a função de RBAC do colaborador da Instância Gerenciada não é aplicada à Instância Gerenciada e não tem efeito.

**Solução alternativa**: Configure a função do colaborador da Instância Gerenciada para usuários no nível de assinatura.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Limitação de failover manual por meio do portal para grupos de failover

Se o grupo de failover se estender entre instâncias em diferentes assinaturas ou grupos de recursos do Azure, o failover manual não poderá ser iniciado a partir da instância primária no grupo de failover.

**Solução alternativa**: Inicie o failover por meio do portal da instância geográfica-secundária.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>As funções do SQL Agent precisam de permissões EXECUTE explícitas para logons não sysadmin

Se os logons não-sysadmin forem adicionados a qualquer uma das funções de banco de dados fixas do [SQL Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles), existe um problema no qual as permissões de execução explícitas precisam ser concedidas aos procedimentos armazenados mestres para que esses logons funcionem. Se este erro for encontrado, a mensagem de erro "A permissão EXECUTE foi negada no objeto <object_name> (Microsoft SQL Server, Erro: 229)" será exibida.

**Solução alternativa**: Depois de adicionar logons a qualquer uma das funções de banco de dados fixas do SQL Agent: SQLAgentUserRole, SQLAgentReaderRole ou SQLAgentOperatorRole, para cada um dos logons adicionados a essas funções, execute o script T-SQL abaixo para conceder explicitamente permissões de execução aos procedimentos armazenados listados.

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

**(Resolvido em março de 2020)** o SQL Agent cria uma nova sessão toda vez que o trabalho é iniciado, aumentando gradualmente o consumo de memória. Para evitar atingir o limite de memória interna que bloquearia a execução de trabalhos agendados, o processo do agente será reiniciado quando seu consumo de memória atingir o limite. Isso pode resultar na interrupção da execução de trabalhos em execução no momento da reinicialização.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>Os limites de memória OLTP na memória não são aplicados

A camada de serviço comercialmente crítica não aplicará corretamente [limites máximos de memória para objetos com otimização de memória](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space) em alguns casos. A instância gerenciada pode permitir que a carga de trabalho use mais memória para operações OLTP na memória, o que pode afetar a disponibilidade e a estabilidade da instância. As consultas OLTP na memória que estão atingindo os limites podem não falhar imediatamente. Esse problema será corrigido em breve. As consultas que usam mais memória OLTP na memória falharão mais cedo se atingirem os [limites](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space).

**Solução alternativa:** [Monitorar o uso de armazenamento OLTP na memória](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring) usando [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) para garantir que a carga de trabalho não esteja usando mais do que a memória disponível. Aumente os limites de memória que dependem do número de vCores ou otimize sua carga de trabalho para usar menos memória.

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Erro incorreto retornado ao tentar remover um arquivo que não está vazio

SQL Server/Instância Gerenciada [não permite que o usuário descarte um arquivo que não esteja vazio](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Se você tentar remover um arquivo de dados não vazio usando a `ALTER DATABASE REMOVE FILE`instrução, o erro `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` não será retornado imediatamente. A Instância Gerenciada continuará tentando descartar o arquivo, e a operação falhará após 30 min com `Internal server error`.

**Solução alternativa**: Remova o conteúdo do arquivo usando o comando `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)`. Se esse for o único arquivo no grupo de arquivos, você precisará excluir dados da tabela ou da partição associada a esse grupo de arquivos antes de reduzir o arquivo e, opcionalmente, carregar esses dados em outra tabela/partição.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>A alteração da camada de serviço e a criação de operações de instância são bloqueadas pela restauração de banco de dados em andamento

A instrução de `RESTORE` contínua, o processo de migração do serviço de migração de dados e a restauração pontual interna bloquearão a atualização da camada de serviço ou o redimensionamento da instância existente e a criação de novas instâncias até que o processo de restauração seja concluído. O processo de restauração bloqueará essas operações nas instâncias gerenciadas e nos pools de instância na mesma sub-rede em que o processo de restauração está em execução. As instâncias em pools de instâncias não são afetadas. A criação ou a alteração das operações da camada de serviço não falharão ou atingirão o tempo limite - elas continuarão quando o processo de restauração for concluído ou cancelado.

**Solução alternativa**: Aguarde até que o processo de restauração seja concluído ou cancele o processo de restauração se a operação de criação ou atualização da camada de serviço tiver prioridade mais alta.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>O Resource Governor na camada de serviço comercialmente crítica talvez precise ser reconfigurado após o failover

O recurso [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) que permite limitar os recursos atribuídos à carga de trabalho do usuário pode classificar incorretamente alguma carga de trabalho do usuário após o failover ou a alteração da camada de serviço iniciada pelo usuário (por exemplo, a alteração do tamanho máximo do armazenamento de instância vCore ou máximo).

**Solução alternativa**: Execute `ALTER RESOURCE GOVERNOR RECONFIGURE` periodicamente ou como parte do trabalho do SQL Agent que executa a tarefa SQL quando a instância for iniciada se você estiver usando [Resource Governor](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>As caixas de diálogo de Service Broker entre bancos de dados devem ser reinicializadas após a atualização da camada de serviço

As caixas de diálogo de Service Broker de banco de dados cruzado deixarão de entregar as mensagens para os serviços em outros bancos de dados após a operação de alteração da camada de serviço. As mensagens **não são perdidas** e podem ser encontradas na fila do remetente. Qualquer alteração de tamanho de armazenamento de instância ou vCores no Instância Gerenciada fará com que o `service_broke_guid` valor na exibição [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) seja alterado para todos os bancos de dados. Qualquer `DIALOG` criado usando a instrução [BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) que referencia os agentes de serviço em outro banco de dados interromperá a entrega de mensagens ao serviço de destino.

**Solução alternativa:** Pare qualquer atividade que use conversas de caixa de diálogo Service Broker de banco de dados antes de atualizar a camada de serviço e depois as reinicie. Se houver mensagens restantes que não são entregues após a alteração da camada de serviço, leia as mensagens da fila de origem e reenvie-as para a fila de destino.

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>Não há suporte para a representação de tipos de logon do Azure AD

Não há suporte para a representação usando `EXECUTE AS USER` ou `EXECUTE AS LOGIN` das seguintes entidades do AAD:
-    Usuários com alias do AAD. O seguinte erro é retornado nesse caso `15517`.
- Logons e usuários do AAD com base em aplicativos do AAD ou entidades de serviço. Os seguintes erros são retornados nesse caso `15517` e `15406`.

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@query parâmetro sem suporte no sp_send_db_mail

O `@query`parâmetro no procedimento [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) não funciona.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>A replicação transacional deve ser reconfigurada após o failover geográfico

Se a replicação transacional estiver habilitada em um banco de dados em um grupo de failover automático, o administrador da instância gerenciada deverá limpar todas as publicações no antigo primário e reconfigurá-los no novo primário após a ocorrência de um failover para outra região. Consulte [Replicação](sql-database-managed-instance-transact-sql-information.md#replication) para obter mais detalhes.

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>Não há suporte para logons e usuários do AAD no SSDT

SQL Server Data Tools não dão suporte total a logons e usuários do Azure Active Directory.

### <a name="temporary-database-is-used-during-restore-operation"></a>O banco de dados temporário é usado durante a operação de restauração

Quando um banco de dados estiver restaurando em Instância Gerenciada, o serviço de restauração criará primeiro um banco de dados vazio com o nome desejado para alocar o nome na instância. Após algum tempo, esse banco de dados será descartado e a restauração do banco de dados real será iniciada. O banco de dados que tem no estado *Restaurar* terá um valor temporário de GUID aleatório em vez de nome. O nome temporário será alterado para o nome desejado especificado na instrução `RESTORE` depois que o processo de restauração for concluído. Na fase inicial, o usuário pode acessar o banco de dados vazio e, até mesmo, criar tabelas ou carregar dados nesse banco de dados. Esse banco de dados temporário será descartado quando o serviço de restauração iniciar a segunda fase.

**Solução alternativa**: Não acesse o banco de dados que você está restaurando até ver que a restauração foi concluída.

### <a name="tempdb-structure-and-content-is-re-created"></a>A estrutura TEMPDB e o conteúdo são recriados

O banco de dados `tempdb` sempre é dividido em 12 arquivos de dados e a estrutura do arquivo não pode ser alterada. Esse tamanho máximo por arquivo não pode ser alterado e não possível adicionar novos arquivos a `tempdb`. `Tempdb` é sempre recriado como um banco de dados vazio quando a instância inicia ou faz failover, e quaisquer alterações feitas no `tempdb` não serão preservadas.

### <a name="exceeding-storage-space-with-small-database-files"></a>Excedendo o espaço de armazenamento com arquivos de banco de dados pequenos

As instruções `CREATE DATABASE`, `ALTER DATABASE ADD FILE` e `RESTORE DATABASE` podem falhar porque a instância pode alcançar o limite de armazenamento do Azure.

Cada instância gerenciada de Uso Geral tem até 35 TB de armazenamento reservado para o espaço em disco Premium do Azure. Cada arquivo de banco de dados é colocado em um disco físico separado. Tamanhos de disco podem ser 128 GB, 256 GB, 512 GB, 1 TB ou 4 TB. O espaço não utilizado no disco não é cobrado, mas a soma total dos tamanhos de Disco Premium do Azure não pode exceder 35 TB. Em alguns casos, uma instância gerenciada que não precisa de 8 TB no total pode exceder o limite de 35 TB do Azure em tamanho de armazenamento, devido à fragmentação interna.

Por exemplo, uma instância gerenciada de Uso Geral pode ter um arquivo grande que seja de 1,2 TB de tamanho colocado em um disco de 4 TB. Ele também pode ter 248 arquivos com tamanho de 1 GB cada um colocado em discos separados de 128 GB. Neste exemplo:

- O tamanho do armazenamento em disco total alocado é de 1 x 4 TB + 248 x 128 GB = 35 TB.
- O total de espaço reservado para os bancos de dados na instância é de 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Esse exemplo ilustra que, em determinadas circunstâncias, devido a uma distribuição específica de arquivos, uma instância gerenciada pode alcançar os 35 TB reservados para o Disco Premium do Azure anexado quando talvez isso não seja esperado.

Neste exemplo, bancos de dados existentes continuarão a funcionar e podem crescer sem problemas, desde que não sejam adicionados novos arquivos. No entanto os novos bancos de dados não podem ser criados ou restaurados porque não há espaço suficiente para novas unidades de disco, mesmo se o tamanho total de todos os bancos de dados não alcançar o limite de tamanho de instância. O erro retornado nesse caso não é claro.

Você pode [identificar o número de arquivos restantes](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) usando exibições do sistema. Se você atingir esse limite, tente [esvaziar e excluir alguns dos arquivos menores usando a instrução DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) ou alterne para a [Camada comercialmente crítica, que não tem esse limite](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>Valores de GUID mostrados em vez de nomes de banco de dados

Várias entradas de exibições do sistema, contadores de desempenho, mensagens de erro, XEvents e logs de erros exibem identificadores do banco de dados GUID em vez dos nomes reais do banco de dados. Não dependa desses identificadores GUID porque eles serão substituídos por nomes de banco de dados reais no futuro.

**Solução alternativa**: Use a exibição sys.databases para resolver o nome do banco de dados físico, especificado na forma de identificadores de banco de dados GUID

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>Os logs de erros não são persistentes

Os logs de erros disponíveis na instância gerenciada não são persistentes e seu tamanho não está incluído no limite de armazenamento máximo. Os logs de erros podem ser apagados automaticamente no caso de failover. Pode haver lacunas no histórico do log de erros porque a instância gerenciada foi movida várias vezes em várias máquinas virtuais.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>O escopo de transação em dois bancos de dados dentro da mesma instância não é compatível

**(Resolvido em Março de 2020)** A classe `TransactionScope` em .NET não funciona se duas consultas são enviadas para os dois bancos de dados dentro da mesma instância no mesmo escopo de transação:

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

**Solução alternativa (não é necessária desde março de 2020):** Usar [SqlConnection.ChangeDatabase(String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) para usar outro banco de dados no contexto de conexão em vez de usar duas conexões.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Os módulos CLR e os servidores vinculados às vezes não podem fazer referência ao endereço IP local

Os módulos CLR colocados em uma instância gerenciada e em servidores vinculados ou consultas distribuídas que referenciam a instância atual às vezes não podem resolver o IP da instância local. Esse é um problema temporário.

**Solução alternativa:** Usar conexões de contexto no módulo CLR, se possível.

## <a name="updates"></a>Atualizações

Para atualizações e aprimoramentos do Banco de Dados SQL, consulte [atualizações do serviço de Banco de Dados SQL](https://azure.microsoft.com/updates/?product=sql-database).

Para encontrar atualizações e melhorias para outros serviços do Azure, confira [Atualizações de serviço](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Contribuir com o conteúdo

Para enviar sua contribuição para a documentação do SQL Database, confira o [Guia do colaborador do Docs](https://docs.microsoft.com/contribute/).
