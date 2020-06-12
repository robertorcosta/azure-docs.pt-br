---
title: Melhores práticas do SQL sob demanda (versão prévia)
description: Recomendações e melhores práticas que você deve saber ao trabalhar com o SQL sob demanda (versão prévia).
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 79318ab67ec58ed10520365a366785ea0de41666
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836321"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Práticas recomendadas para SQL sob demanda (versão prévia) no Azure Synapse Analytics

Neste artigo, você encontrará uma coleção de práticas recomendadas para usar o SQL sob demanda (versão prévia). O SQL sob demanda é um recurso do Azure Synapse Analytics.

## <a name="general-considerations"></a>Considerações gerais

O SQL sob demanda permite consultar arquivos em suas contas de armazenamento do Azure. Ele não tem armazenamento local nem recursos de ingestão. Assim, todos os arquivos que você pretende obter com a consulta são externos ao SQL sob demanda. Tudo relacionado à leitura de arquivos do armazenamento pode ter um impacto no desempenho da consulta.

## <a name="colocate-your-azure-storage-account-and-sql-on-demand"></a>Colocar a conta de armazenamento do Azure e o SQL sob demanda na mesma região

Para minimizar a latência, coloque a conta de Armazenamento do Azure e o ponto de extremidade do SQL sob demanda na mesma região. As contas de armazenamento e os pontos de extremidade provisionados durante a criação do workspace estão localizados na mesma região.

Para obter um desempenho ideal, se você acessar outras contas de armazenamento com o SQL sob demanda, verifique se elas estão na mesma região. Se elas não estiverem na mesma região, haverá uma latência maior para a transferência de rede dos dados entre a região remota e as regiões do ponto de extremidade.

## <a name="azure-storage-throttling"></a>Limitação do Armazenamento do Azure

Vários aplicativos e serviços podem acessar sua conta de armazenamento. A limitação de armazenamento ocorre quando o IOPS combinado ou a taxa de transferência gerada por aplicativos, serviços e carga de trabalho do SQL sob demanda excede os limites da conta de armazenamento. Como resultado, você enfrentará um impacto negativo significativo no desempenho da consulta.

Quando a limitação é detectada, o SQL sob demanda manipula internamente para solucioná-la. O SQL sob demanda fará solicitações de armazenamento em um ritmo mais lento até que a limitação seja resolvida.

> [!TIP]
> Para uma execução de consulta ideal, não sobrecarregue a conta de armazenamento com outras cargas de trabalho durante a execução da consulta.

## <a name="prepare-files-for-querying"></a>Preparar arquivos para consulta

Se possível, você pode preparar arquivos para melhorar o desempenho:

