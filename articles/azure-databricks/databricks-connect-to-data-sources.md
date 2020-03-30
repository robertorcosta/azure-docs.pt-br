---
title: 'Conectar-se a diferentes fontes de dados do Azure Databricks '
description: Saiba como se conectar ao Banco de Dados Azure SQL, Azure Data Lake Store, blob storage, Cosmos DB, Event Hubs e Azure SQL Data Warehouse da Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: 80ec9b2bbf10d8f500e7f9e0369faca3b9663633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129392"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Conectar a fontes de dados do Azure Databricks

Este artigo fornece links para todas as diferentes fontes de dados no Azure que podem ser conectadas ao Azure Databricks. Siga os exemplos nesses links para extrair dados das fontes de dados do Azure (por exemplo, Armazenamento de Blobs, Hubs de Eventos do Azure e etc.) em um cluster do Azure Databricks e executar trabalhos analíticos neles. 

## <a name="prerequisites"></a>Pré-requisitos

* É necessário ter um workspace do Azure Databricks e um cluster do Spark. Siga as instruções em [Introdução ao Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Fontes de dados para Azure Databricks

A lista a seguir fornece as fontes de dados no Azure que podem ser utilizadas com Azure Databricks. Para obter uma lista completa de fontes de dados que podem ser utilizadas com Azure Databricks, consulte [Fontes de dados para Azure Databricks](/azure/databricks/data/data-sources/index).

- [Banco de dados Azure SQL](/azure/databricks/data/data-sources/sql-databases)

    Esse link fornece a API de DataFrame para conexão com bancos de dados SQL usando JDBC e como controlar o paralelismo de leituras por meio da interface JDBC. Este tópico fornece exemplos detalhados usando a API do Scala, com exemplos abreviados de Python e Spark SQL no final.
- [Armazenamento do Azure Data Lake](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)

    Este link fornece exemplos de como usar o principal do serviço de diretório ativo do Azure para autenticar com o Azure Data Lake Storage. Ele também fornece instruções sobre como acessar os dados no Azure Data Lake Storage do Azure Databricks.

- [Armazenamento azure Blob](/azure/databricks/data/data-sources/azure/azure-storage)

    Esse link fornece exemplos de como acessar diretamente o Armazenamento de Blobs a partir do Azure Databricks usando a chave de acesso ou a SAS para um determinado contêiner. O link também fornece informações sobre como acessar o Armazenamento de Blobs a partir do Azure Databricks usando a API de RDD.

- [Azure Cosmos DB](/azure/databricks/data/data-sources/azure/cosmosdb-connector)

    Este link fornece instruções sobre como usar o [conector do Apache Spark do Microsoft Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark) a partir do Azure Databricks para acessar dados no Azure Cosmos DB.

- [Hubs de eventos do Azure](/azure/event-hubs/event-hubs-spark-connector)

    Este link fornece instruções sobre como usar o [conector do Apache Spark dos Hubs de Eventos do Azure](https://github.com/Azure/azure-event-hubs-spark) a partir do Azure Databricks para acessar dados nos Hubs de Eventos do Azure.

- [SQL Data Warehouse do Azure](/azure/databricks/data/data-sources/azure/sql-data-warehouse)

    Este link fornece instruções sobre como usar o conector do SQL Data Warehouse do Azure para conectar a partir do Azure Databricks.
    

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre fontes, a partir das quais é possível importar dados para o Azure Databricks, consulte [Fontes de dados para Azure Databricks](/azure/databricks/data/data-sources/index).


