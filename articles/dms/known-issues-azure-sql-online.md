---
title: 'Problemas conhecidos: Migrações on-line para banco de dados SQL'
titleSuffix: Azure Database Migration Service
description: Saiba mais sobre problemas/limitações de migração conhecidas com migrações on-line para o Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: e7efdb7244e2c7e4651a4507b538123f8d320c1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650768"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database"></a>Problemas/limitações de migração conhecidas com migrações on-line para o Banco de Dados SQL do Azure

Os problemas conhecidos e as limitações associados às migrações online do SQL Server para o Banco de Dados SQL do Azure são descritos abaixo.

> [!IMPORTANT]
> Com migrações on-line do SQL Server para o Azure SQL Database, a migração de SQL_variant tipos de dados não é suportada.

### <a name="migration-of-temporal-tables-not-supported"></a>A migração das tabelas temporais não é compatível

**Sintoma**

Se seu banco de dados de origem consiste em uma ou mais tabelas temporais, a migração de banco de dados falha durante a operação de "Carregamento de dados completo" e você pode ver a seguinte mensagem:

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![Exemplo de erros da tabela temporal](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**Solução alternativa**

Use as etapas a seguir.

1. Localize tabelas temporais no seu esquema de origem usando a consulta abaixo.

     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```

2. Exclua estas tabelas da folha **Definir configurações de migração**, em que você especifica tabelas para migração.

3. Execute a atividade de migração novamente.

**Recursos**

Para obter mais informações, confira o artigo [Tabelas temporais](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017).

### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>A migração das tabelas inclui uma ou mais colunas com o tipo de dados hierarchyid

**Sintoma**

Você poderá ver uma exceção do SQL sugerindo que "ntext é incompatível com hierarchyid" durante a operação de "Carregamento de dados completo":

![Exemplo de erros de hierarchyid](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Solução alternativa**

Use as etapas a seguir.

1. Encontre as tabelas de usuário que incluem colunas com o tipo de dados hierarchyid usando a consulta abaixo.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ```

2. Exclua estas tabelas da folha **Definir configurações de migração**, em que você especifica tabelas para migração.

3. Execute a atividade de migração novamente.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>Falhas de migração com várias violações de integridade com gatilhos ativos no esquema durante o "Carregamento de dados completo" ou a "Sincronização de dados incremental"

**Solução alternativa**

Use as etapas a seguir.

1. Localize os gatilhos que estão atualmente ativos no banco de dados de origem usando a consulta abaixo:

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. Desabilite os gatilhos em seu banco de dados de origem usando as etapas fornecidas no artigo [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

3. Execute a atividade de migração novamente.

### <a name="support-for-lob-data-types"></a>Suporte para tipos de dados LOB

**Sintoma**

Se o tamanho da coluna de LOB (Objeto Grande) for maior que 32 KB, os dados poderão ser truncados no destino. Você pode verificar o tamanho da coluna de LOB usando a consulta abaixo:

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**Solução alternativa**

Se você tiver uma coluna LOB maior que 32 KB, entre em contato com a equipe de engenharia da [Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="issues-with-timestamp-columns"></a>Problemas com colunas de carimbo de data/hora

**Sintoma**

O Azure Database Migration Service não migra o valor do carimbo de data-hora de origem; em vez disso, o Azure Database Migration Service gera um novo valor de carimbo de tempo na tabela de destino.

**Solução alternativa**

Se você precisar do Azure Database Migration Service para migrar o valor exato do carimbo de tempo armazenado na tabela de origem, entre em contato com a equipe de engenharia do [Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>Erros de migração de dados não fornecem detalhes adicionais sobre a lâmina de status detalhada do banco de dados

**Sintoma**

Quando você se deparar com falhas de migração nos bancos de dados detalha a exibição de status, a seleção do link **de erros de migração de dados** na faixa superior pode não fornecer detalhes adicionais específicos para as falhas de migração.

![exemplos de erros de migração de dados sem detalhes](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Solução alternativa**

Para obter detalhes específicos de falha, use as seguintes etapas.

1. Feche a folha de status detalhado do banco de dados para exibir a tela Atividade de migração.

     ![tela de atividade de migração](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Selecione **Ver detalhes do erro** para exibir mensagens de erro específicas que ajudam você a solucionar problemas de erros de migração.

### <a name="geography-datatype-not-supported-in-sqldb-online-migration"></a>Tipo de dados de geografia não suportado na migração on-line SQLDB

**Sintoma**

A migração falha com uma mensagem de erro contendo o seguinte texto:

     “** encountered a fatal error”, "errorEvents":<Table>.<Column> is of type 'GEOGRAPHY', which is not supported by 'Full Load' under 'Full LOB' support mode."

**Solução alternativa**

Embora o Azure Database Migration Service suporte o tipo de dados geografia para migrações off-line para o Banco de Dados SQL do Azure, para migrações on-line, o tipo de dados Geografia não é suportado. Tente métodos alternativos para alterar o tipo de dados na origem para um tipo suportado antes de tentar usar o Azure Database Migration Service para uma migração on-line deste banco de dados.

### <a name="supported-editions"></a>Edições com suporte

**Sintoma**

A migração falha com uma mensagem de erro contendo o seguinte texto:

    Migration settings validation error: The edition of the server [Business Intelligence Edition (64-bit)] does not match the supported edition(s) [Enterprise,Standard,Developer].

**Solução alternativa**

O suporte para migrações on-line para o Banco de Dados SQL do Azure usando o Azure Database Migration Service se estende apenas às edições Enterprise, Standard e Developer. Certifique-se de que você está usando uma edição suportada antes de iniciar o processo de migração.
