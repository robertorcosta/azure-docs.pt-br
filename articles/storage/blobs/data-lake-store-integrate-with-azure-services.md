---
title: Integrar Azure Data Lake Storage com os serviços do Azure | Microsoft Docs
description: Saiba mais sobre quais serviços do Azure se integram com o Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: normesta
ms.openlocfilehash: e073c02f19a9add37e684a76839a620af0d07ec0
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301315"
---
# <a name="integrate-azure-data-lake-storage-with-azure-services"></a>Integrar Azure Data Lake Storage com os serviços do Azure

Você pode usar os serviços do Azure para ingerir dados, executar análises e criar representações visuais. Este artigo fornece uma lista de serviços do Azure com suporte e fornece links para artigos que ajudam você a usar esses serviços com Azure Data Lake Storage Gen2.

## <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Serviços do Azure que dão suporte ao Azure Data Lake Storage Gen2

A tabela a seguir lista os serviços do Azure que dão suporte ao Azure Data Lake Storage Gen2.

| Serviço do Azure |  Artigos relacionados |
|---------------|-------------------|
|Fábrica de dados do Azure | [Carregar dados no Azure Data Lake Storage Gen2 com o Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks | [Usar com Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Início Rápido: Analisar dados no Azure Data Lake Storage Gen2 usando o Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Tutorial: Extrair, transformar e carregar dados usando o Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Tutorial: Acessar dados de Data Lake Storage Gen2 com Azure Databricks usando Spark @ no__t-0 |
|Captura de hubs de eventos do Azure| [Capturar eventos por meio dos hubs de eventos do Azure no armazenamento de BLOBs do Azure ou Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Aplicativos Lógicos do Azure | [Visão geral – o que são os aplicativos lógicos do Azure?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Search | [Pesquisando no Armazenamento de blobs com o Azure Search](https://docs.microsoft.com/azure/search/search-blob-storage-integration)|
|Stream Analytics do Azure| [Saída para Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2) |
|Data Box|  [Usar Azure Data Box para migrar dados de um repositório HDFS local para o armazenamento do Azure](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight | [Usar Gen2 de armazenamento do Azure Data Lake com clusters de HDInsight do Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Usando a CLI do HDFS com Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Tutorial: Extrair, transformar e carregar dados usando Apache Hive no Azure HDInsight @ no__t-0 |
|Hub IOT | [Usar o roteamento de mensagens do Hub IoT para enviar mensagens do dispositivo para a nuvem para diferentes pontos de extremidade](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|  [Analisar dados em Data Lake Storage Gen2 usando Power BI](data-lake-storage-use-power-bi.md) |
|SQL Data Warehouse | [Usar com o Azure SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS) | [Gerenciador de conexões do armazenamento do Azure](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre quais ferramentas você pode usar para processar dados no data Lake. Consulte [usando Azure data Lake Storage Gen2 para Big data requisitos](data-lake-storage-data-scenarios.md).

- Saiba mais sobre o Data Lake Storage Gen2 e como começar a usar. Consulte [introdução ao Azure data Lake Storage Gen2](data-lake-storage-introduction.md)