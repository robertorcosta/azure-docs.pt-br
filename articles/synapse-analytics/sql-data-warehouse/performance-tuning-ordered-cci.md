---
title: Ajuste de desempenho com o índice columnstore clusterizado ordenado
description: Recomendações e considerações que você deve saber ao usar o índice de columnstore em cluster ordenado para melhorar o desempenho da consulta.
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
ms.openlocfilehash: 088a0d10b96a30ef830b4e8a8dc12c19127141db
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417050"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Ajuste de desempenho com o índice columnstore clusterizado ordenado  

Quando os usuários consultam uma tabela de columnstore no pool Synapse SQL, o otimizador verifica os valores mínimos e máximos armazenados em cada segmento.  Segmentos que estão fora dos limites do predicado de consulta não são lidos de disco para memória.  Uma consulta pode obter um desempenho mais rápido se o número de segmentos a serem lidos e seu tamanho total for pequeno.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Índice de columnstore agrupado ordenado versus não ordenado

Por padrão, para cada tabela criada sem uma opção de índice, um componente interno (construtor de índices) cria um índice de colunastore (CCI) não ordenado.  Os dados em cada coluna são compactados em um segmento de grupo de linhas CCI separado.  Há metadados na faixa de valor de cada segmento, de modo que segmentos que estão fora dos limites do predicado de consulta não são lidos a partir do disco durante a execução da consulta.  O CCI oferece o mais alto nível de compactação de dados e reduz o tamanho dos segmentos para ler para que as consultas possam ser executadas mais rapidamente. No entanto, como o construtor de índices não classifica os dados antes de comprimi-los em segmentos, segmentos com faixas de valor sobrepostas podem ocorrer, fazendo com que as consultas leiam mais segmentos do disco e demorem mais para serem concluídas.  

Ao criar um CCI ordenado, o mecanismo Synapse SQL classifica os dados existentes na memória pelas teclas de ordem antes que o construtor de índiceos os comprima em segmentos de índice.  Com dados classificados, a sobreposição do segmento é reduzida permitindo que as consultas tenham uma eliminação de segmento mais eficiente e, portanto, um desempenho mais rápido, pois o número de segmentos a serem lidos a partir do disco é menor.  Se todos os dados podem ser classificados na memória de uma só vez, então a sobreposição do segmento pode ser evitada.  Devido a grandes tabelas em data warehouses, esse cenário não acontece com frequência.  

Para verificar os intervalos de segmento de uma coluna, execute o seguinte comando com o nome da tabela e o nome da coluna:

```sql
SELECT o.name, pnp.index_id, 
cls.row_count, pnp.data_compression_desc, 
pnp.pdw_node_id, pnp.distribution_id, cls.segment_id, 
cls.column_id, 
cls.min_data_id, cls.max_data_id, 
cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table Name>' and cols.name = '<Column Name>'  and TMap.physical_name  not like '%HdTable%'
ORDER BY o.name, pnp.distribution_id, cls.min_data_id 


```

> [!NOTE] 
> Em uma tabela CC ordenada, os novos dados resultantes do mesmo lote de operações de DML ou de carregamento de dados são classificados dentro desse lote, não há classificação global em todos os dados da tabela.  Os usuários podem RECONSTRUIR o CCI ordenado para classificar todos os dados na tabela.  No Synapse SQL, o índice de columnstore REBUILD é uma operação offline.  Para uma tabela particionada, o REBUILD é feito uma partição de cada vez.  Os dados na partição que está sendo reconstruída estão "offline" e indisponíveis até que o REBUILD esteja completo para essa partição. 

## <a name="query-performance"></a>Desempenho de consulta

O ganho de desempenho de uma consulta a partir de um CCI ordenado depende dos padrões de consulta, do tamanho dos dados, do quão bem os dados são classificados, da estrutura física dos segmentos e da classe DWU e de recurso escolhida para a execução da consulta.  Os usuários devem rever todos esses fatores antes de escolher as colunas de pedidos ao projetar uma tabela CCI ordenada.

Consultas com todos esses padrões normalmente são mais rápidas com o CCI ordenado.  
1. As consultas têm igualdade, desigualdade ou predicados de alcance
1. As colunas de predicado e as colunas CCI ordenadas são as mesmas.  
1. As colunas de predicado são usadas na mesma ordem que a coluna ordinal das colunas CC ordenadas.  
 
Neste exemplo, a tabela T1 tem um índice de columnstore agrupado ordenado na seqüência de Col_C, Col_B e Col_A.

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A)

```

O desempenho da consulta 1 pode beneficiar mais do CCI encomendado do que as outras três consultas. 

```sql
-- Query #1: 

