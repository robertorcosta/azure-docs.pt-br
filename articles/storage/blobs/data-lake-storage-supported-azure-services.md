---
title: Serviços do Azure que dão suporte a Azure Data Lake Storage Gen2 | Microsoft Docs
description: Saiba mais sobre quais serviços do Azure se integram com o Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: cb68f1bc851a8573ddec01d1eee803135a11b067
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195351"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Serviços do Azure que dão suporte ao Azure Data Lake Storage Gen2

Você pode usar os serviços do Azure para ingerir dados, executar análises e criar representações visuais. Este artigo fornece uma lista de serviços do Azure com suporte, divulga seu nível de suporte e fornece links para artigos que ajudam você a usar esses serviços com Azure Data Lake Storage Gen2.

## <a name="supported-azure-services"></a>Serviços do Azure compatíveis

Esta tabela lista os serviços do Azure que você pode usar com Azure Data Lake Storage Gen2. Os itens que aparecem nessas tabelas serão alterados ao longo do tempo, pois o suporte continuará a ser expandido.

> [!NOTE]
> O nível de suporte refere-se apenas a como o serviço tem suporte com o Data Lake Storage Gen 2.

|Serviço do Azure |Nível de suporte |Artigos relacionados |
|---------------|-------------------|---|
|Fábrica de dados do Azure|Disponível para o público geral|[Carregar dados no Azure Data Lake Storage Gen2 com o Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|Disponível para o público geral|[Usar com Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Início rápido: analisar dados em Azure Data Lake Storage Gen2 usando Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Tutorial: extrair, transformar e carregar dados usando Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Tutorial: acessar dados de Data Lake Storage Gen2 com Azure Databricks usando o Spark](data-lake-storage-use-databricks-spark.md)|
|Captura de hubs de eventos do Azure| Disponível para o público geral|[Capturar eventos por meio dos hubs de eventos do Azure no armazenamento de BLOBs do Azure ou Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Aplicativos Lógicos do Azure|Disponível para o público geral|[Visão geral – o que são os aplicativos lógicos do Azure?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|Disponível para o público geral|[Acessar dados nos serviços de armazenamento do Azure](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Stream Analytics do Azure|Disponível para o público geral|[Início Rápido: criar um trabalho do Stream Analytics usando o Portal do Azure](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Saída para Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Data Box| Disponível para o público geral|[Usar Azure Data Box para migrar dados de um repositório HDFS local para o armazenamento do Azure](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight | Disponível para o público geral|[Usar Gen2 de armazenamento do Azure Data Lake com clusters de HDInsight do Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Usando a CLI do HDFS com Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Tutorial: extrair, transformar e carregar dados usando Apache Hive no Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|Hub IoT | Disponível para o público geral|[Usar o roteamento de mensagens do Hub IoT para enviar mensagens do dispositivo para a nuvem para diferentes pontos de extremidade](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI| Disponível para o público geral|[Analisar dados em Data Lake Storage Gen2 usando Power BI](https://docs.microsoft.com/power-query/connectors/datalakestorage)|
|SQL Data Warehouse|Disponível para o público geral|[Usar com o Azure SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS)|Disponível para o público geral|[Gerenciador de conexões do armazenamento do Azure](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Pesquisa Cognitiva do Azure| Visualização|[Indexar e Pesquisar Azure Data Lake Storage Gen2 documentos (visualização)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Rede de Distribuição de Conteúdo do Azure|Ainda não tem suporte|[Indexar e Pesquisar Azure Data Lake Storage Gen2 documentos (visualização)](https://docs.microsoft.com/azure/cdn/cdn-overview)|


## <a name="see-also"></a>Confira também

- [Problemas conhecidos com o Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Recursos de armazenamento de BLOBs disponíveis no Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Plataformas de software livre que dão suporte a Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Acesso de vários protocolos no Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)