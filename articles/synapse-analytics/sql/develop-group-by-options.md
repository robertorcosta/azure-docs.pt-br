---
title: Use opções DE GRUPO POR EM Synapse SQL
description: O Synapse SQL permite o desenvolvimento de soluções implementando diferentes opções DE GROUP BY.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 261f75344d250ae8a8d9687f4bcd80535d11716b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429038"
---
# <a name="group-by-options-in-synapse-sql"></a>GRUPO POR OPÇÕES em Synapse SQL
O Synapse SQL permite o desenvolvimento de soluções implementando diferentes opções DE GROUP BY. 

## <a name="what-does-group-by-do"></a>O que o GRUPO POR FAZ

A cláusula T-SQL [GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) agrega dados a um conjunto de linhas de resumo.

O SQL sob demanda suporta toda a gama de opções GROUP BY. O pool SQL suporta um número limitado de opções GROUP BY.

## <a name="group-by-options-supported-in-sql-pool"></a>GRUPO POR opções suportadas no pool SQL

Group BY tem algumas opções que o pool SQL não suporta. Essas opções têm alternativas, que são as seguintes:

* GROUP BY com ROLLUP
* GROUPING SETS
* GROUP BY com CUBE

### <a name="rollup-and-grouping-sets-options"></a>O rollup e o agrupamento definem opções

A opção mais simples aqui é usar a UNION ALL para executar o rollup em vez de depender da sintaxe explícita. O resultado é exatamente o mesmo

O exemplo a seguir usa a declaração GROUP BY com a opção ROLLUP:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

Usando ROLLUP, o exemplo anterior solicita as agregações a seguir:

* país e região
* País/Região
* Grande Total

Para substituir o ROLLUP e retornar os mesmos resultados, você pode usar UNION ALL e especificar explicitamente as agregações necessárias:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

Para substituir GROUPING SETS, o princípio de exemplo se aplica. Você só precisa criar seções UNION ALL para os níveis de agregação que você deseja ver.

### <a name="cube-options"></a>Opções Cube

É possível criar um GRUPO POR CUBO usando a abordagem UNION ALL. O problema é que o código pode rapidamente se tornar complicado e difícil. Para mitigar esse problema, você pode usar essa abordagem mais avançada.

A primeira etapa é definir o ‘cube’ que define todos os níveis de agregação que desejamos criar. Tome nota do CROSS JOIN das duas tabelas derivadas, pois ela gera todos os níveis. O resto do código está lá para formatação.

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

A imagem a seguir mostra os resultados da [TABELA CRIAR COMO SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest):

![Agrupar por cubo](./media/develop-group-by-options/develop-group-by-cube.png)

O segundo passo é especificar uma tabela de destino para armazenar resultados provisórios:

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

O terceiro passo é girar sobre o cubo de colunas realizando a agregação. A consulta será executada uma vez para cada linha na tabela temporária #Cube. Os resultados são armazenados na tabela #Results temperatura:

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

Por fim, você pode retornar os resultados lendo a tabela temporária #Results:

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Ao dividir o código em seções e gerar uma construção de looping, o código torna-se mais gerenciável e sustentável.

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](develop-overview.md).
