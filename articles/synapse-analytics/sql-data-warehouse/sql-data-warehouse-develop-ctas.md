---
title: CREATE TABLE AS SELECT (CTAS)
description: Explicação e exemplos da declaração CREATE TABLE AS SELECT (CTAS) no Synapse SQL para o desenvolvimento de soluções.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019, azure-synapse
ms.openlocfilehash: 8e1b75dfc6a979956ff4a2868027bb769bf7c4ed
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633542"
---
# <a name="create-table-as-select-ctas"></a>CREATE TABLE AS SELECT (CTAS)

Este artigo explica a declaração De CRIAÇÃO TABELA COMO SELECT (CTAS) T-SQL no Synapse SQL para o desenvolvimento de soluções. O artigo também fornece exemplos de códigos.

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

A declaração [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAS) é um dos recursos T-SQL mais importantes disponíveis. CTAS é uma operação paralela que cria uma nova tabela com base na saída de uma declaração SELECT. CtAS é a maneira mais simples e rápida de criar e inserir dados em uma tabela com um único comando.

## <a name="selectinto-vs-ctas"></a>Selecione... INTO vs. CTAS

CTAS é uma versão mais personalizável do [SELECT... Em](/sql/t-sql/queries/select-into-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) declaração.

O seguinte é um exemplo de um simples SELECT... Em:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

Selecione... A INTO não permite que você altere o método de distribuição ou o tipo de índice como parte da operação. Você `[dbo].[FactInternetSales_new]` cria usando o tipo de distribuição padrão de ROUND_ROBIN e a estrutura de tabela padrão do ÍNDICE COLUNASTORE CLUSTERED.

Com ctas, por outro lado, você pode especificar tanto a distribuição dos dados da tabela como o tipo de estrutura da tabela. Para converter o exemplo anterior em CTAS:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
 DISTRIBUTION = ROUND_ROBIN
 ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales];
```

> [!NOTE]
> Se você estiver apenas tentando alterar o índice em sua operação CTAS, e a tabela de origem for distribuída por hash, mantenha a mesma coluna de distribuição e tipo de dados. Isso evita a movimentação de dados de distribuição cruzada durante a operação, o que é mais eficiente.

## <a name="use-ctas-to-copy-a-table"></a>Usar CTAS para copiar uma tabela

Talvez um dos usos mais comuns do CTAS seja criar uma cópia de uma tabela para alterar o DDL. Digamos que você originalmente criou sua `ROUND_ROBIN`tabela como , e agora quer alterá-la para uma tabela distribuída em uma coluna. CTAS é como você alteraria a coluna de distribuição. Você também pode usar CTAS para alterar particionamento, indexação ou tipos de colunas.

Digamos que você criou esta tabela usando o `ROUND_ROBIN`tipo de distribuição padrão `CREATE TABLE`de , sem especificar uma coluna de distribuição no .

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25));
```

Agora você deseja criar uma nova cópia `Clustered Columnstore Index`desta tabela, com um , para que você possa aproveitar o desempenho das tabelas clustered Columnstore. Você também quer distribuir `ProductKey`esta tabela, porque você está antecipando a adesão `ProductKey`a esta coluna e quer evitar a movimentação de dados durante a adesão . Por fim, você também deseja `OrderDateKey`adicionar particionamento, para que você possa excluir rapidamente dados antigos, soltando partições antigas. Aqui está a declaração CTAS, que copia sua antiga tabela em uma nova tabela.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Finalmente, você pode renomear suas tabelas, para trocar em sua nova tabela e, em seguida, deixar cair sua tabela antiga.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>Use ctas para contornar recursos sem suporte

Você também pode usar o CTAS para contornar uma série de recursos não suportados listados abaixo. Este método pode muitas vezes ser útil, porque não apenas o seu código será compatível, mas muitas vezes será executado mais rápido no Synapse SQL. Este desempenho é resultado de seu design totalmente paralelo. Os cenários incluem:

* ANSI JOINS em instruções UPDATE
* ANSI JOINs em instruções DELETE
* Instrução MERGE

> [!TIP]
> Tente pensar "CTAS primeiro". Resolver um problema usando CTAS geralmente é uma boa abordagem, mesmo se você estiver escrevendo mais dados como resultado.

## <a name="ansi-join-replacement-for-update-statements"></a>Substituição de junção ANSI para instruções de atualização

Você pode descobrir que você tem uma atualização complexa. A atualização junta mais de duas tabelas usando a sintaxe de adesão ansi para executar a ATUALIZAÇÃO ou DELETE.

Imagine que você precisasse atualizar esta tabela:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
( [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
, [CalendarYear]                    SMALLINT        NOT NULL
, [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
);
```

A consulta original pode ter parecido com este exemplo:

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT [EnglishProductCategoryName]
        , [CalendarYear]
        , SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear];
```

O Synapse SQL não suporta a `FROM` adesão `UPDATE` do ANSI à cláusula de uma declaração, portanto você não pode usar o exemplo anterior sem modificá-lo.

Você pode usar uma combinação de um CTAS e uma junta implícita para substituir o exemplo anterior:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0) AS [EnglishProductCategoryName]
, ISNULL(CAST([CalendarYear] AS SMALLINT),0)  AS [CalendarYear]
, ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)  AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY [EnglishProductCategoryName]
, [CalendarYear];

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]  = AnnualCategorySales.[CalendarYear] ;

