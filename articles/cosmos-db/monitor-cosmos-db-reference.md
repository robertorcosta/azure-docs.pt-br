---
title: Azure Cosmos DB monitoring data reference | Microsoft Docs
description: Log and metrics reference for monitoring data from Azure Cosmos DB.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.subservice: logs
ms.openlocfilehash: d131523e3031f55a818bb1919f39119bf073cb75
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456534"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Azure Cosmos DB monitoring data reference
This article provides a reference of log and metric data collected to analyze the performance and availability of Azure Cosmos DB. See [Monitoring Cosmos DB](monitor-cosmos-db.md) for details on collecting and analyzing monitoring data for Azure Cosmos DB.


## <a name="resource-logs"></a>Logs de recursos
The following table lists the properties for Azure Cosmos DB resource logs when they're collected in Azure Monitor Logs or Azure Storage. In Azure Monitor Logs, they're collected in the **AzureDiagnostics** table with a **ResourceProvider** value of *MICROSOFT.DOCUMENTDB*. 

| Propriedade ou campo de Armazenamento do Microsoft Azure | Azure Monitor Logs property | Descrição |
| --- | --- | --- |
| **time** | **TimeGenerated** | A data e hora (UTC) em que a operação ocorreu. |
| **resourceId** | **Recurso** | A conta do Azure Cosmos DB na qual os logs estão habilitados.|
| **category** | **Categoria** | For Azure Cosmos DB logs, **DataPlaneRequests**, **MongoRequests**, **QueryRuntimeStatistics**, **PartitionKeyStatistics**, **PartitionKeyRUConsumption**, **ControlPlaneRequests** are the available log types. |
| **operationName** | **OperationName** | Nome da operação. Esse valor pode ser uma das seguintes operações: Create, Update, Read, ReadFeed, Delete, Replace, Execute, SqlQuery, Query, JSQuery, Head, HeadFeed ou Upsert.   |
| **properties** | N/D | O conteúdo desse campo é descrito nas linhas a seguir. |
| **activityId** | **activityId_g** | O GUID exclusivo da operação registrada. |
| **userAgent** | **userAgent_s** | Uma cadeia de caracteres que especifica o agente do usuário cliente que executa a solicitação. O formato é {nome do agente do usuário}/{versão}.|
| **requestResourceType** | **requestResourceType_s** | O tipo do recurso acessado. Esse valor pode ser um dos seguintes tipos de recursos: Database, Collection, Document, Attachment, User, Permission, StoredProcedure, Trigger, UserDefinedFunction ou Offer. |
| **statusCode** | **statusCode_s** | O status da resposta da operação. |
| **requestResourceId** | **ResourceId** | O resourceId referente à solicitação. O valor pode apontar para databaseRid, collectionRid ou documentRid, dependendo da operação executada.|
| **clientIpAddress** | **clientIpAddress_s** | Endereço IP do cliente. |
| **requestCharge** | **requestCharge_s** | O número de RUs usadas pela operação |
| **collectionRid** | **collectionId_s** | A ID exclusiva da coleção.|
| **duration** | **duration_s** | The duration of the operation, in milliseconds. |
| **requestLength** | **requestLength_s** | O tamanho da solicitação, em bytes. |
| **responseLength** | **responseLength_s** | O tamanho da resposta, em bytes.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Esse valor não fica vazio quando [tokens de recurso](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) são usados para autenticação. O valor aponta para a ID de recurso do usuário. |

For a list of all Azure Monitor log categories and links to associated schemas, see [Azure Monitor Logs categories and schemas](../azure-monitor/platform/diagnostic-logs-schema.md). 

## <a name="metrics"></a>Métricas
The following tables list the platform metrics collected for Azure CosmOS DB. All metrics are stored in the namespace **Cosmos DB standard metrics**.

For a list of all Azure Monitor support metrics (including CosmosDB), see [Azure Monitor supported metrics](../azure-monitor/platform/metrics-supported.md). 

#### <a name="request-metrics"></a>Métricas de solicitação
            
|Metric (Metric Display Name)|Unit (Aggregation Type) |Descrição|Dimensões| Granularidades de tempo| Mapeamento de métrica herdada | Uso |
|---|---|---|---| ---| ---| ---|
| TotalRequests (Total Requests) | Count (Count) | Número de solicitações feitas| DatabaseName, CollectionName, Region, StatusCode| Tudo | TotalRequests, Http 2xx, Http 3xx, Http 400, Http 401, Erro interno do servidor, Serviço Não Disponível, Solicitações Limitadas, Média de Solicitações por Segundo | Used to monitor requests per status code, container at a minute granularity. Para obter a média de solicitações por segundo, use a agregação de contagem no minuto e divida por 60. |
| MetadataRequests (Metadata Requests) |Count (Count) | Contagem de solicitações de metadados. Azure Cosmos DB maintains system metadata container for each account, that allows you to enumerate collections, databases, etc., and their configurations, free of charge. | DatabaseName, CollectionName, Region, StatusCode| Tudo| |Usada para monitorar os limitadores devido a solicitações de metadados.|
| MongoRequests (Mongo Requests) | Count (Count) | Número de Solicitações do Mongo Feitas | DatabaseName, CollectionName, Region, CommandName, ErrorCode| Tudo |Taxa de Solicitação de Consulta do Mongo, Taxa de Solicitação de Atualização do Mongo, Taxa de Solicitação de Exclusão do Mongo, Taxa de Solicitação de Inserção do Mongo, Taxa de Solicitação de Contagem do Mongo| Usada para monitorar os erros de solicitação do Mongo, usos por tipo de comando. |

