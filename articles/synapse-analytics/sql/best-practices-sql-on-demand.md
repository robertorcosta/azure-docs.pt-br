---
title: Práticas recomendadas para SQL sob demanda (versão prévia) no Azure Synapse Analytics
description: Recomendações e práticas recomendadas que você deve saber ao trabalhar com o SQL sob demanda (versão prévia).
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 86678365d1510199247e8a1aaa48ec844d07de32
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592926"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Práticas recomendadas para SQL sob demanda (versão prévia) no Azure Synapse Analytics

Neste artigo, você encontrará uma coleção de práticas recomendadas para usar o SQL sob demanda (versão prévia). O SQL sob demanda é um recurso adicional no Azure Synapse Analytics.

## <a name="general-considerations"></a>Considerações gerais

O SQL sob demanda permite consultar arquivos em suas contas de armazenamento do Azure. Ele não tem armazenamento local nem recursos de ingestão. Assim, todos os arquivos que você pretende obter com a consulta são externos ao SQL sob demanda. Tudo relacionado à leitura de arquivos do armazenamento pode ter um impacto no desempenho da consulta.

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Localizar a conta de Armazenamento do Azure e o SQL sob demanda na mesma região

Para minimizar a latência, coloque a conta de Armazenamento do Azure e o ponto de extremidade do SQL sob demanda na mesma região. As contas de armazenamento e os pontos de extremidade provisionados durante a criação do workspace estão localizados na mesma região.

Para obter um desempenho ideal, se você acessar outras contas de armazenamento com o SQL sob demanda, verifique se elas estão na mesma região. Se elas não estiverem na mesma região, haverá uma latência maior para a transferência de rede dos dados entre as regiões do ponto de extremidade e remota.

## <a name="azure-storage-throttling"></a>Limitação do Armazenamento do Azure

Vários aplicativos e serviços podem acessar sua conta de armazenamento. A limitação de armazenamento ocorre quando o IOPS combinado ou a taxa de transferência gerada por aplicativos, serviços e carga de trabalho do SQL sob demanda excede os limites da conta de armazenamento. Como resultado, você enfrentará um impacto negativo significativo no desempenho da consulta.

Depois que a limitação é detectada, o SQL sob demanda manipula internamente esse cenário. O SQL sob demanda fará solicitações de armazenamento em um ritmo mais lento até que a limitação seja resolvida.

> [!TIP]
> Para uma execução de consulta ideal, você não deve sobrecarregar a conta de armazenamento com outras cargas de trabalho durante a execução da consulta.

## <a name="prepare-files-for-querying"></a>Preparar arquivos para consulta

Se possível, você pode preparar arquivos para melhorar o desempenho:

