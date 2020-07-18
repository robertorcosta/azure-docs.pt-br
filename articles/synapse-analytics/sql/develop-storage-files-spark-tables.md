---
title: Sincronizar definições de tabela externa do Apache Spark para o Azure Synapse no SQL sob demanda (versão prévia)
description: Visão geral de como consultar tabelas do Spark usando o SQL sob demanda (versão prévia)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: db6b2d95bf8f38495296885d14260b9900af1d51
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86247039"
---
# <a name="synchronize-apache-spark-for-azure-synapse-external-table-definitions-in-sql-on-demand-preview"></a>Sincronizar definições de tabela externa do Apache Spark para o Azure Synapse no SQL sob demanda (versão prévia)

O SQL sob demanda (versão prévia) pode sincronizar automaticamente metadados de pools do Apache Spark para o Azure Synapse. Um banco de dados do SQL sob demanda será criado para cada banco de dados existente nos pools do Spark (versão prévia). 

Para cada tabela externa do Spark baseada em Parquet e localizada no Armazenamento do Azure, uma tabela externa é criada no banco de dados do SQL sob demanda. Assim, você pode desligar seus pools do Spark e ainda consultar tabelas externas do Spark no SQL sob demanda.

Quando uma tabela é particionada no Spark, os arquivos do armazenamento são organizados por pastas. O SQL sob demanda utilizará metadados de partição e apenas terá como destino pastas e arquivos relevantes para a consulta.

A sincronização de metadados é configurada automaticamente para cada Pool do Spark provisionado no workspace do Azure Synapse. Você pode começar a consultar as tabelas externas do Spark instantaneamente.

Cada tabela externa de parquet do Spark localizada no Armazenamento do Azure é representada por uma tabela externa em um esquema dbo que corresponde a um banco de dados do SQL sob demanda. 

Para consultas de tabela externa do Spark, execute uma consulta que tenha como destino uma [tabela_do_Spark] externa. Antes de executar o exemplo abaixo, verifique se você tem o [acesso correto à conta de armazenamento](develop-storage-files-storage-access-control.md) em que os arquivos estão localizados.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

> [!NOTE]
> Adicionar, remover ou alterar a coluna de tabela externa do Spark não refletirá na tabela externa no SQL sob demanda.

## <a name="apache-spark-data-types-to-sql-data-types-mapping"></a>Mapeamento de tipos de dados do Apache Spark para tipos de dados SQL

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
