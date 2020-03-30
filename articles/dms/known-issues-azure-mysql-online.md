---
title: 'Problemas conhecidos: Migrações on-line para o Banco de Dados Azure para MySQL'
titleSuffix: Azure Database Migration Service
description: Saiba mais sobre problemas conhecidos e limitações de migração com migrações on-line para o Azure Database for MySQL ao usar o Serviço de Migração de Banco de Dados do Azure.
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
ms.openlocfilehash: 8c3de28ea934302086a5b14e61482e6a4ab9a7ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235288"
---
# <a name="online-migration-issues--limitations-to-azure-db-for-mysql-with-azure-database-migration-service"></a>Problemas de migração on-line & limitações ao Azure DB para MySQL com o Azure Database Migration Service

Os problemas conhecidos e as limitações associados às migrações online do MySQL para o Banco de Dados do Azure para MySQL são descritos nas seções a seguir.

## <a name="online-migration-configuration"></a>Configuração de migração online


- A versão do MySQL Server de origem precisa ser a 5.6.35, a 5.7.18 ou posteriores
- O Banco de Dados do Azure para MySQL dá suporte a:
  - MySQL Community Edition
  - Mecanismo InnoDB
- Migração de mesma versão. Não há suporte para a migração do MySQL 5.6 para o Banco de Dados do Azure para MySQL 5.7.
- Habilite o log binário em my.ini (Windows) ou em my.cnf (Unix)
  - Defina Server_id para qualquer número maior ou igual a 1, por exemplo, Server_id=1 (somente para o MySQL 5.6)
  - Definir log-bin \<=> de caminho (apenas para MySQL 5.6)
  - Defina binlog_format = row
  - Expire_logs_days = 5 (recomendado, apenas para o MySQL 5.6)
- O usuário precisa ter a função ReplicationAdmin.
- As ordenações definidas para o banco de dados MySQL de origem devem ser as mesmas que as definidos no Banco de Dados do Azure para MySQL de destino.
- O esquema precisa corresponder entre o banco de dados MySQL de origem e o banco de dados de destino no Banco de Dados do Azure para MySQL.
- O esquema no Banco de Dados do Azure para MySQL de destino não pode ter chaves estrangeiras. Use a consulta a seguir para remover as chaves estrangeiras:
    ```
    SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT 
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
          KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
          AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = ['schema_name']) Queries
      GROUP BY SchemaName;
    ```

    Execute a remoção de chave estrangeira (que é a segunda coluna) no resultado da consulta.
- O esquema no Banco de Dados do Azure para MySQL de destino não pode ter nenhum gatilho. Para remover os gatilhos no banco de dados de destino:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Limitações de tipo de dados

- **Limitação**: se houver um tipo de dados JSON no banco de dados MySQL de origem, a migração falhará durante a sincronização contínua.

    **Solução alternativa**: modifique o tipo de dados JSON para texto médio ou longtext no banco de dados MySQL de origem.

- **Limitação**: se não houver nenhuma chave primária nas tabelas, a sincronização contínua falhará.

    **Solução alternativa**: defina temporariamente uma chave primária na tabela para que a migração continue. Você poderá remover a chave primária após a conclusão da migração de dados.

## <a name="lob-limitations"></a>Limitações de LOB

As colunas de LOB (Objeto Grande) são aquelas cujo tamanho pode aumentar muito. Para MySQL, Texto médio, Longtext, Blob, Mediumblob, Longblob, etc., são alguns dos tipos de dados do LOB.

- **Limitação**: se tipos de dados de LOB forem usados como chaves primárias, a migração falhará.

    **Solução alternativa**: substitua chave primária por outros tipos de dados ou colunas que não sejam de LOB.

