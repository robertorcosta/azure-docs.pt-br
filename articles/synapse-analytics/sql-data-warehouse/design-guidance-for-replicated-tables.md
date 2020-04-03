---
title: Orientação de design para tabelas replicadas
description: Recomendações para projetar tabelas replicadas no Synapse SQL
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/19/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 0b240c45afcb2374f41eb26e86e46b106e314e76
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582234"
---
# <a name="design-guidance-for-using-replicated-tables-in-synapse-sql"></a>Orientação de design para o uso de tabelas replicadas no Synapse SQL

Este artigo fornece recomendações para projetar tabelas replicadas em seu esquema Synapse SQL. Use essas recomendações para melhorar o desempenho da consulta ao reduzir a movimentação de dados e a complexidade da consulta.

> [!VIDEO https://www.youtube.com/embed/1VS_F37GI9U]

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que você está familiarizado com os conceitos de distribuição de dados e movimentação de dados no Synapse SQL.Para saber mais, consulte o artigo sobre [arquitetura](massively-parallel-processing-mpp-architecture.md). 

Como parte do design de tabela, compreenda seus dados o tanto quanto possível e a maneira como eles são consultados.Por exemplo, considere estas perguntas:

- Qual é o tamanho da tabela?   
- Com que frequência a tabela é atualizada?   
- Tenho tabelas de fato e dimensão em um banco de dados Synapse SQL?   

## <a name="what-is-a-replicated-table"></a>O que é uma tabela replicada?

Uma tabela replicada tem uma cópia completa da tabela acessível em cada nó de computação. Replicar uma tabela elimina a necessidade de transferir dados entre nós de Computação antes de uma junção ou agregação. Como a tabela tem várias cópias, as tabelas replicadas funcionam melhor quando o tamanho da tabela é menor que 2 GB compactados.  2 GB não é um limite difícil.  Se os dados forem estáticos e não mudarem, você poderá replicar tabelas maiores.

O diagrama a seguir mostra uma tabela replicada que é acessível em cada nó de computação. No Synapse SQL, a tabela replicada é totalmente copiada para um banco de dados de distribuição em cada nó de computação. 

![Tabela replicada](./media/design-guidance-for-replicated-tables/replicated-table.png "Tabela replicada")  

As tabelas replicadas funcionam bem para tabelas de dimensões em um esquema estelar. As tabelas de dimensões são tipicamente unidas a tabelas de fatos que são distribuídas de forma diferente da tabela de dimensões.  As dimensões são geralmente de um tamanho que torna viável armazenar e manter várias cópias. As dimensões armazenam dados descritivos que são alterados lentamente, como nome e endereço do cliente e detalhes do produto. A lenta mudança da natureza dos dados leva a menos manutenção da tabela replicada. 

Considere usar uma tabela replicada quando:

- O tamanho da tabela no disco for menor que 2 GB, independentemente do número de linhas. Para localizar o tamanho de uma tabela, você pode usar o comando [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest): `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')`. 
- A tabela é usada em junções que normalmente exigiriam a movimentação de dados. Ao unir tabelas que não são distribuídas na mesma coluna, como uma tabela distribuída em hash a uma tabela round robin, a movimentação de dados é necessária para concluir a consulta.  Se uma das tabelas é pequena, considere uma tabela replicada. É recomendável usar tabelas replicadas em vez de tabelas round robin na maioria dos casos. Para exibir as operações de movimentação de dados em planos de consulta, use [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  O BroadcastMoveOperation é a operação de movimentação de dados típica que pode ser eliminada por meio de uma tabela replicada.  
 
As tabelas replicadas poderão não render o melhor desempenho de consulta quando:

- A tabela tiver operações frequentes de inserção, atualização e exclusão.As operações de linguagem de manipulação de dados (DML) requerem uma reconstrução da tabela replicada.Recompilar com frequência pode causar um desempenho mais lento.
- O banco de dados Synapse SQL é dimensionado com freqüência. O dimensionamento de um banco de dados altera o número de nós de computação, o que incorre na reconstrução da tabela replicada.
- A tabela tem um grande número de colunas, mas as operações de dados normalmente acessam somente um pequeno número de colunas. Neste cenário, em vez de replicar toda a tabela, pode ser mais eficaz fazer a distribuição da tabela e, em seguida, criar um índice nas colunas acessadas com frequência. Quando uma consulta requer movimentação de dados, apenas os dados das colunas solicitadas são movidos.

## <a name="use-replicated-tables-with-simple-query-predicates"></a>Usar tabelas replicadas com predicados de consulta simples

Antes de optar por distribuir ou replicar uma tabela, considere os tipos de consultas que você planeja executar em relação à tabela. Sempre que possível,

- Use tabelas replicadas para consultas com predicados de consulta simples, como igualdade ou desigualdade.
- Use tabelas distribuídas para consultas com predicados de consulta complexos, como CURTIR ou NÃO CURTIR.

As consultas de uso intensivo da CPU apresentam melhor desempenho quando o trabalho é distribuído entre todos os nós de computação. Por exemplo, as consultas que executam cálculos em cada linha de uma tabela apresentam um desempenho melhor nas tabelas distribuídas do que nas tabelas replicadas. Como uma tabela replicada é armazenada na íntegra em cada nó de computação, uma consulta de uso intensivo da CPU em uma tabela replicada é executada em toda a tabela em cada nó de computação. A computação extra pode diminuir o desempenho da consulta.

Por exemplo, esta consulta tem um predicado complexo.  Ele é executado mais rápido quando os dados estão em uma tabela distribuída em vez de uma tabela replicada. Neste exemplo, os dados podem ser distribuídos por round-robin.

```sql

SELECT EnglishProductName 
FROM DimProduct 
WHERE EnglishDescription LIKE '%frame%comfortable%'
       
```       
           
## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Converter tabelas round robin existentes em tabelas replicadas
Se você já possui tabelas redondas, recomendamos convertê-las em tabelas replicadas se atenderem aos critérios descritos neste artigo. As tabelas replicadas melhoram o desempenho em tabelas round robin porque elas eliminam a necessidade da movimentação de dados.  Uma tabela round robin sempre requer a movimentação de dados para as junções. 

Este exemplo usa [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para alterar a tabela DimSalesTerritory para uma tabela replicada. Este exemplo funciona independentemente de DimSalesTerritory ser distribuído por hash ou por round robin.

```sql
CREATE TABLE [dbo].[DimSalesTerritory_REPLICATE]   
WITH   
  (   
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = REPLICATE  
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]
OPTION  (LABEL  = 'CTAS : DimSalesTerritory_REPLICATE') 

-- Switch table names
RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[DimSalesTerritory_REPLICATE] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
``` 
    
### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Exemplo de desempenho de consulta de round-robin versus replicado 
    
Uma tabela replicada não requer nenhuma movimentação de dados para as junções porque a tabela completa já está presente em cada nó de computação. Se as tabelas de dimensões forem distribuídas por round robin, uma união copiará a tabela de dimensões na íntegra para cada nó de computação. Para mover os dados, o plano de consulta contém uma operação chamada BroadcastMoveOperation. Esse tipo de operação de movimentação de dados reduz o desempenho da consulta e é eliminada usando tabelas replicadas. Para exibir as etapas do plano de consulta, use a exibição de catálogo do sistema [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  

Por exemplo, na consulta a seguir em relação ao esquema AdventureWorks, a tabela `FactInternetSales` é distribuída em hash. As tabelas `DimDate` e `DimSalesTerritory` são tabelas de dimensões menores. Esta consulta retorna o total de vendas na América do Norte, do ano fiscal de 2004:

```sql
SELECT [TotalSalesAmount] = SUM(SalesAmount)
FROM dbo.FactInternetSales s
INNER JOIN dbo.DimDate d
  ON d.DateKey = s.OrderDateKey
INNER JOIN dbo.DimSalesTerritory t
  ON t.SalesTerritoryKey = s.SalesTerritoryKey
WHERE d.FiscalYear = 2004
  AND t.SalesTerritoryGroup = 'North America'
```
Recriamos `DimDate` e `DimSalesTerritory` como tabelas round robin. Como resultado, a consulta mostrou o seguinte plano de consulta, que tem várias operações de movimentação difundidas: 
 
![Plano de consulta round robin](./media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg) 

Recriamos `DimDate` e `DimSalesTerritory` como tabelas replicadas e executamos a consulta novamente. O plano de consulta resultante é muito menor e não tem nenhuma movimentação difundida.

![Plano de consulta replicado](./media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg) 


## <a name="performance-considerations-for-modifying-replicated-tables"></a>Considerações sobre o desempenho para modificar as tabelas replicadas

Uma tabela replicada é implementada mantendo uma versão mestre da tabela. Ele copia a versão mestre para um banco de dados de distribuição em cada nó de computação. Quando há uma mudança, a tabela-mestre é atualizada primeiro. Em seguida, a tabela em cada nó de computação é reconstruída. Uma recompilação de uma tabela replicada inclui copiar a tabela para cada nó de computação e, em seguida, compilar os índices.  Por exemplo, uma tabela replicada em um DW400 tem 5 cópias dos dados.  Uma cópia mestre e uma cópia completa em cada nó de Computação.  Todos os dados são armazenados em bancos de dados de distribuição para suportar declarações de modificação de dados mais rápidas e operações de dimensionamento flexíveis. 

As recompilações são necessárias depois que:
- Os dados são carregados ou modificados
- A ocorrência Synapse SQL é dimensionada para um nível diferente
- A definição da tabela é atualizada

As recompilações não são necessárias após:
- Pausar a operação
- Retomar a operação

A recompilação não acontece imediatamente depois que os dados são modificados. Em vez disso, a recompilação é acionada na primeira vez em que uma consulta faz uma seleção pela tabela.  A consulta que disparou a recompilação imediatamente lê da versão mestre da tabela, enquanto os dados são copiados de forma assíncrona para cada nó de Computação. Até que a cópia de dados esteja concluída, as consultas subsequentes continuarão a usar a versão mestre da tabela.  Se ocorrer qualquer atividade na tabela replicada que força outra recompilação, a cópia dos dados é invalidada e a próxima instrução SELECT disparará dados a serem copiados novamente. 

### <a name="use-indexes-conservatively"></a>Use os índices de forma prudente

As práticas de indexação padrão se aplicam às tabelas replicadas. Cada índice de tabela replicado é reconstruído como parte de uma reconstrução do índice. Use somente os índices quando o ganho de desempenho superar o custo de recompilar os índices.  
 
### <a name="batch-data-loads"></a>Cargas de dados em lotes
Ao carregar dados em tabelas replicadas, tente minimizar as recompilações ao enviar as cargas de envio em lote juntas. Execute todas as cargas em lote antes de executar as instruções SELECT.

Por exemplo, esse padrão de carga carrega dados de quatro fontes e invoca quatro recompilações. 

        Load from source 1.
- A instrução SELECT aciona a recompilação 1.
        Carregar da fonte de dados 2.
- A instrução SELECT aciona a recompilação 2.
- Carregar da fonte de dados 3.
- A instrução SELECT aciona a recompilação 3.
- Carregar da fonte de dados 4.
- A instrução SELECT aciona a recompilação 4.

Por exemplo, esse padrão de carga carrega dados de quatro fontes, mas invoca apenas uma recompilação.

- Carregar da fonte de dados 1.
- Carregar da fonte de dados 2.
- Carregar da fonte de dados 3.
- Carregar da fonte de dados 4.
- A instrução SELECT aciona a recompilação.

### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Recompilar uma tabela replicada após um carregamento em lote

Para garantir tempos de execução de consulta consistentes, considere forçar a compilação das tabelas replicadas após um carregamento em lote. Caso contrário, a primeira consulta ainda usará a movimentação de dados para concluir a consulta. 

Essa consulta usa o DMV [sys.pdw_replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para listar as tabelas replicadas que foram modificadas, mas não foram recompiladas.

```sql 
SELECT [ReplicatedTable] = t.[name]
  FROM sys.tables t  
  JOIN sys.pdw_replicated_table_cache_state c  
    ON c.object_id = t.object_id 
  JOIN sys.pdw_table_distribution_properties p 
    ON p.object_id = t.object_id 
  WHERE c.[state] = 'NotReady'
    AND p.[distribution_policy_desc] = 'REPLICATE'
```
 
Para disparar uma recompilação, execute a instrução a seguir em cada tabela na saída anterior. 

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
```

## <a name="next-steps"></a>Próximas etapas

Para criar uma tabela replicada, use uma dessas instruções:

- [CRIAR TABELA](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CRIAR TABELA COMO SELECIONAR](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Para obter uma visão geral das tabelas distribuídas, consulte [Tabelas distribuídas](sql-data-warehouse-tables-distribute.md).
