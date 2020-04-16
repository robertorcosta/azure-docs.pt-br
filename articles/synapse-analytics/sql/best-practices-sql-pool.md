---
title: Práticas recomendadas para piscinas SQL
description: Recomendações e práticas recomendadas que você deve saber como você trabalha com pools SQL.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427790"
---
# <a name="best-practices-for-sql-pools-in-azure-synapse-analytics"></a>Melhores práticas para pools SQL no Azure Synapse Analytics

Este artigo fornece uma coleção de práticas recomendadas para ajudá-lo a obter o melhor desempenho para pools SQL no Azure Synapse Analytics. Abaixo você encontrará orientação básica e áreas importantes para se concentrar à medida que você constrói sua solução. Cada seção apresenta um conceito e, em seguida, aponta-o para artigos mais detalhados que cobrem o conceito com mais profundidade.

## <a name="sql-pools-loading"></a>Carregamento de piscinas SQL

Para obter orientações de carregamento de pools SQL, consulte [Orientação para carregar dados](data-loading-best-practices.md).

## <a name="reduce-cost-with-pause-and-scale"></a>Reduzir custos com pausa e dimensionamento

Para obter mais informações sobre a redução de custos através de pausas e dimensionamento, consulte [Gerenciar cálculo](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="maintain-statistics"></a>Manter as estatísticas

Enquanto o SQL Server detecta e cria automaticamente ou atualiza estatísticas em colunas, os pools SQL exigem manutenção manual das estatísticas. Você vai querer manter suas estatísticas para garantir que os planos de pool SQL sejam otimizados.  Os planos criados pelo otimizador são tão bons quanto as estatísticas disponíveis.

> [!TIP]
> Criar estatísticas de exemplo em cada coluna é uma maneira fácil de começar a usar as estatísticas.  

 É igualmente importante atualizar as estatísticas quando ocorrem alterações significativas em seus dados.  Pode ser uma abordagem conservadora atualizar as estatísticas diariamente ou após cada carga.  Sempre há compensações entre o desempenho e o custo para a criação e a atualização das estatísticas.

Para reduzir o tempo de manutenção das estatísticas, seja seletivo sobre quais colunas têm estatísticas ou que precisem da atualização mais freqüente. Por exemplo, você pode querer atualizar colunas de data onde novos valores podem ser adicionados diariamente. Foco em ter estatísticas para colunas envolvidas em adesões, colunas usadas na cláusula WHERE e colunas encontradas no GRUPO BY.

Podem ser encontradas informações adicionais sobre estatísticas nas estatísticas da [tabela Gerenciar,](develop-tables-statistics.md) [CRIAR ESTATÍSTICAS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [ATUALIZAR ESTATÍSTICAS.](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="group-insert-statements-into-batches"></a>Agrupar instruções INSERT em lotes

Uma carga única para uma pequena tabela com `INSERT INTO MyLookup VALUES (1, 'Type 1')`uma instrução INSERT, como pode ser a melhor abordagem dependendo de suas necessidades. No entanto, se você precisa carregar milhares ou milhões de linhas ao longo do dia, é provável que os singleton INSERTS não sejam ótimos.

Uma maneira de resolver esse problema é desenvolver um processo que escreva para um arquivo e, em seguida, outro processo para carregar periodicamente este arquivo. Consulte o artigo [INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para obter mais informações.

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Use o PolyBase para carregar e exportar dados rapidamente

O pool SQL suporta o carregamento e a exportação de dados através de várias ferramentas, incluindo Azure Data Factory, PolyBase e BCP.  Para pequenas quantidades de dados em que o desempenho não é essencial, qualquer ferramenta poderá ser suficiente para satisfazer suas necessidades.  

> [!NOTE]
> Polybase é a melhor escolha quando você está carregando ou exportando grandes volumes de dados, ou você precisa de um desempenho mais rápido.

As cargas do PolyBase podem ser executadas usando CTAS ou INSERT INTO. O CTAS minimizará o registro de transações e é a maneira mais rápida de carregar seus dados. O Azure Data Factory também suporta cargas PolyBase e pode alcançar desempenho semelhante ao CTAS. O PolyBase suporta vários formatos de arquivo, incluindo arquivos Gzip.

Para maximizar o throughput ao usar arquivos de texto Gzip, separe arquivos em 60 ou mais arquivos para maximizar o paralelismo da carga.  Para ter uma taxa de transferência total mais rápida, considere carregar os dados simultaneamente. Informações adicionais para os tópicos relevantes para esta seção estão incluídas nos seguintes artigos:

- [Carregar dados](data-loading-overview.md)
- [Guia para usar o PolyBase](data-loading-best-practices.md)
- [Padrões e estratégias de carregamento de pool SQL do Azure](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)
- [Dados de carga com fábrica de dados do Azure](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Mover dados com o Azure Data Factory](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [CRIAR FORMATO DE ARQUIVO EXTERNO](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Criar tabela como select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>Carregar e consultar tabelas externas

Polybase não é ideal para consultas. As tabelas polybase para pools SQL atualmente suportam apenas arquivos azure blob e armazenamento do Azure Data Lake. Esses arquivos não têm nenhum recurso de computação que os suporte. Como resultado, os pools SQL não podem descarregar este trabalho e devem ler o arquivo inteiro carregando-o para tempdb para que ele possa ler os dados.

Se você tiver várias consultas para consultar esses dados, é melhor carregar esses dados uma vez e ter consultas para usar a tabela local. Outras orientações do Polybase estão incluídas no Guia para o uso do artigo [PolyBase.](data-loading-best-practices.md)

## <a name="hash-distribute-large-tables"></a>Tabelas grandes com distribuição Hash

Por padrão, as tabelas são distribuídas pelo método Round Robin.   Esse padrão facilita que os usuários comecem a criar tabelas sem ter que decidir como suas tabelas devem ser distribuídas. Mesas redondas robin pode funcionar o suficiente para algumas cargas de trabalho. Mas, na maioria dos casos, uma coluna de distribuição proporciona melhor desempenho.  

O exemplo mais comum de uma tabela distribuída por uma coluna superando uma tabela Round Robin é quando duas grandes tabelas de fatos são unidas.  

Por exemplo, se você tiver uma tabela de pedidos distribuída por order_id, e uma tabela de transações também distribuída por order_id, quando você junta sua tabela de pedidos à sua tabela de transações em order_id, essa consulta se torna uma consulta de passagem. As operações de movimentação de dados são então eliminadas. Menos etapas significam uma consulta mais rápida. Menos movimento de dados também resulta em consultas mais rápidas.

> [!NOTE]
> Ao carregar uma tabela distribuída, seus dados de entrada não devem ser classificados na chave de distribuição. Fazer isso vai diminuir sua carga.

Os links do artigo fornecidos abaixo fornecerão detalhes adicionais sobre como melhorar o desempenho através da seleção de uma coluna de distribuição. Além disso, você encontrará informações sobre como definir uma tabela distribuída na cláusula WITH da sua declaração CREATE TABLE:

- [Visão geral da tabela](develop-tables-overview.md)
- [Distribuição de tabelas](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Selecionando a distribuição de tabelas](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="do-not-over-partition"></a>Não estender a partição

Embora a particionagem de dados possa ser eficaz para manter seus dados através da comutação de partição ou otimização de varreduras com a eliminação de partições, ter muitas partições pode retardar suas consultas.  Muitas vezes, uma estratégia de particionamento de alta granularidade que pode funcionar bem no SQL Server pode não funcionar bem no pool SQL.  

Ter muitas partições pode reduzir a eficácia dos índices de columnstore agrupados se cada partição tiver menos de 1 milhão de linhas. Os pools SQL particionam automaticamente seus dados em 60 bancos de dados. Então, se você criar uma tabela com 100 partições, o resultado será 6000 partições. Cada carga de trabalho é diferente, então o melhor conselho é experimentar particionamento para ver o que funciona melhor para sua carga de trabalho.  

Uma opção a considerar é o uso de uma granularidade menor do que o que você implementou usando o SQL Server. Por exemplo, considere usar partições semanais ou mensais em vez de partições diárias.

Mais informações sobre particionamento são detalhadas no artigo [de particionamento](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) de tabela.

## <a name="minimize-transaction-sizes"></a>Minimizar os tamanhos das transações

INSIRA, ATUALIZE E EXCLUA as declarações executadas em uma transação. Quando eles falham, eles devem ser revertidos. Para reduzir o potencial de uma longa reversão, minimize os tamanhos das transações sempre que possível.  A minimização dos tamanhos das transações pode ser feita dividindo as instruções INSERT, UPDATE e DELETE em partes. Por exemplo, se você tiver um INSERT que você espera levar 1 hora, você pode dividir o INSERT em quatro partes. Cada corrida será então encurtada para 15 minutos.  

> [!TIP]
> Aproveite casos especiais de Registro Mínimo, como CTAS, TRUNCATE, DROP TABLE ou INSERT, em tabelas vazias para reduzir o risco de reversão.  

Outra maneira de eliminar as reversões é usar as operações de Metadados Somente, como a troca de partição para o gerenciamento de dados.  Por exemplo, em vez de executar uma declaração DELETE para excluir todas as linhas em uma tabela onde o order_date foi em outubro de 2001, você poderia particionar seus dados mensalmente. Em seguida, você pode alternar a partição com dados para uma partição vazia de outra tabela (veja exemplos DE TABELA ALTER).  

Para tabelas não particionadas, considere usar um CTAS para escrever os dados que deseja manter em uma tabela em vez de usar DELETE.  Se um CTAS leva o mesmo tempo, é muito mais seguro ser executado, pois tem registro mínimo de transações e pode ser cancelado rapidamente, se necessário.

Mais informações sobre o conteúdo relacionado a esta seção estão incluídas nos artigos abaixo:

- [Criar tabela como select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Noções básicas sobre transações](develop-transactions.md)
- [Otimização de transações](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Partições de tabela](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [TABELA TRUNCATE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="reduce-query-result-sizes"></a>Reduzir os tamanhos dos resultados da consulta

Reduzir os tamanhos de resultados de consulta ajuda a evitar problemas do lado do cliente causados por grandes resultados de consulta.  Você pode editar sua consulta para reduzir o número de linhas retornadas. Algumas ferramentas de geração de consulta permitem adicionar sintaxe "top N" a cada consulta.  Você também pode CETAS o resultado da consulta para uma tabela temporária e, em seguida, usar a exportação PolyBase para o processamento de nível de baixa.

## <a name="use-the-smallest-possible-column-size"></a>Use o menor tamanho de coluna possível

Ao definir seu DDL, use o menor tipo de dados que suportará seus dados, pois isso melhorará o desempenho da consulta.  Esta recomendação é particularmente importante para as colunas CHAR e VARCHAR.  Se o maior valor em uma coluna for 25 caracteres, então, defina a coluna como VARCHAR(25).  Evite definir todas as colunas de caractere para um tamanho grande padrão.  Além disso, defina colunas como VARCHAR quando isso é tudo o que é necessário em vez de usar NVARCHAR.

Consulte a visão geral da [tabela,](develop-tables-overview.md) [os tipos de dados da tabela](develop-tables-data-types.md)e os artigos CREATE [TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para uma revisão mais detalhada dos conceitos essenciais relevantes para as informações acima.

## <a name="use-temporary-heap-tables-for-transient-data"></a>Usar tabelas de heap temporárias para dados transitórios

Quando você está aterrissando temporariamente dados em pools SQL, as tabelas de pilha geralmente tornarão o processo geral mais rápido.  Se você estiver carregando dados apenas para encená-los antes de executar mais transformações, carregar a tabela para uma tabela de pilhaserá mais rápido do que carregar os dados para uma tabela de colunastore agrupada.  

Carregar dados para uma tabela temporária também carregará muito mais rápido do que carregar uma tabela para armazenamento permanente.  As tabelas temporárias começam com um "#" e só são acessíveis pela sessão que a criou. Consequentemente, eles só podem funcionar em cenários limitados. As tabelas de heap são definidas na cláusula WITH de CREATE TABLE.  Se você usar uma tabela temporária, lembre-se também de criar estatísticas nela.

Para obter orientações adicionais, consulte as [tabelas temporárias](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [CRIAR TABELA](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e CRIAR TABELA COMO [ARTIGOS SELECT.](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="optimize-clustered-columnstore-tables"></a>Otimizar tabelas columnstore clusterizadas

Os índices de columnstore agrupados são uma das maneiras mais eficientes de armazenar seus dados no pool SQL.  Por padrão, as tabelas no pool SQL são criadas como Clustered ColumnStore.  É importante ter um segmento de boa qualidade para obter o melhor desempenho para as consultas nas tabelas columnstore.  Quando as linhas são gravadas nas tabelas columnstore sob pressão da memória, a qualidade do segmento columnstore pode ficar prejudicada.  

A qualidade do segmento pode ser medida pelo número de linhas em um Grupo de Linhas compactados. Consulte as [causas da má qualidade do índice de columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) no artigo [Índices](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) de tabela para obter instruções passo a passo sobre como detectar e melhorar a qualidade do segmento para tabelas de colunastore agrupadas.  

Como os segmentos de columnstore de alta qualidade são importantes, é uma boa ideia usar IDs de usuários que estão na classe de recursos médios ou grandes para carregar dados. Usar [unidades de data warehouse](resource-consumption-models.md) menores significa que você deseja atribuir uma classe de recursos maior ao usuário que está carregando.

As tabelas do Columnstore geralmente não empurram dados para um segmento de columnstore compactado até que haja mais de 1 milhão de linhas por tabela. Cada tabela de bilhar SQL é dividida em 60 tabelas. Como tal, as tabelas de columnstore não beneficiarão uma consulta a menos que a tabela tenha mais de 60 milhões de linhas.  

> [!TIP]
> Para tabelas com menos de 60 milhões de linhas, ter um índice de columnstore pode não ser a solução ideal.  

Se você particionar seus dados, cada partição precisará ter 1 milhão de linhas para se beneficiar de um índice de columnstore agrupado.  Para uma tabela com 100 partições, ela precisa ter pelo menos 6 bilhões de linhas para se beneficiar de uma loja de colunas agrupadas (60 distribuições *100 partições* 1 milhão de linhas).  

Se sua tabela não tem 6 bilhões de linhas, você tem duas opções principais. Reduza o número de partições ou considere usar uma tabela de pilhas.  Também pode valer a pena experimentar para ver se um melhor desempenho pode ser obtido usando uma tabela de pilha com índices secundários em vez de uma tabela de columnstore.

Ao consultar uma tabela columnstore, as consultas serão executadas mais rapidamente se você selecionar apenas as colunas necessárias.  Mais informações sobre índices de tabela e columnstore e podem ser encontradas nos [índices de tabela,](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) [guia de índices de Columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [Artigos de índices de colunastore.](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rebuilding-indexes-to-improve-segment-quality)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Usar uma classe maior de recursos para melhorar o desempenho da consulta

Os pools SQL usam grupos de recursos como uma maneira de alocar memória para consultas. Inicialmente, todos os usuários são atribuídos à classe de pequenos recursos, que concede 100 MB de memória por distribuição.  Há sempre 60 distribuições. Cada distribuição é dada um mínimo de 100 MB. A alocação total de memória em todo o sistema é de 6.000 MB, ou pouco menos de 6 GB.  

Determinadas consultas, como junções grandes ou cargas para as tabelas columnstore clusterizado, se beneficiarão das alocações de memória maiores.  Algumas consultas, como varreduras puras, não verão nenhum benefício. O uso de classes de recursos maiores impacta a concorrência. Então, você vai querer manter esses fatos em mente antes de mover todos os seus usuários para uma grande classe de recursos.

Para obter informações adicionais sobre classes de recursos, consulte as classes de recursos para o artigo [de gerenciamento da carga de trabalho.](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Use classe de recursos menor para aumentar a concorrência

Se você notar um longo atraso nas consultas de usuário, seus usuários podem estar executando em classes de recursos maiores. Esse cenário promove o consumo de slots de concorrência, o que pode fazer com que outras consultas se enfileiram.  Para determinar se as consultas dos `SELECT * FROM sys.dm_pdw_waits` usuários estão na fila, execute para ver se as linhas são devolvidas.

As [classes de recursos para gerenciamento de carga de trabalho](../sql-data-warehouse/resource-classes-for-workload-management.md) e artigos [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) fornecerão mais informações.

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Usar DMVs para monitorar e otimizar suas consultas

Os pools SQL possuem vários DMVs que podem ser usados para monitorar a execução de consultas.  O artigo de monitoramento abaixo orienta você através de instruções passo a passo sobre como visualizar detalhes de uma consulta de execução.  Para encontrar rapidamente as consultas nessas DMVs, usar a opção LABEL com suas consultas poderá ajudar. Para obter informações detalhadas adicionais, consulte os artigos incluídos na lista abaixo:

- [Monitore sua carga de trabalho usando DMVs](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [Rótulo](develop-label.md)
- [Opção](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="next-steps"></a>Próximas etapas

Veja também o artigo [Solução de Problemas](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para problemas e soluções comuns.

Se você precisar de informações não fornecidas neste artigo, use o "Pesquisar documentos" no lado esquerdo desta página para pesquisar todos os documentos do pool SQL.  O [Fórum de Pool SQL](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) é um lugar para você fazer perguntas a outros usuários e ao Grupo de Produtos do pool SQL.  

Monitoramos ativamente esse fórum para garantir que suas perguntas sejam respondidas por outro usuário ou um de nós.  Se você preferir fazer suas perguntas sobre Stack Overflow, também temos um Fórum de Estouro de Stack Stack pool [Do Zure SQL](https://stackoverflow.com/questions/tagged/azure-sqldw).

Para solicitações de recursos, use a página [Feedback do pool Azure SQL.](https://feedback.azure.com/forums/307516-sql-data-warehouse)  Adicionar seus pedidos ou aumentar a votação de outras solicitações nos ajuda a focar nos recursos mais procurados.