- **Limitação**: se o tamanho da coluna de LOB (Objeto Grande) for maior que 32 KB, os dados poderão ser truncados no destino. Você pode verificar o tamanho da coluna de LOB usando esta consulta:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Solução de solução**: Se você tiver um objeto LOB maior que 32 KB, entre em contato com a equipe de engenharia da [Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>Limitações ao migrar on-line do AWS RDS MySQL

Quando você tenta executar uma migração on-line do AWS RDS MySQL para o Azure Database for MySQL, você pode se deparar com os seguintes erros.

- **Erro:** O{0}banco de dados tem tecla estrangeira no alvo. Corrija o destino e inicie uma nova atividade de migração de dados. Execute abaixo o script no alvo para listar as teclas estrangeiras

  **Limitação**: Se você tiver chaves estrangeiras no seu esquema, a carga inicial e a sincronização contínua da migração falharão.
  **Solução de solução**: Execute o seguinte script na bancada mySQL para extrair o script de chave estrangeira e adicionar script de chave estrangeiro:

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **Erro:** O{0}banco de dados ' ' não existe no servidor. O servidor de origem do MySQL fornecido diferencia maiúsculas de minúsculas. Verifique o nome do banco de dados.

  **Limitação**: Ao migrar um banco de dados MySQL para o Azure usando a Interface de Linha de Comando (CLI), os usuários podem acertar esse erro. O serviço não conseguiu localizar o banco de dados no servidor de origem, o que pode ser porque você pode ter fornecido o nome do banco de dados incorreto ou o banco de dados não existe no servidor listado. Nota que os nomes do banco de dados são sensíveis a maiúsculas e minúsculas.

  **Solução :** Forneça o nome exato do banco de dados e tente novamente.

- **Erro:** Existem tabelas com o mesmo nome no banco de dados '{banco de dados}'. O Banco de Dados do Azure para MySQL não oferece suporte a diferenciação de maiúsculas de minúsculas para tabelas.

  **Limitação**: Este erro acontece quando você tem duas tabelas com o mesmo nome no banco de dados de origem. O Banco de Dados Azure para MySQL não suporta tabelas sensíveis a maiúsculas e minúsculas.

  **Solução de solução**: Atualize os nomes da tabela para serem únicos e tente novamente.

- **Erro:** O banco de dados de destino {banco de dados} está vazio. Migre o esquema.

  **Limitação**: Esse erro ocorre quando o banco de dados Azure de destino para o banco de dados MySQL não tem o esquema necessário. A migração de esquemas é necessária para permitir a migração de dados para o seu destino.

  **Solução :** [Migre o esquema](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#migrate-the-sample-schema) do banco de dados de origem para o banco de dados de destino.

## <a name="other-limitations"></a>Outras limitações

- Não há suporte para cadeia de caracteres de senha com colchetes {} de abertura e fechamento em seu início e final. Essa limitação aplica-se tanto à conexão com o MySQL de origem quanto com o Banco de Dados do Azure para MySQL de destino.
- Não há suporte para as DDLs a seguir:
  - Todas as DDLs de partição
  - Remover tabela
  - Renomear tabela
- Não há suporte para usar a instrução *alter table <table_name> add column <column_name>* para adicionar colunas ao início ou ao meio de uma tabela. A instrução *alter table <table_name> add column <column_name>* adiciona a coluna ao final da tabela.
- Não há suporte para índices criados com apenas uma parte dos dados da coluna. A instrução a seguir é um exemplo que cria um índice usando apenas uma parte dos dados da coluna:

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- No Azure Database Migration Service, o limite de bancos de dados para migrar em uma única atividade de migração é de quatro.

- **Erro:** Tamanho da linha muito grande (> 8126). Alterar algumas colunas para Texto ou BLOB pode ajudar. No formato de linha atual, o prefixo BLOB de 0 bytes é armazenado em linha.

  **Limitação**: Este erro acontece quando você está migrando para o Banco de Dados Azure para MySQL usando o mecanismo de armazenamento InnoDB e qualquer tamanho de linha de tabela é muito grande (>8126 bytes).

  **Solução de solução**: Atualize o esquema da tabela que tem um tamanho de linha maior que 8126 bytes. Não recomendamos mudar o modo estrito porque os dados serão truncados. Mudar a page_size não é suportado.
