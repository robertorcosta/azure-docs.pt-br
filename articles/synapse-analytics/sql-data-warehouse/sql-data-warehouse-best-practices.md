---
title: Práticas recomendadas para o pool do SQL Synapse no Azure Synapse Analytics (anteriormente conhecido como SQL DW)
description: Recomendações e práticas recomendadas para o desenvolvimento de soluções para o pool do SQL no Azure Synapse Analytics (anteriormente conhecido como SQL DW).
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80745345"
---
# <a name="best-practices-for-synapse-sql-pool-in-azure-synapse-analytics-formerly-sql-dw"></a>Práticas recomendadas para o pool do SQL Synapse no Azure Synapse Analytics (anteriormente conhecido como SQL DW)

Este artigo é uma coleção de práticas recomendadas para ajudá-lo a obter o desempenho ideal da implantação do seu [pool do SQL](sql-data-warehouse-overview-what-is.md) .  A finalidade deste artigo é fornecer algumas diretrizes básicas e destacar áreas importantes de foco.  

## <a name="reduce-cost-with-pause-and-scale"></a>Reduzir custos com pausa e dimensionamento

Para obter mais informações sobre como reduzir os custos por meio de pausa e dimensionamento, confira [Gerenciar computação](sql-data-warehouse-manage-compute-overview.md).

## <a name="maintain-statistics"></a>Manter as estatísticas

O pool SQL pode ser configurado para detectar e criar estatísticas automaticamente em colunas.  Os planos de consulta criados pelo otimizador são tão bons quanto as estatísticas disponíveis.  

Recomendamos que você habilite AUTO_CREATE_STATISTICS para seus bancos de dados e mantenha as estatísticas atualizadas diariamente ou após cada carga para garantir que as estatísticas nas colunas usadas em suas consultas estejam sempre atualizadas.

Se você achar que está demorando muito para atualizar todas as suas estatísticas, convém tentar ser mais seletivos sobre quais colunas precisam de atualizações de estatísticas frequentes. Por exemplo, convém atualizar as colunas de data, onde novos valores podem ser adicionados, diariamente.

> [!TIP]
> Você obterá mais benefícios com a atualização de estatísticas em colunas envolvidas em junções, colunas usadas na cláusula WHERE e colunas encontradas em GROUP BY.

