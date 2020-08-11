---
title: Azure Cosmos DB referência de dados de monitoramento | Microsoft Docs
description: Referência de log e métricas para monitorar dados de Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2020
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: 21e1d93e206751b5a55b0b3549e8bd566612ddbe
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080446"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Referência de dados de monitoramento do Azure Cosmos DB

Este artigo apresenta uma referência de dados de log e métricas coletados para analisar o desempenho e a disponibilidade do Azure Cosmos DB. Consulte o artigo [Azure Cosmos DB do monitor](monitor-cosmos-db.md) para saber como coletar e analisar dados de monitoramento para Azure Cosmos DB.

## <a name="resource-logs"></a>Logs de recursos

A tabela a seguir lista as propriedades dos logs de recursos no Azure Cosmos DB. Os logs de recursos são coletados em logs de Azure Monitor ou no armazenamento do Azure. No Azure Monitor, os logs são coletados na tabela **AzureDiagnostics** sob o provedor de recursos * * nome do `MICROSOFT.DOCUMENTDB` .

| Propriedade ou campo de Armazenamento do Microsoft Azure | Propriedade de logs de Azure Monitor | Descrição |
| --- | --- | --- |
| **time** | **TimeGenerated** | A data e hora (UTC) em que a operação ocorreu. |
| **resourceId** | **Recurso** | A conta do Azure Cosmos DB na qual os logs estão habilitados.|
| **category** | **Categoria** | Para Azure Cosmos DB, **DataPlaneRequests**, **MongoRequests**, **QueryRuntimeStatistics**, **PartitionKeyStatistics**, **PartitionKeyRUConsumption**, **ControlPlaneRequests** são os tipos de log disponíveis. |
| **operationName** | **OperationName** | Nome da operação. O nome da operação pode ser,,,,,,, `Create` `Update` ,,,, `Read` `ReadFeed` `Delete` `Replace` `Execute` `SqlQuery` `Query` `JSQuery` `Head` `HeadFeed` ou `Upsert` .   |
| **properties** | N/D | O conteúdo desse campo é descrito nas linhas a seguir. |
| **activityId** | **activityId_g** | O GUID exclusivo da operação registrada. |
| **userAgent** | **userAgent_s** | Uma cadeia de caracteres que especifica o agente de usuário do cliente do qual a solicitação foi enviada. O formato do agente do usuário é `{user agent name}/{version}` .|
| **requestResourceType** | **requestResourceType_s** | O tipo do recurso acessado. Esse valor pode ser banco de dados, contêiner, documento, anexo, usuário, permissão, procedimento armazenado, gatilho, função definida pelo usuário ou uma oferta. |
| **statusCode** | **statusCode_s** | O status da resposta da operação. |
| **requestResourceId** | **ResourceId** | O resourceId referente à solicitação. Dependendo da operação executada, esse valor pode apontar para `databaseRid` , `collectionRid` ou `documentRid` .|
| **clientIpAddress** | **clientIpAddress_s** | Endereço IP do cliente. |
| **requestCharge** | **requestCharge_s** | O número de RU/s que são usados pela operação |
| **collectionRid** | **collectionId_s** | A ID exclusiva da coleção.|
| **duration** | **duration_d** | A duração da operação, em milissegundos. |
| **requestLength** | **requestLength_s** | O tamanho da solicitação, em bytes. |
| **responseLength** | **responseLength_s** | O tamanho da resposta, em bytes.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Esse valor não fica vazio quando [tokens de recurso](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) são usados para autenticação. O valor aponta para a ID de recurso do usuário. |
| **responseLength** | **responseLength_s** | O tamanho da resposta, em bytes.|

Para obter uma lista de todas as categorias de log Azure Monitor e links para esquemas associados, consulte [Azure monitor categorias e esquemas de logs](../azure-monitor/platform/diagnostic-logs-schema.md). 

## <a name="metrics"></a>Métricas
As tabelas a seguir listam as métricas de plataforma coletadas para o Azure CosmOS DB. Todas as métricas são armazenadas no namespace **Cosmos DB métricas padrão**.

Para obter uma lista de todas as métricas de suporte de Azure Monitor (incluindo Azure Cosmos DB), consulte [Azure monitor métricas com suporte](../azure-monitor/platform/metrics-supported.md). 

