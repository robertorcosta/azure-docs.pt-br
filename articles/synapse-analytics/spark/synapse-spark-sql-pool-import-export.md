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
ms.openlocfilehash: 1a2b9c739f3583fb5d842bd9d3834252d542cb7d
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83739270"
---
# <a name="introduction"></a>Introdução

O conector de Apache Spark para SQL do Synapse do Azure Synapse foi projetado para transferir dados de maneira eficiente entre o pools do Spark (versão prévia) e pools de SQL no Azure Synapse. O conector de Apache Spark para SQL do Synapse do Azure Synapse funciona somente em pools SQL, e não funciona com o SQL sob demanda.

## <a name="design"></a>Design

A transferência de dados entre pools do Spark e pools de SQL pode ser feita usando o JDBC. No entanto, considerando dois sistemas distribuídos, como os pools do Spark e de SQL, o JDBC tende a ser um gargalo com a transferência de dados seriais.

O conector do pool do Apache Spark para SQL do Synapse do Azure Synapse é uma implementação de fonte de dados para o Apache Spark. Ele usa o Azure Data Lake Storage Gen2 e o PolyBase em pools de SQL para transferir dados com eficiência entre o cluster do Spark e a instância do SQL do Synapse.

![Arquitetura do conector](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Autenticação no Azure Synapse Analytics

A autenticação entre sistemas é simplificada no Azure Synapse Analytics. Há um serviço de token que se conecta ao Azure Active Directory a fim de obter tokens de segurança para uso ao acessar a conta de armazenamento ou o servidor de data warehouse. 

Por esse motivo, não há necessidade de criar nem de especificar credenciais na API do conector, desde que Autenticação do AAD esteja configurada na conta de armazenamento e no servidor de data warehouse. Caso contrário, a Autenticação SQL poderá ser especificada. Encontre mais detalhes na seção [Uso](#usage).

## <a name="constraints"></a>Restrições

- Esse conector funciona apenas no Scala.

## <a name="prerequisites"></a>Pré-requisitos

- Ter a função **db_exporter** no banco de dados/no pool de SQL no qual você deseja transferir os dados.

Para criar usuários, conecte-se ao banco de dados e siga estes exemplos:

```sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Para atribuir uma função:

```sql
EXEC sp_addrolemember 'db_exporter', 'Mary';
```

## <a name="usage"></a>Uso

As instruções de importação não são obrigatórias, elas são pré-importadas para a experiência de notebook.

### <a name="transferring-data-to-or-from-a-sql-pool-in-the-logical-server-dw-instance-attached-with-the-workspace"></a>Como transferir dados em um pool de SQL no servidor lógico (instância de DW) anexado ao workspace

> [!NOTE]
> **Importações não necessárias na experiência do notebook**

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>API de leitura

```scala
val df = spark.read.sqlanalytics("[DBName].[Schema].[TableName]")
```

A API acima funcionará em tabelas internas (gerenciadas) e externas no pool de SQL.

#### <a name="write-api"></a>API de gravação

```scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

em que TableType pode ser Constants.INTERNAL ou Constants.EXTERNAL

```scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.INTERNAL)
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.EXTERNAL)
```

A autenticação no Armazenamento e no SQL Server foi concluída

### <a name="if-you-are-transferring-data-to-or-from-a-sql-pool-or-database-in-a-logical-server-outside-the-workspace"></a>Se você estiver transferindo dados em um banco de dados ou um pool de SQL em um servidor lógico fora do workspace

> [!NOTE]
> Importações não necessárias na experiência do notebook

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>API de leitura

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>API de gravação

```scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-sql-auth-instead-of-aad"></a>Como usar a Autenticação SQL em vez do AAD

#### <a name="read-api"></a>API de leitura

Atualmente, o conector não dá suporte à autenticação baseada em token em um pool de SQL que esteja fora do workspace. Você precisará usar a Autenticação SQL.

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>API de gravação

```scala
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

Crie uma tabela temporária usando o dataframe no PySpark:

```py
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

Execute uma célula do Scala no notebook do PySpark usando magics:

```scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

pysparkdftemptable.write.sqlanalytics("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```

Da mesma forma, no cenário de leitura, leia os dados usando o Scala, grave-os em uma tabela temporária e use o SQL do Spark no PySpark para consultar a tabela temporária em um dataframe.

## <a name="allowing-other-users-to-use-the-dw-connector-in-your-workspace"></a>Permitindo que outras pessoas usem o conector do DW em seu workspace

Você precisa ser Proprietário de Dados do Blob de Armazenamento na conta de armazenamento do ADLS Gen2 conectada ao workspace para alterar permissões ausentes para outras pessoas. Verifique se o usuário tem acesso ao workspace e às permissões para executar notebooks.

### <a name="option-1"></a>Opção 1

- Tornar o usuário um Colaborador/Proprietário de Dados do Blob de Armazenamento

### <a name="option-2"></a>Opção 2

- Especificar as seguintes ACLs na estrutura de pastas:

| Pasta | / | synapse | workspaces  | <workspacename> | sparkpools | <sparkpoolname>  | sparkpoolinstances  |
|--|--|--|--|--|--|--|--|
| Permissões de acesso | --X | --X | --X | --X | --X | --X | -WX |
| Permissões padrão | ---| ---| ---| ---| ---| ---| ---|

- Você deve ser capaz de usar a ACL em todas as pastas de "synapse" para baixo no portal do Azure. Para usar a ACL na pasta raiz "/", siga as instruções abaixo.

- Conecte-se à conta de armazenamento conectada com o workspace do Gerenciador de Armazenamento usando o AAD
- Selecione sua Conta e forneça a URL do ADLS Gen2 e o sistema de arquivos padrão para o workspace
- Quando vir a conta de armazenamento listada, clique com o botão direito do mouse no workspace da lista e selecione "Gerenciar Acesso"
- Adicione o Usuário à pasta / com a permissão de acesso "Executar". Selecione "Ok"

> [!IMPORTANT]
> Não selecione "Padrão" se não pretende fazer isso.

## <a name="next-steps"></a>Próximas etapas

- [Criar um pool de SQL usando o portal do Azure](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
- [Criar um pool do Apache Spark usando o portal do Azure](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md) 
