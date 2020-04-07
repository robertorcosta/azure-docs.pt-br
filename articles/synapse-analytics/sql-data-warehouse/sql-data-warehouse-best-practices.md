---
title: Melhores práticas para pool Synapse SQL no Azure Synapse Analytics (anteriormente SQL DW)
description: Recomendações e práticas recomendadas para o desenvolvimento de soluções para pool SQL no Azure Synapse Analytics (anteriormente SQL DW).
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 4462bf0fc2057922340eb01cb8c786dbc63ce290
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745345"
---
# <a name="best-practices-for-synapse-sql-pool-in-azure-synapse-analytics-formerly-sql-dw"></a>Melhores práticas para pool Synapse SQL no Azure Synapse Analytics (anteriormente SQL DW)

Este artigo é uma coleção de práticas recomendadas para ajudá-lo a obter o melhor desempenho a partir da implantação do [pool SQL.](sql-data-warehouse-overview-what-is.md)  O objetivo deste artigo é dar-lhe algumas orientações básicas e destacar áreas importantes de foco.  

## <a name="reduce-cost-with-pause-and-scale"></a>Reduzir custos com pausa e dimensionamento

Para obter mais informações sobre como reduzir os custos por meio de pausa e dimensionamento, confira [Gerenciar computação](sql-data-warehouse-manage-compute-overview.md).

## <a name="maintain-statistics"></a>Manter as estatísticas

O pool SQL pode ser configurado para detectar e criar estatísticas automaticamente nas colunas.  Os planos de consulta criados pelo otimizador são tão bons quanto as estatísticas disponíveis.  

Recomendamos que você habilite AUTO_CREATE_STATISTICS para seus bancos de dados e mantenha as estatísticas atualizadas diariamente ou após cada carga para garantir que as estatísticas nas colunas usadas em suas consultas estejam sempre atualizadas.

Se você achar que está demorando muito para atualizar todas as suas estatísticas, você pode querer tentar ser mais seletivo sobre quais colunas precisam de atualizações estatísticas frequentes. Por exemplo, convém atualizar as colunas de data, onde novos valores podem ser adicionados, diariamente.

> [!TIP]
> Você ganhará mais benefícios por ter atualizado estatísticas sobre colunas envolvidas em adesões, colunas usadas na cláusula WHERE e colunas encontradas no GRUPO BY.

