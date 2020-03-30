---
title: Melhores práticas de desenvolvimento
description: Recomendações e práticas recomendadas que você deve conhecer à medida que desenvolve soluções para o pool Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/04/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a4902d2817c98b002b9767ad55d7ac60f5cb0c93
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350699"
---
# <a name="development-best-practices-for-synapse-sql-pool"></a>Práticas recomendadas de desenvolvimento para pool Synapse SQL
Este artigo descreve orientações e práticas recomendadas à medida que você desenvolve sua solução de data warehouse. 

## <a name="tune-query-performance-with-new-product-enhancements"></a>Ajuste o desempenho da consulta com novos aprimoramentos do produto  
- [Ajuste de desempenho com exibições materializadas](performance-tuning-materialized-views.md)
- [Ajuste de desempenho com o índice columnstore clusterizado ordenado](performance-tuning-ordered-cci.md)
- [Ajuste de desempenho com cache de conjunto de resultados](performance-tuning-result-set-caching.md)

## <a name="reduce-cost-with-pause-and-scale"></a>Reduzir custos com pausa e dimensionamento
Para obter mais informações sobre como reduzir os custos por meio de pausa e dimensionamento, confira [Gerenciar computação](sql-data-warehouse-manage-compute-overview.md). 

## <a name="maintain-statistics"></a>Manter as estatísticas
O pool Synapse SQL pode ser configurado para detectar e criar estatísticas automaticamente nas colunas.  Os planos de consulta criados pelo otimizador são tão bons quanto as estatísticas disponíveis.  Recomendamos que você habilite AUTO_CREATE_STATISTICS para seus bancos de dados e mantenha as estatísticas atualizadas diariamente ou após cada carga para garantir que as estatísticas nas colunas usadas em suas consultas estejam sempre atualizadas. 

Se você achar que está demorando muito para atualizar todas as suas estatísticas, você pode querer tentar ser mais seletivo sobre quais colunas precisam de atualizações estatísticas frequentes. Por exemplo, convém atualizar as colunas de data, onde novos valores podem ser adicionados, diariamente. **Você ganhará mais benefícios por ter atualizado estatísticas sobre colunas envolvidas em adesões, colunas usadas na cláusula WHERE e colunas encontradas no GRUPO BY.**

