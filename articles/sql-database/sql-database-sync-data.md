---
title: Sincronização de Dados
description: Esta visão geral apresenta a Sincronização de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 08/20/2019
ms.openlocfilehash: 1ee2efbb8aebfc2f1a94c89edef6166898946d8a
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422531"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Sincronizar dados entre vários bancos de dados locais e de nuvem com a Sincronização de Dados SQL

Sincronização de Dados SQL é um serviço baseado no Banco de Dados SQL do Azure que lhe permite sincronizar os dados que você selecionar bidirecionalmente em vários bancos de dados SQL e instâncias do SQL Server.

> [!IMPORTANT]
> Azure SQL Data Sync does not support Azure SQL Database Managed Instance at this time.

## <a name="when-to-use-data-sync"></a>Quando usar a Sincronização de Dados

Data Sync is useful in cases where data needs to be kept updated across several Azure SQL databases or SQL Server databases. Estes são os casos de uso principais para Sincronização de Dados:

- **Sincronização de Dados Híbrida:** com a Sincronização de Dados, você pode manter os dados sincronizados entre os bancos de dados locais e os bancos de dados SQL do Azure para habilitar aplicativos híbridos. Esse recurso pode ser atraente para clientes que estejam avaliando a mudança para a nuvem e gostariam de colocar alguns dos seus aplicativos no Azure.
- **Aplicativos Distribuídos:** Em muitos casos, é útil separar diferentes cargas de trabalho em bancos de dados diferentes. Por exemplo, se você tiver um banco de dados de produção grande, mas você também precisa executar uma carga de trabalho de relatório ou análise de dados, é útil ter um segundo banco de dados para essa carga de trabalho adicional. Essa abordagem minimiza o impacto no desempenho da sua carga de trabalho de produção. Você pode usar a Sincronização de Dados para manter esses dois bancos de dados sincronizados.
- **Globally Distributed Applications:** Many businesses span several regions and even several countries/regions. Para minimizar a latência de rede, é melhor ter seus dados em uma região perto de você. Com a sincronização de dados, você pode facilmente manter os bancos de dados em regiões de todo o mundo sincronizados.

Data Sync isn't the preferred solution for the following scenarios:

