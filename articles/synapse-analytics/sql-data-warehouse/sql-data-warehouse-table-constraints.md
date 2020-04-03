---
title: Chaves primárias, estrangeiras e únicas
description: Suporte a restrições de tabela no pool Synapse SQL no Azure Synapse Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 881e4f0110e3c0f35301e2ae6be40f2510f42539
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583486"
---
# <a name="primary-key-foreign-key-and-unique-key-in-synapse-sql-pool"></a>Chave primária, chave estrangeira e chave única no pool Synapse SQL

Saiba mais sobre as restrições de tabela no pool Synapse SQL, incluindo chave primária, tecla estrangeira e chave única.

## <a name="table-constraints"></a>Restrições de tabela

O pool Synapse SQL suporta essas restrições de tabela: 
- Key PRIMARY só é suportado quando não agrupados e NÃO APLICADOs são usados.    
- A restrição UNIQUE só é suportada com NÃO APLICADO é usado.

A restrição FOREIGN KEY não é suportada no pool Synapse SQL.  

## <a name="remarks"></a>Comentários

Ter chave primária e/ou chave exclusiva permite que o mecanismo de pool SQL sinapse gere um plano de execução ideal para uma consulta.  Todos os valores em uma coluna de chave primária ou uma coluna de restrição única devem ser únicos.

Depois de criar uma tabela com chave primária ou restrição única no pool Synapse SQL, os usuários precisam ter certeza de que todos os valores nessas colunas são únicos.  Uma violação disso pode fazer com que a consulta retorne resultado impreciso.  Este exemplo mostra como uma consulta pode retornar resultado impreciso se a chave principal ou a coluna de restrição exclusiva incluir valores duplicados.  

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

Crie uma tabela de bilhar Synapse SQL com uma chave principal: 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```
Crie uma tabela de bilhar Synapse SQL com uma restrição única:

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>Próximas etapas

Depois de criar as tabelas para o seu pool Synapse SQL, o próximo passo é carregar dados na tabela. Para obter um tutorial de carregamento, consulte ['Carregar dados' para o pool Synapse SQL](load-data-wideworldimportersdw.md).