#### <a name="request-metrics"></a>Métricas de solicitação
            
|Métrica (nome para exibição da métrica)|Unidade (tipo de agregação) |Descrição|Dimensões| Granularidades de tempo| Mapeamento de métrica herdada | Uso |
|---|---|---|---| ---| ---| ---|
| TotalRequests (total de solicitações) | Contagem (contagem) | Número de solicitações feitas| DatabaseName, CollectionName, Region, StatusCode| Todos | TotalRequests, Http 2xx, Http 3xx, Http 400, Http 401, Erro interno do servidor, Serviço Não Disponível, Solicitações Limitadas, Média de Solicitações por Segundo | Usado para monitorar solicitações por código de status, contêiner a uma granularidade de minuto. Para obter a média de solicitações por segundo, use a agregação de contagem no minuto e divida por 60. |
| MetadataRequests (solicitações de metadados) |Contagem (contagem) | Contagem de solicitações de metadados. Azure Cosmos DB mantém o contêiner de metadados do sistema para cada conta, que permite enumerar coleções, bancos de dados, etc., e suas configurações, gratuitamente. | DatabaseName, CollectionName, Region, StatusCode| Todos| |Usada para monitorar os limitadores devido a solicitações de metadados.|
| MongoRequests (solicitações de Mongo) | Contagem (contagem) | Número de Solicitações do Mongo Feitas | DatabaseName, CollectionName, Region, CommandName, ErrorCode| Todos |Taxa de Solicitação de Consulta do Mongo, Taxa de Solicitação de Atualização do Mongo, Taxa de Solicitação de Exclusão do Mongo, Taxa de Solicitação de Inserção do Mongo, Taxa de Solicitação de Contagem do Mongo| Usada para monitorar os erros de solicitação do Mongo, usos por tipo de comando. |

#### <a name="request-unit-metrics"></a>Métricas de unidade de solicitação

|Métrica (nome para exibição da métrica)|Unidade (tipo de agregação)|Descrição|Dimensões| Granularidades de tempo| Mapeamento de métrica herdada | Uso |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (encargo de solicitação Mongo) | Contagem (total) |Unidades Solicitadas do Mongo Consumidas| DatabaseName, CollectionName, Region, CommandName, ErrorCode| Todos |Encargo de Solicitação de Consulta do Mongo, Encargo de Solicitação de Atualização do Mongo, Encargo de Solicitação de Exclusão do Mongo, Encargo de Solicitação de Inserção do Mongo, Encargo de Solicitação de Contagem do Mongo| Usada para monitorar RUs de recurso do Mongo em um minuto.|
| TotalRequestUnits (total de unidades de solicitação)| Contagem (total) | Unidades Solicitadas Consumidas| DatabaseName, CollectionName, Region, StatusCode |Todos| TotalRequestUnits| Usada para monitorar o uso de RU Total a uma granularidade de minuto. Para obter a média de RU consumida por segundo, use a agregação total no minuto e divida por 60.|
| ProvisionedThroughput (taxa de transferência provisionada)| Contagem (máximo) |Taxa de transferência provisionada na granularidade do contêiner| DatabaseName, ContainerName| 5 M| | Usado para monitorar a taxa de transferência provisionada por contêiner.|

#### <a name="storage-metrics"></a>Métricas de armazenamento

