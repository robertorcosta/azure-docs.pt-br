---
title: Integrar com o Apache Spark – Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo mostra como integrar com o Apache Spark para habilitar o streaming estruturado com os hubs de eventos.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 4c4fd74e9123e1310be297a15090433d365d24cf
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311675"
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Integrando o Apache Spark com Hubs de Eventos

O Hubs de Eventos do Azure integra-se perfeitamente com [Apache Spark](https://spark.apache.org/) para habilitar a compilação de aplicativos streaming distribuídos. A integração é compatível com [Spark Core](https://spark.apache.org/docs/latest/rdd-programming-guide.html), [Spark Streaming](https://spark.apache.org/docs/latest/streaming-programming-guide.html) e [Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html). O conector do Hubs de Eventos para o Apache Spark está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-spark). Esta biblioteca também está disponível para uso em projetos Maven no [Repositório Central do Maven](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C).

Este artigo descreve como criar um aplicativo contínuo no [Azure Databricks](https://azure.microsoft.com/services/databricks/). Embora este artigo use o Azure Databricks, clusters do Spark também estão disponíveis com o [HDInsight](../hdinsight/spark/apache-spark-overview.md).

O exemplo neste artigo usa dois blocos de anotações Scala, um para transmitir eventos de um hub de eventos e outro para enviar eventos de volta para ele.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Uma instância de Hubs de Eventos. [Crie uma](event-hubs-create.md) se ainda não tiver feito isso.
* Uma instância do [Azure Databricks](https://azure.microsoft.com/services/databricks/). [Crie uma](../azure-databricks/quickstart-create-databricks-workspace-portal.md) se ainda não tiver feito isso.
* [Crie uma biblioteca usando coordenadas de maven](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package): `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.1`.

Transmita eventos de seu hub de eventos usando o seguinte código:

```scala
import org.apache.spark.eventhubs._

// To connect to an event hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build 
val ehConf = EventHubsConf(connectionString)
  .setStartingPosition(EventPosition.fromEndOfStream)

// Create a stream that reads data from the specified Event Hub.
val reader = spark.readStream
  .format("eventhubs")
  .options(ehConf.toMap)
  .load()
val eventhubs = reader.select($"body" cast "string")

eventhubs.writeStream
  .format("console")
  .outputMode("append")
  .start()
  .awaitTermination()
```
O código a seguir envia eventos para seu hub de eventos com as APIs de lote do Spark. Também é possível escrever uma consulta de streaming para enviar eventos ao hub de eventos:

```scala
import org.apache.spark.eventhubs._
import org.apache.spark.sql.functions._

// To connect to an event hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build
val ehConf = EventHubsConf(connectionString)

// Create random strings as the body of the message.
val bodyColumn = concat(lit("random nunmber: "), rand()).as("body")

// Write 200 rows to the specified event hub.
val df = spark.range(200).select(bodyColumn)
df.write
  .format("eventhubs")
  .options(ehConf.toMap)
  .save() 
```

## <a name="next-steps"></a>Próximos passos

Agora você sabe configurar um fluxo dimensionável e tolerante a falhas usando o conector de Hubs de Eventos para o Apache Spark. Saiba mais sobre usar os Hubs de Eventos com Fluxo Estruturado e Fluxo Spark seguindo os links a seguir:

* [Streaming estruturado + guia de integração de Hubs de Eventos do Azure](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Spark Streaming + guia de integração de Hubs de Eventos](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
