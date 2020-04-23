---
title: Melhores práticas de desenvolvimento para Synapse SQL
description: Recomendações e melhores práticas que você deve saber como você desenvolve para synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: ed2638cfe4ab7e849e428729ccd17ffdeb6314af
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086344"
---
# <a name="development-best-practices-for-synapse-sql"></a>Melhores práticas de desenvolvimento para Synapse SQL
Este artigo descreve orientações e práticas recomendadas à medida que você desenvolve sua solução de data warehouse. 

## <a name="sql-pool-development-best-practices"></a>Práticas recomendadas de desenvolvimento de pool SQL

### <a name="reduce-cost-with-pause-and-scale"></a>Reduzir custos com pausa e dimensionamento

Para obter mais informações sobre como reduzir os custos por meio de pausa e dimensionamento, confira [Gerenciar computação](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="maintain-statistics"></a>Manter as estatísticas

Certifique-se de que atualizar as estatísticas diariamente ou após cada carga.  Sempre há compensações entre o desempenho e o custo para a criação e a atualização das estatísticas. Se você achar que está demorando muito para manter todas as suas estatísticas, seja mais seletivo sobre quais colunas têm estatísticas ou quais colunas precisam de atualização frequente.  

Por exemplo, você pode querer atualizar colunas de data, onde novos valores podem ser adicionados diariamente. 

> [!NOTE]
> Você ganhará mais benefícios por ter estatísticas sobre colunas envolvidas em adesões, colunas usadas na cláusula WHERE e colunas encontradas no GRUPO BY.

Veja também [Gerenciar estatísticas da tabela](develop-tables-statistics.md), CRIAR [ESTATÍSTICAS,](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [ATUALIZAR ESTATÍSTICAS](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="hash-distribute-large-tables"></a>Tabelas grandes com distribuição Hash

Por padrão, as tabelas são distribuídas pelo método Round Robin.  Isso facilita que os usuários comecem a criar tabelas sem ter que decidir como suas tabelas devem ser distribuídas.  Mesas redondas robin pode funcionar o suficiente para algumas cargas de trabalho. Mas, na maioria dos casos, selecionar uma coluna de distribuição terá um desempenho muito melhor.  

O exemplo mais comum de quando uma tabela distribuída por uma coluna superará bastante uma tabela Round Robin ocorre quando duas tabelas de fatos grandes são unidas.  

Por exemplo, se você tiver uma tabela de pedidos, que é distribuída por order_id, e uma tabela de transações, que também é distribuída por order_id, quando você junta sua tabela de pedidos à sua tabela de transações em order_id, essa consulta se torna uma consulta de passagem. 

Isso significa que eliminamos as operações de movimentação de dados.  Menos etapas significam uma consulta mais rápida.  Menos movimento de dados também resulta em consultas mais rápidas.

> [!TIP]
> Ao carregar uma tabela distribuída, verifique se os dados de entrada não estão classificados na chave de distribuição, pois isso reduzirá a velocidade das cargas.  

Veja os links a seguir para obter detalhes adicionais sobre como selecionar uma coluna de distribuição pode melhorar o desempenho, bem como como definir uma tabela distribuída na cláusula COM da sua declaração CREATE TABLES.

Veja também [visão geral da tabela,](develop-tables-overview.md) [distribuição da tabela,](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) [distribuição da tabela de seleção,](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/) [TABELA CRIAR](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e CRIAR TABELA [COMO SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="do-not-over-partition"></a>Não estender a partição
Embora a particionagem de dados possa ser eficaz para manter seus dados através da comutação de partição ou otimização de varreduras com a eliminação de partições, ter muitas partições pode retardar suas consultas.  Muitas vezes, uma estratégia de particionamento de alta granularidade que pode funcionar bem no SQL Server pode não funcionar bem no pool SQL.  

> [!NOTE]
> Muitas vezes, uma estratégia de particionamento de alta granularidade que pode funcionar bem no SQL Server pode não funcionar bem no pool SQL.  

Ter muitas partições também poderá reduzir a eficiência dos índices columnstore clusterizados se cada partição tiver menos de 1 milhão de linhas. O pool SQL divide seus dados para você em 60 bancos de dados. 

Então, se você criar uma tabela com 100 partições, o resultado será 6000 partições.  Cada carga de trabalho é diferente, assim, o melhor conselho é fazer experiências com o particionamento para ver o que funciona melhor para sua carga de trabalho.  

Uma opção a considerar é o uso de uma granularidade menor do que o que pode ter funcionado para você no SQL Server.  Por exemplo, considere usar partições semanais ou mensais, em vez de partições diárias.

Veja também [particionamento de tabela](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="minimize-transaction-sizes"></a>Minimizar os tamanhos das transações

As instruções INSERT, UPDATE e DELETE são executadas em uma transação e quando falham, devem ser revertidas.  Para minimizar a possibilidade de uma reversão longa, minimize os tamanhos das transações sempre que possível.  Isso pode ser feito dividindo as instruções INSERT, UPDATE e DELETE em partes.  

Por exemplo, se você tiver um INSERT que você espera levar 1 hora, você pode dividir o INSERT em quatro partes, encurtando assim cada corrida para 15 minutos.

> [!TIP]
> Aproveite os casos especiais de Registro em Log Mínimo, como CTAS, TRUNCATE, DROP TABLE ou INSERT, para esvaziar as tabelas, reduzindo o risco de reversão.  

Outra maneira de eliminar as reversões é usar as operações de Metadados Somente, como a troca de partição para o gerenciamento de dados.  

Por exemplo, em vez de executar uma instrução DELETE para excluir todas as linhas em uma tabela onde order_date foi em outubro de 2001, você poderia particionar os dados mensalmente e, em seguida, trocar a partição com os dados por uma partição vazia a partir de outra tabela (consulte exemplos de ALTER TABLE).  

Para as tabelas não particionadas, considere usar um CTAS para gravar os dados que você deseja manter em uma tabela em vez de usar DELETE.  Se um CTAS demorar a mesma quantidade de tempo, será uma operação muito mais segura de executar quando ele tiver um log de transação mínimo e puder ser cancelado rapidamente, se necessário.

Veja também [Entendendo transações,](develop-transactions.md) [Otimizando transações,](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) [Particionamento de tabelas,](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) [TABELA TRUNCATE,](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [TABELA ALTER](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e Criar tabela como [select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="use-the-smallest-possible-column-size"></a>Use o menor tamanho de coluna possível

Ao definir a DDL, usar o menor tipo de dados que oferece suporte a seus dados melhorará o desempenho da consulta.  Isso é especialmente importante para as colunas CHAR e VARCHAR.  

Se o maior valor em uma coluna for 25 caracteres, então, defina a coluna como VARCHAR(25).  Evite definir todas as colunas de caractere para um tamanho grande padrão.  Além disso, defina as colunas como VARCHAR quando isso for realmente necessário em vez de usar NVARCHAR.

Consulte também [visão geral da tabela,](develop-tables-overview.md) [tipos de dados da tabela](develop-tables-data-types.md)e [TABELA CRIAR](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="optimize-clustered-columnstore-tables"></a>Otimizar tabelas columnstore clusterizadas

Os índices de columnstore agrupados são uma das maneiras mais eficientes de armazenar seus dados no pool SQL.  Por padrão, as tabelas no pool SQL são criadas como Clustered ColumnStore.  

É importante ter um segmento de boa qualidade para obter o melhor desempenho para as consultas nas tabelas columnstore.  Quando as linhas são gravadas nas tabelas columnstore sob pressão da memória, a qualidade do segmento columnstore pode ficar prejudicada.  

A qualidade de segmento pode ser medida pelo número de linhas em um grupo de linhas compactado.  Consulte as [causas da má qualidade do índice de columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) e o artigo [índices de tabela](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para instruções passo a passo sobre a detecção e melhoria da qualidade do segmento para tabelas de colunastore agrupadas.  

Como os segmentos columnstore de alta qualidade são importantes, é uma boa ideia usar IDs de usuários que estão na classe de recursos de médio ou grande para carregamento de dados. Usar [unidades de data warehouse](resource-consumption-models.md) menores significa que você deseja atribuir uma classe de recursos maior ao usuário que está carregando.

Uma vez que as tabelas do columnstore geralmente não empurram dados para um segmento de columnstore compactado até que haja mais de 1 milhão de linhas por tabela, e cada tabela de bilhar SQL é dividida em 60 tabelas, as tabelas de columnstore não beneficiarão uma consulta a menos que a tabela tenha mais de 60 milhões de linhas.  

> [!TIP]
> Para tabelas com menos de 60 milhões de linhas, ter um índice de columstore pode não ser a solução ideal.  

Além disso, se você particionar seus dados, desejará considerar que cada partição precisará ter 1 milhão de linhas para se beneficiar de um índice columnstore clusterizado.  Se uma tabela tiver 100 partições, então ela precisará ter pelo menos 6 bilhões de linhas para se beneficiar de um armazenamento de colunas agrupadas (60 distribuições *100 partições* 1 milhão de linhas).  

Se sua tabela não tiver 6 bilhões de linhas, reduza o número de partições ou considere usar uma tabela de pilhas.  Também pode valer a pena experimentar para ver se um melhor desempenho pode ser obtido usando uma tabela de pilha com índices secundários em vez de uma tabela de columnstore.

Ao consultar uma tabela columnstore, as consultas serão executadas mais rapidamente se você selecionar apenas as colunas necessárias.  

Veja também [índices de tabela,](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) [guia de índices de columnstore,](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [reconstrução de índices de columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality).

## <a name="sql-on-demand-development-best-practices"></a>Melhores práticas recomendadas de desenvolvimento sob demanda sql

### <a name="general-considerations"></a>Considerações gerais

O SQL sob demanda permite que você consulta arquivos em suas contas de armazenamento do Azure. Ele não possui recursos locais de armazenamento ou ingestão, o que significa que todos os arquivos que as metas de consulta são externos ao SQL sob demanda. Portanto, tudo relacionado à leitura de arquivos do armazenamento pode ter um impacto no desempenho da consulta.

### <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Colocate Conta de armazenamento Azure e SQL sob demanda

Para minimizar a latência, colocalize sua conta de armazenamento Azure e seu ponto final SQL sob demanda. Contas de armazenamento e pontos finais provisionados durante a criação do espaço de trabalho estão localizados na mesma região.

Para obter um desempenho ideal, se você acessar outras contas de armazenamento com SQL sob demanda, certifique-se de que elas estão na mesma região. Caso contrário, haverá aumento da latência para a transferência de rede dos dados da região remota para a região do ponto final.

### <a name="azure-storage-throttling"></a>Estrangulamento do armazenamento azure

Vários aplicativos e serviços podem acessar sua conta de armazenamento. Quando o IOPS combinado ou o throughput gerado por aplicativos, serviços e carga de trabalho sob demanda excedem os limites da conta de armazenamento, ocorre o estrangulamento do armazenamento. Quando ocorre o estrangulamento do armazenamento, há um efeito negativo substancial no desempenho da consulta.

Uma vez detectado o estrangulamento, o SQL sob demanda tem um tratamento incorporado deste cenário. O SQL sob demanda fará solicitações de armazenamento em um ritmo mais lento até que o estrangulamento seja resolvido. 

No entanto, para uma execução ideal de consulta, é aconselhável que você não estresse a conta de armazenamento com outras cargas de trabalho durante a execução da consulta.

### <a name="prepare-files-for-querying"></a>Preparar arquivos para consulta

Se possível, você pode preparar arquivos para um melhor desempenho:

- Converter CSV para Parquet – Parquet é formato colunar. Uma vez que é compactado, ele tem tamanhos de arquivo menores do que arquivos CSV com os mesmos dados, e o SQL sob demanda precisará de menos tempo e solicitações de armazenamento para lê-lo.
- Se uma consulta tiver como alvo um único arquivo grande, você se beneficiará de dividi-lo em vários arquivos menores.
- Tente manter o tamanho do arquivo CSV abaixo de 10GB.
- É preferível ter arquivos de tamanho igual para um único caminho OPENROWSET ou uma tabela externa LOCATION.
- Particione seus dados armazenando partições para diferentes pastas ou nomes de arquivos - verifique [o uso de funções de nome de arquivo e filepath para segmentar partições específicas](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Use funções fileinfo e filepath para segmentar partições específicas

Os dados são frequentemente organizados em partições. Você pode instruir o SQL sob demanda a consultar pastas e arquivos específicos. Isso reduzirá o número de arquivos e a quantidade de dados que a consulta precisa ler e processar. 

Consequentemente, você alcançará um melhor desempenho. Para obter mais informações, verifique o [nome do arquivo](develop-storage-files-overview.md#filename-function) e as funções e exemplos do [filepath](develop-storage-files-overview.md#filepath-function) sobre como [consultar arquivos específicos](query-specific-files.md).

Se seus dados em armazenamento não forem particionados, considere particioná-los para que você possa usar essas funções para otimizar consultas direcionadas a esses arquivos.

Ao [consultar tabelas Spark particionadas](develop-storage-files-spark-tables.md) do SQL sob demanda, a consulta terá como destino automaticamente apenas os arquivos necessários.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Use cetas para melhorar o desempenho da consulta e se junte

[O CETAS](develop-tables-cetas.md) é um dos recursos mais importantes disponíveis no SQL sob demanda. O CETAS é uma operação paralela que cria metadados de tabela externa e exporta o resultado da consulta SELECT para um conjunto de arquivos em sua conta de armazenamento.

Você pode usar o CETAS para armazenar parte frequentemente usada de consultas, como tabelas de referência unidas, para um novo conjunto de arquivos. Mais tarde, você pode se juntar a esta única tabela externa em vez de repetir as junções comuns em várias consultas. 

À medida que o CETAS gera arquivos Parquet, as estatísticas serão criadas automaticamente quando a primeira consulta atingir esta tabela externa e você ganhará um desempenho melhor.

### <a name="next-steps"></a>Próximas etapas

Se você precisar de informações não fornecidas neste artigo, use o "Pesquisar documentos" no lado esquerdo desta página para pesquisar todos os documentos do pool SQL.  O [Fórum de Pool SQL](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) é um lugar para você fazer perguntas a outros usuários e ao Grupo de Produtos do pool SQL.  

Monitoramos ativamente esse fórum para garantir que suas perguntas sejam respondidas por outro usuário ou um de nós.  Se você preferir fazer suas perguntas sobre Stack Overflow, também temos um Fórum de Estouro de Stack Stack pool [Do Zure SQL](https://stackoverflow.com/questions/tagged/azure-sqldw).
 