Veja também [Gerenciar estatísticas da tabela](sql-data-warehouse-tables-statistics.md), CRIAR [ESTATÍSTICAS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)e [ATUALIZAR ESTATÍSTICAS](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Usar DMVs para monitorar e otimizar suas consultas

O pool SQL possui vários DMVs que podem ser usados para monitorar a execução de consultas.  O Monitor sua carga de trabalho usando o artigo de DMVs detalha instruções passo a passo sobre como olhar os detalhes de uma consulta de execução.  

Para encontrar rapidamente as consultas nessas DMVs, usar a opção LABEL com suas consultas poderá ajudar.

Veja também [Monitorar sua carga de trabalho usando DMVs](sql-data-warehouse-manage-monitor.md), [LABEL](sql-data-warehouse-develop-label.md), [OPTION](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)e [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="tune-query-performance-with-new-product-enhancements"></a>Ajuste o desempenho da consulta com novos aprimoramentos do produto

- [Ajuste de desempenho com exibições materializadas](performance-tuning-materialized-views.md)
- [Ajuste de desempenho com o índice columnstore clusterizado ordenado](performance-tuning-ordered-cci.md)
- [Ajuste de desempenho com cache de conjunto de resultados](performance-tuning-result-set-caching.md)

## <a name="group-insert-statements-into-batches"></a>Agrupar instruções INSERT em lotes

Uma carga única para uma pequena tabela com uma instrução INSERT ou até mesmo uma recarga `INSERT INTO MyLookup VALUES (1, 'Type 1')`periódica de uma pesquisa pode funcionar bem para suas necessidades com uma declaração como .  

No entanto, se você precisar carregar milhares ou milhões de linhas ao longo do dia, poderá achar que os INSERTS individuais poderão não dar conta.  Em vez disso, desenvolva seus processos de modo que eles gravem em um arquivo e outro processo adiante-se periodicamente e carregue esse arquivo.

Veja também [INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Use o PolyBase para carregar e exportar dados rapidamente

O pool SQL suporta o carregamento e a exportação de dados através de várias ferramentas, incluindo Azure Data Factory, PolyBase e BCP.  Para pequenas quantidades de dados em que o desempenho não é essencial, qualquer ferramenta poderá ser suficiente para satisfazer suas necessidades.  No entanto, quando você estiver carregando ou exportando grandes volumes de dados ou um desempenho rápido for necessário, o PolyBase será a melhor opção.  

O PolyBase foi projetado para aproveitar a arquitetura MPP (Massively Parallel Processing) e irá carregar e exportar magnitudes de dados mais rapidamente do que qualquer outra ferramenta.  As cargas do PolyBase podem ser executadas usando CTAS ou INSERT INTO.  

> [!TIP]
> Usar o CTAS minimizará o registro em log das transações e será o modo mais rápido de carregar os dados.

Além disso, o Azure Data Factory dá suporte a cargas de PolyBase e pode alcançar um desempenho semelhante como CTAS.  O PolyBase oferece suporte a vários formatos de arquivo, incluindo os arquivos Gzip.  
  
> [!NOTE]
> Para maximizar o throughput ao usar arquivos de texto gzip, separe arquivos em 60 ou mais arquivos para maximizar o paralelismo da carga.   Para ter uma taxa de transferência total mais rápida, considere carregar os dados simultaneamente.

Veja também [dados de carga,](design-elt-data-loading.md) [guia para usar o PolyBase,](guidance-for-loading-data.md) [padrões e estratégias de carregamento de pool SQL,](https://blogs.msdn.microsoft.com/sqlcat/20../../) [Dados de carga com fábrica de dados do Azure,]( ../../data-factory/load-azure-sql-data-warehouse.md) [mover dados com a fábrica de dados do Azure,](../../data-factory/transform-data-using-machine-learning.md) [criar formato de arquivo externo](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)e criar tabela como select [(CTAS).](sql-data-warehouse-develop-ctas.md)

## <a name="load-then-query-external-tables"></a>Carregar e consultar tabelas externas

Embora o Polybase, também conhecido como tabelas externas, possa ser a maneira mais rápida de carregar dados, ele não é o ideal para consultas. Atualmente, as tabelas polybase suportam apenas arquivos azure blob e armazenamento do Azure Data Lake. Esses arquivos não tem quaisquer recursos de computação que os assegure.  

Como resultado, o pool SQL não pode descarregar este trabalho e, portanto, deve ler o arquivo inteiro carregando-o para tempdb, a fim de ler os dados.  Desse modo, se você tiver várias consultas que usarão esses dados, é melhor carregá-los apenas uma vez e fazer com que as consultas usem a tabela local.

Consulte também [o Guia para usar o PolyBase](guidance-for-loading-data.md).

## <a name="hash-distribute-large-tables"></a>Tabelas grandes com distribuição Hash

Por padrão, as tabelas são distribuídas pelo método Round Robin.  Isso facilita para os usuários começar a criar tabelas sem ter de decidir sobre como as tabelas deverão ser distribuídas.  Tabelas Round Robin podem apresentar desempenho suficiente para algumas cargas de trabalho, mas, na maioria das vezes, o desempenho será muito melhor se uma coluna de distribuição for escolhida.  

O exemplo mais comum de quando uma tabela distribuída por uma coluna superará bastante uma tabela Round Robin ocorre quando duas tabelas de fatos grandes são unidas.  

Por exemplo, se você tiver uma tabela de pedidos, que é distribuída por order_id, e uma tabela de transações, que também é distribuída por order_id, quando você unir a tabela de pedidos e a tabela de transações em order_id, a consulta se tornará uma consulta de passagem, o que significa que podemos eliminar as operações de movimentação de dados.  Menos etapas significam uma consulta mais rápida.  Menos movimento de dados também resulta em consultas mais rápidas.  

> [!TIP]
> Ao carregar uma tabela distribuída, verifique se os dados de entrada não estão classificados na chave de distribuição, pois isso reduzirá a velocidade das cargas.  

Veja os links a seguir para obter mais detalhes sobre como selecionar uma coluna de distribuição pode melhorar o desempenho, bem como como definir uma tabela distribuída na cláusula COM da sua declaração CREATE TABLE.

Veja também [visão geral da tabela](sql-data-warehouse-tables-overview.md), [distribuição da tabela,](sql-data-warehouse-tables-distribute.md) [distribuição da tabela de seleção,](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/) [TABELA CRIAR,](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)CRIAR TABELA COMO [SELEÇÃO](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="do-not-over-partition"></a>Não estender a partição

Embora a particionagem de dados possa ser eficaz para manter seus dados através da comutação de partição ou otimização de varreduras com a eliminação de partições, ter muitas partições pode retardar suas consultas.  Muitas vezes, uma estratégia de particionamento de alta granularidade, que pode funcionar bem no SQL Server pode não funcionar bem no pool SQL.  

Ter muitas partições também poderá reduzir a eficiência dos índices columnstore clusterizados se cada partição tiver menos de 1 milhão de linhas.  Tenha em mente que, nos bastidores, o pool SQL divide seus dados para você em 60 bancos de dados, portanto, se você criar uma tabela com 100 partições, isso realmente resulta em 6000 partições.  

Cada carga de trabalho é diferente, assim, o melhor conselho é fazer experiências com o particionamento para ver o que funciona melhor para sua carga de trabalho.  Considere uma granularidade menor do que pode ter funcionado para você no SQL Server.  Por exemplo, considere usar partições semanais ou mensais, em vez de partições diárias.

Veja também [particionamento de tabela](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>Minimizar os tamanhos das transações

As instruções INSERT, UPDATE e DELETE são executadas em uma transação e quando falham, devem ser revertidas.  Para minimizar a possibilidade de uma reversão longa, minimize os tamanhos das transações sempre que possível.  Isso pode ser feito dividindo as instruções INSERT, UPDATE e DELETE em partes.  

Por exemplo, se você tiver um INSERT que você espera levar 1 hora, se possível, quebre o INSERT em quatro partes, que cada uma funcionará em 15 minutos.  Aproveite casos especiais de Registro Mínimo, como CTAS, TRUNCATE, DROP TABLE ou INSERT em mesas vazias, para reduzir o risco de reversão.  

Outra maneira de eliminar as reversões é usar as operações de Metadados Somente, como a troca de partição para o gerenciamento de dados.  Por exemplo, em vez de executar uma declaração DELETE para excluir todas as linhas em uma tabela onde o order_date foi em outubro de 2001, você poderia particionar seus dados mensalmente e, em seguida, alternar a partição com dados para uma partição vazia de outra tabela (veja exemplos [DE TABELA ALTER).](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  

Para tabelas não particionadas, considere usar um CTAS para escrever os dados que deseja manter em uma tabela em vez de usar DELETE.  Se um CTAS leva o mesmo tempo, é uma operação muito mais segura para ser executada, pois tem registro mínimo de transações e pode ser cancelada rapidamente, se necessário.

Veja também [Entendendo transações,](sql-data-warehouse-develop-transactions.md) [Otimizando transações,](sql-data-warehouse-develop-best-practices-transactions.md) [Particionamento de tabelas,](sql-data-warehouse-tables-partition.md) [TABELA TRUNCATE,](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) [TABELA ALTER](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)e Criar tabela como [select (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="reduce-query-result-sizes"></a>Reduzir os tamanhos dos resultados da consulta

Esta etapa ajuda você a evitar problemas do lado do cliente causados por um grande resultado de consulta.  Você pode editar sua consulta para reduzir o número de linhas retornadas. Algumas ferramentas de geração de consulta permitem adicionar sintaxe "top N" a cada consulta.  Você também pode CETAS o resultado da consulta para uma tabela temporária e, em seguida, usar a exportação PolyBase para o processamento de nível de baixa.

## <a name="use-the-smallest-possible-column-size"></a>Use o menor tamanho de coluna possível

Ao definir seu DDL, usar o menor tipo de dados que suportará seus dados melhorará o desempenho da consulta.  Esta abordagem é particularmente importante para as colunas CHAR e VARCHAR.  

Se o maior valor em uma coluna for 25 caracteres, então, defina a coluna como VARCHAR(25).  Evite definir todas as colunas de caractere para um tamanho grande padrão.  Além disso, defina as colunas como VARCHAR quando isso for realmente necessário em vez de usar NVARCHAR.

Veja também [visão geral da tabela,](sql-data-warehouse-tables-overview.md) [tipos de dados da tabela,](sql-data-warehouse-tables-data-types.md) [TABELA CRIAR](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-temporary-heap-tables-for-transient-data"></a>Usar tabelas de heap temporárias para dados transitórios

Quando você estiver aterrissando temporariamente os dados, você pode descobrir que o uso de uma tabela de pilhas tornará o processo geral mais rápido.  Se você estiver carregando dados apenas para prepará-los antes de executar mais transformações, carregar a tabela na tabela de heap será muito mais rápido do que carregar os dados em uma tabela columnstore clusterizada.  

Além disso, o carregamento de dados em uma tabela temporária também carregará muito mais rapidamente do que carregar uma tabela em um armazenamento permanente.  As tabelas temporárias começam com um "#" e só são acessíveis pela sessão que a criou, de modo que elas só podem funcionar em cenários limitados.

As tabelas de heap são definidas na cláusula WITH de CREATE TABLE.  Se você usar uma tabela temporária, lembre-se também de criar estatísticas nela.

Veja também [tabelas temporárias,](sql-data-warehouse-tables-temporary.md) [CRIAR TABELA,](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) [CRIAR TABELA COMO SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="optimize-clustered-columnstore-tables"></a>Otimizar tabelas columnstore clusterizadas

Os índices de columnstore agrupados são uma das maneiras mais eficientes de armazenar seus dados no pool SQL.  Por padrão, as tabelas no pool SQL são criadas como Clustered ColumnStore.  É importante ter um segmento de boa qualidade para obter o melhor desempenho para as consultas nas tabelas columnstore.  

Quando as linhas são gravadas nas tabelas columnstore sob pressão da memória, a qualidade do segmento columnstore pode ficar prejudicada.  A qualidade de segmento pode ser medida pelo número de linhas em um grupo de linhas compactado.  Consulte as [causas da má qualidade do índice de columnstore](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) no artigo [Índices](sql-data-warehouse-tables-index.md) de tabela para obter instruções passo a passo sobre como detectar e melhorar a qualidade do segmento para tabelas de colunastore agrupadas.  

Como os segmentos de columnstore de alta qualidade são importantes, é uma boa ideia usar IDs de usuários que estão na classe de recursos médios ou grandes para carregar dados. Usar [unidades de data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) menores significa que você deseja atribuir uma classe de recursos maior ao usuário que está carregando.

Uma vez que as tabelas de columnstore geralmente não empurram dados para um segmento de columnstore compactado até que haja mais de 1 milhão de linhas por tabela e cada tabela de bilhar SQL seja dividida em 60 tabelas, como regra geral, as tabelas de columnstore não beneficiarão uma consulta a menos que a tabela tenha mais de 60 milhões de linhas.  Para a tabela com menos de 60 milhões de linhas, talvez não faça sentido ter um índice columnstore.  Também pode não prejudicar.  

Além disso, se você particionar seus dados, desejará considerar que cada partição precisará ter 1 milhão de linhas para se beneficiar de um índice columnstore clusterizado.  Se uma tabela tiver 100 partições, então ela precisará ter pelo menos 6 bilhões de linhas para se beneficiar de um armazenamento de colunas agrupadas (60 distribuições *100 partições* 1 milhão de linhas).  

Se a tabela não tiver as 6 bilhões de linhas neste exemplo, reduza o número de partições ou considere usar uma tabela de heap.  Também pode valer a pena testar para ver se um melhor desempenho pode ser obtido com uma tabela de heap com índices secundários em vez de uma tabela columnstore.

> [!TIP]
> Ao consultar uma tabela columnstore, as consultas serão executadas mais rapidamente se você selecionar apenas as colunas necessárias.  

Confira também [Índices de tabela](sql-data-warehouse-tables-index.md), [Guia dos índices columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) e [Recriando índices columnstore](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Usar uma classe maior de recursos para melhorar o desempenho da consulta

O pool SQL usa grupos de recursos como uma maneira de alocar memória para consultas.  Fora da caixa, todos os usuários são atribuídos à classe de pequenos recursos, que concede 100 MB de memória por distribuição.  Como sempre há 60 distribuições e cada distribuição recebe um mínimo de 100 MB, a alocação de memória total de todo o sistema é de 6.000 MB ou menos de 6 GB.  

Determinadas consultas, como junções grandes ou cargas para as tabelas columnstore clusterizado, se beneficiarão das alocações de memória maiores.  Algumas consultas, como varreduras puras, não renderão nenhum benefício.  No entanto, utilizar classes de recursos maiores reduz a concorrência, então você vai querer levar esse impacto em consideração antes de mover todos os seus usuários para uma grande classe de recursos.

Consulte também [classes de recursos para gerenciamento de carga de trabalho](resource-classes-for-workload-management.md).

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Usar uma Classe de Recurso Menor para Aumentar a Simultaneidade

Se você notar que as consultas de usuário parecem ter um longo atraso, pode ser que seus usuários estejam sendo executados em classes de recursos maiores e estão consumindo muitos slots de suturas, fazendo com que outras filas se enfileiram.  Para saber se as consultas dos usuários estão em fila, execute `SELECT * FROM sys.dm_pdw_waits` para ver se alguma linha é retornada.

Consulte também classes de recursos para gerenciamento de [carga horária,](resource-classes-for-workload-management.md) [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="other-resources"></a>Outros recursos

Confira também nosso artigo de [Solução de problemas](sql-data-warehouse-troubleshoot.md) para conhecer os problemas e as soluções comuns.

Se você não encontrou o que está procurando neste artigo, tente usar o "Search for docs" no lado esquerdo desta página para pesquisar todos os documentos do Azure Synapse.  O [Azure Synapse Forum](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) é um lugar para você postar perguntas para outros usuários e para o Grupo de Produtos Sinapse Azure. Monitoramos ativamente esse fórum para garantir que suas perguntas sejam respondidas por outro usuário ou um de nós.  

Se você preferir fazer suas perguntas sobre Stack Overflow, também temos [um Fórum de Estouro de Pilha de Sinapse Azure](https://stackoverflow.com/questions/tagged/azure-sqldw).

Por favor, use a página [Azure Synapse Feedback](https://feedback.azure.com/forums/307516-sql-data-warehouse) para fazer solicitações de recursos.  Adicionar suas solicitações ou recomendar outras solicitações realmente nos ajudará a priorizar os recursos.
