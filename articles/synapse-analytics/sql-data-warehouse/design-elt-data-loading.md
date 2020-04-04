---
title: Em vez de ETL, design ELT
description: Implemente estratégias flexíveis de carregamento de dados para o pool Synapse SQL no Azure Synapse Analytics
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/19/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 87b33e91076f8f7f31740795f0ec05cea49a1e83
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631199"
---
# <a name="data-loading-strategies-for-synapse-sql-pool"></a>Estratégias de carregamento de dados para pool Synapse SQL

Os pools SQL smp tradicionais usam um processo Deextração, Transformação e Carga (ETL) para carregar dados. O pool Synapse SQL, dentro do Azure Synapse Analytics, possui uma arquitetura de processamento massivamente paralela (MPP) que aproveita a escalabilidade e flexibilidade dos recursos de computação e armazenamento.

O uso de um processo de Extrato, Carga e Transformação (ELT) aproveita o MPP e elimina os recursos necessários para a transformação de dados antes do carregamento.

Embora o pool SQL suporte muitos métodos de carregamento, incluindo opções populares do SQL Server, como [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) e [a API SqlBulkCopy,](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)a maneira mais rápida e escalável de carregar dados é através de tabelas externas do PolyBase e da [declaração COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (visualização).

Com o PolyBase e a instrução COPY, você pode acessar dados externos armazenados no armazenamento Azure Blob ou no Azure Data Lake Store através do idioma T-SQL. Para obter a maior flexibilidade ao carregar, recomendamos o uso da declaração COPY.

> [!NOTE]  
> Atualmente, a instrução COPY está em visualização pública. Para fornecer feedback, envie e-mail sqldwcopypreview@service.microsoft.compara a seguinte lista de distribuição: .

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="what-is-elt"></a>O que é ELT?

Extrair, Carregar e Transformar (ELT) é um processo pelo qual os dados são extraídos de um sistema de origem, carregados em um pool SQL e transformados.

As etapas básicas para a implementação do ELT são:

1. Extrair os dados de origem em arquivos de texto.
2. Descarregar os dados no armazenamento de Blobs do Azure ou no Azure Data Lake Store.
3. Preparar os dados para o carregamento.
4. Carregue os dados em tabelas de preparação com o polyBase ou o comando COPY.
5. Transformar os dados.
6. Inserir os dados nas tabelas de produção.

Para obter um tutorial de carregamento do PolyBase, consulte [Use PolyBase para carregar dados do armazenamento blob do Azure](load-data-from-azure-blob-storage-using-polybase.md).

Para obter mais informações, consulte [Blog de padrão de carga](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/).

## <a name="1-extract-the-source-data-into-text-files"></a>1. Extrair os dados de origem em arquivos de texto

Obter dados de fora do seu sistema de origem depende da localização de armazenamento.  O objetivo é mover os dados para o PolyBase e os arquivos detexto delimitado ou CSV suportados pelo COPY.

### <a name="polybase-and-copy-external-file-formats"></a>Formatos de arquivos externos PolyBase e COPY

Com o PolyBase e a declaração COPY, você pode carregar dados de arquivos de texto delimitado ou CSV codificados utf-8 e UTF-16. Além de arquivos de texto ou CSV delimitados, ele carrega a partir dos formatos de arquivo Hadoop, como ORC e Parquet. O PolyBase e a declaração COPY também podem carregar dados de arquivos compactados Gzip e Snappy.

Não são suportados formatos ASCII, largura fixa e formatos aninhados, como WinZip ou XML. Se você estiver exportando do SQL Server, você pode usar a [ferramenta bcp command-line](/sql/tools/bcp-utility?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para exportar os dados para arquivos de texto delimitados.

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Aterre os dados no armazenamento Azure Blob ou no Azure Data Lake Store

Para pousar os dados no armazenamento Do Zure, você pode movê-los para [o armazenamento Azure Blob](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ou [Para o Azure Data Lake Store Gen2](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Em qualquer localização, os dados devem ser armazenados em arquivos de texto. O PolyBase e a declaração COPY podem ser carregados de qualquer local.

Ferramentas e serviços que você pode usar para mover dados para o Armazenamento do Microsoft Azure:

- O serviço [Azure ExpressRoute](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) melhora a taxa de transferência de rede, o desempenho e a previsibilidade. O ExpressRoute é um serviço que encaminha os dados por uma conexão privada dedicada para o Azure. As conexões do ExpressRoute não encaminham dados pela Internet pública. As conexões oferecem mais confiabilidade e velocidade, latências menores e maior segurança do que as conexões comuns pela Internet.
- O [Utilitário AZCopy](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) move os dados para o Armazenamento do Microsoft Azure pela internet pública. Isso funciona se os tamanhos dos seus dados forem inferiores a 10 TB. Para executar cargas regularmente com AZCopy, teste a velocidade da rede para ver se ela é aceitável.
- O [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) tem um gateway que você pode instalar no seu servidor local. Em seguida, você pode criar um pipeline para mover os dados do seu servidor local para o Armazenamento do Microsoft Azure. Para usar a Fábrica de Dados com o SQL Analytics, consulte [Carregar dados para SQL Analytics](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="3-prepare-the-data-for-loading"></a>3. Prepare os dados para o carregamento

Você pode precisar preparar e limpar os dados em sua conta de armazenamento antes de carregar. A preparação de dados pode ser executada enquanto seus dados estiverem na origem, conforme você exporta os dados para arquivos de texto ou após os dados no Armazenamento do Microsoft Azure.  É mais fácil trabalhar com os dados o mais precocemente possível no processo.  

### <a name="define-external-tables"></a>Definir tabelas externas

Se você estiver usando o PolyBase, você precisa definir tabelas externas no seu pool SQL antes de carregar. As tabelas externas não são exigidas pela declaração COPY. O PolyBase usa tabelas externas para definir e acessar os dados no Armazenamento do Microsoft Azure.

Uma tabela externa é semelhante a uma exibição de banco de dados. A tabela externa contém o esquema da tabela e aponta para dados armazenados fora do pool SQL.

Definir tabelas externas envolve a especificação da fonte de dados, o formato dos arquivos de texto e as definições de tabela. Os artigos de referência de sintaxe T-SQL que você precisará são:

- [CRIAR UMA FONTE DE DADOS EXTERNA](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CRIAR FORMATO DE ARQUIVO EXTERNO](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CRIAR TABELA EXTERNA](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Ao carregar o Parquet, o mapeamento do tipo de dados SQL é:

| **Tipo de dados do Parquet** | **Tipo de dados SQL** |
| :-------------------: | :---------------: |
|        TINYINT        |      TINYINT      |
|       SMALLINT        |     SMALLINT      |
|          INT          |        INT        |
|        BIGINT         |      BIGINT       |
|        booleano        |        bit        |
|        double         |       FLOAT       |
|         FLOAT         |       real        |
|        double         |       money       |
|        double         |    SMALLMONEY     |
|        string         |       NCHAR       |
|        string         |     NVARCHAR      |
|        string         |       char        |
|        string         |      varchar      |
|        binary         |      binary       |
|        binary         |     varbinary     |
|        timestamp       |       date        |
|        timestamp       |   smalldatetime   |
|        timestamp       |     datetime2     |
|        timestamp       |     DATETIME      |
|        timestamp       |       time        |
|         date          |       date        |
|        decimal        |      decimal      |

Para obter um exemplo de criação de objetos externos, consulte a etapa [Criar tabelas externas](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) no tutorial de carregamento.

### <a name="format-text-files"></a>Arquivos de texto formatados

Se você estiver usando o PolyBase, os objetos externos definidos precisam alinhar as linhas dos arquivos de texto com a definição de formato de tabela e arquivo externo. Os dados em cada linha do arquivo de texto devem se alinhar com a definição da tabela.
Para formatar os arquivos de texto:

- Se os seus dados forem provenientes de uma fonte não relacional, você precisa transformá-los em linhas e colunas. Se os dados forem de uma fonte relacional ou não, os dados devem ser transformados para se alinharem com as definições de coluna para a tabela na qual você planeja carregar os dados.
- Formatar dados no arquivo de texto para alinhar com as colunas e tipos de dados na tabela de destino. O desalinhamento entre os tipos de dados nos arquivos de texto externos e na tabela de pool SQL faz com que as linhas sejam rejeitadas durante a carga.
- Separar os campos no arquivo de texto com um terminador.  Certifique-se de usar um caractere ou uma seqüência de caracteres que não seja encontrado em seus dados de origem. Use o terminador especificado com [CRIAR FORMATO DE ARQUIVO EXTERNO](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. Carregue os dados usando o PolyBase ou a declaração COPY

É uma melhor prática carregar dados em uma tabela de preparo. Tabelas de preparo permitem manipular erros sem interferir nas tabelas de produção. Uma tabela de estadiamento também lhe dá a oportunidade de usar o MPP do pool SQL para transformações de dados antes de inserir os dados em tabelas de produção.

A tabela precisará ser pré-criada ao carregar em uma tabela de preparação com COPY.

### <a name="options-for-loading-with-polybase-and-copy-statement"></a>Opções para carregamento com a declaração PolyBase e COPY

Para carregar dados com o PolyBase, é possível usar qualquer uma destas opções de carregamento:

- O [PolyBase com o T-SQL](load-data-from-azure-blob-storage-using-polybase.md) funciona bem quando os seus dados estiverem no armazenamento de Blobs do Azure ou no Azure Data Lake Store. Ele oferece mais controle sobre o processo de carregamento, mas também exige que você defina objetos de dados externos. Os outros métodos definem esses objetos em segundo plano, como mapear as tabelas de origem para as tabelas de destino.  Para coordenar as cargas de T-SQL, você pode usar o Azure Data Factory, SSIS ou as funções do Azure.
- O [PolyBase com o SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) funciona bem quando os seus dados de origem estiverem no SQL Server, no SQL Server local ou na nuvem. O SSIS define a origem para mapeamentos de tabela de destino e também coordena a carga. Se você já tiver pacotes SSIS, você pode modificar os pacotes para trabalhar com o novo destino do data warehouse.
- [A declaração PolyBase e COPY com a Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) é outra ferramenta de orquestração.  Ele define um pipeline e agenda de trabalhos.
- [O PolyBase com o Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) transfere dados de uma tabela para um dataframe do Databricks e/ou grava dados de um dataframe do Databricks para uma tabela usando o PolyBase.

### <a name="other-loading-options"></a>Outras opções de carregamento

Além do PolyBase e da declaração COPY, você pode usar [bcp](https://docs.microsoft.com/sql/tools/bcp-utility?view=sql-server-ver15) ou a [API SqlBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). bcp carrega diretamente para o banco de dados sem passar pelo armazenamento Azure Blob, e é destinado apenas para pequenas cargas.

> [!NOTE]
> Observe que o desempenho de carga dessas opções é mais lento do que o PolyBase e a declaração COPY.

## <a name="5-transform-the-data"></a>5. Transforme os dados

Enquanto os dados estão na tabela de preparo, execute as transformações que a sua carga de trabalho exige. Em seguida, mova os dados para uma tabela de produção.

## <a name="6-insert-the-data-into-production-tables"></a>6. Insira os dados nas tabelas de produção

A INSERÇÃO EM ... A declaração SELECT move os dados da tabela de estadiamento para a tabela permanente.

Ao criar um processo de ETL, tente executar o processo em uma amostra de teste pequena. Tente extrair 1000 linhas da tabela para um arquivo, movê-lo para o Azure e, em seguida, tente carregá-lo em uma tabela de preparo.

## <a name="partner-loading-solutions"></a>Soluções de carregamento de parceiros

Muitos de nossos parceiros têm soluções de carregamento. Para saber mais, consulte uma lista dos nossos [parceiros de solução](sql-data-warehouse-partner-business-intelligence.md).

## <a name="next-steps"></a>Próximas etapas

Para orientações sobre carregamento, consulte [Diretrizes para carregar dados](guidance-for-loading-data.md).