#### <a name="request-unit-metrics"></a>Métricas de unidade de solicitação

|Metric (Metric Display Name)|Unit (Aggregation Type)|Descrição|Dimensões| Granularidades de tempo| Mapeamento de métrica herdada | Uso |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo Request Charge) | Count (Total) |Unidades Solicitadas do Mongo Consumidas| DatabaseName, CollectionName, Region, CommandName, ErrorCode| Tudo |Encargo de Solicitação de Consulta do Mongo, Encargo de Solicitação de Atualização do Mongo, Encargo de Solicitação de Exclusão do Mongo, Encargo de Solicitação de Inserção do Mongo, Encargo de Solicitação de Contagem do Mongo| Usada para monitorar RUs de recurso do Mongo em um minuto.|
| TotalRequestUnits (Total Request Units)| Count (Total) | Unidades Solicitadas Consumidas| DatabaseName, CollectionName, Region, StatusCode |Tudo| TotalRequestUnits| Usada para monitorar o uso de RU Total a uma granularidade de minuto. Para obter a média de RU consumida por segundo, use a agregação total no minuto e divida por 60.|
| ProvisionedThroughput (Provisioned Throughput)| Count (Maximum) |Provisioned throughput at container granularity| DatabaseName, ContainerName| 5 M| | Used to monitor provisioned throughput per container.|

#### <a name="storage-metrics"></a>Métricas de armazenamento

|Metric (Metric Display Name)|Unit (Aggregation Type)|Descrição|Dimensões| Granularidades de tempo| Mapeamento de métrica herdada | Uso |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (Available Storage) |Bytes (Total) | Total available storage reported at 5-minutes granularity per region| DatabaseName, CollectionName, Region| 5 M| Armazenamento disponível| Usada para monitorar a capacidade de armazenamento disponível (aplicável apenas para coleções de armazenamento fixas). A granularidade mínima deve ser de 5 minutos.| 
| DataUsage (Data Usage) |Bytes (Total) |Total data usage reported at 5-minutes granularity per region| DatabaseName, CollectionName, Region| 5 M |Tamanho dos dados | Used to monitor total data usage at container and region, minimum granularity should be 5 minutes.|
| IndexUsage (Index Usage) | Bytes (Total) |Total Index usage reported at 5-minutes granularity per region| DatabaseName, CollectionName, Region| 5 M| Tamanho do Índice| Used to monitor total data usage at container and region, minimum granularity should be 5 minutes. |
| DocumentQuota (Document Quota) | Bytes (Total) | Total storage quota reported at 5-minutes granularity per region.| DatabaseName, CollectionName, Region| 5 M |Capacidade de Armazenamento| Used to monitor total quota at container and region, minimum granularity should be 5 minutes.|
| DocumentCount (Document Count) | Count (Total) |Total document count reported at 5-minutes granularity per region| DatabaseName, CollectionName, Region| 5 M |Contagem de documentos|Used to monitor document count at container and region, minimum granularity should be 5 minutes.|

#### <a name="latency-metrics"></a>Métricas de latência

|Metric (Metric Display Name)|Unit (Aggregation Type)|Descrição|Dimensões| Granularidades de tempo| Uso |
|---|---|---|---| ---| ---|
| ReplicationLatency (Replication Latency)| MilliSeconds (Minimum, Maximum, Average) | Latência de replicação P99 nas regiões de origem e de destino para conta habilitada geograficamente| SourceRegion, TargetRegion| Tudo | Usada para monitorar a latência de replicação P99 entre quaisquer duas regiões para uma conta com replicação geográfica. |


#### <a name="availability-metrics"></a>Métricas de disponibilidade

|Metric (Metric Display Name) |Unit (Aggregation Type)|Descrição| Granularidades de tempo| Mapeamento de métrica herdada | Uso |
|---|---|---|---| ---| ---|
| ServiceAvailability (Service Availability)| Percent (Minimum, Maximum) | Disponibilidade de solicitações de conta na granularidade de uma hora| 1H | Disponibilidade do serviço | Represents the percent of total passed requests. Uma solicitação será considerada com falha devido a erro de sistema se o código de status for 410, 500 ou 503. Usada para monitorar a disponibilidade da conta na granularidade de hora. |


#### <a name="cassandra-api-metrics"></a>Métricas da API do Cassandra

|Metric (Metric Display Name)|Unit (Aggregation Type)|Descrição|Dimensões| Granularidades de tempo| Uso |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra Requests) | Count (Count) | Número de solicitações da API do Cassandra feitas| DatabaseName, CollectionName, ErrorCode, Region, OperationType, ResourceType| Tudo| Usada para monitorar solicitações do Cassandra em uma granularidade de minuto. Para obter a média de solicitações por segundo, use a agregação de contagem no minuto e divida por 60.|
| CassandraRequestCharges (Cassandra Request Charges) | Count (Sum, Min, Max, Avg) | Unidades de solicitação consumidas pelas solicitações de API do Cassandra| DatabaseName, CollectionName, Region, OperationType, ResourceType| Tudo| Usada para monitorar as RUs usadas por minuto por uma conta de API do Cassandra.|
| CassandraConnectionClosures (Cassandra Connection Closures) |Count (Count) |Número de conexões do Cassandra fechadas| ClosureReason, Region| Tudo | Usada para monitorar a conectividade entre os clientes e a API do Cassandra do Azure Cosmos DB.|

## <a name="see-also"></a>Consulte também

- See [Monitoring Azure Cosmos DB](monitor-cosmos-db.md) for a description of monitoring Azure Cosmos DB.
- See [Monitoring Azure resources with Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) for details on monitoring Azure resources.
