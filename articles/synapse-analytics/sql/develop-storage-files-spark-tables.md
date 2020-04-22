---
title: Consultar tabelas do Spark usando o SQL sob demanda (versão prévia)
description: Visão geral de como consultar tabelas do Spark usando o SQL sob demanda (versão prévia)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 41e31a322a3d771557474fdf5c318960822bcfe1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420070"
---
# <a name="query-spark-tables-with-azure-synapse-analytics-using-sql-on-demand-preview"></a>Consultar tabelas do Spark com o Azure Synapse Analytics usando o SQL sob demanda (versão prévia)

O SQL sob demanda (versão prévia) pode sincronizar automaticamente os metadados de pools do Spark no workspace do Synapse (versão prévia). Um banco de dados do SQL sob demanda será criado para cada banco de dados existente nos pools do Spark (versão prévia). Para cada tabela do Spark baseada em Parquet ou CSV, uma tabela externa é criada no banco de dados do SQL sob demanda. Assim, você pode desligar seus pools do Spark e ainda consultar tabelas do Spark no SQL sob demanda.

Quando uma tabela é particionada no Spark, os arquivos do armazenamento são organizados por pastas. O SQL sob demanda utilizará metadados de partição e apenas terá como destino pastas e arquivos relevantes para a consulta.

A sincronização de metadados é configurada automaticamente para cada Pool do Spark provisionado no workspace do Azure Synapse. Comece a consultar as tabelas do Spark instantaneamente.

Cada tabela do Spark é representada por uma tabela externa em um esquema dbo que corresponde a um banco de dados do SQL sob demanda. Para consultas de tabela do Spark, execute uma consulta que tenha como destino uma [tabela_do_Spark] externa. Antes de executar o exemplo abaixo, verifique se você tem o [acesso correto à conta de armazenamento](develop-storage-files-storage-access-control.md) em que os arquivos estão localizados.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

## <a name="spark-data-types-to-sql-data-types-mapping"></a>Mapeamento de tipos de dados do Spark para tipos de dados SQL

| Tipo de dados do Spark | Tipo de dados SQL               |
| --------------- | --------------------------- |
| ByteType        | SMALLINT                    |
| ShortType       | SMALLINT                    |
| IntegerType     | INT                         |
| LongType        | BIGINT                      |
| FloatType       | real                        |
| DoubleType      | FLOAT                       |
| DecimalType     | decimal                     |
| TimestampType   | datetime2                   |
| DateType        | date                        |
| StringType      | varchar(max)*               |
| BinaryType      | varbinary                   |
| BooleanType     | bit                         |
| ArrayType       | varchar(max)* (into JSON)** |
| MapType         | varchar(max)* (into JSON)** |
| StructType      | varchar(max)* (into JSON)** |

\* A ordenação usada é Latin1_General_100_BIN2_UTF8.

** ArrayType, MapType e StructType são representados como JSONs.



## <a name="next-steps"></a>Próximas etapas

Avance para o artigo [Controle de acesso de armazenamento](develop-storage-files-storage-access-control.md) para saber mais sobre o controle de acesso de armazenamento.
