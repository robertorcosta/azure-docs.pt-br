---
title: Instruções T-SQL
description: Links para a documentação de declarações T-SQL suportadas no pool Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 011a850a44948bf5eee28a40d59dcf7672d866e8
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586359"
---
# <a name="t-sql-statements-supported-in-synapse-sql-pool"></a>Instruções T-SQL suportadas no pool Synapse SQL

Links para a documentação de declarações T-SQL suportadas no pool Synapse SQL.

## <a name="data-definition-language-ddl-statements"></a>Instruções de DDL (Linguagem de Definição de Dados)

* [ALTERAR BANCO DE DADOS](https://msdn.microsoft.com/library/mt204042.aspx)
* [ALTER INDEX](https://msdn.microsoft.com/library/ms188388.aspx)
* [ALTERAR VISÃO MATERIALIZADA](/sql/t-sql/statements/alter-materialized-view-transact-sql?view=azure-sqldw-latest) (Visualização) 
* [ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)
* [ALTER SCHEMA](https://msdn.microsoft.com/library/ms173423.aspx)
* [TABELA DE ALTERAÇÃO](https://msdn.microsoft.com/library/ms190273.aspx)
* [CREATE COLUMNSTORE INDEX](https://msdn.microsoft.com/library/gg492153.aspx)
* [CRIAR BANCO DE DADOS](https://msdn.microsoft.com/library/mt204021.aspx)
* [CRIAR UMA CREDENCIAL COM ESCOPO DE BANCO DE DADOS](https://msdn.microsoft.com/library/mt270260.aspx)
* [CRIAR UMA FONTE DE DADOS EXTERNA](https://msdn.microsoft.com/library/dn935022.aspx)
* [CRIAR FORMATO DE ARQUIVO EXTERNO](https://msdn.microsoft.com/library/dn935026.aspx)
* [CRIAR TABELA EXTERNA](https://msdn.microsoft.com/library/dn935021.aspx)
* [CRIAR FUNÇÃO](https://msdn.microsoft.com/library/mt203952.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [CRIAR VISUALIZAÇÃO MATERIALIZADA COMO SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (Visualização) 
* [CREATE PROCEDURE](https://msdn.microsoft.com/library/ms187926.aspx)
* [CREATE SCHEMA](https://msdn.microsoft.com/library/ms189462.aspx)
* [CRIAR ESTATÍSTICAS](https://msdn.microsoft.com/library/ms188038.aspx)
* [CRIAR TABELA](https://msdn.microsoft.com/library/mt203953.aspx)
* [CREATE TABLE AS SELECT](https://msdn.microsoft.com/library/mt204041.aspx)
* [CRIAR VISUALIZAÇÃO](https://msdn.microsoft.com/library/ms187956.aspx)
* [CRIAR CLASSIFICADOR DE CARGA DE TRABALHO](/sql/t-sql/statements/create-workload-classifier-transact-sql)
* [DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/mt146367.aspx)
* [DROP EXTERNAL FILE FORMAT](https://msdn.microsoft.com/library/mt146379.aspx)
* [DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/mt130698.aspx)
* [DROP INDEX](https://msdn.microsoft.com/library/ms176118.aspx)
* [DROP PROCEDURE](https://msdn.microsoft.com/library/ms174969.aspx)
* [DROP STATISTICS](https://msdn.microsoft.com/library/ms175075.aspx)
* [TABELA DROP](https://msdn.microsoft.com/library/ms173790.aspx)
* [DROP SCHEMA](https://msdn.microsoft.com/library/ms186751.aspx)
* [VISTA DE QUEDA](https://msdn.microsoft.com/library/ms173492.aspx)
* [CLASSEIFICADOR DE CARGA DE TRABALHO DE QUEDA](/sql/t-sql/statements/drop-workload-classifier-transact-sql)
* [Renomear](https://msdn.microsoft.com/library/mt631611.aspx)
* [SET RESULT_SET_CACHING](/sql/t-sql/statements/set-result-set-caching-transact-sql) 
* [TABELA TRUNCATE](https://msdn.microsoft.com/library/ms177570.aspx)
* [ATUALIZAR ESTATÍSTICAS](https://msdn.microsoft.com/library/ms187348.aspx)

## <a name="data-manipulation-language-dml-statements"></a>Instruções de DML (Linguagem de Manipulação de Dados)

* [Delete (excluir)](https://msdn.microsoft.com/library/ms189835.aspx)
* [Inserir](https://msdn.microsoft.com/library/ms174335.aspx)
* [Atualização](https://msdn.microsoft.com/library/ms177523.aspx)

## <a name="database-console-commands"></a>Comandos de Console de Banco de Dados

* [DBCC DROPCLEANBUFFERS](https://msdn.microsoft.com/library/ms187762.aspx)
* [DBCC DROPRESULTSETCACHE](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest) (Visualização)
* [DBCC FREEPROCCACHE](https://msdn.microsoft.com/library/mt204018.aspx)
* [DBCC SHRINKLOG](https://msdn.microsoft.com/library/mt204020.aspx)
* [DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx)
* [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest)
* [DBCC SHOWRESULTCACHESPACEUSED](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql) (Visualização)
* [DBCC PDW_SHOWPARTITIONSTATS](https://msdn.microsoft.com/library/mt204013.aspx)
* [DBCC PDW_SHOWSPACEUSED](https://msdn.microsoft.com/library/mt204028.aspx)
* [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/mt204043.aspx)

## <a name="query-statements"></a>Instruções de consulta

* [Selecione](https://msdn.microsoft.com/library/ms189499.aspx)
* [WITH common_table_expression](https://msdn.microsoft.com/library/ms175972.aspx)
* [EXCEPT e INTERSECT](https://msdn.microsoft.com/library/ms188055.aspx)
* [Explicar](https://msdn.microsoft.com/library/mt631615.aspx)
* [De](https://msdn.microsoft.com/library/ms177634.aspx)
* [Como usar PIVOT e UNPIVOT](https://msdn.microsoft.com/library/ms177410.aspx)
* [GROUP BY](https://msdn.microsoft.com/library/ms177673.aspx)
* [HAVING](https://msdn.microsoft.com/library/ms180199.aspx)
* [ORDEM POR](https://msdn.microsoft.com/library/ms188385.aspx)
* [Opção](https://msdn.microsoft.com/library/ms190322.aspx)
* [União](https://msdn.microsoft.com/library/ms180026.aspx)
* [Onde](https://msdn.microsoft.com/library/ms188047.aspx)
* [Início](https://msdn.microsoft.com/library/ms189463.aspx)
* [Atribuição de alias](https://msdn.microsoft.com/library/mt631614.aspx)
* [Condição de pesquisa](https://msdn.microsoft.com/library/ms173545.aspx)
* [Subconsultas](https://msdn.microsoft.com/library/mt631613.aspx)

## <a name="security-statements"></a>Instruções de segurança

* Permissões: [GRANT](https://msdn.microsoft.com/library/ms187965.aspx), [DENY](https://msdn.microsoft.com/library/ms188338.aspx), [REVOKE](https://msdn.microsoft.com/library/ms187728.aspx)
* [ALTER AUTHORIZATION](https://msdn.microsoft.com/library/ms187359.aspx)
* [ALTER CERTIFICATE](https://msdn.microsoft.com/library/ms189511.aspx)
* [ALTER DATABASE ENCRYPTION KEY](https://msdn.microsoft.com/library/bb630389.aspx)
* [ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)
* [ALTER MASTER KEY](https://msdn.microsoft.com/library/ms186937.aspx)
* [ALTER ROLE](https://msdn.microsoft.com/library/ms189775.aspx)
* [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx)
* [BACKUP CERTIFICATE](https://msdn.microsoft.com/library/ms178578.aspx)
* [CLOSE MASTER KEY](https://msdn.microsoft.com/library/ms188387.aspx)
* [CREATE CERTIFICATE](https://msdn.microsoft.com/library/ms187798.aspx)
* [CREATE DATABASE ENCRYPTION KEY](https://msdn.microsoft.com/library/bb677241.aspx)
* [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx)
* [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx)
* [CRIAR PAPEL](https://msdn.microsoft.com/library/ms187936.aspx)
* [CRIAR USUÁRIO](https://msdn.microsoft.com/library/ms173463.aspx)
* [DROP CERTIFICATE](https://msdn.microsoft.com/library/ms179906.aspx)
* [DROP DATABASE ENCRYPTION KEY](https://msdn.microsoft.com/library/bb630256.aspx)
* [DROP LOGIN](https://msdn.microsoft.com/library/ms188012.aspx)
* [DROP MASTER KEY](https://msdn.microsoft.com/library/ms180071.aspx)
* [DROP ROLE](https://msdn.microsoft.com/library/ms174988.aspx)
* [DROP USER](https://msdn.microsoft.com/library/ms189438.aspx)
* [OPEN MASTER KEY](https://msdn.microsoft.com/library/ms174433.aspx)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações de referência, consulte [os elementos de linguagem T-SQL no pool Synapse SQL](sql-data-warehouse-reference-tsql-language-elements.md)e [as visualizações do sistema no pool Synapse SQL](sql-data-warehouse-reference-tsql-system-views.md).
