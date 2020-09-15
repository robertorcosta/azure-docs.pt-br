---
title: Cosmos DB o conector de Apache Spark para recursos e notas de versão da API do SQL
description: Saiba mais sobre o conector de Apache Spark Azure Cosmos DB para a API do SQL, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão do SDK do Java assíncrono do SQL Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 05f81e4d93244db854bf8d0ec254ee647f81d9cc
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90069162"
---
# <a name="azure-cosmos-db-apache-spark-connector-for-core-sql-api-release-notes-and-resources"></a>API do Azure Cosmos DB Apache Spark Connector para núcleo (SQL): notas de versão e recursos
> [!div class="op_single_selector"]
> * [SDK v3 do .NET](sql-api-sdk-dotnet-standard.md)
> * [SDK do .NET v2](sql-api-sdk-dotnet.md)
> * [SDK v2 do .NET Core](sql-api-sdk-dotnet-core.md)
> * [SDK v2 do feed de alterações do .NET](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [SDK do Java v4](sql-api-sdk-java-v4.md)
> * [SDK do Java Assíncrono v2](sql-api-sdk-async-java.md)
> * [SDK do Java Síncrono v2](sql-api-sdk-java.md)
> * [Spring data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring data v3](sql-api-sdk-java-spring-v3.md)
> * [Conector do Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Provedor de recursos REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor em massa-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor em massa – Java](sql-api-sdk-bulk-executor-java.md)

Você pode acelerar a análise de Big Data usando o conector do Azure Cosmos DB Apache Spark para núcleo (SQL). O conector do Spark permite executar trabalhos do [Spark](https://spark.apache.org/) em dados armazenados no Azure Cosmos DB. Há suporte para processamento em lote e fluxo.

Você pode usar o conector com o [Azure Databricks](https://azure.microsoft.com/services/databricks) ou o [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), que fornece clusters Spark gerenciados no Azure. A tabela a seguir mostra as versões com suporte:

| Componente | Versão |
|---------|-------|
| Apache Spark | 2,4.*x*, 2,3. *x*, 2,2. *x*e 2,1. *x* |
| Scala | 2,11 |
| Azure Databricks (versão de tempo de execução) | Posterior a 3,4 |

> [!WARNING]
> Este conector dá suporte à API de núcleo (SQL) do Azure Cosmos DB.
> Para a API de Cosmos DB para MongoDB, use o [conector do MongoDB para Spark](https://docs.mongodb.com/spark-connector/master/).
> Para o API do Cassandra de Cosmos DB, use o [conector do Cassandra Spark](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="resources"></a>Recursos

| Recurso | Link |
|---|---|
| **Baixe o SDK** | [Baixar de Apache Spark](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) |
|**Documentação da API** | [Referência do conector do Spark]() |
|**Contribuir para o SDK** | [Conector de Azure Cosmos DB para Apache Spark no GitHub](https://github.com/Azure/azure-cosmosdb-spark) | 
|**Introdução** | [Acelere a análise de big data usando o conector do Apache Spark para o Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/spark-connector#bk_working_with_connector) <br> [Use o Streaming Estruturado do Apache Spark com o Apache Kafka e o Azure Cosmos DB](https://docs.microsoft.com/azure/hdinsight/apache-kafka-spark-structured-streaming-cosmosdb?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>Histórico de versões

### <a name="311"></a>3.1.1
#### <a name="key-bug-fixes"></a>Correções de bug de chave
* Corrige um caso de borda de ponto de verificação de streaming no qual o `ID` contém o caractere de pipe (|) com a `ChangeFeedMaxPagesPerBatch` configuração aplicada.

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>Novos recursos
* Adiciona suporte para atualizações em massa quando chaves de partição aninhadas são usadas.
* Adiciona suporte para tipos de dados decimal e float durante gravações para Azure Cosmos DB.
* Adiciona suporte para tipos de carimbo de data/hora quando eles estão usando tempo longo (época do UNIX) como um valor.

### <a name="308"></a>3.0.8
#### <a name="key-bug-fixes"></a>Correções de bug de chave
* Corrige a exceção conversão que ocorre quando a `WriteThroughputBudget` configuração é usada.

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>Novos recursos
* Adiciona informações de erro para falhas em massa à exceção e ao log.

### <a name="306"></a>3.0.6
#### <a name="key-bug-fixes"></a>Correções de bug de chave
* Corrige problemas de ponto de verificação de streaming.

### <a name="305"></a>3.0.5
#### <a name="key-bug-fixes"></a>Correções de bug de chave
* Para reduzir o ruído, o nível de log de uma mensagem permanece involuntariamente com erro de nível.

### <a name="304"></a>3.0.4
#### <a name="key-bug-fixes"></a>Correções de bug de chave
* Corrige um bug no streaming estruturado durante as divisões de partição. O bug pode resultar em alguns registros de feed de alteração ausentes ou exceções nulas para gravações de ponto de verificação.

### <a name="303"></a>3.0.3
#### <a name="key-bug-fixes"></a>Correções de bug de chave
* Corrige um bug que faz com que um esquema personalizado fornecido para readStream seja ignorado.

### <a name="302"></a>3.0.2
#### <a name="key-bug-fixes"></a>Correções de bug de chave
* Corrige uma regressão (JAR não sombreado inclui todas as dependências sombreadas) que aumenta o tempo de compilação em 50%.

### <a name="301"></a>3.0.1
#### <a name="key-bug-fixes"></a>Correções de bug de chave
* Corrige um problema de dependência que faz com que o transporte direto sobre TCP falhe com RequestTimeoutException.

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>Novos recursos
* Melhora o gerenciamento de conexões e o pool de conexões para reduzir o número de chamadas de metadados.

## <a name="faq"></a>Perguntas frequentes
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Saiba mais sobre o [Apache Spark](https://spark.apache.org/).