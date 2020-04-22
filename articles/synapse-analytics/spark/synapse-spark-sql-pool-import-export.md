---
title: Importar e exportar dados entre pools do Spark (versão prévia) e pools de SQL
description: Este artigo fornece informações sobre como usar o conector personalizado para mover dados bidirecionalmente entre pools de SQL e pools do Spark (versão prévia).
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: f92c05476c9e85690fdeacade5463a43d0a4af42
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420420"
---
# <a name="introduction"></a>Introdução

O Conector da Análise de SQL do Spark foi projetado para transferir dados de maneira eficiente entre o Pool do Spark (versão prévia) e os pools de SQL no Azure Synapse. O Conector da Análise de SQL do Spark funciona somente em pools de SQL, não com o SQL sob demanda.

## <a name="design"></a>Design

A transferência de dados entre pools do Spark e pools de SQL pode ser feita usando o JDBC. No entanto, considerando dois sistemas distribuídos, como os pools do Spark e de SQL, o JDBC tende a ser um gargalo com a transferência de dados seriais.

O Conector da Análise de SQL aos pools do Spark é uma implementação de fonte de dados para o Apache Spark. Ele usa o Azure Data Lake Storage Gen2 e o PolyBase nos pools de SQL para transferir dados com eficiência entre o cluster do Spark e a instância de Análise de SQL.

![Arquitetura do conector](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Autenticação no Azure Synapse Analytics

A autenticação entre sistemas é simplificada no Azure Synapse Analytics. Há um serviço de token que se conecta ao Azure Active Directory a fim de obter tokens de segurança para uso ao acessar a conta de armazenamento ou o servidor de data warehouse. Por esse motivo, não há necessidade de criar nem de especificar credenciais na API do conector, desde que Autenticação do AAD esteja configurada na conta de armazenamento e no servidor de data warehouse. Caso contrário, a Autenticação SQL poderá ser especificada. Encontre mais detalhes na seção [Uso](#usage).

## <a name="constraints"></a>Restrições

- Esse conector funciona apenas no Scala.

## <a name="prerequisites"></a>Pré-requisitos

- Ter a função **db_exporter** no banco de dados/no pool de SQL no qual você deseja transferir os dados.

Para criar usuários, conecte-se ao banco de dados e siga estes exemplos:

```Sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Para atribuir uma função:

```Sql
EXEC sp_addrolemember 'db_exporter', 'Mary';
```

## <a name="usage"></a>Uso

As instruções de importação não precisam ser fornecidas; elas são pré-importadas para a experiência de notebook.

### <a name="transferring-data-to-or-from-a-sql-pool-in-the-logical-server-dw-instance-attached-with-the-workspace"></a>Como transferir dados em um pool de SQL no servidor lógico (instância de DW) anexado ao workspace

> [!NOTE]
> **Importações não necessárias na experiência do notebook**

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>API de leitura

```Scala
val df = spark.read.sqlanalytics("[DBName].[Schema].[TableName]")
```

A API acima funcionará em tabelas internas (gerenciadas) e externas no pool de SQL.

#### <a name="write-api"></a>API de gravação

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

em que TableType pode ser Constants.INTERNAL ou Constants.EXTERNAL

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.INTERNAL)
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.EXTERNAL)
```

A autenticação no Armazenamento e no SQL Server foi concluída

### <a name="if-you-are-transferring-data-to-or-from-a-sql-pool-or-database-in-a-logical-server-outside-the-workspace"></a>Se você estiver transferindo dados em um banco de dados ou um pool de SQL em um servidor lógico fora do workspace

> [!NOTE]
> Importações não necessárias na experiência do notebook

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>API de leitura

```Scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>API de gravação

```Scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-sql-auth-instead-of-aad"></a>Como usar a Autenticação SQL em vez do AAD

#### <a name="read-api"></a>API de leitura

Atualmente, o conector não dá suporte à autenticação baseada em token em um pool de SQL que esteja fora do workspace. Você precisará usar a Autenticação SQL.

```Scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>API de gravação

```Scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-the-pyspark-connector"></a>Como usar o conector do PySpark

> [!NOTE]
> Este exemplo é fornecido apenas com a experiência de notebook mantida em mente.

Suponha que você tenha um dataframe "pyspark_df" que deseje gravar no DW.

Criar uma tabela temporária usando o dataframe no PySpark

```Python
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

Executar uma célula do Scala no notebook do PySpark usando magics

```Scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

pysparkdftemptable.write.sqlanalytics("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```
Da mesma forma, no cenário de leitura, leia os dados usando o Scala, grave-os em uma tabela temporária e use o SQL do Spark no PySpark para consultar a tabela temporária em um dataframe.

## <a name="next-steps"></a>Próximas etapas

- [Criar um pool de SQL]([Create a new Apache Spark pool for an Azure Synapse Analytics workspace](../../synapse-analytics/quickstart-create-apache-spark-pool.md))
- [Criar um Pool do Apache Spark para um workspace do Azure Synapse Analytics](../../synapse-analytics/quickstart-create-apache-spark-pool.md) 