|Métrica (nome para exibição da métrica)|Unidade (tipo de agregação)|Descrição|Dimensões| Granularidades de tempo| Mapeamento de métrica herdada | Uso |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (armazenamento disponível) |Bytes (total) | Armazenamento total disponível relatado com granularidade de 5 minutos por região| DatabaseName, CollectionName, Region| 5 M| Armazenamento disponível| Usada para monitorar a capacidade de armazenamento disponível (aplicável apenas para coleções de armazenamento fixas). A granularidade mínima deve ser de 5 minutos.| 
| Datautilization (uso de dados) |Bytes (total) |Uso total de dados relatados com granularidade de 5 minutos por região| DatabaseName, CollectionName, Region| 5 M |Tamanho dos dados | Usado para monitorar o uso total de dados no contêiner e na região, a granularidade mínima deve ser de 5 minutos.|
| IndexUsage (uso de índice) | Bytes (total) |Uso total do índice relatado com granularidade de 5 minutos por região| DatabaseName, CollectionName, Region| 5 M| Tamanho do Índice| Usado para monitorar o uso total de dados no contêiner e na região, a granularidade mínima deve ser de 5 minutos. |
| DocumentQuota (cota de documentos) | Bytes (total) | Cota de armazenamento total relatada à granularidade de 5 minutos por região.| DatabaseName, CollectionName, Region| 5 M |Capacidade de Armazenamento| Usado para monitorar a cota total no contêiner e na região, a granularidade mínima deve ser de 5 minutos.|
| DocumentCount (contagem de documentos) | Contagem (total) |Contagem total de documentos relatados à granularidade de 5 minutos por região| DatabaseName, CollectionName, Region| 5 M |Contagem de documentos|Usado para monitorar a contagem de documentos no contêiner e na região, a granularidade mínima deve ser de 5 minutos.|

#### <a name="latency-metrics"></a>Métricas de latência

|Métrica (nome para exibição da métrica)|Unidade (tipo de agregação)|Descrição|Dimensões| Granularidades de tempo| Uso |
|---|---|---|---| ---| ---|
| ReplicationLatency (latência de replicação)| Milissegundos (mínimo, máximo, média) | Latência de replicação P99 nas regiões de origem e de destino para conta habilitada geograficamente| SourceRegion, TargetRegion| Todos | Usada para monitorar a latência de replicação P99 entre quaisquer duas regiões para uma conta com replicação geográfica. |
| Latência do lado do servidor| Milissegundos (média) | Tempo gasto pelo servidor para processar a solicitação. | CollectionName, ConnectionMode, DatabaseName, OperationType, PublicAPIType, região | Todos | Usado para monitorar a latência de solicitação no servidor de Azure Cosmos DB. |



#### <a name="availability-metrics"></a>Métricas de disponibilidade

|Métrica (nome para exibição da métrica) |Unidade (tipo de agregação)|Descrição| Granularidades de tempo| Mapeamento de métrica herdada | Uso |
|---|---|---|---| ---| ---|
| Indisponibilidade (disponibilidade do serviço)| Percentual (mínimo, máximo) | Disponibilidade de solicitações de conta na granularidade de uma hora| 1H | Disponibilidade do serviço | Representa a porcentagem do total de solicitações passadas. Uma solicitação será considerada com falha devido a erro de sistema se o código de status for 410, 500 ou 503. Usada para monitorar a disponibilidade da conta na granularidade de hora. |


#### <a name="cassandra-api-metrics"></a>Métricas da API do Cassandra

|Métrica (nome para exibição da métrica)|Unidade (tipo de agregação)|Descrição|Dimensões| Granularidades de tempo| Uso |
|---|---|---|---| ---| ---|
| CassandraRequests (solicitações de Cassandra) | Contagem (contagem) | Número de solicitações da API do Cassandra feitas| DatabaseName, CollectionName, ErrorCode, Region, OperationType, ResourceType| Todos| Usada para monitorar solicitações do Cassandra em uma granularidade de minuto. Para obter a média de solicitações por segundo, use a agregação de contagem no minuto e divida por 60.|
| CassandraRequestCharges (encargos de solicitação do Cassandra) | Contagem (Sum, min, Max, AVG) | Unidades de solicitação consumidas pelo API do Cassandra | DatabaseName, CollectionName, Region, OperationType, ResourceType| Todos| Usada para monitorar as RUs usadas por minuto por uma conta de API do Cassandra.|
| CassandraConnectionClosures (fechamentos de conexão do Cassandra) |Contagem (contagem) |Número de conexões do Cassandra fechadas| ClosureReason, Region| Todos | Usada para monitorar a conectividade entre os clientes e a API do Cassandra do Azure Cosmos DB.|

## <a name="see-also"></a>Consulte Também

- Consulte [monitoramento Azure Cosmos DB](monitor-cosmos-db.md) para obter uma descrição do Azure Cosmos DB de monitoramento.
- Confira [Como monitorar os recursos do Azure com o Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) para obter detalhes sobre o monitoramento de recursos do Azure.