| Cenário | Algumas soluções recomendadas |
|----------|----------------------------|
| Recuperação de desastres | [Backups com redundância geográfica do Azure](sql-database-automated-backups.md) |
| Escala de Leitura | [Usar réplicas somente leitura para balancear a carga de cargas de trabalho de consulta somente leitura (versão prévia)](sql-database-read-scale-out.md) |
| ETL (OLTP para OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) ou [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| Migração do SQL Server local para um Banco de Dados SQL do Azure | [Serviço de Migração de Banco de Dados do Azure](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="overview-of-sql-data-sync"></a>Visão geral da Sincronização de Dados SQL

A Sincronização de Dados é baseada em torno do conceito de um Grupo de Sincronização. Um Grupo de Sincronização é um grupo de bancos de dados que você deseja sincronizar.

A Sincronização de Dados usa uma topologia hub-spoke para sincronizar os dados. Você define um dos bancos de dados no grupo de sincronização como o Banco de Dados Hub. O restante dos bancos de dados são bancos de dados de membros. A sincronização ocorre apenas entre o Hub e membros individuais.

- O **Banco de Dados Hub** deve ser um Banco de Dados SQL do Azure.
- Os **bancos de dados de membro** podem ser Bancos de Dados SQL, bancos de dados locais do SQL Server ou instâncias do SQL Server em máquinas virtuais do Azure.
- The **Sync Database** contains the metadata and log for Data Sync. The Sync Database has to be an Azure SQL Database located in the same region as the Hub Database. O Banco de Dados de Sincronização é criado pelo cliente e é propriedade do cliente.

> [!NOTE]
> Se você usando um banco de dados local como um banco de dados de membro, você terá que [instalar e configurar um agente de sincronização local](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Sincronizar dados entre bancos de dados](media/sql-database-sync-data/sync-data-overview.png)

Um Grupo de Sincronização tem as seguintes propriedades:

- O **Esquema de Sincronização** descreve quais dados estão sendo sincronizados.
- A **Direção da Sincronização** pode ser bidirecional ou pode fluir em uma única direção. Ou seja, a Direção da Sincronização pode ser *Hub para Membro* ou *Membro para Hub*, ou ambos.
- O **Intervalo de Sincronização** descreve a frequência com a qual ocorre a sincronização.
- A **Política de Resolução de Conflito** é uma política em nível de grupo, que pode ser *Hub ganha* ou *Membro ganha*.

## <a name="how-does-data-sync-work"></a>Como a Sincronização de Dados funciona?

- **Controle de alterações de dados:** A Sincronização de Dados controla alterações usando os gatilhos inserir, atualizar e excluir. As alterações são registradas em uma tabela secundária do banco de dados do usuário. Note that BULK INSERT doesn't fire triggers by default. If FIRE_TRIGGERS isn't specified, no insert triggers execute. Adicionar a opção de FIRE_TRIGGERS para a Sincronização de dados rastrear essas inserções. 
- **Sincronização de dados:** a Sincronização de Dados é criada em um modelo Hub-Spoke. O Hub é sincronizado com cada membro individualmente. As alterações do Hub são baixadas para o membro e, em seguida, as alterações do membro são carregadas para o Hub.
- **Resolução de conflitos:** A Sincronização de Dados fornece duas opções para a resolução de conflito, *Hub ganha* ou *Membro ganha*.
  - Se você selecionar *Hub ganha*, as alterações no hub sempre substituem as alterações no membro.
  - Se você selecionar *Membro ganha*, as alterações no membro sempre substituem as alterações no hub. Se houver mais de um membro, o valor final depende de qual membro será sincronizado pela primeira vez.

## <a name="compare-data-sync-with-transactional-replication"></a>Comparar a sincronização de dados com a replicação transacional

| | Sincronização de Dados | Replicação transacional |
|---|---|---|
| Vantagens | – Suporte ativo-ativo<br/>– Bidirecional entre o Banco de Dados SQL do Azure e o local | – Menor latência<br/>– Consistência transacional<br/>– Reutilização da topologia existente após a migração |
| Desvantagens | – Latência de 5 minutos ou mais<br/>– Não há consistência transacional<br/>– Maior impacto do desempenho | – Não pode publicar do banco de dados individual ou em pool do Banco de Dados SQL do Azure<br/>– Alto custo de manutenção |

## <a name="get-started-with-sql-data-sync"></a>Introdução à Sincronização de Dados SQL

### <a name="set-up-data-sync-in-the-azure-portal"></a>Configurar a Sincronização de Dados no Portal do Azure

- [Configurar a Sincronização de Dados SQL do Azure](sql-database-get-started-sql-data-sync.md)
- Agente de Sincronização de Dados - [Agente de Sincronização de Dados para Sincronização de Dados SQL do Azure](sql-database-data-sync-agent.md)

### <a name="set-up-data-sync-with-powershell"></a>Configurar a Sincronização de Dados com o PowerShell

- [Usar o PowerShell para sincronização entre vários banco de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
- [Usar o PowerShell para sincronizar entre um Banco de Dados SQL do Azure e um banco de dados local do SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Revisar as práticas recomendadas para a Sincronização de Dados

- [Melhores práticas para a Sincronização de Dados SQL do Azure](sql-database-best-practices-data-sync.md)

### <a name="did-something-go-wrong"></a>Algo deu errado?

- [Solucionar problemas com a Sincronização de Dados SQL do Azure](sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>Consistência e desempenho

### <a name="eventual-consistency"></a>Consistência eventual

Since Data Sync is trigger-based, transactional consistency isn't guaranteed. Microsoft guarantees that all changes are made eventually and that Data Sync doesn't cause data loss.

### <a name="performance-impact"></a>Impacto sobre o desempenho

A Sincronização de Dados usa os gatilhos inserir, atualizar e excluir para controlar as alterações. Ela cria tabelas secundárias no banco de dados do usuário para controle de alterações. Essas atividades de controle de alterações têm um impacto sobre sua carga de trabalho do banco de dados. Avalie sua camada de serviço e faça a atualização necessário.

Provisionamento e desprovisionamento durante a criação do grupo de sincronização, atualização e exclusão também podem afetar o desempenho do banco de dados.

## <a name="sync-req-lim"></a> Requisitos e limitações

### <a name="general-requirements"></a>Requisitos gerais

- Cada tabela deve ter uma chave primária. Não altere o valor da chave primária em nenhuma linha. Se você tiver de alterar o valor de uma chave primária, exclua a linha e recrie-a com o novo valor de chave primária.

> [!IMPORTANT]
> Changing the value of an existing primary key will result in the following faulty behavior:
> - Data between hub and member can be lost even though sync does not report any issue.
> - Sync can fail because the tracking table has a non-existing row from source due to the primary key change.

- O isolamento de instantâneo deve estar habilitado. Para obter mais informações, consulte [Isolamento de instantâneo no SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Limitações gerais

- A table can't have an identity column that isn't the primary key.
- A primary key can't have the following data types: sql_variant, binary, varbinary, image, xml.
- Tenha cuidado ao usar os seguintes tipos de dados como uma chave primária, porque a precisão com suporte é apenas para o segundo: time, datatime, datetime2 e datetimeoffset.
- The names of objects (databases, tables, and columns) can't contain the printable characters period (.), left square bracket ([), or right square bracket (]).
- Azure Active Directory authentication isn't supported.
- Tables with same name but different schema (for example, dbo.customers and sales.customers) aren't supported.
- Columns with User Defined Data Types aren't supported

#### <a name="unsupported-data-types"></a>Tipos de dados sem suporte

- FileStream
- SQL/CLR UDT
- XMLSchemaCollection (suporte para XML)
- Cursor, RowVersion, Timestamp, Hierarchyid

#### <a name="unsupported-column-types"></a>Não há suporte para os tipos de coluna

A Sincronização de Dados não pode sincronizar colunas somente leitura ou geradas pelo sistema. Por exemplo:

- Colunas computadas.
- Colunas geradas pelo sistema para tabelas temporais.

#### <a name="limitations-on-service-and-database-dimensions"></a>Limitações nas dimensões de serviço e do banco de dados

| **Dimensões**                                                  | **Limite**              | **Solução alternativa**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Número máximo de grupos de sincronização aos quais qualquer banco de dados pode pertencer.       | 5                      |                             |
| Número máximo de pontos de extremidade em um único grupo de sincronização              | 30                     |                             |
| Número máximo de pontos de extremidade locais em um único grupo de sincronização. | 5                      | Criar vários grupos de sincronização |
| Nomes de coluna, tabela, esquema e banco de dados                       | 50 caracteres por nome |                             |
| Tabelas em um grupo de sincronização                                          | 500                    | Criar vários grupos de sincronização |
| Colunas em uma tabela em um grupo de sincronização                              | 1\.000                   |                             |
| Tamanho da linha de dados em uma tabela                                        | 24 Mb                  |                             |
| Intervalo de sincronização mínima                                           | 5 minutos              |                             |

> [!NOTE]
> Pode haver até 30 pontos de extremidade em um único grupo de sincronização, se houver apenas um grupo de sincronização. Se houver mais de um grupo de sincronização, o número total de pontos de extremidade em todos os grupos de sincronização não pode exceder 30. Se um banco de dados pertencer a vários grupos de sincronização, ele será contado como vários pontos de extremidade, não um.

## <a name="faq-about-sql-data-sync"></a>Perguntas Frequentes sobre a Sincronização de Dados SQL

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Quanto custa o serviço de Sincronização de Dados SQL?

There's no charge for the SQL Data Sync service itself. However, you still collect data transfer charges for data movement in and out of your SQL Database instance. Para saber mais, veja [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Quais regiões dão suporte à Sincronização de Dados?

A Sincronização de Dados SQL está disponível em todas as regiões.

### <a name="is-a-sql-database-account-required"></a>É necessária uma conta do Banco de Dados SQL?

Sim. Você deve ter uma conta do Banco de Dados SQL para hospedar o Banco de Dados Hub.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Posso usar a Sincronização de Dados para sincronizar somente entre bancos de dados locais do SQL Server?

Não diretamente. Contudo, é possível sincronizar entre bancos de dados locais do SQL Server indiretamente criando um banco de dados Hub no Azure e adicionando os bancos de dados locais ao grupo de sincronização.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Posso usar a Sincronização de Dados para sincronizar entre os Bancos de Dados SQL que pertencem a assinaturas diferentes?

Sim. É possível sincronizar entre os Bancos de Dados SQL que pertencem a grupos de recursos pertencentes a assinaturas diferentes.

- Se as assinaturas pertencerem ao mesmo locatário e você tiver permissão para todas as assinaturas, será possível configurar o grupo de sincronização no Portal do Azure.
- Caso contrário, será necessário usar o PowerShell para adicionar os membros de sincronização que pertencem a assinaturas diferentes.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Can I use Data Sync to sync between SQL Databases that belong to different clouds (like Azure Public Cloud and Azure China 21Vianet)

Sim. É possível sincronizar entre Bancos de Dados SQL que pertencem a nuvens diferentes e, para isso, você precisa usar o PowerShell para adicionar os membros de sincronização que pertencem a assinaturas diferentes.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Posso usar a Sincronização de Dados para propagar dados do meu banco de dados de produção para um banco de dados vazio e sincronizá-los?

Sim. Crie o esquema manualmente no novo banco de dados desenvolvendo o script com base no original. Depois de criar o esquema, adicione as tabelas a um grupo de sincronização para copiar os dados e mantê-los sincronizados.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Devo usar a Sincronização de Dados SQL para fazer backup e restaurar meus bancos de dados?

It isn't recommended to use SQL Data Sync to create a backup of your data. You can't back up and restore to a specific point in time because SQL Data Sync synchronizations are not versioned. Furthermore, SQL Data Sync does not back up other SQL objects, such as stored procedures, and doesn't do the equivalent of a restore operation quickly.

Para obter uma técnica de backup recomendada, veja [Copiar um banco de dados SQL do Azure](sql-database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>A Sincronização de Dados pode sincronizar tabelas e colunas criptografadas?

- Se um banco de dados usar Always Encrypted, será possível sincronizar apenas as tabelas e colunas *não* criptografadas. Não é possível sincronizar as colunas criptografadas porque a sincronização de dados não pode descriptografar os dados.
- Se uma coluna usar a CLE (Criptografia em Nível de Coluna), será possível sincronizar a coluna, desde que o tamanho da linha seja menor que o tamanho máximo de 24 MB. A Sincronização de Dados trata a coluna criptografada pela chave (CLE) como dados binários normais. Para descriptografar os dados em outros membros de sincronização, é necessário ter o mesmo certificado.

### <a name="is-collation-supported-in-sql-data-sync"></a>Ordenações são compatíveis com a Sincronização de Dados SQL?

Sim. A Sincronização de Dados SQL dá suporte a ordenações nos seguintes cenários:

- If the selected sync schema tables aren't already in your hub or member databases, then when you deploy the sync group, the service automatically creates the corresponding tables and columns with the collation settings selected in the empty destination databases.
- Se as tabelas a serem sincronizadas já existirem nos bancos de dados hub e membro, a Sincronização de Dados SQL exigirá que as colunas de chave primária tenham a mesma ordenação entre bancos de dados hub e membro para implantar com êxito o grupo de sincronização. Não há nenhuma restrição de ordenação em colunas que não sejam colunas de chave primária.

### <a name="is-federation-supported-in-sql-data-sync"></a>A federação é compatível com a Sincronização de Dados SQL?

O Banco de Dados de Raiz da Federação pode ser usado no Serviço da Sincronização de Dados SQL sem qualquer limitação. You can't add the Federated Database endpoint to the current version of SQL Data Sync.

## <a name="next-steps"></a>Próximos passos

### <a name="update-the-schema-of-a-synced-database"></a>Atualizar o esquema de um banco de dados sincronizado

Você precisa atualizar o esquema de um banco de dados em um grupo de sincronização? Schema changes aren't automatically replicated. Para algumas soluções, consulte os seguintes artigos:

- [Automatize a replicação de alterações de esquema na Sincronização de Dados SQL Azure](sql-database-update-sync-schema.md)
- [Usar o PowerShell para atualizar o esquema de sincronização em um grupo de sincronização existente](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>Monitorar e solucionar problemas

Is SQL Data Sync doing as expected? Para monitorar a atividade e solucionar problemas, consulte os seguintes artigos:

- [Monitor Azure SQL Data Sync with Azure Monitor logs](sql-database-sync-monitor-oms.md)
- [Solucionar problemas com a Sincronização de Dados SQL do Azure](sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>Saiba mais sobre o Banco de Dados SQL do Azure

Para mais informações sobre Banco de Dados SQL, veja os seguintes artigos:

- [Visão geral do Banco de Dados SQL](sql-database-technical-overview.md)
- [Gerenciamento de ciclo de vida do banco de dados](https://msdn.microsoft.com/library/jj907294.aspx)
