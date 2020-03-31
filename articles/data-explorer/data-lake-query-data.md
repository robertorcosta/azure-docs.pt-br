---
title: Dados de consulta no Lago de Dados do Azure usando o Azure Data Explorer
description: Saiba como consultar dados no Lago de Dados Do Azure usando o Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: 8240b1a01aa39e53b9ae41f73543ccf9774290b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161742"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer"></a>Dados de consulta no Lago de Dados do Azure usando o Azure Data Explorer

O Azure Data Lake Storage é uma solução de lago de dados altamente escalável e econômica para análise de big data. Ele combina o poder de um sistema de arquivos de alto desempenho com escala e economia maciças para ajudá-lo a acelerar seu tempo de insights. O Data Lake Storage Gen2 amplia os recursos de armazenamento do Azure Blob e é otimizado para cargas de trabalho de análise.
 
O Azure Data Explorer integra-se ao Azure Blob Storage e ao Azure Data Lake Storage (Gen1 e Gen2), fornecendo acesso rápido, armazenado e indexado aos dados no lago. Você pode analisar e consultar dados no lago sem a ingestão prévia no Azure Data Explorer. Você também pode consultar dados de lagos nativos ingeridos e não ingeridos simultaneamente.  

> [!TIP]
> O melhor desempenho de consulta requer a ingestão de dados no Azure Data Explorer. A capacidade de consultar dados externos sem ingestão prévia só deve ser usada para dados históricos ou dados que raramente são consultados. [Otimize o desempenho da consulta no lago](#optimize-your-query-performance) para obter melhores resultados.
 

## <a name="create-an-external-table"></a>Criar uma tabela externa

 > [!NOTE]
 > Atualmente, as contas de armazenamento suportadas são Azure Blob Storage ou Azure Data Lake Storage (Gen1 e Gen2).

1. Use `.create external table` o comando para criar uma tabela externa no Azure Data Explorer. Comandos adicionais de `.show` `.drop`tabela `.alter` externa, tais como , e estão documentados em [comandos de tabela externa](/azure/kusto/management/externaltables).

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```
    
    > [!NOTE]
    > * Espera-se um desempenho maior com particionamento mais granular. Por exemplo, consultas sobre tabelas externas com partições diárias, terão melhor desempenho do que aquelas consultas com tabelas particionadas mensais.
    > * Quando você define uma tabela externa com partições, espera-se que a estrutura de armazenamento seja idêntica.
Por exemplo, se a tabela for definida com uma partição DateTime no formato yyyy/MM/dd (padrão), o caminho do arquivo de armazenamento URI deve ser *container1/yyyy/MM/dd/all_exported_blobs*. 
    > * Se a tabela externa for particionada por uma coluna de data-hora, inclua sempre um `ArchivedProducts | where Timestamp between (ago(1h) .. 10m)` filtro de tempo para um intervalo `ArchivedProducts | where Timestamp > ago(1h)` fechado em sua consulta (por exemplo, a consulta - - deve ter um desempenho melhor do que este (intervalo aberto) um - ). 
    > * Todos os [formatos de ingestão suportados](ingest-data-overview.md#supported-data-formats) podem ser consultados usando tabelas externas.

1. A tabela externa é visível no painel esquerdo da UI web

    ![tabela externa na web UI](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>Crie uma tabela externa com formato json

Você pode criar uma tabela externa com formato json. Para obter mais informações, consulte [comandos de tabela externa](/azure/kusto/management/externaltables)

1. Use `.create external table` o comando para criar uma tabela chamada *ExternalTableJson*:

    ```kusto
    .create external table ExternalTableJson (rownumber:int, rowguid:guid) 
    kind=blob
    dataformat=json
    ( 
       h@'http://storageaccount.blob.core.windows.net/container1;secretKey'
    )
    with 
    (
       docstring = "Docs",
       folder = "ExternalTables",
       namePrefix="Prefix"
    ) 
    ```
 
1. O formato Json requer uma segunda etapa de criação de mapeamento para colunas, conforme mostrado abaixo. Na consulta a seguir, crie um mapeamento json específico chamado *mappingName*:

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>Permissões de mesa externas
 
* O usuário do banco de dados pode criar uma tabela externa. O criador da tabela torna-se automaticamente o administrador da tabela.
* O cluster, o banco de dados ou o administrador de tabela saem de uma tabela existente.
* Qualquer usuário ou leitor de banco de dados pode consultar uma tabela externa.
 
## <a name="query-an-external-table"></a>Consultar uma tabela externa
 
Para consultar uma tabela externa, use a `external_table()` função e forneça o nome da tabela como argumento de função. O resto da consulta é o idioma de consulta kusto padrão.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> O Intellisense não é suportado atualmente em consultas de tabela externas.

### <a name="query-an-external-table-with-json-format"></a>Consulta uma tabela externa com formato json

Para consultar uma tabela externa com formato `external_table()` json, use a função e forneça o nome da tabela e o nome de mapeamento como argumentos de função. Na consulta abaixo, se *mappingName* não for especificado, um mapeamento que você criou anteriormente será usado.

```kusto
external_table(‘ExternalTableJson’, ‘mappingName’)
```

## <a name="query-external-and-ingested-data-together"></a>Consulta dados externos e ingeridos juntos

Você pode consultar tabelas externas e tabelas de dados ingeridas na mesma consulta. Você [`join`](/azure/kusto/query/joinoperator) [`union`](/azure/kusto/query/unionoperator) ou a tabela externa com dados adicionais do Azure Data Explorer, servidores SQL ou outras fontes. Use [`let( ) statement`](/azure/kusto/query/letstatement) a para atribuir um nome taquigrafia a uma referência de tabela externa.

No exemplo abaixo, *Products* é uma tabela de dados ingerida e *ArchivedProducts* é uma tabela externa que contém dados no Azure Data Lake Storage Gen2:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>Consultar *taxirides* tabela externa no cluster de ajuda

O conjunto de dados de amostra *TaxiRides* contém dados de táxi da [Nyc Taxi and Limousine Commission](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page).

### <a name="create-external-table-taxirides"></a>Criar tabela externa *TaxiRides* 

> [!NOTE]
> Esta seção mostra a consulta usada para criar a tabela externa *TaxiRides* no cluster *de ajuda.* Uma vez que esta tabela já foi criada, você pode pular esta seção e executar [a consulta de dados de tabela externa *TaxiRides* ](#query-taxirides-external-table-data). 

1. A consulta a seguir foi usada para criar a tabela externa *TaxiRides* no cluster de ajuda. 

    ```kusto
    .create external table TaxiRides
    (
    trip_id: long,
    vendor_id: string, 
    pickup_datetime: datetime,
    dropoff_datetime: datetime,
    store_and_fwd_flag: string,
    rate_code_id: int,
    pickup_longitude: real,
    pickup_latitude: real,
    dropoff_longitude: real,
    dropoff_latitude: real,
    passenger_count: int,
    trip_distance: real,
    fare_amount: real,
    extra: real,
    mta_tax: real,
    tip_amount: real,
    tolls_amount: real,
    ehail_fee: real,
    improvement_surcharge: real,
    total_amount: real,
    payment_type: string,
    trip_type: int,
    pickup: string,
    dropoff: string,
    cab_type: string,
    precipitation: int,
    snow_depth: int,
    snowfall: int,
    max_temperature: int,
    min_temperature: int,
    average_wind_speed: int,
    pickup_nyct2010_gid: int,
    pickup_ctlabel: string,
    pickup_borocode: int,
    pickup_boroname: string,
    pickup_ct2010: string,
    pickup_boroct2010: string,
    pickup_cdeligibil: string,
    pickup_ntacode: string,
    pickup_ntaname: string,
    pickup_puma: string,
    dropoff_nyct2010_gid: int,
    dropoff_ctlabel: string,
    dropoff_borocode: int,
    dropoff_boroname: string,
    dropoff_ct2010: string,
    dropoff_boroct2010: string,
    dropoff_cdeligibil: string,
    dropoff_ntacode: string,
    dropoff_ntaname: string,
    dropoff_puma: string
    )
    kind=blob 
    partition by bin(pickup_datetime, 1d)
    dataformat=csv
    ( 
        h@'http://storageaccount.blob.core.windows.net/container1;secretKey''
    )
    ```
1. A tabela resultante foi criada no cluster *de ajuda:*

    ![Tabela externa TaxiRides](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>Query *TaxiRides* dados de tabela externa 

Faça login [https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples) para consultar a tabela externa *TaxiRides.* 

#### <a name="query-taxirides-external-table-without-partitioning"></a>Consulta *TaxiRides* tabela externa sem particionamento

[Execute esta consulta](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) na tabela externa *TaxiRides* para retratar passeios para cada dia da semana, em todo o conjunto de dados. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

Esta consulta mostra o dia mais movimentado da semana. Como os dados não são particionados, essa consulta pode levar muito tempo para retornar os resultados (até vários minutos).

![renderizar consulta não particionada](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>Query TaxiRides tabela externa com particionamento 

[Execute esta consulta](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) na tabela externa *TaxiRides* mostrando tipos de táxi (amarelo ou verde) usados em janeiro de 2017. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

Esta consulta usa particionamento, o que otimiza o tempo e o desempenho da consulta. A consulta filtra uma coluna particionada (pickup_datetime) e retorna os resultados em poucos segundos.

![renderizar consulta particionada](media/data-lake-query-data/taxirides-with-partition.png)
  
Você pode escrever consultas adicionais para executar na tabela externa *TaxiRides* e aprender mais sobre os dados. 

## <a name="optimize-your-query-performance"></a>Otimize o desempenho da consulta

Otimize o desempenho da consulta no lago usando as seguintes práticas recomendadas para consultar dados externos. 
 
### <a name="data-format"></a>Formato de dados
 
Use um formato de colunar para consultas analíticas desde:
* Somente as colunas relevantes para uma consulta podem ser lidas. 
* As técnicas de codificação de colunas podem reduzir significativamente o tamanho dos dados.  
O Azure Data Explorer suporta formatos columnares Parquet e ORC. O formato parquet é sugerido devido à implementação otimizada. 
 
### <a name="azure-region"></a>Região do Azure 
 
Verifique se os dados externos residem na mesma região do Azure que o cluster Azure Data Explorer. Isso reduz o custo e o tempo de busca de dados.
 
### <a name="file-size"></a>Tamanho de arquivo
 
O tamanho ideal do arquivo é de centenas de Mb (até 1 Gb) por arquivo. Evite muitos arquivos pequenos que requerem sobrecarga desnecessária, como processo de enumeração de arquivo mais lento e uso limitado do formato columnar. Observe que o número de arquivos deve ser maior do que o número de núcleos de CPU no cluster Do Azure Data Explorer. 
 
### <a name="compression"></a>Compactação
 
Use compactação para reduzir a quantidade de dados que estão sendo obtidos no armazenamento remoto. Para o formato Parquet, use o mecanismo interno de compressão parquet que comprime os grupos de colunas separadamente, permitindo assim que você os leia separadamente. Para validar o uso do mecanismo de compressão,<filename>verifique se os arquivos são<filename>nomeados da seguinte forma:<filename>" .gz.parquet" ou " .snappy.parquet" em oposição a " .parquet.gz"). 
 
### <a name="partitioning"></a>Particionamento
 
Organize seus dados usando partições de "pasta" que permitem que a consulta pule caminhos irrelevantes. Ao planejar particionamento, considere o tamanho do arquivo e filtros comuns em suas consultas, como carimbo de data e hora ou ID do inquilino.
 
### <a name="vm-size"></a>Tamanho da VM
 
Selecione As SKUs VM com mais núcleos e maior throughput de rede (a memória é menos importante). Para obter mais informações, [consulte Selecione o SKU VM correto para o cluster Azure Data Explorer](manage-cluster-choose-sku.md).

## <a name="next-steps"></a>Próximas etapas

Consulta seus dados no Lago de Dados do Azure usando o Azure Data Explorer. Aprenda a [escrever consultas](write-queries.md) e obtenha insights adicionais de seus dados.
