---
title: Analisar o esquema com matrizes e estruturas aninhadas
description: Como analisar matrizes e estruturas aninhadas com Apache Spark e SQL
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 06/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: ad6761466cc958235557609e929e641a0311ee43
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "86999149"
---
# <a name="analyze-complex-data-types-in-azure-synapse-analytics"></a>Analisar tipos de dados complexos no Azure Synapse Analytics

Este artigo é relevante para arquivos e contêineres parquet no [link do Synapse para Azure Cosmos DB](.\synapse-link\how-to-connect-synapse-link-cosmos-db.md). Ele explica como os usuários podem usar o Spark ou o SQL para ler ou transformar dados com esquemas complexos, como matrizes ou estruturas aninhadas. O exemplo a seguir é concluído com um único documento, mas pode ser facilmente dimensionado para bilhões de documentos com Spark ou SQL. O código incluído neste artigo usa PySpark (Python).

## <a name="use-case"></a>Caso de uso

Os tipos de dados complexos são cada vez mais comuns e representam um desafio para os engenheiros de dados, pois analisar o esquema aninhado e as matrizes tendem a incluir consultas SQL demoradas e complexas. Além disso, pode ser difícil renomear ou converter o tipo de dados de colunas aninhadas. Além disso, ocorrem problemas de desempenho ao trabalhar com objetos profundamente aninhados.

Os engenheiros de dados precisam entender como processar com eficiência tipos de dados complexos e torná-los facilmente acessíveis a todos.

No exemplo a seguir, o Spark Synapse é usado para ler e transformar objetos em uma estrutura plana por meio de quadros de dados. O Synapse SQL Serverless é usado para consultar esses objetos diretamente e retornar esses resultados como uma tabela normal.

## <a name="what-are-arrays-and-nested-structures"></a>O que são matrizes e estruturas aninhadas?

