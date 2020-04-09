---
title: Serviços do Azure que suportam o Azure Data Lake Storage Gen2 | Microsoft Docs
description: Saiba quais serviços do Azure integram com o Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: edfcb1d0bfc87c84620b13ed26ec681fef32f1e3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878316"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Serviços do Azure que suportam o Azure Data Lake Storage Gen2

Você pode usar os serviços do Azure para ingerir dados, executar análises e criar representações visuais. Este artigo fornece uma lista de serviços suportados do Azure, divulga seu nível de suporte e fornece links para artigos que ajudam você a usar esses serviços com o Azure Data Lake Storage Gen2.

## <a name="supported-azure-services"></a>Serviços do Azure compatíveis

Esta tabela lista os serviços do Azure que você pode usar com o Azure Data Lake Storage Gen2. Os itens que aparecem nessas tabelas mudarão ao longo do tempo à medida que o suporte continua a se expandir.

> [!NOTE]
> O nível de suporte refere-se apenas à forma como o serviço é suportado com data lake storage Gen 2.

|Serviço do Azure |Nível de suporte |Artigos relacionados |
|---------------|-------------------|---|
|Fábrica de dados do Azure|Disponível para o público geral|[Carregar dados no Azure Data Lake Storage Gen2 com o Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|Disponível para o público geral|[Usar com o Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Quickstart: analise dados no Azure Data Lake Storage Gen2 usando o Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Tutorial: Extraia, transforme e carregue dados usando o Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Tutorial: Acesso dados de armazenamento do lago gen2 dados com databricks do Azure usando spark](data-lake-storage-use-databricks-spark.md)|
|Hub de Eventos do Azure|Disponível para o público geral|[Capture eventos por meio dos Hubs de Eventos do Azure no Armazenamento de Blobs do Azure ou no Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Grade de Eventos do Azure|Disponível para o público geral|[Tutorial: Implemente o padrão de captura do lago de dados para atualizar uma tabela Databricks Delta](data-lake-storage-events.md)|
|Aplicativos Lógicos do Azure|Disponível para o público geral|[Visão geral – O que são Aplicativos Lógicos do Azure?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|Disponível para o público geral|[Dados de acesso em serviços de armazenamento do Azure](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Stream Analytics do Azure|Disponível para o público geral|[Início Rápido: criar um trabalho do Stream Analytics usando o portal do Azure](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Egressa para Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Data Box|Disponível para o público geral|[Use a Caixa de Dados do Azure para migrar dados de uma loja HDFS no local para o Armazenamento Azure](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight |Disponível para o público geral|[Use o Azure Data Lake Storage Gen2 com clusters Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Usando a CLI do HDFS com o Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Tutorial: Extraia, transforme e carregue dados usando a Colmeia Apache no Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|Hub IoT |Disponível para o público geral|[Use o roteamento de mensagens do IoT Hub para enviar mensagens de dispositivo para nuvem para diferentes pontos finais](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|Disponível para o público geral|[Analisar dados em Data Lake Storage Gen2 usando power bi](https://docs.microsoft.com/power-query/connectors/datalakestorage)|
|SQL Data Warehouse|Disponível para o público geral|[Usar com SQL Data Warehouse do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS)|Disponível para o público geral|[Gerenciador de conexão Azure Storage](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Pesquisa Cognitiva do Azure|Visualização|[Indexe e pesquise documentos Gen2 do Azure Data Lake Storage Gen2 (visualização)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure Data Explorer|Visualização|[Dados de consulta no Lago de Dados do Azure usando o Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)|
|Rede de Distribuição de Conteúdo do Azure|Ainda não suportado|[Indexe e pesquise documentos Gen2 do Azure Data Lake Storage Gen2 (visualização)](https://docs.microsoft.com/azure/cdn/cdn-overview)|


## <a name="see-also"></a>Confira também

- [Problemas conhecidos com o Azure Data Lake Storage Gen2 | Microsoft Docs](data-lake-storage-known-issues.md)
- [Recursos de armazenamento Blob disponíveis no Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Plataformas de código aberto que suportam o Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Acesso de vários protocolos no Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)