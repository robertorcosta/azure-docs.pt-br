---
title: Como proteger as credenciais de acesso com serviços vinculados no Apache Spark para o Azure Synapse Analytics
description: Este artigo apresenta conceitos sobre como integrar com segurança o Apache Spark para Synapse Analytics a outros serviços usando os serviços vinculados e a biblioteca de tokens
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 08/26/2020
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: 1cdb010e34674d52ebe2135ad1591a163a078708
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324951"
---
# <a name="securing-your-credentials-through-linked-services-with-the-tokenlibrary"></a>Como proteger suas credenciais por meio de serviços vinculados com a TokenLibrary
O acesso a dados de fontes externas é um padrão comum. A menos que a fonte de dados externa permita acesso anônimo, é provável que você precise proteger sua conexão com uma credencial, um segredo ou uma cadeia de conexão.  

O Azure Synapse Analytics fornece serviços vinculados para simplificar o processo de integração armazenando os detalhes da conexão em um serviço vinculado ou no Azure Key Vault. Após você criar um serviço vinculado, o Apache Spark pode fazer referência a ele para aplicar as informações de conexão em seu código. 

Para obter mais informações, confira [serviços vinculados](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
> [!NOTE]
> O acesso a arquivos do Azure Data Lake Storage no workspace usa a passagem do AAD para autenticação, portanto, não será necessário usar a TokenLibrary. 


## <a name="prerequisite"></a>Pré-requisito
* Serviço vinculado – você deve criar um serviço vinculado para a fonte de dados externa e fazer referência a ele da biblioteca de tokens. Saiba mais sobre [serviços vinculados](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).


## <a name="connect-to-adls-gen2-outside-of-synapse-workspace"></a>Conectar-se ao ADLS Gen2 fora do workspace do Azure Synapse

O Synapse proporciona uma experiência integrada de serviços vinculados para Azure Data Lake Storage Gen2.

```scala
// Scala code
val sc = spark.sparkContext
sc.hadoopConfiguration.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
sc.hadoopConfiguration.set("fs.azure.account.auth.type", "SAS")
sc.hadoopConfiguration.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```

```python
# Python code
sc._jsc.hadoopConfiguration().set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type", "SAS")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```
## <a name="use-the-token-library"></a>Usar a biblioteca de tokens

Para conectar-se a outros serviços vinculados, você pode fazer uma chamada direta à TokenLibrary.

### <a name="getconnectionstring"></a>GetConnectionString
 Para recuperar a cadeia de conexão, use a função <b>getConnectionString</b> e passe o <b>nome do serviço vinculado</b>.

```scala
// Scala
// retrieve connectionstring from TokenLibrary

import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getConnectionString("<LINKED SERVICE NAME>")
println(connectionString)
```

```python
# Python
# retrieve connectionstring from TokenLibrary

from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
connection_string = token_library.getConnectionString("<LINKED SERVICE NAME>")
print(connection_string)
```
```csharp
// C#
// retrieve connectionstring from TokenLibrary

using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.GetConnectionString(<LINKED SERVICE NAME>);
Console.WriteLine(connectionString);
```

### <a name="getconnectionstringasmap"></a>GetConnectionStringAsMap
Para analisar valores específicos de um par <i>chave=valor</i> na cadeia de conexão, como 

<i>DefaultEndpointsProtocol=https;AccountName=\<AccountName>;AccountKey=\<AccountKey></i>

use a função <b>getConnectionStringAsMap</b> e passe a chave para retornar o valor.
```scala
// Linked services can be used for storing and retreiving credentials (e.g, account key)
// Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val accountKey: String = TokenLibrary.getConnectionStringAsMap("<LINKED SERVICE NAME">).get("<KEY NAME>")
println(accountKey)
```

```python
# Linked services can be used for storing and retreiving credentials (e.g, account key)
# Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
accountKey = token_library.getConnectionStringAsMap("<LINKED SERVICE NAME>").get("<KEY NAME>")
print(accountKey)
```

## <a name="next-steps"></a>Próximas etapas

- [Gravar no pool de SQL dedicado](./synapse-spark-sql-pool-import-export.md)