- Converta CSV e JSON em Parquet. Parquet é um formato de coluna. Como ele é compactado, os tamanhos de arquivo são menores do que arquivos CSV ou JSON com os mesmos dados. O SQL sob demanda precisará de menos tempo e menos solicitações de armazenamento para lê-lo.
- Se uma consulta tiver como objetivo um único arquivo grande, dividi-lo em vários arquivos menores será vantajoso para você.
- Tente manter o tamanho do arquivo CSV abaixo de 10 GB.
- É melhor ter arquivos com o mesmo tamanho para um único caminho OPENROWSET ou um local de tabela externa.
- Particione os dados armazenando partições em diferentes pastas ou nomes de arquivos. Consulte [Usar as funções fileinfo e filepath para segmentar partições específicas](#use-filename-and-filepath-functions-to-target-specific-partitions).

## <a name="push-wildcards-to-lower-levels-in-the-path"></a>Enviar curingas por push para níveis inferiores no caminho

Você pode usar curingas no seu caminho para [consultar vários arquivos e pastas](develop-storage-files-overview.md#query-multiple-files-or-folders). O SQL sob demanda lista os arquivos na conta de armazenamento a partir do primeiro * usando a API de armazenamento. Ele elimina os arquivos que não correspondem ao caminho especificado. A redução da lista de arquivos inicial pode melhorar o desempenho se houver muitos arquivos que correspondam ao caminho especificado até o primeiro caractere curinga.

## <a name="use-appropriate-data-types"></a>Usar tipos de dados apropriados

Os tipos de dados usados na consulta afetam o desempenho. Obtenha um melhor desempenho segundo as instruções a seguir: 

- Usar o menor tamanho de dados que acomodará o maior valor possível.
  - Se o comprimento máximo do valor de caracteres for de 30 caracteres, use um tipo de dados de caractere de comprimento 30.
  - Se todos os valores de coluna de caracteres forem de tamanho fixo, use **char** ou **nchar**. Caso contrário, use **varchar** ou **nvarchar**.
  - Se o valor máximo inteiro da coluna for 500, use **smallint**, pois é o menor tipo de dados que pode acomodar esse valor. Encontre os intervalos de tipo de dados inteiro [neste artigo](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=sql-server-ver15).
- Se possível, use **varchar** e **char** em vez de **nvarchar** e **nchar**.
- Use tipos de dados baseados em inteiro, se possível. As operações SORT, JOIN e GROUP são concluídas mais rapidamente em dados do tipo inteiro do que em dados de caracteres.
- Se você estiver usando a inferência de esquema, [verifique os tipos de dados inferidos](#check-inferred-data-types).

## <a name="check-inferred-data-types"></a>Verificar tipos de dados inferidos

A [inferência de esquema](query-parquet-files.md#automatic-schema-inference) ajuda a escrever rapidamente consultas e explorar dados sem conhecer os esquemas de arquivos. O custo dessa conveniência é que os tipos de dados inferidos são maiores que os tipos de dados reais. Isso acontece quando não há informações suficientes nos arquivos de origem para garantir que o tipo de dados apropriado seja usado. Por exemplo, os arquivos Parquet não contêm metadados sobre o comprimento máximo da coluna de caracteres. Portanto, o SQL sob demanda infere como varchar (8000).

Use [sp_describe_first_results_set](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15) para verificar os tipos de dados resultantes da consulta.

O exemplo a seguir mostra como você pode otimizar os tipos de dados inferidos. Esse procedimento é usado para mostrar os tipos de dados inferidos: 
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

Este é o conjunto de resultados:

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar(8000)|8000|
|0|2|pickup_datetime|Datetime2 (7)|8|
|0|3|passenger_count|INT|4|

Depois de conhecermos os tipos de dados inferidos para a consulta, podemos especificar os tipos de dados apropriados:

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of the inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>Usar as funções fileinfo e filepath para segmentar partições específicas

Os dados geralmente são organizados em partições. Você pode instruir o SQL sob demanda para consultar arquivos e pastas específicas. Fazer isso reduzirá o número de arquivos e a quantidade de dados que a consulta precisa ler e processar. Um bônus adicional é que você obterá um melhor desempenho.

Para obter mais informações, leia sobre as funções [filename](develop-storage-files-overview.md#filename-function) e [filepath](develop-storage-files-overview.md#filepath-function) e veja os exemplos para [consultar arquivos específicos](query-specific-files.md).

> [!TIP]
> Sempre converta os resultados das funções filepath e filename nos tipos de dados apropriados. Se você usar os tipos de dados de caracteres, certifique-se de usar o comprimento apropriado.

> [!NOTE]
> Atualmente, não há suporte para funções usadas para eliminação de partição, filepath e filename, em tabelas externas diferentes daquelas criadas automaticamente para cada tabela criada no Apache Spark do Azure Synapse Analytics.

Se os dados armazenados não estiverem particionados, considere particioná-los. Dessa forma, você pode usar essas funções para otimizar consultas direcionadas a esses arquivos. Quando você [consultar o Apache Spark particionado para tabelas do Synapse do Azure](develop-storage-files-spark-tables.md) a partir do SQL sob demanda, a consulta será automaticamente direcionada somente aos arquivos necessários.

## <a name="use-parser_version-20-to-query-csv-files"></a>Usar PARSER_VERSION 2.0 para consultar arquivos CSV

Você pode usar um analisador otimizado para desempenho ao consultar arquivos CSV. Para obter detalhes, consulte [PARSER_VERSION](develop-openrowset.md).

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Use o CETAS para aprimorar o desempenho e as junções de consulta

[CETAS](develop-tables-cetas.md) é um dos recursos mais importantes disponíveis no SQL sob demanda. CETAS é uma operação paralela que cria metadados de tabela externa e exporta os resultados da consulta SELECT para um conjunto de arquivos em sua conta de armazenamento.

Você pode usar a operação CETAS para armazenar partes usadas com frequência de consultas, como tabelas de referência unidas, em um novo conjunto de arquivos. Você pode unir a essa única tabela externa em vez de repetir junções comuns em várias consultas.

Como a operação CETAS gera arquivos Parquet, as estatísticas serão criadas automaticamente quando a primeira consulta for direcionada a essa tabela externa, resultando em um desempenho aprimorado.

## <a name="azure-ad-pass-through-performance"></a>Desempenho de passagem do Azure AD

O SQL sob demanda permite que você acesse arquivos no armazenamento usando as credenciais de passagem ou de SAS do Azure Active Directory (Azure AD). Você pode enfrentar um desempenho mais lento com a passagem em comparação à SAS do Azure AD.

Se precisar de um melhor desempenho, experimente usar as credenciais SAS para acessar o armazenamento até que o desempenho de passagem do Azure AD seja aprimorado.

## <a name="next-steps"></a>Próximas etapas

Veja o artigo de [solução de problemas](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para conhecer os problemas e as soluções comuns. Se estiver trabalhando com o pool de SQL em vez do SQL sob demanda, consulte [Melhores práticas para o pool do SQL](best-practices-sql-pool.md) para obter diretrizes específicas.