O objeto a seguir vem do [app Insight](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Nesse objeto, há estruturas aninhadas e matrizes que contêm estruturas aninhadas.

```json
{
    "id": "66532691-ab20-11ea-8b1d-936b3ec64e54",
    "context": {
        "data": {
            "eventTime": "2020-06-10T13:43:34.553Z",
            "samplingRate": "100.0",
            "isSynthetic": "false"
        },
        "session": {
            "isFirst": "false",
            "id": "38619c14-7a23-4687-8268-95862c5326b1"
        },
        "custom": {
            "dimensions": [
                {
                    "customerInfo": {
                        "ProfileType": "ExpertUser",
                        "RoomName": "",
                        "CustomerName": "diamond",
                        "UserName": "XXXX@yahoo.com"
                    }
                },
                {
                    "customerInfo": {
                        "ProfileType": "Novice",
                        "RoomName": "",
                        "CustomerName": "topaz",
                        "UserName": "XXXX@outlook.com"
                    }
                }
            ]
        }
    }
}
```

### <a name="schema-example-of-arrays-and-nested-structures"></a>Exemplo de esquema de matrizes e estruturas aninhadas
Ao imprimir o esquema do quadro de dados do objeto (chamado de **DF**) com o comando `df.printschema` , vemos a seguinte representação:

* A cor amarela representa a estrutura aninhada
* A cor verde representa uma matriz com dois elementos

[![Origem do esquema](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

**_rid**, **_ts**e **_etag** foram adicionados ao sistema, pois o documento foi ingerido em Azure Cosmos DB armazenamento transacional.

O quadro de dados acima conta somente para 5 colunas e 1 linha. Após a transformação, o quadro de dados organizado terá 13 colunas e 2 linhas em um formato tabular.

## <a name="flatten-nested-structures-and-explode-arrays-with-apache-spark"></a>Mesclar estruturas aninhadas e detalhar matrizes com Apache Spark

Com o Synapse Spark, é fácil transformar estruturas aninhadas em colunas e elementos de matriz em várias linhas. As etapas a seguir podem ser usadas para implementação.

[![Etapas de transformações do Spark](./media/how-to-complex-schema/spark-transform-steps.png)](./media/how-to-complex-schema/spark-transform-steps.png#lightbox)

**Etapa 1**: definimos uma função para mesclar o esquema aninhado. Essa função pode ser usada sem alteração. Crie uma célula em um [bloco de anotações PySpark](quickstart-apache-spark-notebook.md) com a seguinte função:

```python
from pyspark.sql.functions import col

def flatten_df(nested_df):
    stack = [((), nested_df)]
    columns = []

    while len(stack) > 0:
        parents, df = stack.pop()

        flat_cols = [
            col(".".join(parents + (c[0],))).alias("_".join(parents + (c[0],)))
            for c in df.dtypes
            if c[1][:6] != "struct"
        ]

        nested_cols = [
            c[0]
            for c in df.dtypes
            if c[1][:6] == "struct"
        ]

        columns.extend(flat_cols)

        for nested_col in nested_cols:
            projected_df = df.select(nested_col + ".*")
            stack.append((parents + (nested_col,), projected_df))

    return nested_df.select(columns)
```

**Etapa 2**: Use a função para mesclar o esquema aninhado do data frame (**DF**) em um novo quadro de dados `df_flat` :

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

A função de exibição deve retornar 10 colunas e 1 linha. A matriz e seus elementos aninhados ainda estão lá.

**Etapa 3**: transformar a matriz `context_custom_dimensions` no quadro de dados `df_flat` em um novo dataframe `df_flat_explode` . No código a seguir, também definimos qual coluna selecionar:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

A função de exibição deve retornar 10 colunas e 2 linhas. A próxima etapa é mesclar esquemas aninhados com a função definida na etapa 1.

**Etapa 4**: Use a função para mesclar o esquema aninhado do quadro de dados `df_flat_explode` em um novo quadro de dados `df_flat_explode_flat` :
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

A função de exibição deve mostrar 13 colunas e 2 linhas.

A função `printSchema` do quadro de dados `df_flat_explode_flat` retorna o seguinte resultado:

[![Final do esquema](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly-with-sql-serverless"></a>Ler matrizes e estruturas aninhadas diretamente com o SQL Server

É possível consultar e criar exibições e tabelas em tais objetos com o SQL Server.

Primeiro, dependendo de como os dados foram armazenados, os usuários devem usar a taxonomia a seguir. Tudo o que é mostrado em letras maiúsculas é específico ao seu caso de uso:

| BULK              | FORMAT |
| -------------------- | --- |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |' Parquet ' (ADLSg2)|
| N'endpoint = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; Account = ACCOUNTNAME; banco de dados = DatabaseName; coleção = CollectionName; região = REGIONTOQUERY, segredo = ' YOURSECRET ' |' CosmosDB ' (link do Synapse)|


> [!NOTE]
> O SQL Serverless dará suporte ao serviço vinculado para Synapse link para passagem do Azure Cosmos e AAD. No momento, a funcionalidade está sob a visualização restrita para o link Synapse.

Substitua cada campo da seguinte maneira:
* ' Sua massa acima ' = a cadeia de conexão da fonte de dados à qual você se conecta
* ' Seu tipo acima ' = o formato usado para se conectar à origem

```sql
select *
FROM
openrowset(
BULK 'YOUR BULK ABOVE',
            FORMAT='YOUR TYPE ABOVE'
)
with (id varchar(50),
        contextdataeventTime varchar(50) '$.context.data.eventTime',
        contextdatasamplingRate varchar(50) '$.context.data.samplingRate',
        contextdataisSynthetic varchar(50) '$.context.data.isSynthetic',
        contextsessionisFirst varchar(50) '$.context.session.isFirst',
        contextsessionid varchar(50) '$.context.session.id',
        contextcustomdimensions varchar(max) '$.context.custom.dimensions'
) as q 
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType',
            RoomName varchar(50) '$.customerInfo.RoomName',
            CustomerName varchar(50) '$.customerInfo.CustomerName',
            UserName varchar(50) '$.customerInfo.UserName'
    )
```

Há dois tipos diferentes de operações:

O primeiro tipo de operação é indicado na linha de código a seguir, que define a coluna chamada `contextdataeventTime` que se refere ao elemento aninhado: Context. Data. EventTime 
```sql
contextdataeventTime varchar(50) '$.context.data.eventTime'
```

Essa linha definirá a coluna chamada contextdataeventTime que se refere ao elemento Nest: contexto>dados>EventTime

O segundo tipo de operação usa `cross apply` para criar novas linhas para cada elemento na matriz e, em seguida, define cada objeto aninhado semelhante ao primeiro ponto de marcador: 
```sql
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
```

Se a matriz tivesse cinco elementos com 4 estruturas aninhadas, o SQL Server sem retorno retornaria 5 linhas e 4 colunas. O SQL Server sem fazer consultas no local, mapear a matriz em 2 linhas e exibir todas as estruturas aninhadas em colunas.

## <a name="next-steps"></a>Próximas etapas

* [Saiba como consultar o link do Synapse para Azure Cosmos DB com o Spark](./synapse-link/how-to-query-analytical-store-spark.md)
* [Tipos aninhados parquet de consulta](./sql/query-parquet-nested-types.md) 