SELECT * FROM T1 WHERE Col_C = 'c' AND Col_B = 'b' AND Col_A = 'a';

-- Query #2

SELECT * FROM T1 WHERE Col_B = 'b' AND Col_C = 'c' AND Col_A = 'a';

-- Query #3
SELECT * FROM T1 WHERE Col_B = 'b' AND Col_A = 'a';

-- Query #4
SELECT * FROM T1 WHERE Col_A = 'a' AND Col_C = 'c';

```

## <a name="data-loading-performance"></a>Desempenho de carregamento de dados

O desempenho do carregamento de dados em uma tabela CCI ordenada é semelhante a uma tabela particionada.  O carregamento de dados em uma tabela CCI ordenada pode levar mais tempo do que uma tabela CCI não ordenada por causa da operação de classificação de dados, no entanto, as consultas podem ser executadas mais rapidamente depois com o CCI ordenado.  

Aqui está um exemplo de comparação de desempenho de carregamento de dados em tabelas com diferentes esquemas.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


Aqui está um exemplo de comparação de desempenho de consulta entre O CCI e o CCI ordenado.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>Reduzir a sobreposição do segmento

O número de segmentos sobrepostos depende do tamanho dos dados para classificar, da memória disponível e do grau máximo de configuração do paralelismo (MAXDOP) durante a criação do CCI ordenado. Abaixo estão as opções para reduzir a sobreposição do segmento ao criar o CCI ordenado.

- Use a classe de recursos xlargerc em um DWU mais alto para permitir mais memória para classificação de dados antes que o construtor de índicecomprima os dados em segmentos.  Uma vez em um segmento de índice, a localização física dos dados não pode ser alterada.  Não há classificação de dados dentro de um segmento ou entre segmentos.  

- Criar CCI encomendado com MAXDOP = 1.  Cada segmento usado para a criação de CCI ordenado funciona em um subconjunto de dados e os classifica localmente.  Não há classificação global entre dados classificados por diferentes segmentos.  O uso de roscas paralelas pode reduzir o tempo para criar um CCI ordenado, mas gerará segmentos mais sobrepostos do que usando um único segmento.  Atualmente, a opção MAXDOP só é suportada na criação de uma tabela CCI ordenada usando o comando CREATE TABLE AS SELECT.  A criação de um CCI ordenado via CREATE INDEX ou comandos CREATE TABLE não suporta a opção MAXDOP. Por exemplo,

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```

- Pré-classifique os dados pelas teclas de classificação antes de carregá-los em tabelas.

Aqui está um exemplo de uma distribuição ordenada da tabela CCI que tem zero sobreposição de segmento seguindo as recomendações acima. A tabela CC ordenada é criada em um banco de dados DWU1000c via CTAS a partir de uma tabela de pilha de 20 GB usando MAXDOP 1 e xlargerc.  O CCI é encomendado em uma coluna BIGINT sem duplicatas.  

![Segment_No_Overlapping](./media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>Criar CCI encomendado em mesas grandes

Criar um CCI ordenado é uma operação offline.  Para tabelas sem partições, os dados não estarão acessíveis aos usuários até que o processo de criação do CCI ordenado seja concluído.   Para tabelas particionadas, uma vez que o mecanismo cria a partição DE CCI ordenada por partição, os usuários ainda podem acessar os dados em partições onde a criação do CCI ordenado não está em processo.   Você pode usar esta opção para minimizar o tempo de inatividade durante a criação do CCI ordenado em tabelas grandes: 

1.    Crie partições na mesa grande alvo (chamada Table_A).
2.    Crie uma tabela CCI ordenada vazia (chamada Table_B) com a mesma tabela e esquema de partição da Tabela A.
3.    Mude uma partição da Tabela A para a Tabela B.
4.    Executar ALTER INDEX <Ordered_CCI_Index> ON <Table_B> RECONSTRUÇÃO PARTIÇÃO = <Partition_ID> na Tabela B para reconstruir a partição comutada.  
5.    Repita os passos 3 e 4 para cada partição em Table_A.
6.    Uma vez que todas as partições sejam trocadas de Table_A para Table_B e tenham sido reconstruídas, solte Table_A e renomeie Table_B para Table_A. 

## <a name="examples"></a>Exemplos

**A. Verificar as colunas encomendadas e ordenar a ordinal:**

```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. Para alterar a ordinal da coluna, adicionar ou remover colunas da lista de pedidos ou alterar de CCI para CCI ordenado:**

```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](sql-data-warehouse-overview-develop.md).
