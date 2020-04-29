---
title: Práticas recomendadas de desenvolvimento para SQL Synapse
description: Recomendações e práticas recomendadas que você deve saber ao desenvolver para Synapse SQL.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82086344"
---
# <a name="development-best-practices-for-synapse-sql"></a>Práticas recomendadas de desenvolvimento para SQL Synapse
Este artigo descreve orientações e práticas recomendadas à medida que você desenvolve sua solução de data warehouse. 

## <a name="sql-pool-development-best-practices"></a>Práticas recomendadas de desenvolvimento do pool do SQL

### <a name="reduce-cost-with-pause-and-scale"></a>Reduzir custos com pausa e dimensionamento

Para obter mais informações sobre como reduzir os custos por meio de pausa e dimensionamento, confira [Gerenciar computação](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="maintain-statistics"></a>Manter as estatísticas

Certifique-se de que atualizar as estatísticas diariamente ou após cada carga.  Sempre há compensações entre o desempenho e o custo para a criação e a atualização das estatísticas. Se você achar que está demorando muito para manter todas as suas estatísticas, seja mais seletivo sobre quais colunas têm estatísticas ou quais colunas precisam de atualização frequente.  

Por exemplo, talvez você queira atualizar as colunas de data, onde novos valores podem ser adicionados diariamente. 

> [!NOTE]
> Você obterá mais benefícios ao ter estatísticas em colunas envolvidas em junções, colunas usadas na cláusula WHERE e colunas encontradas em GROUP BY.

Consulte também [gerenciar estatísticas de tabela](develop-tables-statistics.md), [criar estatísticas](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [atualizar estatísticas](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="hash-distribute-large-tables"></a>Tabelas grandes com distribuição Hash

Por padrão, as tabelas são distribuídas pelo método Round Robin.  Isso facilita para os usuários começarem a criar tabelas sem a necessidade de decidir como suas tabelas devem ser distribuídas.  As tabelas Round Robin podem executar o suficiente para algumas cargas de trabalho. Mas, na maioria dos casos, a seleção de uma coluna de distribuição terá um desempenho muito melhor.  

O exemplo mais comum de quando uma tabela distribuída por uma coluna superará bastante uma tabela Round Robin ocorre quando duas tabelas de fatos grandes são unidas.  

Por exemplo, se você tiver uma tabela de pedidos, que é distribuída por order_id e uma tabela de transações, que também é distribuída pelo order_id, ao unir a tabela de pedidos à tabela de transações no order_id, essa consulta se torna uma consulta de passagem. 

Isso significa que eliminamos as operações de movimentação de dados.  Menos etapas significam uma consulta mais rápida.  Menos movimento de dados também resulta em consultas mais rápidas.

> [!TIP]
> Ao carregar uma tabela distribuída, verifique se os dados de entrada não estão classificados na chave de distribuição, pois isso reduzirá a velocidade das cargas.  

Consulte os links a seguir para obter detalhes adicionais sobre como selecionar uma coluna de distribuição pode melhorar o desempenho, bem como definir uma tabela distribuída na cláusula WITH da sua instrução CREATE TABLEs.

Consulte também [visão geral da tabela](develop-tables-overview.md), [distribuição de tabela](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), seleção de [distribuição de tabela](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [CREATE TABLE como SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="do-not-over-partition"></a>Não estender a partição
Embora o particionamento de dados possa ser eficaz para manter seus dados por meio da alternância de partição ou otimização de verificações com a eliminação de partição, ter muitas partições pode retardar suas consultas.  Geralmente, uma estratégia de particionamento de alta granularidade que pode funcionar bem em SQL Server pode não funcionar bem no pool do SQL.  

> [!NOTE]
> Geralmente, uma estratégia de particionamento de alta granularidade que pode funcionar bem em SQL Server pode não funcionar bem no pool do SQL.  

Ter muitas partições também poderá reduzir a eficiência dos índices columnstore clusterizados se cada partição tiver menos de 1 milhão de linhas. O pool do SQL particiona seus dados para você em bancos de dado 60. 

Portanto, se você criar uma tabela com 100 partições, o resultado será de 6000 partições.  Cada carga de trabalho é diferente, assim, o melhor conselho é fazer experiências com o particionamento para ver o que funciona melhor para sua carga de trabalho.  

Uma opção a ser considerada é usar uma granularidade menor do que o que pode ter trabalhado para você em SQL Server.  Por exemplo, considere usar partições semanais ou mensais, em vez de partições diárias.

Consulte também [particionamento de tabela](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="minimize-transaction-sizes"></a>Minimizar os tamanhos das transações

As instruções INSERT, UPDATE e DELETE são executadas em uma transação e quando falham, devem ser revertidas.  Para minimizar a possibilidade de uma reversão longa, minimize os tamanhos das transações sempre que possível.  Isso pode ser feito dividindo as instruções INSERT, UPDATE e DELETE em partes.  

Por exemplo, se você tiver uma inserção que espera levar 1 hora, poderá dividir a inserção em quatro partes, reduzindo cada vez cada execução para 15 minutos.

> [!TIP]
> Aproveite os casos especiais de Registro em Log Mínimo, como CTAS, TRUNCATE, DROP TABLE ou INSERT, para esvaziar as tabelas, reduzindo o risco de reversão.  

Outra maneira de eliminar as reversões é usar as operações de Metadados Somente, como a troca de partição para o gerenciamento de dados.  

Por exemplo, em vez de executar uma instrução DELETE para excluir todas as linhas em uma tabela onde order_date foi em outubro de 2001, você poderia particionar os dados mensalmente e, em seguida, trocar a partição com os dados por uma partição vazia a partir de outra tabela (consulte exemplos de ALTER TABLE).  

Para as tabelas não particionadas, considere usar um CTAS para gravar os dados que você deseja manter em uma tabela em vez de usar DELETE.  Se um CTAS demorar a mesma quantidade de tempo, será uma operação muito mais segura de executar quando ele tiver um log de transação mínimo e puder ser cancelado rapidamente, se necessário.

Consulte também [noções básicas sobre transações](develop-transactions.md), [otimização de transações](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [particionamento de tabela](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [alterar tabela](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [criar tabela como SELECT (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="use-the-smallest-possible-column-size"></a>Use o menor tamanho de coluna possível

Ao definir a DDL, usar o menor tipo de dados que oferece suporte a seus dados melhorará o desempenho da consulta.  Isso é especialmente importante para as colunas CHAR e VARCHAR.  

Se o maior valor em uma coluna for 25 caracteres, então, defina a coluna como VARCHAR(25).  Evite definir todas as colunas de caractere para um tamanho grande padrão.  Além disso, defina as colunas como VARCHAR quando isso for realmente necessário em vez de usar NVARCHAR.

Consulte também [visão geral da tabela](develop-tables-overview.md), [tipos de dados de tabela](develop-tables-data-types.md)e [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="optimize-clustered-columnstore-tables"></a>Otimizar tabelas columnstore clusterizadas

Os índices columnstore clusterizados são uma das maneiras mais eficientes de armazenar seus dados no pool SQL.  Por padrão, as tabelas no pool SQL são criadas como ColumnStore clusterizado.  

É importante ter um segmento de boa qualidade para obter o melhor desempenho para as consultas nas tabelas columnstore.  Quando as linhas são gravadas nas tabelas columnstore sob pressão da memória, a qualidade do segmento columnstore pode ficar prejudicada.  

A qualidade de segmento pode ser medida pelo número de linhas em um grupo de linhas compactado.  Consulte o artigo [causas de baixa qualidade de índice columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) e [índices de tabela](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para obter instruções passo a passo sobre como detectar e melhorar a qualidade de segmento para tabelas columnstore clusterizadas.  

Como os segmentos columnstore de alta qualidade são importantes, é uma boa ideia usar IDs de usuários que estão na classe de recursos de médio ou grande para carregamento de dados. Usar [unidades de data warehouse](resource-consumption-models.md) menores significa que você deseja atribuir uma classe de recursos maior ao usuário que está carregando.

Como as tabelas columnstore geralmente não enviam dados para um segmento columnstore compactado até que haja mais de 1 milhão linhas por tabela, e cada tabela de pool do SQL é particionada em 60 tabelas, as tabelas columnstore não beneficiarão uma consulta, a menos que a tabela tenha mais de 60 milhões linhas.  

> [!TIP]
> Para tabelas com menos de 60 milhões linhas, ter um índice columstore pode não ser a solução ideal.  

Além disso, se você particionar seus dados, desejará considerar que cada partição precisará ter 1 milhão de linhas para se beneficiar de um índice columnstore clusterizado.  Se uma tabela tiver 100 partições, será necessário ter pelo menos 6.000.000.000 linhas para se beneficiar de um repositório de colunas clusterizado (60 distribuições *100 partições* 1 milhão linhas).  

Se a tabela não tiver 6.000.000.000 linhas, reduza o número de partições ou considere usar uma tabela de heap.  Também pode valer a pena fazer testes para ver se um desempenho melhor pode ser obtido usando uma tabela de heap com índices secundários em vez de uma tabela columnstore.

Ao consultar uma tabela columnstore, as consultas serão executadas mais rapidamente se você selecionar apenas as colunas necessárias.  

Consulte também [índices de tabela](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Guia de índices columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [recriando índices columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality).

## <a name="sql-on-demand-development-best-practices"></a>Práticas recomendadas de desenvolvimento sob demanda do SQL

### <a name="general-considerations"></a>Considerações gerais

O SQL sob demanda permite consultar arquivos em suas contas de armazenamento do Azure. Ele não tem armazenamento local nem recursos de ingestão, o que significa que todos os arquivos que a consulta tem como destino são externos ao SQL sob demanda. Portanto, tudo relacionado à leitura de arquivos do armazenamento pode ter um impacto no desempenho da consulta.

### <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Colocar a conta de armazenamento do Azure e o SQL sob demanda

Para minimizar a latência, coloque a conta de armazenamento do Azure e o ponto de extremidade sob demanda do SQL. As contas de armazenamento e os pontos de extremidade provisionados durante a criação do espaço de trabalho estão localizados na mesma região.

Para obter um desempenho ideal, se você acessar outras contas de armazenamento com o SQL sob demanda, verifique se elas estão na mesma região. Caso contrário, haverá uma latência maior para a transferência de rede dos dados da região remota para a região do ponto de extremidade.

### <a name="azure-storage-throttling"></a>Limitação de armazenamento do Azure

Vários aplicativos e serviços podem acessar sua conta de armazenamento. Quando o IOPS combinado ou a taxa de transferência gerada por aplicativos, serviços e carga de trabalho sob demanda do SQL exceder os limites da conta de armazenamento, ocorrerá a limitação de armazenamento. Quando ocorre a limitação de armazenamento, há um efeito negativo significativo no desempenho da consulta.

Depois que a limitação é detectada, o SQL sob demanda tem manipulação interna desse cenário. O SQL sob demanda fará solicitações de armazenamento em um ritmo mais lento até que a limitação seja resolvida. 

No entanto, para a execução de consulta ideal, é recomendável que você não enfatize a conta de armazenamento com outras cargas de trabalho durante a execução da consulta.

### <a name="prepare-files-for-querying"></a>Preparar arquivos para consulta

Se possível, você pode preparar arquivos para melhorar o desempenho:

- Converta CSV em parquet – parquet é o formato de coluna. Como ele é compactado, ele tem tamanhos de arquivo menores do que arquivos CSV com os mesmos dados, e o SQL sob demanda precisará de menos tempo e solicitações de armazenamento para lê-lo.
- Se uma consulta tiver como alvo um único arquivo grande, você se beneficiará de dividi-lo em vários arquivos menores.
- Tente manter o tamanho do arquivo CSV abaixo de 10 GB.
- É preferível ter arquivos igualmente dimensionados para um único caminho OPENROWSET ou um local de tabela externa.
- Particione seus dados armazenando partições em diferentes pastas ou nomes de arquivo-marque as [funções filename e filePath para direcionar partições específicas](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Usar funções FileInfo e filePath para direcionar partições específicas

Os dados geralmente são organizados em partições. Você pode instruir o SQL sob demanda para consultar arquivos e pastas particulares. Isso reduzirá o número de arquivos e a quantidade de dados que a consulta precisa ler e processar. 

Consequentemente, você obterá um melhor desempenho. Para obter mais informações, verifique as funções [filename](develop-storage-files-overview.md#filename-function) e [FilePath](develop-storage-files-overview.md#filepath-function) e exemplos sobre como [consultar arquivos específicos](query-specific-files.md).

Se os dados no armazenamento não estiverem particionados, considere particioná-los para que você possa usar essas funções para otimizar as consultas direcionadas a esses arquivos.

Ao [consultar tabelas do Spark particionadas](develop-storage-files-spark-tables.md) do SQL sob demanda, a consulta irá automaticamente direcionar apenas os arquivos necessários.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Use o CETAS para aprimorar o desempenho e as junções de consulta

O [CETAS](develop-tables-cetas.md) é um dos recursos mais importantes disponíveis no SQL sob demanda. CETAS é uma operação paralela que cria metadados de tabela externa e exporta o resultado da consulta SELECT para um conjunto de arquivos em sua conta de armazenamento.

Você pode usar CETAS para armazenar a parte usada com frequência de consultas, como tabelas de referência Unidas, em um novo conjunto de arquivos. Posteriormente, você pode ingressar nessa única tabela externa em vez de repetir junções comuns em várias consultas. 

Como o CETAS gera arquivos parquet, as estatísticas serão criadas automaticamente quando a primeira consulta for direcionada a essa tabela externa e você obterá um desempenho aprimorado.

### <a name="next-steps"></a>Próximas etapas

Se você precisar de informações não fornecidas neste artigo, use "Pesquisar documentos" no lado esquerdo desta página para pesquisar todos os documentos do pool de SQL.  O [Fórum do pool do SQL](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) é um local para você colocar perguntas para outros usuários e para o grupo de produtos do pool do SQL.  

Monitoramos ativamente esse fórum para garantir que suas perguntas sejam respondidas por outro usuário ou um de nós.  Se você preferir fazer suas perguntas em Stack Overflow, também temos um [Fórum do pool SQL do Azure Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw).
 