--Drop the interim table
DROP TABLE CTAS_acs;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>Substituição de junção ANSI para instruções delete

Às vezes, a melhor abordagem para excluir dados `DELETE` é usar CTAS, especialmente para declarações que usam sintaxe de adesão ansi. Isso porque o Synapse SQL não suporta a `FROM` adesão da ANSI à cláusula de uma `DELETE` declaração. Em vez de excluir os dados, selecione os dados que deseja manter.

A seguir, um exemplo `DELETE` de uma declaração convertida:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you want to keep
SELECT p.ProductKey
, p.EnglishProductName
,  p.Color
FROM  dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON p.ProductKey = s.ProductKey;

RENAME OBJECT dbo.DimProduct TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Substituir instruções merge

Você pode substituir as instruções de mesclagem, pelo menos em parte, usando CTAS. Você pode `INSERT` combinar `UPDATE` o e o em uma única declaração. Quaisquer registros excluídos devem `SELECT` ser restringidos da declaração para omitir dos resultados.

O exemplo a `UPSERT`seguir é:

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT s.[ProductKey]
, s.[EnglishProductName]
, s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
, p.[EnglishProductName]
, p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
);

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];
```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>Declarar explicitamente o tipo de dados e a nulidade da saída

Ao migrar código, você pode encontrar que você executar através deste tipo de padrão de codificação:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f;
```

Você pode pensar que deve migrar este código para CTAS, e você estaria correto. No entanto, há um problema oculto aqui.

O código a seguir não produz o mesmo resultado:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result;
```

Observe que a coluna "resultado" transfere os valores de tipo de dados e de nulidade da expressão. Carregar o tipo de dados para a frente pode levar a variações sutis nos valores se você não tomar cuidado.

Experimente este exemplo:

```sql
SELECT result,result*@d
from result;

SELECT result,result*@d
from ctas_r;
```

O valor armazenado para o resultado é diferente. Como o valor persistido na coluna de resultados é usado em outras expressões, o erro torna-se ainda mais significativo.

![Captura de tela dos resultados do CTAS](./media/sql-data-warehouse-develop-ctas/ctas-results.png)

Isso é importante para migrações de dados. Mesmo que a segunda consulta seja indiscutivelmente mais precisa, há um problema. Os dados seriam diferentes em comparação com o sistema de origem, e isso leva a questões de integridade na migração. Este é um dos raros casos em que a resposta "errada" é, na verdade, a melhor!

A razão pela qual vemos uma disparidade entre os dois resultados é devido ao casting implícito do tipo. No primeiro exemplo, a tabela define a definição da coluna. Quando a linha é inserida, ocorre uma conversão implícita do tipo. No segundo exemplo, não há conversão implícita de tipo, pois a expressão define o tipo de dados da coluna.

Observe também que a coluna no segundo exemplo foi definida como uma coluna NULLable, enquanto no primeiro exemplo não tem. Quando a tabela foi criada no primeiro exemplo, a nulidade da coluna foi explicitamente definida. No segundo exemplo, ele foi deixado para a expressão, e por padrão resultaria em uma definição NULL.

Para resolver esses problemas, você deve definir explicitamente a conversão de tipo e a nulidade na parte SELECT da instrução CTAS. Você não pode definir essas propriedades em 'CRIAR TABELA'.
O exemplo a seguir demonstra como corrigir o código:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Observe o seguinte:

* Você pode usar CAST ou CONVERT.
* Use ISNULL, não COALESCE, para forçar a nullability. Veja a nota a seguir.
* ISNULL é a função mais externa.
* A segunda parte do ISNULL é uma constante, 0.

> [!NOTE]
> Para que a nulidade seja corretamente definida, é vital usar ISNULL e não COALESCE. Coalesce não é uma função determinista, e assim o resultado da expressão será sempre NULLable. Com ISNULL, é diferente. É determinista. Portanto, quando a segunda parte da função ISNULL for uma constante ou uma literal, o valor resultante não será NULO.

Garantir a integridade de seus cálculos também é importante para a comutação de partição de tabela. Imagine que você tem esta tabela definida como uma tabela de fatos:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
, [product]   INT     NOT NULL
, [store]     INT     NOT NULL
, [quantity]  INT     NOT NULL
, [price]     MONEY   NOT NULL
, [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
);
```

No entanto, o campo de quantidade é uma expressão calculada. Não faz parte dos dados de origem.

Para criar seu conjunto de dados particionado, você pode querer usar o seguinte código:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]
,   [product]
,   [store]
,   [quantity]
,   [price]
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

A consulta funcionaria perfeitamente bem. O problema vem quando você tenta fazer o interruptor de partição. As definições da tabela não batem. Para fazer as definições da tabela coincidirem, modifique o CTAS para adicionar uma `ISNULL` função para preservar o atributo de nulidade da coluna.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
  [date]
, [product]
, [store]
, [quantity]
, [price]
, ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Você pode ver que a consistência do tipo e a manutenção de propriedades de nulidade em um CTAS é uma prática recomendada de engenharia. Ele ajuda a manter a integridade em seus cálculos, e também garante que a comutação de partição seja possível.

CTAS é uma das declarações mais importantes no Synapse SQL. Certifique-se compreendê-la totalmente. Consulte a documentação do [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, confira a [visão geral sobre desenvolvimento](sql-data-warehouse-overview-develop.md).
