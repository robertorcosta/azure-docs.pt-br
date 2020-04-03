---
title: Notas de versão
description: Conheça os novos recursos e melhorias no serviço de banco de dados SQL do Azure e na documentação do Banco de Dados SQL do Azure
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: sstein
ms.openlocfilehash: 9fa93af72c2869efd7b6d2f1e8b96b0e667f8b16
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607543"
---
# <a name="sql-database-release-notes"></a>Notas de versão do Banco de Dados SQL

Este artigo lista os recursos do Banco de Dados SQL que estão atualmente em visualização pública. Para obter atualizações e melhorias do Banco de Dados SQL, consulte [atualizações do serviço SQL Database](https://azure.microsoft.com/updates/?product=sql-database). Para obter atualizações e melhorias em outros serviços do Azure, consulte [Atualizações do Serviço](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Recursos em visualização pública

### <a name="single-database"></a>[Banco de dados individual](#tab/single-database)

| Recurso | Detalhes |
| ---| --- |
| Novas gerações de hardware da série FSV2 e M| Para obter informações, consulte [As gerações de hardware](sql-database-service-tiers-vcore.md#hardware-generations).|
| [Link privado do Azure](https://azure.microsoft.com/updates/private-link-now-available-in-preview/)| O Private Link simplifica a arquitetura da rede e protege a conexão entre os pontos finais no Azure, mantendo dados na rede Azure, eliminando assim a exposição à internet. O Private Link também permite que você crie e preste seus próprios serviços no Azure. |
| Recuperação acelerada do banco de dados com bancos de dados únicos e pools elásticos | Para obter informações, consulte [Recuperação acelerada do banco de dados](sql-database-accelerated-database-recovery.md).|
|Distinção de contagem aproximada|Para obter informações, consulte [Contagem Aproximada Distinta](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Modo de lote no Rowstore (sob o nível de compatibilidade 150)|Para obter informações, consulte [Modo de lote na Rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Descoberta e classificação de dados  |Para obter informações, consulte [o Banco de Dados Azure SQL e a classificação de & de descoberta de dados do SQL Data Warehouse](sql-database-data-discovery-and-classification.md).|
| Trabalhos de banco de dados elástico | Para obter informações, consulte [Criar, configurar e gerenciar trabalhos elásticos](elastic-jobs-overview.md). |
| Consultas elásticas | Para obter informações, consulte [Visão geral da consulta Elástica](sql-database-elastic-query-overview.md). |
| Transações elásticas | [Transações distribuídas em bancos de dados em nuvem](sql-database-elastic-transactions-overview.md). |
|Feedback de concessão de memória (modo de linha) (sob o nível de compatibilidade 150)|Para obter informações, consulte [Feedback de concessão de memória (modo de linha)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Editor de consulta no portal Azure |Para obter informações, consulte [Use o editor de consulta SQL do portal Azure para conectar e consultar dados](sql-database-connect-query-portal.md).|
| Serviços r / aprendizado de máquina com bancos de dados únicos e piscinas elásticas |Para obter informações, consulte [Serviços de aprendizado de máquina no banco de dados SQL do Azure](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
|Análise de SQL|Para obter informações, consulte [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).|
|Compilação diferida variável de tabela (sob o nível de compatibilidade 150)|Para obter informações, consulte [Compilação diferida de variáveis de tabela](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instance"></a>[Instância gerenciada](#tab/managed-instance)

| Recurso | Detalhes |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Pools da instância</a> | Uma maneira conveniente e econômica de migrar instâncias SQL menores para a nuvem. |
| <a href="https://aka.ms/managed-instance-aadlogins">Principais de servidor Ad azure em nível de instância (logins)</a> | Crie logins em nível de servidor usando create login da declaração <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">do provedor externo.</a> |
| [Replicação transacional](sql-database-managed-instance-transactional-replication.md) | Replique as alterações de suas tabelas em outros bancos de dados colocados em instâncias gerenciadas, bancos de dados únicos ou instâncias do SQL Server ou atualize suas tabelas quando algumas linhas forem alteradas em outras instâncias gerenciadas ou na instância do SQL Server. Para obter informações, consulte [Configurar a replicação em um banco de dados gerenciado do Banco de Dados SQL do Azure](replication-with-sql-database-managed-instance.md). |
| Detecção de ameaças |Para obter informações, consulte [Configurar a detecção de ameaças na instância gerenciada do Azure SQL Database](sql-database-managed-instance-threat-detection.md).|

---

## <a name="managed-instance---new-features-and-known-issues"></a>Instância gerenciada - novos recursos e problemas conhecidos

### <a name="managed-instance-h2-2019-updates"></a>Atualizações da instância gerenciada H2 2019

- [Configuração de sub-rede auxiliada por serviço](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) Uma maneira segura e conveniente de gerenciar a configuração da sub-rede onde você controla o tráfego de dados enquanto a instância gerenciada garante o fluxo ininterrupto do tráfego de gerenciamento
- [A criptografia de dados transparente (TDE) com o Bring Your Own Key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) permite o cenário de trazer sua própria chave (BYOK) para proteção de dados em repouso e permite que as organizações separem as funções de gerenciamento de chaves e dados.
- [Os grupos de failover automático](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) permitem replicar todos os bancos de dados da instância primária para uma instância secundária em outra região.
- Configure seu comportamento de instância gerenciada com [sinalizadores de rastreamento Global](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/).

### <a name="managed-instance-h1-2019-updates"></a>Atualizações da instância gerenciada H1 2019

Os seguintes recursos estão habilitados no modelo de implantação de instância gerenciada no H1 2019:
  - Suporte para assinaturas com <a href="https://aka.ms/sql-mi-visual-studio-subscribers">crédito mensal do Azure para assinantes do Visual Studio</a> e aumento dos limites [regionais.](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)
  - Suporte para <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 e SharePoint 2019 </a> e <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central </a>
  - Crie instâncias com <a href="https://aka.ms/managed-instance-collation">a colagem no nível</a> do servidor e o <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">fuso horário</a> de sua escolha.
  - As instâncias gerenciadas agora são protegidas com <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">firewall integrado</a>.
  - Configure instâncias para usar [pontos finais públicos,](sql-database-managed-instance-public-endpoint-configure.md)conexão [de substituição proxy](sql-database-connectivity-architecture.md#connection-policy) para obter melhor desempenho de rede, 4 <a href="https://aka.ms/four-cores-sql-mi-update">vCores na geração de hardware Gen5</a> ou Configurar retenção de <a href="https://aka.ms/managed-instance-configurable-backup-retention">backup até 35 dias</a> para restauração point-in-time. A retenção de backup a longo prazo (até 10 anos) ainda não está ativada para que você possa usar <a href="https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server">backups somente</a> para cópia como uma alternativa.
  - Novas funcionalidades permitem que você <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">restaure geografemente seu banco de dados para outro data center usando PowerShell,</a> [renomeie banco de dados,](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/) [exclua cluster virtual](sql-database-managed-instance-delete-virtual-cluster.md).
  - A nova [função de contribuinte](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) de instância incorporada permite a separação do cumprimento do serviço (SoD) dos princípios de segurança e o cumprimento das normas corporativas.
  - A instância gerenciada está disponível nas seguintes regiões do Governo Azure para ga (US Gov Texas, US Gov Arizona) bem como na China North 2 e China East 2. Também está disponível nas seguintes regiões públicas: Austrália Central, Austrália Central 2, Brasil Sul, França Sul, Emirados Central dos Emirados Unidos, Norte dos Emirados Americanos, África do Sul Norte, África do Sul Oeste.

### <a name="known-issues"></a>Problemas conhecidos

|Problema  |Data descoberta  |Status  |Data resolvida  |
|---------|---------|---------|---------|
|[Permissões em grupo de recursos não aplicadas à Instância Gerenciada](#permissions-on-resource-group-not-applied-to-managed-instance)|Fev 2020|Tem solução de solução||
|[Limitação do failover manual via portal para grupos de failover](#limitation-of-manual-failover-via-portal-for-failover-groups)|Jan 2020|Tem solução de solução||
|[As funções do Agente SQL precisam de permissões execute explícitas para logins não-sysadmin](#in-memory-oltp-memory-limits-are-not-applied)|Dez 2019|Tem solução de solução||
|[Os trabalhos do Agente SQL podem ser interrompidos pela reinicialização do processo do Agente](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Dez 2019|Sem solução de solução|Mar 2020|
|[Logins AAD e usuários não são suportados no SSDT](#aad-logins-and-users-are-not-supported-in-ssdt)|Novembro 2019|Sem solução de solução||
|[Os limites de memória OLTP na memória não são aplicados](#in-memory-oltp-memory-limits-are-not-applied)|outubro de 2019|Tem solução de solução||
|[Erro errado retornado ao tentar remover um arquivo que não está vazio](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|outubro de 2019|Tem solução de solução||
|[Alterar o nível de serviço e criar operações de instância são bloqueados pela restauração contínua do banco de dados](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Setembro 2019|Tem solução de solução||
|[O governador de recursos no nível de serviço Business Critical pode precisar ser reconfigurado após o failover](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Setembro 2019|Tem solução de solução||
|[As caixas de diálogo saem do Inter-Database Service Broker devem ser reinicializadas após a atualização do nível de serviço](#cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade)|Ago 2019|Tem solução de solução||
|[A impersonificação dos tipos de login do Azure AD não é suportada](#impersonification-of-azure-ad-login-types-is-not-supported)|Jul 2019|Sem solução de solução||
|[@queryparâmetro não suportado em sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|Apr 2019|Sem solução de solução||
|[A replicação transacional deve ser reconfigurada após o geo-failover](#transactional-replication-must-be-reconfigured-after-geo-failover)|Mar 2019|Sem solução de solução||
|[Banco de dados temporário é usado durante a operação RESTORE](#temporary-database-is-used-during-restore-operation)||Tem solução de solução||
|[Estrutura e conteúdo tempdb é recriado](#tempdb-structure-and-content-is-re-created)||Sem solução de solução||
|[Excedendo o espaço de armazenamento com arquivos de banco de dados pequenos](#exceeding-storage-space-with-small-database-files)||Tem solução de solução||
|[Valores GUID mostrados em vez de nomes de banco de dados](#guid-values-shown-instead-of-database-names)||Tem solução de solução||
|[Os registros de erro não são persistidos](#error-logs-arent-persisted)||Sem solução de solução||
|[O escopo de transação em dois bancos de dados dentro da mesma instância não é suportado](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Tem solução de solução||
|[Módulos CLR e servidores vinculados às vezes não podem referenciar um endereço IP local](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Tem solução de solução||
|Consistência do banco de dados não verificada usando DBCC CHECKDB após restaurar banco de dados do Azure Blob Storage.||Resolvido|Novembro 2019|
|A restauração do banco de dados point-in-time do nível Business Critical para o nível De Propósito Geral não terá sucesso se o banco de dados de origem contiver objetos OLTP na memória.||Resolvido|outubro de 2019|
|Recurso de correio de banco de dados com servidores de e-mail externos (não-Azure) usando conexão segura||Resolvido|outubro de 2019|
|Bancos de dados contidos não suportados em instância gerenciada||Resolvido|Ago 2019|

### <a name="permissions-on-resource-group-not-applied-to-managed-instance"></a>Permissões em grupo de recursos não aplicadas à instância gerenciada

A função RBAC do contribuinte de instância gerenciada quando aplicada a um grupo de recursos (RG) não é aplicada à Instância Gerenciada e não tem efeito.

**Solução de solução**: Configuração função contribuinte de instância gerenciada para usuários no nível de assinatura.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Limitação do failover manual via portal para grupos de failover

Se o grupo failover se estender por instâncias em diferentes assinaturas do Azure ou grupos de recursos, o failover manual não pode ser iniciado a partir da instância primária no grupo failover.

**Solução :** Inicie failover via portal a partir da instância geosecundária.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>As funções do Agente SQL precisam de permissões execute explícitas para logins não-sysadmin

Se os logins não-sysadmin forem adicionados a qualquer uma das funções de banco de [dados fixos do SQL Agent,](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles)existe um problema no qual permissões explícitas de EXECUTE precisam ser concedidas aos procedimentos armazenados mestres para que esses logins funcionem. Se esse problema for encontrado, a mensagem de erro "A permissão EXECUTE foi negada no objeto <object_name> (Microsoft SQL Server, Error: 229)" será exibida.

**Solução de solução**: Uma vez que você adicione logins a qualquer uma das funções de banco de dados fixos do SQL Agent: SQLAgentUserRole, SQLAgentReaderRole ou SQLAgentOperatorRole, para cada um dos logins adicionados a essas funções execute o script T-SQL abaixo para conceder explicitamente permissões EXECUTE aos procedimentos armazenados listados.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>Os trabalhos do Agente SQL podem ser interrompidos pela reinicialização do processo do Agente

O SQL Agent cria uma nova sessão a cada vez que o trabalho é iniciado, aumentando gradualmente o consumo de memória. Para evitar atingir o limite de memória interna que bloquearia a execução de trabalhos programados, o processo do Agente será reiniciado assim que seu consumo de memória atingir o limite. Pode resultar na interrupção da execução de trabalhos executados no momento da reinicialização.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>Os limites de memória OLTP na memória não são aplicados

O nível de serviço business critical não aplicará corretamente [limites máximos de memória para objetos otimizados pela memória](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space) em alguns casos. A instância gerenciada pode permitir que a carga de trabalho use mais memória para operações OLTP na memória, o que pode afetar a disponibilidade e a estabilidade da instância. Consultas OLTP na memória que estão chegando aos limites podem não falhar imediatamente. Este problema será corrigido em breve. As consultas que usam mais memória OLTP na memória falharão mais cedo se atingirem os [limites](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space).

**Solução de solução:** [Monitorar o uso de armazenamento OLTP na memória](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring) usando o [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) para garantir que a carga de trabalho não esteja usando mais do que a memória disponível. Aumente os limites de memória que dependem do número de vCores ou otimize sua carga de trabalho para usar menos memória.

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Erro errado retornado ao tentar remover um arquivo que não está vazio

SQL Server/Instância Gerenciada [não permite que o usuário solte um arquivo que não esteja vazio](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Se você tentar remover um arquivo `ALTER DATABASE REMOVE FILE` de dados `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` não vazio usando a declaração, o erro não será imediatamente devolvido. A instância gerenciada continuará tentando soltar o arquivo e a `Internal server error`operação falhará após 30 min com .

**Solução de solução**: Remova `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` o conteúdo do arquivo usando o comando. Se este for o único arquivo no grupo de arquivos, você precisará excluir dados da tabela ou partição associada a este grupo de arquivos antes de encolher o arquivo e, opcionalmente, carregar esses dados em outra tabela/partição.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Alterar o nível de serviço e criar operações de instância são bloqueados pela restauração contínua do banco de dados

A `RESTORE` declaração contínua, o processo de migração do Serviço de Migração de Dados e a restauração de tempo de ponto incorporado bloquearão a atualização do nível de serviço ou o redimensionamento da instância existente e criarão novas instâncias até que o processo de restauração seja concluído. O processo de restauração bloqueará essas operações nas instâncias gerenciadas e nos pools de instâncias na mesma sub-rede onde o processo de restauração está sendo executado. As instâncias em pools de instâncias não são afetadas. As operações de nível de serviço não falharão ou serão concluídas - elas continuarão assim que o processo de restauração for concluído ou cancelado.

**Solução de solução**: Aguarde até que o processo de restauração termine ou cancele o processo de restauração se a criação ou a operação de nível de serviço de atualização tiver prioridade maior.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>O governador de recursos no nível de serviço Business Critical pode precisar ser reconfigurado após o failover

O recurso [Degovernador de recursos](/sql/relational-databases/resource-governor/resource-governor) que permite limitar os recursos atribuídos à carga de trabalho do usuário pode classificar incorretamente alguma carga de trabalho do usuário após failover ou alteração iniciada pelo usuário do nível de serviço (por exemplo, a alteração do tamanho máximo de armazenamento vCore ou max instância).

**Solução de** `ALTER RESOURCE GOVERNOR RECONFIGURE` solução : Execute periodicamente ou como parte do SQL Agent Job que executa a tarefa SQL quando a instância é iniciada se você estiver usando [O Governador de Recursos](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>As caixas de diálogo saem do Inter-Database Service Broker devem ser reinicializadas após a atualização do nível de serviço

As diálogos do Inter-Database Service Broker deixarão de entregar as mensagens aos serviços em outros bancos de dados após a operação do nível de serviço de alteração. As mensagens não são **perdidas** e podem ser encontradas na fila do remetente. Qualquer alteração de vCores ou tamanho de armazenamento `service_broke_guid` de instâncias na Instância Gerenciada, fará com que o valor na exibição [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) seja alterado para todos os bancos de dados. Qualquer `DIALOG` criado usando a declaração [START DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) que faz referência aos Corretores de Serviços em outro banco de dados deixará de entregar mensagens ao serviço de destino.

**Solução de solução:** Interrompa qualquer atividade que use conversas de diálogo do Service Broker de banco de dados antes de atualizar o nível de serviço e reinicialize-as depois. Se houver mensagens restantes que não forem entregues após a alteração do nível de serviço, leia as mensagens da fila de origem e envie-as para a fila de destino.

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>A impersonificação dos tipos de login do Azure AD não é suportada

Não é `EXECUTE AS USER` suportado o uso de representação ou `EXECUTE AS LOGIN` de seguir os principais AAD:
-   Usuários AAD com aliased. O erro a seguir é `15517`devolvido neste caso .
- Logins AAD e usuários com base em aplicativos AAD ou princípios de serviço. Os seguintes erros são `15517` devolvidos neste caso e `15406`.

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@queryparâmetro não suportado em sp_send_db_mail

O `@query` parâmetro no procedimento [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) não funciona.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>A replicação transacional deve ser reconfigurada após o geo-failover

Se a Replicação Transacional estiver habilitada em um banco de dados em um grupo de failover automático, o administrador de instância gerenciada deve limpar todas as publicações na antiga primária e reconfigurá-las na nova primária após o failover para outra região ocorrer. Consulte [Replicação](sql-database-managed-instance-transact-sql-information.md#replication) para obter mais detalhes.

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>Logins AAD e usuários não são suportados no SSDT

As ferramentas de dados do SQL Server não suportam totalmente os logins e usuários do diretório Ativo do Azure.

### <a name="temporary-database-is-used-during-restore-operation"></a>Banco de dados temporário é usado durante a operação RESTORE

Quando um banco de dados estiver restaurando em Instância Gerenciada, o serviço de restauração criará primeiro um banco de dados vazio com o nome desejado para alocar o nome na instância. Após algum tempo, este banco de dados será descartado e a restauração do banco de dados real será iniciada. O banco de dados que está em estado *de restauração* terá temporariamente um valor GUID aleatório em vez de nome. O nome temporário será alterado para o `RESTORE` nome desejado especificado na declaração assim que o processo de restauração for concluído. Na fase inicial, o usuário pode acessar o banco de dados vazio e até mesmo criar tabelas ou carregar dados neste banco de dados. Este banco de dados temporário será descartado quando o serviço de restauração iniciar a segunda fase.

**Solução :** Não acesse o banco de dados que você está restaurando até ver que a restauração está concluída.

### <a name="tempdb-structure-and-content-is-re-created"></a>Estrutura e conteúdo tempdb é recriado

O `tempdb` banco de dados é sempre dividido em 12 arquivos de dados e a estrutura do arquivo não pode ser alterada. O tamanho máximo por arquivo não pode ser alterado `tempdb`e novos arquivos não podem ser adicionados a . `Tempdb`é sempre recriado como um banco de dados vazio quando a `tempdb` instância inicia ou falha, e quaisquer alterações feitas não serão preservadas.

### <a name="exceeding-storage-space-with-small-database-files"></a>Excedendo o espaço de armazenamento com arquivos de banco de dados pequenos

`CREATE DATABASE`, `ALTER DATABASE ADD FILE`e `RESTORE DATABASE` as instruções podem falhar porque a instância pode atingir o limite de armazenamento do Azure.

Cada instância gerenciada de Propósito Geral tem até 35 TB de armazenamento reservado para o espaço do Disco Premium do Azure. Cada arquivo de banco de dados é colocado em um disco físico separado. Tamanhos de disco podem ser 128 GB, 256 GB, 512 GB, 1 TB ou 4 TB. O espaço não utilizado no disco não é carregado, mas a soma total dos tamanhos de disco Premium do Azure não pode exceder 35 TB. Em alguns casos, uma instância gerenciada que não precise de 8 TB no total pode exceder o limite de 35 TB Azure no tamanho do armazenamento devido à fragmentação interna.

Por exemplo, uma instância gerenciada de Propósito Geral pode ter um arquivo grande de 1,2 TB de tamanho colocado em um disco de 4 TB. Ele também pode ter 248 arquivos com tamanho de 1 GB cada um que são colocados em discos separados de 128 GB. Neste exemplo:

- O tamanho do armazenamento em disco total alocado é de 1 x 4 TB + 248 x 128 GB = 35 TB.
- O total de espaço reservado para os bancos de dados na instância é de 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Este exemplo ilustra que, em certas circunstâncias, devido a uma distribuição específica de arquivos, uma instância gerenciada pode atingir o limite de 35 TB reservado para um Disco Premium Azure conectado quando você pode não esperar que ele o faça.

Neste exemplo, os bancos de dados existentes continuam a funcionar e podem crescer sem qualquer problema, desde que novos arquivos não sejam adicionados. Novos bancos de dados não podem ser criados ou restaurados porque não há espaço suficiente para novos drives de disco, mesmo que o tamanho total de todos os bancos de dados não atinja o limite de tamanho da ocorrência. O erro que foi devolvido nesse caso não está claro.

Você pode [identificar o número de arquivos restantes](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) usando visualizações do sistema. Se você atingir esse limite, tente [esvaziar e excluir alguns dos arquivos menores usando a declaração DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) ou mudar para o [nível Business Critical, que não tem esse limite](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>Valores GUID mostrados em vez de nomes de banco de dados

Várias entradas de exibições do sistema, contadores de desempenho, mensagens de erro, XEvents e logs de erros exibem identificadores do banco de dados GUID em vez dos nomes reais do banco de dados. Não confie nesses identificadores GUID porque eles são substituídos por nomes reais de banco de dados no futuro.

**Solução de solução**: Use sys.databases view para resolver o nome real do banco de dados a partir do nome do banco de dados físico, especificado na forma de identificadores de banco de dados GUID

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>Os registros de erro não são persistidos

Os registros de erro disponíveis na instância gerenciada não são persistidos e seu tamanho não está incluído no limite máximo de armazenamento. Os registros de erro podem ser apagados automaticamente se ocorrer failover. Pode haver lacunas no histórico de registros de erros porque a Instância Gerenciada foi movida várias vezes em várias máquinas virtuais.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>O escopo de transação em dois bancos de dados dentro da mesma instância não é suportado

A `TransactionScope` classe em .NET não funciona se duas consultas forem enviadas para dois bancos de dados na mesma instância sob o mesmo escopo de transação:

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

Embora este código funcione com dados dentro da mesma instância, ele exigiu MSDTC.

**Solução de solução:** Use [SqlConnection.ChangeDatabase(String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) para usar outro banco de dados em um contexto de conexão em vez de usar duas conexões.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Módulos CLR e servidores vinculados às vezes não podem referenciar um endereço IP local

Módulos CLR colocados em uma instância gerenciada e servidores vinculados ou consultas distribuídas que fazem referência a uma instância atual às vezes não podem resolver o IP de uma instância local. Esse é um problema temporário.

**Solução de solução:** Use conexões de contexto em um módulo CLR, se possível.

## <a name="updates"></a>Atualizações

Para obter uma lista de atualizações e melhorias do Banco de Dados SQL, consulte [atualizações do serviço sql database](https://azure.microsoft.com/updates/?product=sql-database).

Para obter atualizações e melhorias em todos os serviços do Azure, consulte [atualizações do serviço](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Contribuir para o conteúdo

Para contribuir com a documentação do Banco de Dados SQL do Azure, consulte o [Guia de Contribuinte do Docs](https://docs.microsoft.com/contribute/).