Confira também [Gerenciar as estatísticas da tabela](sql-data-warehouse-tables-statistics.md), [CREATE STATISTICS](sql-data-warehouse-tables-statistics.md), [UPDATE STATISTICS](sql-data-warehouse-tables-statistics.md#updating-statistics)

## <a name="hash-distribute-large-tables"></a>Tabelas grandes com distribuição Hash
Por padrão, as tabelas são distribuídas pelo método Round Robin.  Esse design facilita que os usuários ainiciem a criação de tabelas sem ter que decidir como suas tabelas devem ser distribuídas.  Tabelas Round Robin podem apresentar desempenho suficiente para algumas cargas de trabalho, mas, na maioria das vezes, o desempenho será muito melhor se uma coluna de distribuição for escolhida.  O exemplo mais comum de quando uma tabela distribuída por uma coluna superará bastante uma tabela Round Robin ocorre quando duas tabelas de fatos grandes são unidas.  Por exemplo, se você tiver uma tabela de pedidos, que é distribuída por order_id, e uma tabela de transações, que também é distribuída por order_id, quando você unir a tabela de pedidos e a tabela de transações em order_id, a consulta se tornará uma consulta de passagem, o que significa que podemos eliminar as operações de movimentação de dados.  Menos etapas significam uma consulta mais rápida.  Menos movimento de dados também resulta em consultas mais rápidas.  Esta explicação só arranha a superfície. Ao carregar uma tabela distribuída, verifique se os dados de entrada não estão classificados na chave de distribuição, pois isso reduzirá a velocidade das cargas.  Veja os links abaixo para obter mais detalhes sobre como selecionar uma coluna de distribuição pode melhorar o desempenho, bem como como definir uma tabela distribuída na cláusula COM da sua declaração CREATE TABLES.

Confira também [Visão geral das tabelas](sql-data-warehouse-tables-overview.md), [Distribuição de tabelas](sql-data-warehouse-tables-distribute.md), [Selecionando a distribuição de tabelas](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/), [CREATE TABLE](sql-data-warehouse-tables-overview.md), [CREATE TABLE AS SELECT](sql-data-warehouse-develop-ctas.md)

## <a name="do-not-over-partition"></a>Não estender a partição
Embora a particionagem de dados possa ser eficaz para manter seus dados através da comutação de partição ou otimização de varreduras com a eliminação de partições, ter muitas partições pode retardar suas consultas.  Muitas vezes, uma estratégia de particionamento de alta granularidade que pode funcionar bem no SQL Server pode não funcionar bem no pool SQL.  Ter muitas partições também poderá reduzir a eficiência dos índices columnstore clusterizados se cada partição tiver menos de 1 milhão de linhas.  Tenha em mente que, nos bastidores, o pool SQL divide seus dados para você em 60 bancos de dados, portanto, se você criar uma tabela com 100 partições, isso realmente resulta em 6000 partições.  Cada carga de trabalho é diferente, assim, o melhor conselho é fazer experiências com o particionamento para ver o que funciona melhor para sua carga de trabalho.  Considere uma granularidade menor do que pode ter funcionado para você no SQL Server.  Por exemplo, considere usar partições semanais ou mensais, em vez de partições diárias.

Confira também [Particionamento de tabelas](sql-data-warehouse-tables-partition.md)

## <a name="minimize-transaction-sizes"></a>Minimizar os tamanhos das transações
As instruções INSERT, UPDATE e DELETE são executadas em uma transação e quando falham, devem ser revertidas.  Para minimizar a possibilidade de uma reversão longa, minimize os tamanhos das transações sempre que possível.  Isso pode ser feito dividindo as instruções INSERT, UPDATE e DELETE em partes.  Por exemplo, se você tiver um INSERT, que você espera levar 1 hora, se possível, quebre o INSERT em quatro partes, que cada uma funcionará em 15 minutos.  Aproveite os casos especiais de Registro em Log Mínimo, como CTAS, TRUNCATE, DROP TABLE ou INSERT, para esvaziar as tabelas, reduzindo o risco de reversão.  Outra maneira de eliminar as reversões é usar as operações de Metadados Somente, como a troca de partição para o gerenciamento de dados.  Por exemplo, em vez de executar uma instrução DELETE para excluir todas as linhas em uma tabela onde order_date foi em outubro de 2001, você poderia particionar os dados mensalmente e, em seguida, trocar a partição com os dados por uma partição vazia a partir de outra tabela (consulte exemplos de ALTER TABLE).  Para as tabelas não particionadas, considere usar um CTAS para gravar os dados que você deseja manter em uma tabela em vez de usar DELETE.  Se um CTAS leva o mesmo tempo, é uma operação muito mais segura para ser executada, pois tem registro mínimo de transações e pode ser cancelada rapidamente, se necessário.

Veja também [Entendendo transações,](sql-data-warehouse-develop-transactions.md) [Otimizando transações,](sql-data-warehouse-develop-best-practices-transactions.md) [Particionamento de tabelas,](sql-data-warehouse-tables-partition.md) [TABELA TRUNCATE,](https://msdn.microsoft.com/library/ms177570.aspx) [TABELA ALTER,](https://msdn.microsoft.com/library/ms190273.aspx) [Criar tabela como select (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="use-the-smallest-possible-column-size"></a>Use o menor tamanho de coluna possível
Ao definir seu DDL, usar o menor tipo de dados que suportará seus dados melhorará o desempenho da consulta.  Isso é especialmente importante para as colunas CHAR e VARCHAR.  Se o maior valor em uma coluna for 25 caracteres, então, defina a coluna como VARCHAR(25).  Evite definir todas as colunas de caractere para um tamanho grande padrão.  Além disso, defina as colunas como VARCHAR quando isso for realmente necessário em vez de usar NVARCHAR.

Confira também [Visão geral das tabelas](sql-data-warehouse-tables-overview.md), [Tipos de dados da tabela](sql-data-warehouse-tables-data-types.md), [CREATE TABLE](sql-data-warehouse-tables-overview.md)

## <a name="optimize-clustered-columnstore-tables"></a>Otimizar tabelas columnstore clusterizadas
Os índices de columnstore agrupados são uma das maneiras mais eficientes de armazenar seus dados no pool SQL.  Por padrão, as tabelas no pool SQL são criadas como Clustered ColumnStore.  É importante ter um segmento de boa qualidade para obter o melhor desempenho para as consultas nas tabelas columnstore.  Quando as linhas são gravadas nas tabelas columnstore sob pressão da memória, a qualidade do segmento columnstore pode ficar prejudicada.  A qualidade de segmento pode ser medida pelo número de linhas em um grupo de linhas compactado.  Confira as [Causas da má qualidade de índice columnstore](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) no artigo sobre [Índices de tabela](sql-data-warehouse-tables-index.md) para obter instruções passo a passo sobre como detectar e melhorar a qualidade do segmento para as tabelas columnstore clusterizadas.  Como os segmentos de columnstore de alta qualidade são importantes, é uma boa ideia usar IDs de usuários que estão na classe de recursos médios ou grandes para carregar dados. Usar [unidades de data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) menores significa que você deseja atribuir uma classe de recursos maior ao usuário que está carregando.

Uma vez que as tabelas de columnstore geralmente não empurram dados para um segmento de columnstore compactado até que haja mais de 1 milhão de linhas por tabela e cada tabela de bilhar SQL seja dividida em 60 tabelas, como regra geral, as tabelas de columnstore não beneficiarão uma consulta a menos que a tabela tem mais de 60 milhões de linhas.  Para a tabela com menos de 60 milhões de linhas, talvez não faça sentido ter um índice columnstore.  Também pode não prejudicar.  Além disso, se você particionar seus dados, desejará considerar que cada partição precisará ter 1 milhão de linhas para se beneficiar de um índice columnstore clusterizado.  Se uma tabela tiver 100 partições, será necessário ter pelo menos 6 bilhões de linhas para se beneficiar de um columnstore clusterizado (60 distribuições * 100 partições * 1 milhão de linhas).  Se a tabela não tiver as 6 bilhões de linhas neste exemplo, reduza o número de partições ou considere usar uma tabela de heap.  Também pode valer a pena testar para ver se um melhor desempenho pode ser obtido com uma tabela de heap com índices secundários em vez de uma tabela columnstore.

Ao consultar uma tabela columnstore, as consultas serão executadas mais rapidamente se você selecionar apenas as colunas necessárias.  

Confira também [Índices de tabela](sql-data-warehouse-tables-index.md), [Guia dos índices columnstore](https://msdn.microsoft.com/library/gg492088.aspx) e [Recriando índices columnstore](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)

## <a name="next-steps"></a>Próximas etapas
Se você não encontrar o que está procurando neste artigo, tente usar o "Search for docs" no lado esquerdo desta página para pesquisar todos os documentos do Azure Synapse Analytics.  O [Fórum do SQL Data Warehouse do Azure](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) é um local para você fazer perguntas a outros usuários e ao Grupo de Produtos do SQL Data Warehouse.  Monitoramos ativamente esse fórum para garantir que suas perguntas sejam respondidas por outro usuário ou um de nós.  Caso você prefira fazer perguntas sobre o Stack Overflow, também temos um [Fórum sobre o Stack Overflow do Azure SQL Data Warehouse](https://stackoverflow.com/questions/tagged/azure-sqldw).

Por fim, use a página [Comentários do Azure SQL Data Warehouse](https://feedback.azure.com/forums/307516-sql-data-warehouse) para fazer solicitações de recurso.  Adicionar suas solicitações ou recomendar outras solicitações realmente nos ajudará a priorizar os recursos.