- Converta CSV e JSON em Parquet – Parquet é o formato de coluna. Como ele é compactado, seus tamanhos de arquivo são menores do que arquivos CSV ou JSON com os mesmos dados. O SQL sob demanda precisará de menos tempo e solicitações de armazenamento para lê-lo.
- Se uma consulta tiver como objetivo um único arquivo grande, dividi-lo em vários arquivos menores será vantajoso para você.
- Tente manter o tamanho do arquivo CSV abaixo de 10 GB.
- É melhor ter arquivos com o mesmo tamanho para um único caminho OPENROWSET ou um local de tabela externa.
- Particione seus dados armazenando partições em diferentes pastas ou nomes de arquivos, marque [usar as funções filename e filepath para partições específicas](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

## <a name="push-wildcards-to-lower-levels-in-path"></a>Enviar curingas por push para níveis inferiores no caminho

Você pode usar curingas no seu caminho para [consultar vários arquivos e pastas](develop-storage-files-overview.md#query-multiple-files-or-folders). O SQL sob demanda lista arquivos em sua conta de armazenamento começando do primeiro * usando a API de armazenamento e elimina os arquivos que não correspondem ao caminho especificado. A redução da lista inicial de arquivos pode melhorar o desempenho se houver muitos arquivos que correspondam ao caminho especificado até o primeiro caractere curinga.

## <a name="use-appropriate-data-types"></a>Usar tipos de dados apropriados

Os tipos de dados usados em sua consulta afetam o desempenho. Você pode obter um melhor desempenho se: 

- Usar o menor tamanho de dados que acomodará o maior valor possível.
  - Se o comprimento máximo do valor de caracteres for de 30 caracteres, use o tipo de dados de caractere de comprimento 30.
  - Se todos os valores de coluna de caracteres forem de tamanho fixo, use char ou nchar. Caso contrário, use varchar ou nvarchar.
  - Se o valor máximo da coluna inteiro for 500, use smallint, pois é o menor tipo de dados que pode acomodar esse valor. Você pode encontrar intervalos de tipo de dados inteiro [aqui](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=sql-server-ver15).
- Se possível, use varchar e char em vez de nvarchar e nchar.
- Use tipos de dados baseados em inteiro, se possível. As operações de classificação (sort), junção (join) e agrupar por (group by) são executadas mais rapidamente em dados do tipo inteiro do que em dados de caracteres.
- Se você estiver usando a inferência de esquema, [verificar o tipo de dados inferido](#check-inferred-data-types).

## <a name="check-inferred-data-types"></a>Verificar tipos de dados inferidos

A [inferência de esquema](query-parquet-files.md#automatic-schema-inference) ajuda a escrever rapidamente consultas e explorar dados sem conhecer o esquema de arquivos. Essa conveniência tem a contrapartida dos tipos de dados inferidos serem maiores do que eles realmente são. Isso acontece quando não há informações suficientes nos arquivos de origem para garantir que o tipo de dados apropriado seja usado. Por exemplo, os arquivos Parquet não contêm metadados sobre o comprimento máximo da coluna de caracteres e o SQL sob demanda infere-o como varchar(8000). 

Você pode verificar os tipos de dados resultantes de sua consulta usando [sp_describe_first_results_set](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15).

O exemplo a seguir mostra como você pode otimizar os tipos de dados inferidos. O procedimento é usado para mostrar tipos de dados inferidos. 
```sql  
EXEC sp_describe_first_result_set N'
    SELECT
        vendor_id, pickup_datetime, passenger_count
    FROM 
        OPENROWSET(
            BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*'',
            FORMAT=''PARQUET''
        ) AS nyc';
```

Este é o conjunto de resultados.

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar(8000)|8000|
|0|2|pickup_datetime|Datetime2 (7)|8|
|0|3|passenger_count|INT|4|

Depois de conhecermos os tipos de dados inferidos para consulta, podemos especificar os tipos de dados apropriados:

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Use as funções fileinfo e filepath para segmentar partições específicas

Os dados geralmente são organizados em partições. Você pode instruir o SQL sob demanda para consultar arquivos e pastas específicas. Essa função reduzirá o número de arquivos e a quantidade de dados que a consulta precisa ler e processar. Um bônus adicional é que você obterá um melhor desempenho.

Para obter mais informações, confira as funções [filename](develop-storage-files-overview.md#filename-function) e [filepath](develop-storage-files-overview.md#filepath-function) e exemplos sobre como [consultar arquivos específicos](query-specific-files.md).

> [!TIP]
> Sempre converta o resultado das funções filepath e fileinfo para os tipos de dados apropriado. Se você usar os tipos de dados de caractere, use o comprimento apropriado.

> [!NOTE]
> Atualmente, não há suporte para funções usadas para eliminação de partição, filepath e fileinfo, para tabelas externas diferentes daquelas criadas automaticamente para cada tabela criada no Apache Spark para o Azure Synapse Analytics.

Se os dados armazenados não estiverem particionados, considere particioná-los para que você possa usar essas funções para otimizar as consultas voltadas a esses arquivos. Quando é feita a [consulta de tabelas do Spark particionadas](develop-storage-files-spark-tables.md) do SQL sob demanda, a consulta irá automaticamente considerar apenas os arquivos necessários.

## <a name="use-parser_version-20-for-querying-csv-files"></a>Use PARSER_VERSION 2.0 para consultar os arquivos CSV

Você pode usar o analisador otimizado para desempenho ao consultar arquivos CSV. Confira [PARSER_VERSION](develop-openrowset.md) para obter detalhes.

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Use o CETAS para aprimorar o desempenho e as junções de consulta

[CETAS](develop-tables-cetas.md) é um dos recursos mais importantes disponíveis no SQL sob demanda. CETAS é uma operação paralela que cria metadados de tabela externa e exporta os resultados da consulta SELECT para um conjunto de arquivos em sua conta de armazenamento.

Você pode usar a operação CETAS para armazenar partes usadas com frequência de consultas, como tabelas de referência unidas, em um novo conjunto de arquivos. Em seguida, você pode unir a esta única tabela externa em vez de repetir junções comuns em várias consultas.

Como a operação CETAS gera arquivos Parquet, as estatísticas serão criadas automaticamente quando a primeira consulta for direcionada a essa tabela externa, resultando em um desempenho aprimorado.

## <a name="aad-pass-through-performance"></a>Desempenho de passagem do AAD

O SQL sob demanda permite que você acesse arquivos no armazenamento usando as credenciais de passagem ou de SAS do AAD. Você pode enfrentar um desempenho mais lento com a passagem em comparação à SAS do AAD. 

Se você precisar de um melhor desempenho, tente as credenciais SAS para acessar o armazenamento até que o desempenho de passagem do AAD seja melhorado.

## <a name="next-steps"></a>Próximas etapas

Veja o artigo de [Solução de problemas](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para conhecer os problemas e as soluções comuns. Se você estiver trabalhando com o pool de SQL em vez do SQL sob demanda, consulte o artigo [Práticas recomendadas para o pool de SQL](best-practices-sql-pool.md) para obter diretrizes específicas.
