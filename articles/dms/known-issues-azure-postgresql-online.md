---
title: 'Problemas conhecidos: Migrações on-line do PostgreSQL para o Azure Database para PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Saiba mais sobre problemas conhecidos e limitações de migração com migrações on-line do PostgreSQL para o Azure Database for PostgreSQL usando o Serviço de Migração de Banco de Dados do Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 3d1bc627ccb8814ab2dfb61fb0653ef0ac644038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235259"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql"></a>Problemas/limitações de migração conhecidas com migrações on-line de PostgreSQL para Azure DB para PostgreSQL

O problemas e limitações conhecidos associados às migrações online do PostgreSQL para o Banco de Dados do Azure para PostgreSQL são descritos nas seções a seguir.

## <a name="online-migration-configuration"></a>Configuração de migração online

- O servidor PostgreSQL de origem deve estar executando a versão 9.4, 9.5, 9.6, 10 ou 11. Para obter mais informações, consulte o artigo [Versões com suporte do Banco de Dados do PostgreSQL](../postgresql/concepts-supported-versions.md).
- Apenas migrações para a mesma versão ou uma versão superior são suportadas. Por exemplo, a migração do PostgreSQL 9.5 para o Azure Database para PostgreSQL 9.6 ou 10 é suportada, mas a migração do PostgreSQL 11 para o PostgreSQL 9.6 não é suportada.
- Para habilitar a replicação lógica no arquivo ** postgresql.conf do PostgreSQL de origem** defina os seguintes parâmetros:
  - **wal_level** = lógico
  - **max_replication_slots** = [pelo menos número máximo de bancos de dados para migração]; se você quiser migrar quatro bancos de dados, defina o valor para pelo menos 4.
  - **max_wal_senders** = [número de bancos de dados executados simultaneamente]; o valor recomendado é 10
- Adicionar IP do agente DMS à fonte PostgreSQL pg_hba.conf
  1. Anote o endereço IP do DMS depois de terminar de provisionar uma instância do Serviço de Migração de Banco de Dados Do Azure.
  2. Adicione o endereço IP ao arquivo pg_hba.conf conforme mostrado:

      ```
          host  all     172.16.136.18/10    md5
          host  replication postgres    172.16.136.18/10    md5
      ```

- O usuário deve ter a função REPLICATION no servidor que hospeda o banco de dados de origem.
- Os esquemas de banco de dados de origem e alvo devem coincidir.
- O esquema no banco de dados Azure de destino para servidor PostgreSQL-Single não deve ter chaves estrangeiras. Use a consulta a seguir para remover as chaves estrangeiras:

    ```
                                SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ', 
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema, 
        tc.constraint_name as foreignkey, 
        tc.table_name as tableName, 
        kcu.column_name, 
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name 
    FROM 
        information_schema.table_constraints AS tc 
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    
    ```

    Execute a remoção de chave estrangeira (que é a segunda coluna) no resultado da consulta.

- O esquema no banco de dados Azure de destino para servidor PostgreSQL-Single não deve ter nenhum gatilho. Use o seguinte para desabilitar gatilhos no banco de dados de destino:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>Limitações de tipo de dados

  **Limitação**: Se não houver chave principal nas tabelas, as alterações podem não ser sincronizadas com o banco de dados de destino.

  **Solução alternativa**: defina temporariamente uma chave primária na tabela para que a migração continue. Você poderá remover a chave primária após a conclusão da migração de dados.

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>Limitações ao migrar on-line do AWS RDS PostgreSQL

Quando você tenta executar uma migração on-line do AWS RDS PostgreSQL para o Azure Database para PostgreSQL, você pode encontrar os seguintes erros.

- **Erro**: O valor padrão da coluna '{column}' na tabela '{table}' no banco de dados '{banco de dados}' é diferente nos servidores de origem e destino. É '{value on source}' na origem e '{value on target}' no destino.

  **Limitação**: Esse erro ocorre quando o valor padrão em um esquema de coluna é diferente entre os bancos de dados de origem e de destino.
  **Solução de solução**: Certifique-se de que o esquema no alvo corresponde ao esquema na fonte. Para obter detalhes sobre o esquema de migração, consulte a [documentação de migração on-line do Azure PostgreSQL](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema).

- **Erro**: O banco de dados de destino '{banco de dados}' tem tabelas '{número de tabelas}', enquanto o banco de dados de origem '{banco de dados}' tem tabelas '{número de tabelas}'. O número de tabelas nos bancos de dados de origem e destino deve corresponder.

  **Limitação**: Esse erro ocorre quando o número de tabelas é diferente entre as bases de dados de origem e de destino.

  **Solução de solução**: Certifique-se de que o esquema no alvo corresponde ao esquema na fonte. Para obter detalhes sobre o esquema de migração, consulte a [documentação de migração on-line do Azure PostgreSQL](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema).

- **Erro:** O banco de dados de origem {banco de dados} está vazio.

  **Limitação**: Esse erro ocorre quando o banco de dados de origem está vazio. É mais provável porque você selecionou o banco de dados errado como fonte.

  **Solução de solução**: Verifique duas vezes o banco de dados de origem selecionado para migração e tente novamente.

- **Erro:** O banco de dados de destino {banco de dados} está vazio. Migre o esquema.

  **Limitação**: Este erro ocorre quando não há esquema no banco de dados de destino. Certifique-se de que o esquema no alvo corresponda ao esquema na fonte.
  **Solução de solução**: Certifique-se de que o esquema no alvo corresponde ao esquema na fonte. Para obter detalhes sobre o esquema de migração, consulte a [documentação de migração on-line do Azure PostgreSQL](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema).

## <a name="other-limitations"></a>Outras limitações

- O nome do banco de dados não pode incluir um ponto e vírgula (;).
- Uma tabela capturada deve ter uma Chave Primária. Se uma tabela não tiver uma chave primária, o resultado das operações de registro EXCLUIR e ATUALIZAR será imprevisível.
- A atualização de um segmento de chave primária é ignorada. Nesses casos, a aplicação de tal atualização será identificada pelo destino como uma atualização que não atualizou nenhuma linha e resultará em um registro gravado na tabela de exceções.
- A migração de várias tabelas com o mesmo nome, mas um caso diferente (por exemplo, tabela1, TABELA1 e Tabela1) pode causar um comportamento imprevisível e, portanto, não tem suporte.
- Processamento de mudança de [CREATE | ALTER | GOTA | TRUNCATE] tabela DDLs não é suportado.
- No Azure Database Migration Service, uma única atividade de migração só pode acomodar até quatro bancos de dados.