Consulte também [gerenciar estatísticas de tabela](sql-data-warehouse-tables-statistics.md), [criar estatísticas](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)e [atualizar estatísticas](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Usar DMVs para monitorar e otimizar suas consultas

O pool do SQL tem várias DMVs que podem ser usadas para monitorar a execução da consulta.  O artigo monitorar sua carga de trabalho usando DMVs fornece detalhes passo a passo sobre como examinar os detalhes de uma consulta em execução.  

Para encontrar rapidamente as consultas nessas DMVs, usar a opção LABEL com suas consultas poderá ajudar.

Consulte também [monitorar sua carga de trabalho usando DMVs](sql-data-warehouse-manage-monitor.md), [Label](sql-data-warehouse-develop-label.md), [Option](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [Sys. dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [Sys. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [Sys. dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys. dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [Sys. dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)e [Sys. dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="tune-query-performance-with-new-product-enhancements"></a>Ajustar o desempenho de consultas com novos aprimoramentos de produtos

- [Ajuste de desempenho com exibições materializadas](performance-tuning-materialized-views.md)
- [Ajuste de desempenho com o índice columnstore clusterizado ordenado](performance-tuning-ordered-cci.md)
- [Ajuste de desempenho com cache de conjunto de resultados](performance-tuning-result-set-caching.md)

## <a name="group-insert-statements-into-batches"></a>Agrupar instruções INSERT em lotes

Uma única carga para uma pequena tabela com uma instrução INSERT ou até mesmo uma recarga periódica de uma pesquisa pode ter um bom desempenho para suas necessidades com uma instrução como `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  

No entanto, se você precisar carregar milhares ou milhões de linhas ao longo do dia, poderá achar que os INSERTS individuais poderão não dar conta.  Em vez disso, desenvolva seus processos de modo que eles gravem em um arquivo e outro processo adiante-se periodicamente e carregue esse arquivo.

Consulte também [Inserir](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Use o PolyBase para carregar e exportar dados rapidamente

O pool do SQL dá suporte ao carregamento e exportação de dados por meio de várias ferramentas, incluindo Azure Data Factory, polybase e BCP.  Para pequenas quantidades de dados em que o desempenho não é essencial, qualquer ferramenta poderá ser suficiente para satisfazer suas necessidades.  No entanto, quando você estiver carregando ou exportando grandes volumes de dados ou um desempenho rápido for necessário, o PolyBase será a melhor opção.  

O polybase foi projetado para aproveitar a arquitetura MPP (processamento paralelo maciço) e carregará e exportará as magnitudes de dados mais rápido do que qualquer outra ferramenta.  As cargas do PolyBase podem ser executadas usando CTAS ou INSERT INTO.  

> [!TIP]
> Usar o CTAS minimizará o registro em log das transações e será o modo mais rápido de carregar os dados.

Além disso, o Azure Data Factory dá suporte a cargas de PolyBase e pode alcançar um desempenho semelhante como CTAS.  O PolyBase oferece suporte a vários formatos de arquivo, incluindo os arquivos Gzip.  
  
> [!NOTE]
> Para maximizar a taxa de transferência ao usar arquivos de texto gzip, divida arquivos em 60 ou mais arquivos para maximizar o paralelismo de sua carga.   Para ter uma taxa de transferência total mais rápida, considere carregar os dados simultaneamente.

Consulte também [carregar dados](design-elt-data-loading.md), [guia para usar o polybase](guidance-for-loading-data.md), [padrões e estratégias de carregamento de pool do SQL](https://blogs.msdn.microsoft.com/sqlcat/20../../), [carregar dados com Azure data Factory]( ../../data-factory/load-azure-sql-data-warehouse.md), [mover dados com Azure data Factory](../../data-factory/transform-data-using-machine-learning.md), [criar formato de arquivo externo](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)e [criar tabela como SELECT (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="load-then-query-external-tables"></a>Carregar e consultar tabelas externas

Embora o Polybase, também conhecido como tabelas externas, possa ser a maneira mais rápida de carregar dados, ele não é o ideal para consultas. Atualmente, as tabelas do polybase dão suporte apenas a arquivos de blob do Azure e armazenamento de Azure Data Lake. Esses arquivos não tem quaisquer recursos de computação que os assegure.  

Como resultado, o pool do SQL não pode descarregar esse trabalho e, portanto, deve ler o arquivo inteiro carregando-o para tempdb a fim de ler os dados.  Desse modo, se você tiver várias consultas que usarão esses dados, é melhor carregá-los apenas uma vez e fazer com que as consultas usem a tabela local.

Consulte também [guia para usar o polybase](guidance-for-loading-data.md).

## <a name="hash-distribute-large-tables"></a>Tabelas grandes com distribuição Hash

Por padrão, as tabelas são distribuídas pelo método Round Robin.  Isso facilita para os usuários começar a criar tabelas sem ter de decidir sobre como as tabelas deverão ser distribuídas.  Tabelas Round Robin podem apresentar desempenho suficiente para algumas cargas de trabalho, mas, na maioria das vezes, o desempenho será muito melhor se uma coluna de distribuição for escolhida.  

O exemplo mais comum de quando uma tabela distribuída por uma coluna superará bastante uma tabela Round Robin ocorre quando duas tabelas de fatos grandes são unidas.  

Por exemplo, se você tiver uma tabela de pedidos, que é distribuída por order_id, e uma tabela de transações, que também é distribuída por order_id, quando você unir a tabela de pedidos e a tabela de transações em order_id, a consulta se tornará uma consulta de passagem, o que significa que podemos eliminar as operações de movimentação de dados.  Menos etapas significam uma consulta mais rápida.  Menos movimento de dados também resulta em consultas mais rápidas.  

> [!TIP]
> Ao carregar uma tabela distribuída, verifique se os dados de entrada não estão classificados na chave de distribuição, pois isso reduzirá a velocidade das cargas.  

Consulte os links a seguir para obter mais detalhes sobre como selecionar uma coluna de distribuição pode melhorar o desempenho, bem como definir uma tabela distribuída na cláusula WITH da sua instrução CREATE TABLE.

Consulte também [visão geral da tabela](sql-data-warehouse-tables-overview.md), [distribuição de tabela](sql-data-warehouse-tables-distribute.md), [selecionando distribuição de tabela](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [CREATE TABLE como SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="do-not-over-partition"></a>Não estender a partição

Embora o particionamento de dados possa ser eficaz para manter seus dados por meio da alternância de partição ou otimização de verificações com a eliminação de partição, ter muitas partições pode retardar suas consultas.  Geralmente, uma estratégia de particionamento de alta granularidade, que pode funcionar bem em SQL Server pode não funcionar bem no pool do SQL.  

Ter muitas partições também poderá reduzir a eficiência dos índices columnstore clusterizados se cada partição tiver menos de 1 milhão de linhas.  Tenha em mente que, por trás dos bastidores, o pool do SQL particiona seus dados para você em databases 60, portanto, se você criar uma tabela com 100 partições, isso realmente resultará em 6000 partições.  

Cada carga de trabalho é diferente, assim, o melhor conselho é fazer experiências com o particionamento para ver o que funciona melhor para sua carga de trabalho.  Considere uma granularidade menor do que pode ter funcionado para você no SQL Server.  Por exemplo, considere usar partições semanais ou mensais, em vez de partições diárias.

Consulte também [particionamento de tabela](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>Minimizar os tamanhos das transações

As instruções INSERT, UPDATE e DELETE são executadas em uma transação e quando falham, devem ser revertidas.  Para minimizar a possibilidade de uma reversão longa, minimize os tamanhos das transações sempre que possível.  Isso pode ser feito dividindo as instruções INSERT, UPDATE e DELETE em partes.  

Por exemplo, se você tiver uma inserção que espera levar 1 hora, se possível, divida a inserção em quatro partes, que será executada em 15 minutos.  Aproveite casos de registro em log mínimos especiais, como CTAS, TRUNCATE, DROP TABLE ou INSERT em tabelas vazias, para reduzir o risco de reversão.  

Outra maneira de eliminar as reversões é usar as operações de Metadados Somente, como a troca de partição para o gerenciamento de dados.  Por exemplo, em vez de executar uma instrução DELETE para excluir todas as linhas de uma tabela em que o order_date estava em outubro de 2001, você pode particionar seus dados mensalmente e, em seguida, remover a partição com os dados de uma partição vazia de outra tabela (consulte exemplos de [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) ).  

Para tabelas não particionadas, considere usar um CTAS para gravar os dados que você deseja manter em uma tabela em vez de usar DELETE.  Se um CTAS levar o mesmo período de tempo, será uma operação muito mais segura para ser executada, pois ele tem o registro em log de transações mínimo e poderá ser cancelado rapidamente, se necessário.

Consulte também [noções básicas sobre transações](sql-data-warehouse-develop-transactions.md), [otimização de transações](sql-data-warehouse-develop-best-practices-transactions.md), [particionamento de tabela](sql-data-warehouse-tables-partition.md), [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [alterar tabela](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)e [criar tabela como SELECT (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="reduce-query-result-sizes"></a>Reduzir tamanhos de resultados de consulta

Esta etapa ajuda a evitar problemas do lado do cliente causados por grandes resultados de consulta.  Você pode editar sua consulta para reduzir o número de linhas retornadas. Algumas ferramentas de geração de consulta permitem que você adicione a sintaxe "Top N" a cada consulta.  Você também pode CETAS o resultado da consulta a uma tabela temporária e, em seguida, usar a exportação do polybase para o processamento de nível inferior.

## <a name="use-the-smallest-possible-column-size"></a>Use o menor tamanho de coluna possível

Ao definir o DDL, usar o menor tipo de dados que dará suporte aos seus dados melhorará o desempenho da consulta.  Essa abordagem é particularmente importante para colunas CHAR e VARCHAR.  

Se o maior valor em uma coluna for 25 caracteres, então, defina a coluna como VARCHAR(25).  Evite definir todas as colunas de caractere para um tamanho grande padrão.  Além disso, defina as colunas como VARCHAR quando isso for realmente necessário em vez de usar NVARCHAR.

Consulte também [visão geral da tabela](sql-data-warehouse-tables-overview.md), [tipos de dados de tabela](sql-data-warehouse-tables-data-types.md) [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-temporary-heap-tables-for-transient-data"></a>Usar tabelas de heap temporárias para dados transitórios

Quando os dados são temporários temporariamente, você pode descobrir que usar uma tabela de heap tornará o processo geral mais rápido.  Se você estiver carregando dados apenas para prepará-los antes de executar mais transformações, carregar a tabela na tabela de heap será muito mais rápido do que carregar os dados em uma tabela columnstore clusterizada.  

Além disso, o carregamento de dados em uma tabela temporária também carregará muito mais rapidamente do que carregar uma tabela em um armazenamento permanente.  As tabelas temporárias começam com um "#" e só podem ser acessadas pela sessão que a criou, para que elas só possam funcionar em cenários limitados.

As tabelas de heap são definidas na cláusula WITH de CREATE TABLE.  Se você usar uma tabela temporária, lembre-se também de criar estatísticas nela.

Consulte também [tabelas temporárias](sql-data-warehouse-tables-temporary.md), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [CREATE TABLE como SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="optimize-clustered-columnstore-tables"></a>Otimizar tabelas columnstore clusterizadas

Os índices columnstore clusterizados são uma das maneiras mais eficientes de armazenar seus dados no pool SQL.  Por padrão, as tabelas no pool SQL são criadas como ColumnStore clusterizado.  É importante ter um segmento de boa qualidade para obter o melhor desempenho para as consultas nas tabelas columnstore.  

Quando as linhas são gravadas nas tabelas columnstore sob pressão da memória, a qualidade do segmento columnstore pode ficar prejudicada.  A qualidade de segmento pode ser medida pelo número de linhas em um grupo de linhas compactado.  Consulte as [causas da qualidade de índice columnstore ruim](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) no artigo [índices de tabela](sql-data-warehouse-tables-index.md) para obter instruções passo a passo sobre como detectar e melhorar a qualidade do segmento para tabelas columnstore clusterizadas.  

Como os segmentos columnstore de alta qualidade são importantes, é uma boa ideia usar IDs de usuários que estão na classe de recursos médio ou grande para carregar dados. Usar [unidades de data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) menores significa que você deseja atribuir uma classe de recursos maior ao usuário que está carregando.

Como as tabelas columnstore geralmente não enviam dados para um segmento columnstore compactado até que haja mais de 1 milhão linhas por tabela e cada tabela de pool do SQL seja particionada em 60 tabelas, como uma regra geral, as tabelas columnstore não beneficiarão uma consulta, a menos que a tabela tenha mais de 60 milhões linhas.  Para a tabela com menos de 60 milhões de linhas, talvez não faça sentido ter um índice columnstore.  Também pode não prejudicar.  

Além disso, se você particionar seus dados, desejará considerar que cada partição precisará ter 1 milhão de linhas para se beneficiar de um índice columnstore clusterizado.  Se uma tabela tiver 100 partições, será necessário ter pelo menos 6.000.000.000 linhas para se beneficiar de um repositório de colunas clusterizado (60 distribuições *100 partições* 1 milhão linhas).  

Se a tabela não tiver as 6 bilhões de linhas neste exemplo, reduza o número de partições ou considere usar uma tabela de heap.  Também pode valer a pena testar para ver se um melhor desempenho pode ser obtido com uma tabela de heap com índices secundários em vez de uma tabela columnstore.

> [!TIP]
> Ao consultar uma tabela columnstore, as consultas serão executadas mais rapidamente se você selecionar apenas as colunas necessárias.  

Confira também [Índices de tabela](sql-data-warehouse-tables-index.md), [Guia dos índices columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) e [Recriando índices columnstore](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Usar uma classe maior de recursos para melhorar o desempenho da consulta

O pool SQL usa grupos de recursos como uma maneira de alocar memória para consultas.  Para uso pronto, todos os usuários são atribuídos à classe de recursos pequena, que concede 100 MB de memória por distribuição.  Como sempre há 60 distribuições e cada distribuição recebe um mínimo de 100 MB, a alocação de memória total de todo o sistema é de 6.000 MB ou menos de 6 GB.  

Determinadas consultas, como junções grandes ou cargas para as tabelas columnstore clusterizado, se beneficiarão das alocações de memória maiores.  Algumas consultas, como verificações puras, não produzirão nenhum benefício.  No entanto, a utilização de classes de recursos maiores reduz a simultaneidade, portanto, você desejará levar esse impacto em consideração antes de mover todos os usuários para uma classe de recursos grande.

Consulte também [classes de recursos para gerenciamento de carga de trabalho](resource-classes-for-workload-management.md).

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Usar uma Classe de Recurso Menor para Aumentar a Simultaneidade

Se você perceber que as consultas do usuário parecem ter um longo atraso, pode ser que os usuários estejam executando em classes de recursos maiores e consumam muitos slots de simultaneidade, fazendo com que outras consultas sejam enfileiradas.  Para saber se as consultas dos usuários estão em fila, execute `SELECT * FROM sys.dm_pdw_waits` para ver se alguma linha é retornada.

Consulte também [classes de recurso para gerenciamento de carga de trabalho](resource-classes-for-workload-management.md), [Sys. dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="other-resources"></a>Outros recursos

Confira também nosso artigo de [Solução de problemas](sql-data-warehouse-troubleshoot.md) para conhecer os problemas e as soluções comuns.

Se você não encontrou o que está procurando neste artigo, tente usar "Pesquisar documentos" no lado esquerdo desta página para pesquisar todos os documentos do Azure Synapse.  O [Fórum do Azure Synapse](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) é um lugar para você postar perguntas para outros usuários e para o grupo de produtos Synapse do Azure. Monitoramos ativamente esse fórum para garantir que suas perguntas sejam respondidas por outro usuário ou um de nós.  

Se você preferir fazer suas perguntas em Stack Overflow, também temos um [Fórum do Azure Synapse Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw).

Use a página de [comentários do Azure Synapse](https://feedback.azure.com/forums/307516-sql-data-warehouse) para fazer solicitações de recursos.  Adicionar suas solicitações ou recomendar outras solicitações realmente nos ajudará a priorizar os recursos.
