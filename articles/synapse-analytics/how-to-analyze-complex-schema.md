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
ms.openlocfilehash: b02c3627cea5e441739c77d1882505c6b82489bc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84907865"
---
# <a name="analyze-complex-data-types-in-synapse"></a>Analisar tipos de dados complexos no Synapse

Este artigo é relevante para arquivos e contêineres parquet no **link Synapse do Azure para Azure Cosmos DB**. Ele explica como os usuários podem usar o Spark ou o SQL para ler ou transformar dados com esquema complexo, como matrizes ou estruturas aninhadas. O exemplo a seguir é feito com um único documento, mas pode ser facilmente dimensionado para bilhões de documentos com Spark ou SQL. O código a seguir usa PySpark (Python).

## <a name="use-case"></a>Caso de uso

Com os tipos de dados modernos, os tipos de dados complexos geralmente são comuns para lidar e representar um desafio para engenheiros de dados. A análise de esquemas aninhados e matrizes apresenta alguns desafios:
* Complexo para escrever consultas SQL
* Difícil de renomear/converter DataType de colunas aninhadas
* Problemas de desempenho de acesso com objetos profundamente aninhados

Os engenheiros de dados precisam entender como processar com eficiência esses tipos de dados e torná-los facilmente acessíveis por todos.

No exemplo a seguir, o Spark Synapse será usado para ler e transformar objetos por meio de quadros de dados em uma estrutura plana. O Synapse SQL Serverless é usado para consultar diretamente esses objetos e retornar esses resultados como uma tabela normal.

## <a name="what-are-arrays-and-nested-structures"></a>O que são matrizes e estruturas aninhadas?

O objeto a seguir vem do App Insight. Nesse objeto, há estruturas aninhadas, mas também matrizes que contêm estruturas aninhadas.

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
Ao imprimir o esquema do quadro de dados desse objeto (chamado de **DF**) com o comando **DF. MySchema**, vemos a seguinte representação:

* a cor amarela representa a estrutura aninhada
* a cor verde representa uma matriz com dois elementos

[![Origem do esquema](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

_rid, _ts e _etag foram adicionados ao sistema, pois o documento foi ingerido em Azure Cosmos DB armazenamento transacional.

O quadro de dados acima conta somente para 5 colunas e 1 linha. Após a transformação, o quadro de dados organizado terá 13 colunas e 2 linhas em um formato tabular.

## <a name="flatten-nested-structures-and-explode-arrays-with-apache-spark"></a>Mesclar estruturas aninhadas e detalhar matrizes com Apache Spark

Com o Synapse Spark, é fácil transformar estruturas aninhadas em colunas e elementos de matriz em várias linhas. As etapas a seguir podem ser usadas por todos para sua própria implementação.

[![Etapas de transformações do Spark](./media/how-to-complex-schema/spark-transfo-steps.png)](./media/how-to-complex-schema/spark-transfo-steps.png#lightbox)

**Etapa 1**: definimos uma função para mesclar o esquema aninhado. Essa função pode ser usada sem alteração. Crie uma célula em um bloco de anotações Pyspark com essa função:

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

**Etapa 2**: Use a função para mesclar o esquema aninhado do data frame **DF** em um novo quadro de dados **df_flat**:

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

A função de exibição deve retornar 10 colunas e 1 linha. A matriz e seus elementos aninhados ainda estão lá.

**Etapa 3**: agora transformamos a matriz **context_custom_dimensions** no quadro de dados **df_flat** em um novo dataframe **df_flat_explode**. No código abaixo, também definimos a coluna que selecionamos:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

A função de exibição deve retornar o seguinte resultado: 10 colunas e 2 linhas. A próxima etapa é mesclar esquemas aninhados com a função definida na etapa 1.

**Etapa 4**: Use a função para mesclar o esquema aninhado do quadro de dados **df_flat_explode** em um novo quadro de dados **df_flat_explode_flat**:
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

A função de exibição deve mostrar 13 colunas e 2 linhas:

A função dischema do quadro de dados df_flat_explode_flat retorna o seguinte resultado:

[![Final do esquema](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly-with-sql-serverless"></a>Ler matrizes e estruturas aninhadas diretamente com o SQL Server

Consultar, criar exibições e tabelas em tais objetos é possível com o SQL Server.

Em primeiro lugar, dependendo de como os dados foram armazenados, os usuários devem usar a taxonomia a seguir. Todas as letras maiúsculas são específicas ao seu caso de uso:

| BULK              | FORMAT |
| -------------------- | --- |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |' Parquet ' (ADLSg2)|
| N'endpoint = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; Account = ACCOUNTNAME; banco de dados = DatabaseName; coleção = CollectionName; região = REGIONTOQUERY, segredo = ' YOURSECRET ' |' CosmosDB ' (link do Synapse)|



O **SQL Serverless** dará suporte ao serviço vinculado para o link Synapse do Azure para passagem Azure Cosmos DB e AAD. No momento, a funcionalidade está sob a visualização restrita para o link Synapse.

Substituir abaixo:
* ' Seu em massa acima ' pela cadeia de conexão da fonte de dados à qual você se conecta
* ' Seu tipo acima ' pelo formato usado para se conectar à origem

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

Há dois tipos diferentes de operações feitas:
* A linha de código abaixo definirá a coluna chamada contextdataeventTime, que se refere ao elemento aninhado: Context. Data. EventTime
```sql
contextdataeventTime varchar(50) '$.context.data.eventTime'
```

Essa linha definirá a coluna chamada contextdataeventTime que se refere ao elemento Nest: contexto>dados>EventTime

* a **aplicação cruzada** é usada para criar novas linhas para cada elemento na matriz e, em seguida, com define cada objeto aninhado semelhante ao primeiro ponto de marcador: 
```sql
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
```

Se a matriz tivesse cinco elementos com 4 estruturas aninhadas, o SQL Server sem retorno retornaria 5 linhas e 4 colunas.

O SQL Server sem fazer consultas no local, mapear a matriz em 2 linhas e exibir todas as estruturas aninhadas em colunas.

## <a name="next-steps"></a>Próximas etapas

* [Saiba como consultar o link do Azure Synapse para obter Azure Cosmos DB com o Spark](./synapse-link/how-to-query-analytical-store-spark.md)
* [Tipos aninhados parquet de consulta](./sql/query-parquet-nested-types.md) 