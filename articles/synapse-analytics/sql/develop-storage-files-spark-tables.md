---
title: Sincronizar o Apache Spark para definições de tabela externa no pool de SQL sem servidor (versão prévia)
description: Visão geral de como consultar tabelas do Spark usando o pool de SQL sem servidor (versão prévia)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: ea4e7cd009be8a78faa0dcfab44371a350b6a200
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93315832"
---
# <a name="synchronize-apache-spark-for-azure-synapse-external-table-definitions-in-serverless-sql-pool-preview"></a>Sincronizar o Apache Spark para definições de tabela externa do Azure Synapse no pool de SQL sem servidor (versão prévia)

O pool de SQL sem servidor (versão prévia) pode sincronizar automaticamente metadados do Apache Spark. Um banco de dados do pool de SQL sem servidor será criado para cada banco de dados existente nos pools do Apache Spark sem servidor (versão prévia). 

Para cada tabela externa do Spark baseada em Parquet e localizada no Armazenamento do Azure, uma tabela externa é criada no banco de dados do pool de SQL sem servidor. Assim, você pode desligar os seus pools do Spark e ainda consultar tabelas externas do Spark no pool de SQL sem servidor.

Quando uma tabela é particionada no Spark, os arquivos do armazenamento são organizados por pastas. O pool de SQL sem servidor usará metadados de partição e apenas terá como destino pastas e arquivos relevantes para a consulta.

A sincronização de metadados é configurada automaticamente para cada Pool do Apache Spark sem servidor provisionado no workspace do Azure Synapse. Você pode começar a consultar as tabelas externas do Spark instantaneamente.

Cada tabela externa de parquet do Spark localizada no Armazenamento do Azure é representada por uma tabela externa em um esquema dbo que corresponde a um banco de dados do pool de SQL sem servidor. 

Para consultas de tabela externa do Spark, execute uma consulta que tenha como destino uma [tabela_do_Spark] externa. Antes de executar o exemplo a seguir, verifique se você tem o [acesso correto à conta de armazenamento](develop-storage-files-storage-access-control.md) na qual os arquivos estão localizados.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

> [!NOTE]
> Adicionar, remover ou alterar os comandos de tabela externa do Spark em uma coluna não refletirá na tabela externa no pool de SQL sem servidor.

## <a name="apache-spark-data-types-to-sql-data-types-mapping"></a>Mapeamento de tipos de dados do Apache Spark para tipos de dados SQL

| Tipo de dados do Spark | Tipo de dados SQL               |
| --------------- | --------------------------- |
| ByteType        | SMALLINT                    |
| Tipo curto      | SMALLINT                    |
| IntegerType     | INT                         |
| LongType        | BIGINT                      |
| FloatType       | real                        |
| DoubleType      | FLOAT                       |
| DecimalType     | decimal                     |
| TimestampType   | datetime2                   |
| DateType        | date                        |
| StringType      | varchar(max)\*               |
| BinaryType      | varbinary                   |
| BooleanType     | bit                         |
| ArrayType       | varchar(max)\* (no JSON)\** |
| MapType         | varchar(max)\* (no JSON)\** |
| StructType      | varchar(max)\* (no JSON)\** |

\* A ordenação usada é Latin1_General_100_BIN2_UTF8.

\** ArrayType, MapType e StructType são representados como JSONs.



## <a name="next-steps"></a>Próximas etapas

Avance para o artigo [Controle de acesso de armazenamento](develop-storage-files-storage-access-control.md) para saber mais sobre o controle de acesso de armazenamento.
