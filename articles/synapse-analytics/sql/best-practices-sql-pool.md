---
title: Práticas recomendadas para pools SQL
description: Recomendações e práticas recomendadas que você deve saber ao trabalhar com pools do SQL.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: b3dc111fe62cbae857f3369165ba29cf40e90342
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81427790"
---
# <a name="best-practices-for-sql-pools-in-azure-synapse-analytics"></a>Práticas recomendadas para pools SQL no Azure Synapse Analytics

Este artigo fornece uma coleção de práticas recomendadas para ajudá-lo a obter o desempenho ideal para pools SQL no Azure Synapse Analytics. Abaixo você encontrará orientações básicas e áreas importantes para se concentrar ao criar sua solução. Cada seção apresenta um conceito e, em seguida, aponta para artigos mais detalhados que abordam o conceito em mais detalhes.

## <a name="sql-pools-loading"></a>Carregamento de pools do SQL

Para obter diretrizes de carregamento de pools SQL, consulte [diretrizes para carregar dados](data-loading-best-practices.md).

## <a name="reduce-cost-with-pause-and-scale"></a>Reduzir custos com pausa e dimensionamento

Para obter mais informações sobre como reduzir os custos por meio de pausa e dimensionamento, consulte [gerenciar computação](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="maintain-statistics"></a>Manter as estatísticas

Embora SQL Server automaticamente detecte e crie ou atualize estatísticas em colunas, os pools do SQL exigem a manutenção manual de estatísticas. Você desejará manter suas estatísticas para garantir que os planos de pool do SQL sejam otimizados.  Os planos criados pelo otimizador são tão bons quanto as estatísticas disponíveis.

> [!TIP]
> Criar estatísticas de exemplo em cada coluna é uma maneira fácil de começar a usar as estatísticas.  

 É igualmente importante atualizar as estatísticas quando ocorrem alterações significativas em seus dados.  Pode ser uma abordagem conservadora atualizar as estatísticas diariamente ou após cada carga.  Sempre há compensações entre o desempenho e o custo para a criação e a atualização das estatísticas.

Para reduzir o tempo de manutenção das estatísticas, seja seletivo sobre quais colunas têm estatísticas ou necessidade de atualização mais frequente. Por exemplo, talvez você queira atualizar as colunas de data em que novos valores podem ser adicionados diariamente. Concentre-se em ter estatísticas de colunas envolvidas em junções, colunas usadas na cláusula WHERE e colunas encontradas em GROUP BY.

Informações adicionais sobre estatísticas podem ser encontradas nos artigos [gerenciar estatísticas de tabela](develop-tables-statistics.md), [criar estatísticas](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [atualizar estatísticas](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) .

## <a name="group-insert-statements-into-batches"></a>Agrupar instruções INSERT em lotes

Uma carga única para uma pequena tabela com uma instrução INSERT, como `INSERT INTO MyLookup VALUES (1, 'Type 1')`pode ser a melhor abordagem, dependendo de suas necessidades. No entanto, se você precisar carregar milhares ou milhões de linhas ao longo do dia, é provável que INSERções singleton não sejam ideais.

Uma maneira de resolver esse problema é desenvolver um processo que grave em um arquivo e, em seguida, outro processo para carregar periodicamente esse arquivo. Consulte o artigo [Inserir](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para obter mais informações.

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Use o PolyBase para carregar e exportar dados rapidamente

O pool do SQL dá suporte ao carregamento e exportação de dados por meio de várias ferramentas, incluindo Azure Data Factory, polybase e BCP.  Para pequenas quantidades de dados em que o desempenho não é essencial, qualquer ferramenta poderá ser suficiente para satisfazer suas necessidades.  

> [!NOTE]
> O polybase é a melhor opção quando você estiver carregando ou exportando grandes volumes de dados ou precisar de um desempenho mais rápido.

As cargas do PolyBase podem ser executadas usando CTAS ou INSERT INTO. O CTAS minimizará o log de transações e será a maneira mais rápida de carregar seus dados. O Azure Data Factory também dá suporte a cargas de polybase e pode alcançar desempenho semelhante ao CTAS. O polybase dá suporte a vários formatos de arquivo, incluindo arquivos gzip.

Para maximizar a taxa de transferência ao usar arquivos de texto gzip, divida arquivos em 60 ou mais arquivos para maximizar o paralelismo de sua carga.  Para ter uma taxa de transferência total mais rápida, considere carregar os dados simultaneamente. Informações adicionais para os tópicos relevantes para esta seção estão incluídas nos seguintes artigos:

- [Carregar dados](data-loading-overview.md)
- [Guia para usar o PolyBase](data-loading-best-practices.md)
- [Padrões e estratégias de carregamento de pool SQL do Azure](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)
- [Carregar dados com Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Mover dados com o Azure Data Factory](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Criar tabela como SELECT (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>Carregar e consultar tabelas externas

O polybase não é ideal para consultas. As tabelas do polybase para pools SQL atualmente dão suporte apenas a arquivos de blob do Azure e armazenamento de Azure Data Lake. Esses arquivos não têm nenhum recurso de computação de backup. Como resultado, os pools do SQL não podem descarregar esse trabalho e devem ler o arquivo inteiro carregando-o para tempdb para que possa ler os dados.

Se você tiver várias consultas para consultar esses dados, é melhor carregar esses dados uma vez e fazer com que as consultas usem a tabela local. Mais diretrizes do polybase estão incluídas no artigo [sobre como usar o polybase](data-loading-best-practices.md) .

## <a name="hash-distribute-large-tables"></a>Tabelas grandes com distribuição Hash

Por padrão, as tabelas são distribuídas pelo método Round Robin.   Esse padrão facilita para os usuários começarem a criar tabelas sem a necessidade de decidir como suas tabelas devem ser distribuídas. As tabelas Round Robin podem executar o suficiente para algumas cargas de trabalho. Mas, na maioria dos casos, uma coluna de distribuição fornece um melhor desempenho.  

O exemplo mais comum de uma tabela distribuída por uma coluna que supera uma tabela Round Robin é quando duas tabelas de fatos grandes são unidas.  

Por exemplo, se você tiver uma tabela de pedidos distribuída por order_id e uma tabela de transações também distribuída por order_id, ao unir sua tabela de pedidos à tabela de transações no order_id, essa consulta se tornará uma consulta de passagem. As operações de movimentação de dados são então eliminadas. Menos etapas significam uma consulta mais rápida. Menos movimento de dados também resulta em consultas mais rápidas.

> [!NOTE]
> Ao carregar uma tabela distribuída, seus dados de entrada não devem ser classificados na chave de distribuição. Isso reduzirá suas cargas.

Os links de artigo fornecidos abaixo fornecerão detalhes adicionais sobre como melhorar o desempenho por meio da seleção de uma coluna de distribuição. Além disso, você encontrará informações sobre como definir uma tabela distribuída na cláusula WITH de sua instrução de CREATE TABLE:

- [Visão geral da tabela](develop-tables-overview.md)
- [Distribuição de tabela](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Selecionando a distribuição de tabelas](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="do-not-over-partition"></a>Não estender a partição

Embora o particionamento de dados possa ser eficaz para manter seus dados por meio da alternância de partição ou otimização de verificações com a eliminação de partição, ter muitas partições pode retardar suas consultas.  Geralmente, uma estratégia de particionamento de alta granularidade que pode funcionar bem em SQL Server pode não funcionar bem no pool do SQL.  

Ter muitas partições pode reduzir a eficácia de índices columnstore clusterizados se cada partição tiver menos de 1 milhão linhas. Os pools do SQL particionam automaticamente seus dados em bancos de dado 60. Portanto, se você criar uma tabela com 100 partições, o resultado será de 6000 partições. Cada carga de trabalho é diferente, portanto, o melhor conselho é experimentar o particionamento para ver o que funciona melhor para sua carga de trabalho.  

Uma opção a ser considerada é usar uma granularidade menor do que o que você implementou usando SQL Server. Por exemplo, considere o uso de partições semanais ou mensais em vez de partições diárias.

Mais informações sobre particionamento são detalhadas no artigo [particionamento de tabelas](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

## <a name="minimize-transaction-sizes"></a>Minimizar os tamanhos das transações

As instruções INSERT, UPDATE e DELETE são executadas em uma transação. Quando eles falharem, eles deverão ser revertidos. Para reduzir o potencial de uma longa reversão, minimize os tamanhos de transação sempre que possível.  Minimizar os tamanhos de transação pode ser feito dividindo as instruções INSERT, UPDATE e DELETE em partes. Por exemplo, se você tiver uma inserção que espera levar 1 hora, poderá dividir a inserção em quatro partes. Cada execução será reduzida em 15 minutos.  

> [!TIP]
> Aproveite casos de registro em log mínimos especiais, como CTAS, TRUNCATE, DROP TABLE ou INSERT em tabelas vazias para reduzir o risco de reversão.  

Outra maneira de eliminar as reversões é usar as operações de Metadados Somente, como a troca de partição para o gerenciamento de dados.  Por exemplo, em vez de executar uma instrução DELETE para excluir todas as linhas de uma tabela em que a order_date estava em outubro de 2001, você pode particionar seus dados mensalmente. Em seguida, você pode alternar a partição com os dados para uma partição vazia de outra tabela (consulte exemplos de ALTER TABLE).  

Para tabelas não particionadas, considere usar um CTAS para gravar os dados que você deseja manter em uma tabela em vez de usar DELETE.  Se um CTAS levar o mesmo período de tempo, será muito mais seguro executar, pois ele tem o registro em log de transações mínimo e poderá ser cancelado rapidamente, se necessário.

Informações adicionais sobre o conteúdo relacionado a esta seção estão incluídas nos artigos abaixo:

- [Criar tabela como SELECT (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Noções básicas sobre transações](develop-transactions.md)
- [Otimização de transações](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Partições de tabela](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [ALTERAR TABELA](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="reduce-query-result-sizes"></a>Reduzir tamanhos de resultados de consulta

Reduzir os tamanhos dos resultados da consulta ajuda a evitar problemas do lado do cliente causados por grandes resultados de consulta.  Você pode editar sua consulta para reduzir o número de linhas retornadas. Algumas ferramentas de geração de consulta permitem que você adicione a sintaxe "Top N" a cada consulta.  Você também pode CETAS o resultado da consulta a uma tabela temporária e, em seguida, usar a exportação do polybase para o processamento de nível inferior.

## <a name="use-the-smallest-possible-column-size"></a>Use o menor tamanho de coluna possível

Ao definir o DDL, use o menor tipo de dados que dará suporte aos seus dados, pois isso melhorará o desempenho da consulta.  Essa recomendação é particularmente importante para colunas CHAR e VARCHAR.  Se o maior valor em uma coluna for 25 caracteres, então, defina a coluna como VARCHAR(25).  Evite definir todas as colunas de caractere para um tamanho grande padrão.  Além disso, defina colunas como VARCHAR quando isso for necessário, em vez de usar NVARCHAR.

Consulte a [visão geral da tabela](develop-tables-overview.md), os [tipos de dados da tabela e os](develop-tables-data-types.md)artigos [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para obter uma revisão mais detalhada dos conceitos essenciais relevantes para as informações acima.

## <a name="use-temporary-heap-tables-for-transient-data"></a>Usar tabelas de heap temporárias para dados transitórios

Quando você estiver temporariamente descarregando dados em pools SQL, as tabelas de heap geralmente farão o processo geral mais rapidamente.  Se você estiver carregando dados somente para prepará-los antes de executar mais transformações, carregar a tabela em uma tabela de heap será mais rápido do que carregar os dados em uma tabela columnstore clusterizada.  

Carregar dados em uma tabela temporária também será carregado muito mais rapidamente do que carregar uma tabela no armazenamento permanente.  As tabelas temporárias começam com um "#" e só podem ser acessadas pela sessão que a criou. Consequentemente, eles só podem funcionar em cenários limitados. As tabelas de heap são definidas na cláusula WITH de CREATE TABLE.  Se você usar uma tabela temporária, lembre-se também de criar estatísticas nela.

Para obter diretrizes adicionais, consulte as [tabelas temporárias](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [CREATE TABLE como](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) os artigos selecionados.

## <a name="optimize-clustered-columnstore-tables"></a>Otimizar tabelas columnstore clusterizadas

Os índices columnstore clusterizados são uma das maneiras mais eficientes de armazenar seus dados no pool SQL.  Por padrão, as tabelas no pool SQL são criadas como ColumnStore clusterizado.  É importante ter um segmento de boa qualidade para obter o melhor desempenho para as consultas nas tabelas columnstore.  Quando as linhas são gravadas nas tabelas columnstore sob pressão da memória, a qualidade do segmento columnstore pode ficar prejudicada.  

A qualidade do segmento pode ser medida pelo número de linhas em um grupo de linhas compactado. Consulte as [causas da qualidade de índice columnstore ruim](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) no artigo [índices de tabela](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para obter instruções passo a passo sobre como detectar e melhorar a qualidade do segmento para tabelas columnstore clusterizadas.  

Como os segmentos columnstore de alta qualidade são importantes, é uma boa ideia usar IDs de usuários que estão na classe de recursos médio ou grande para carregar dados. Usar [unidades de data warehouse](resource-consumption-models.md) menores significa que você deseja atribuir uma classe de recursos maior ao usuário que está carregando.

As tabelas columnstore geralmente não enviam dados para um segmento columnstore compactado até que haja mais de 1 milhão linhas por tabela. Cada tabela de pool do SQL é particionada em tabelas 60. Dessa forma, as tabelas columnstore não beneficiarão uma consulta, a menos que a tabela tenha mais de 60 milhões linhas.  

> [!TIP]
> Para tabelas com menos de 60 milhões linhas, ter um índice columnstore pode não ser a solução ideal.  

Se você particionar seus dados, cada partição precisará ter 1 milhão linhas para se beneficiar de um índice columnstore clusterizado.  Para uma tabela com 100 partições, ela precisa ter pelo menos 6.000.000.000 linhas para se beneficiar de um repositório de colunas clusterizado (60 distribuições *100 partições* 1 milhão linhas).  

Se a tabela não tiver 6.000.000.000 linhas, você terá duas opções principais. Reduza o número de partições ou considere usar uma tabela de heap em vez disso.  Também pode valer a pena fazer testes para ver se um desempenho melhor pode ser obtido usando uma tabela de heap com índices secundários em vez de uma tabela columnstore.

Ao consultar uma tabela columnstore, as consultas serão executadas mais rapidamente se você selecionar apenas as colunas necessárias.  Mais informações sobre índices de tabela e columnstore e podem ser encontradas nos artigos [índices de tabela](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [índices columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [recriação de índices columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rebuilding-indexes-to-improve-segment-quality) .

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Usar uma classe maior de recursos para melhorar o desempenho da consulta

Os pools SQL usam grupos de recursos como uma maneira de alocar memória para consultas. Inicialmente, todos os usuários são atribuídos à classe Small Resource, que concede 100 MB de memória por distribuição.  Há sempre distribuições de 60. Cada distribuição recebe um mínimo de 100 MB. A alocação total de memória em todo o sistema é de 6.000 MB ou apenas menos de 6 GB.  

Determinadas consultas, como junções grandes ou cargas para as tabelas columnstore clusterizado, se beneficiarão das alocações de memória maiores.  Algumas consultas, como verificações puras, não verão nenhum benefício. A utilização de classes de recursos maiores afeta a simultaneidade. Portanto, você desejará manter esses fatos em mente antes de mover todos os seus usuários para uma classe de recursos grande.

Para obter informações adicionais sobre classes de recursos, consulte o artigo [classes de recursos para gerenciamento de carga de trabalho](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Usar classe de recurso menor para aumentar a simultaneidade

Se você notar um longo atraso nas consultas de usuário, os usuários poderão estar executando em classes de recursos maiores. Esse cenário promove o consumo de Slots de simultaneidade, o que pode fazer com que outras consultas sejam enfileiradas.  Para determinar se as consultas de usuários são enfileiradas, execute `SELECT * FROM sys.dm_pdw_waits` para ver se todas as linhas são retornadas.

As [classes de recursos para o gerenciamento de carga de trabalho](../sql-data-warehouse/resource-classes-for-workload-management.md) e os artigos [Sys. dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) fornecerão mais informações.

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Usar DMVs para monitorar e otimizar suas consultas

Os pools do SQL têm várias DMVs que podem ser usadas para monitorar a execução da consulta.  O artigo de monitoramento abaixo orienta você pelas instruções passo a passo sobre como exibir detalhes de uma consulta em execução.  Para encontrar rapidamente as consultas nessas DMVs, usar a opção LABEL com suas consultas poderá ajudar. Para obter informações detalhadas adicionais, consulte os artigos incluídos na lista abaixo:

- [Monitore sua carga de trabalho usando DMVs](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [CHAMADA](develop-label.md)
- [OPTION](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="next-steps"></a>Próximas etapas

Consulte também o artigo de [solução de problemas](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para problemas comuns e soluções.

Se você precisar de informações não fornecidas neste artigo, use "Pesquisar documentos" no lado esquerdo desta página para pesquisar todos os documentos do pool de SQL.  O [Fórum do pool do SQL](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) é um local para você colocar perguntas para outros usuários e para o grupo de produtos do pool do SQL.  

Monitoramos ativamente esse fórum para garantir que suas perguntas sejam respondidas por outro usuário ou um de nós.  Se você preferir fazer suas perguntas em Stack Overflow, também temos um [Fórum do pool SQL do Azure Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw).

Para solicitações de recursos, use a página de [comentários do pool SQL do Azure](https://feedback.azure.com/forums/307516-sql-data-warehouse) .  A adição de suas solicitações ou a votação de outras solicitações nos ajuda a concentrar-se nos recursos mais sob demanda.
