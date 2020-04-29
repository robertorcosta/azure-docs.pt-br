---
title: SQL Server migração de banco de dados para um banco de dados único/em pool
description: Saiba mais sobre a migração do banco de dados do SQL Server para um banco de dados individual ou de pool elástico no Banco de Dados SQL do Azure.
keywords: migração de banco de dados, migração de banco de dados do sql server, ferramentas de migração de banco de dados, migrar banco de dados, migrar banco de dados sql
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 02/11/2019
ms.openlocfilehash: 9cec91ccc80b9072b1a3da756f26f47eb88b951c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79268607"
---
# <a name="sql-server-database-migration-to-azure-sql-database"></a>Migração do banco de dados do SQL Server para o Banco de Dados SQL do Azure

Neste artigo, você aprende sobre os principais métodos para migrar um banco de dados do SQL Server 2005 ou posterior para um banco de dados individual ou em pool no Banco de Dados SQL do Azure. Para obter informações sobre como migrar para uma Instância Gerenciada, consulte [Migrar uma instância do SQL Server para uma Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-migrate.md). Para obter informações de migração sobre como migrar de outras plataformas, confira o [Guia de Migração de Banco de Dados do Azure](https://datamigration.microsoft.com/).

## <a name="migrate-to-a-single-database-or-a-pooled-database"></a>Migrar para um banco de dados individual ou um banco de dados em pool

Há dois métodos principais para migrar um banco de dados do SQL Server 2005 ou posterior para um banco de dados individual ou em pool no Banco de Dados SQL do Azure. O primeiro método é mais simples, mas requer algum tempo de inatividade, possivelmente substancial, durante a migração. O segundo método é mais complexo, mas elimina substancialmente o tempo de inatividade durante a migração.

Em ambos os casos, você precisa garantir que o banco de dados de origem seja compatível com o Banco de Dados SQL do Azure usando o [DMA (Data Migration Assistant)](https://www.microsoft.com/download/details.aspx?id=53595). O Banco de Dados SQL V12 está se aproximando da [paridade de recursos](sql-database-features.md) com o SQL Server, além dos problemas relacionados às operações em nível do servidor e entre bancos de dados. Os bancos de dados e os aplicativos que dependem de [funções com suporte parcial ou sem suporte](sql-database-transact-sql-information.md) precisarão de alguma [reengenharia que corrija essas incompatibilidades](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues) para que o banco de dados SQL Server possa ser migrado.

> [!NOTE]
> Para migrar um banco de dados não SQL Server, incluindo Microsoft Access, Sybase, MySQL Oracle e DB2 para o Banco de Dados SQL do Azure, confira [SQL Server Migration Assistant](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/).

## <a name="method-1-migration-with-downtime-during-the-migration"></a>Método 1: migração com tempo de inatividade durante a migração

 Use esse método para migrar para um banco de dados individual ou em pool se você puder ter algum tempo de inatividade ou se estiver executando uma migração de teste de um banco de dados de produção para migração posterior. Para ver um tutorial, consulte [Migrar um Banco de Dados do SQL Server](../dms/tutorial-sql-server-to-azure-sql.md).

A lista a seguir contém o fluxo de trabalho geral para uma migração de banco de dados do SQL Server de um banco de dados individual ou em pool usando esse método. Para obter a migração para uma Instância Gerenciada, consulte [Migração para uma Instância Gerenciada](sql-database-managed-instance-migrate.md).

  ![Diagrama de migração do VSSSDT](./media/sql-database-cloud-migrate/azure-sql-migration-sql-db.png)

1. [Avalie](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) o banco de dados em termos de compatibilidade usando a versão mais recente do [DMA (Assistente de Migração de Dados)](https://www.microsoft.com/download/details.aspx?id=53595).
2. Prepare as correções necessárias como scripts Transact-SQL.
3. Faça uma cópia transacionalmente consistente do banco de dados de origem que está sendo migrado ou interrompa novas transações no banco de dados de origem durante a migração. Os métodos para realizar essa última opção incluem a desabilitação da conectividade de cliente ou a criação de um [instantâneo do banco de dados](https://msdn.microsoft.com/library/ms175876.aspx). Após a migração, você poderá usar a replicação transacional para atualizar os bancos de dados migrados com alterações ocorridas após o ponto de corte para a migração. Consulte [Migrar usando a migração transacional](sql-database-single-database-migrate.md#method-2-use-transactional-replication).  
4. Implante os scripts Transact-SQL para aplicar as correções à cópia do banco de dados.
5. [Migre](https://docs.microsoft.com/sql/dma/dma-migrateonpremsql) a cópia do banco de dados para um novo Banco de Dados SQL do Azure usando o Assistente de Migração de Dados.

> [!NOTE]
> Em vez de usar o DMA, também use um arquivo BACPAC. Consulte [Importar um arquivos BACPAC para um novo Banco de Dados SQL do Azure](sql-database-import.md).

### <a name="optimizing-data-transfer-performance-during-migration"></a>Otimizando o desempenho de transferência de dados durante a migração

A lista a seguir contém recomendações para melhorar o desempenho durante o processo de importação.

- Escolha a camada de serviço e tamanho da computação mais altos que seu orçamento permitir para maximizar o desempenho de transferência. Você pode reduzir verticalmente após a migração para economizar dinheiro.
- Minimize a distância entre o arquivo BACPAC e o data center de destino.
- Desabilitar estatísticas automaticamente durante a migração
- Índices e tabelas de partição
- Descartar exibições indexadas e recriá-las após a conclusão
- Remova dados históricos raramente consultados para outro banco de dados e migre esses dados históricos para um banco de dados SQL do Azure separado. Em seguida, você pode consultar esses dados históricos usando [consultas elásticas](sql-database-elastic-query-overview.md).

### <a name="optimize-performance-after-the-migration-completes"></a>Otimizar o desempenho após a migração ser concluída

[Atualize as estatísticas](https://docs.microsoft.com/sql/t-sql/statements/update-statistics-transact-sql) com uma verificação completa após a migração ser concluída.

## <a name="method-2-use-transactional-replication"></a>Método 2: usar replicação transacional

Quando não houver a possibilidade de remover seu banco de dados do SQL Server da produção durante a migração, você poderá usar a replicação transacional do SQL Server como sua solução de migração. Para usar esse método, o banco de dados de origem deve atender a [requisitos para replicação transacional](https://msdn.microsoft.com/library/mt589530.aspx) e ser compatível com o banco de dados SQL do Azure. Para saber mais sobre a replicação do SQL com o AlwaysOn, consulte [Configurar a replicação para Grupos de Disponibilidade AlwaysOn (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server).

Para usar esta solução, você pode configurar o Banco de Dados SQL do Azure como um assinante da instância do SQL Server que você deseja migrar. O distribuidor de replicação transacional sincroniza os dados do banco de dados a ser sincronizado (o editor), enquanto as novas transações continuam a ocorrer.

Com a replicação transacional, todas as alterações de dados ou esquema aparecem no seu Banco de Dados SQL do Azure. Quando a sincronização for concluída e você estiver pronto para migrar, altere a cadeia de conexão de seus aplicativos para apontá-los para seu Banco de Dados SQL do Azure. Assim que a replicação transacional realizar todas as alterações restantes no banco de dados e todos os aplicativos apontarem para o Banco de Dados do Azure, você poderá desinstalar a replicação transacional. O Banco de Dados SQL do Azure agora é o sistema de produção.

 ![Diagrama do SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)

> [!TIP]
> Você também pode usar a replicação transacional para migrar um subconjunto do banco de dados de origem. A publicação que você replica no Banco de Dados SQL do Azure pode ser limitada a um subconjunto de tabelas no banco de dados que está sendo replicado. Para cada tabela sendo replicada, você poderá limitar os dados a um subconjunto de linhas e/ou um subconjunto de colunas.

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Migração para o Banco de Dados SQL usando o fluxo de trabalho da Replicação de Transação

> [!IMPORTANT]
> Você deve usar a versão mais recente do SQL Server Management Studio a fim de permanecer sincronizado com as atualizações no Microsoft Azure e no Banco de Dados SQL. Versões anteriores do SQL Server Management Studio não podem configurar o Banco de Dados SQL como um assinante. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

1. Configurar a distribuição
   - [Usando o SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1)
   - [Usando o Transact-SQL](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2)

2. Criar a publicação
   - [Usando o SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1)
   - [Usando o Transact-SQL](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2)
3. Criar Assinatura
   - [Usando o SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0)
   - [Usando o Transact-SQL](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1)

Algumas dicas e diferenças da migração para o Banco de Dados SQL

- Usar um distribuidor local
  - Fazer isso causa um impacto de desempenho no servidor.
  - Se o impacto de desempenho for inaceitável, você poderá usar outro servidor, mas isso adicionará complexidade ao gerenciamento e à administração.
- Ao selecionar uma pasta de instantâneo, verifique se a pasta selecionada é grande o suficiente para manter um BCP de cada tabela que você deseja replicar.
- A criação de instantâneos bloqueia as tabelas associadas até ser concluída. Portanto, agende o instantâneo adequadamente.
- Há suporte apenas para assinaturas push no Banco de Dados SQL do Azure. Você só pode adicionar assinantes do banco de dados de origem.

## <a name="resolving-database-migration-compatibility-issues"></a>Resolvendo problemas de compatibilidade de migração do banco de dados

Há uma grande variedade de problemas de compatibilidade que você pode encontrar, dependendo da versão do SQL Server no banco de dados de origem e da complexidade do banco de dados que você está migrando. Versões anteriores do SQL Server têm mais problemas de compatibilidade. Use os recursos a seguir, além de uma pesquisa direcionada na Internet, usando o mecanismo de pesquisa de sua preferência:

- [Recursos de banco de dados do SQL Server sem suporte no Banco de Dados SQL do Azure](sql-database-transact-sql-information.md)
- [Funcionalidade do Mecanismo de Banco de Dados descontinuada no SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [Funcionalidade do Mecanismo de Banco de Dados descontinuada no SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [Funcionalidade de Mecanismo de Banco de Dados descontinuada no SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [Discontinued Database Engine Functionality in SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [Discontinued Database Engine Functionality in SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Além de pesquisar na Internet e usar esses recursos, use os [fóruns da comunidade do SQL Server no MSDN](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) ou o [StackOverflow](https://stackoverflow.com/).

> [!IMPORTANT]
> A Instância Gerenciada do Banco de Dados SQL permite que você migre uma instância existente do SQL Server e seus bancos de dados com o mínimo ou sem problemas de compatibilidade. Consulte [O que é uma Instância Gerenciada](sql-database-managed-instance.md).

## <a name="next-steps"></a>Próximas etapas

- Use o script no blog dos Engenheiros EMEA do Azure SQL para [Monitorar o uso de tempdb durante migração](https://blogs.msdn.microsoft.com/azuresqlemea/2016/12/28/lesson-learned-10-monitoring-tempdb-usage/).
- Use o script no blog dos Engenheiros EMEA do Azure SQL para [Monitorar o espaço de log de transações do banco de dados enquanto a migração está ocorrendo](https://docs.microsoft.com/archive/blogs/azuresqlemea/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database).
- Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos BACPAC, confira [Migrando do SQL Server para o Banco de Dados SQL do Azure usando arquivos BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- Para obter informações sobre como trabalhar com a hora UTC após a migração, confira [Modificando o fuso horário padrão para o fuso horário local](https://blogs.msdn.microsoft.com/azuresqlemea/2016/07/27/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone/).
- Para obter informações sobre como alterar o idioma padrão de um banco de dados após a migração, confira [Como alterar o idioma padrão do Banco de Dados SQL do Azure](https://blogs.msdn.microsoft.com/azuresqlemea/2017/01/13/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database/).
