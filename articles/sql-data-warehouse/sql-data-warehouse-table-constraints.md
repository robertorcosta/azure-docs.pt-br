---
title: Chaves primárias, estrangeiras e exclusivas
description: Suporte a restrições de tabela na análise de SQL no Azure Synapse Analytics
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 0379bed08c3ee6931e931a78a2d2c91664535250
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198126"
---
# <a name="primary-key-foreign-key-and-unique-key-in-sql-analytics"></a>Chave primária, chave estrangeira e chave exclusiva na análise do SQL

Saiba mais sobre as restrições de tabela na análise de SQL, incluindo chave primária, chave estrangeira e chave exclusiva.

## <a name="table-constraints"></a>Restrições de tabela 
A análise do SQL dá suporte a essas restrições de tabela: 
- Só há suporte para a chave primária quando não CLUSTERIZAdo e não imposto são usados.    
- Só há suporte para a restrição UNIQUE quando não imposta é usado.   

Não há suporte para a restrição de chave estrangeira na análise de SQL.  

## <a name="remarks"></a>Comentários
Ter a chave primária e/ou a chave exclusiva permite que o mecanismo de análise do SQL gere um plano de execução ideal para uma consulta.  Todos os valores em uma coluna de chave primária ou uma coluna de restrição UNIQUE devem ser exclusivos. 

Depois de criar uma tabela com uma restrição PRIMARY KEY ou Unique na análise do SQL, os usuários precisam garantir que todos os valores nessas colunas sejam exclusivos.  Uma violação disso pode fazer com que a consulta retorne um resultado impreciso.  Este exemplo mostra como uma consulta pode retornar um resultado impreciso se a coluna de restrição PRIMARY KEY ou Unique tiver valores duplicados.  

```sql
 -- Create table t1
CREATE TABLE t1 (a1 INT NOT NULL, b1 INT) WITH (DISTRIBUTION = ROUND_ROBIN)

-- Insert values to table t1 with duplicate values in column a1.
INSERT INTO t1 VALUES (1, 100)
INSERT INTO t1 VALUES (1, 1000)
INSERT INTO t1 VALUES (2, 200)
INSERT INTO t1 VALUES (3, 300)
INSERT INTO t1 VALUES (4, 400)

-- Run this query.  No primary key or unique constraint.  4 rows returned. Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
1           2
2           1
3           1
4           1

(4 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 ADD CONSTRAINT unique_t1_a1 unique (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, count(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key constraint
ALTER TABLE t1 add CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Manually fix the duplicate values in a1
UPDATE t1 SET a1 = 0 WHERE b1 = 1000

-- Verify no duplicate values in column a1 
SELECT * FROM t1

/*
a1          b1
----------- -----------
2           200
3           300
4           400
0           1000
1           100

(5 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 add CONSTRAINT unique_t1_a1 UNIQUE (a1) NOT ENFORCED  

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) as total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key contraint
ALTER TABLE t1 ADD CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

```

## <a name="examples"></a>Exemplos
Crie uma tabela de análise de SQL com uma chave primária: 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```
Crie uma tabela de análise de SQL com uma restrição UNIQUE:

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>Próximas etapas

Depois de criar as tabelas para o banco de dados do SQL Analytics, a próxima etapa será carregar os dados na tabela. Para obter um tutorial de carregamento, consulte [carregando dados nos bancos de dado do SQL Analytics](load-data-wideworldimportersdw.md).
