---
title: Azure Cosmos DB monitorando referência de dados | Microsoft Docs
description: Referência de log e métricas para monitoramento de dados do Azure Cosmos DB.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.subservice: logs
ms.openlocfilehash: d243224192b5761af45d387690f5fb41b84481e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588715"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Referência de dados de monitoramento do Azure Cosmos DB
Este artigo apresenta uma referência de dados de log e métricas coletados para analisar o desempenho e a disponibilidade do Azure Cosmos DB. Consulte [Monitorando cosmos DB](monitor-cosmos-db.md) para obter detalhes sobre a coleta e análise de dados de monitoramento para o Azure Cosmos DB.


## <a name="resource-logs"></a>Logs de recursos
A tabela a seguir lista as propriedades dos registros de recursos do Azure Cosmos DB quando eles são coletados no Azure Monitor Logs ou no Azure Storage. No Azure Monitor Logs, eles são coletados na tabela **AzureDiagnostics** com um valor **resourceprovider** da *MICROSOFT. DOCUMENTDB*. 

| Propriedade ou campo de Armazenamento do Microsoft Azure | Propriedade do Azure Monitor Logs | Descrição |
| --- | --- | --- |
| **Tempo** | **TimeGenerated** | A data e hora (UTC) em que a operação ocorreu. |
| **Resourceid** | **Recurso** | A conta do Azure Cosmos DB na qual os logs estão habilitados.|
| **Categoria** | **Categoria** | Para logs do Azure Cosmos DB, **DataPlaneRequests,** **MongoRequests,** **QueryRuntimeStatistics,** **PartitionKeyStatistics,** **PartitionKeyRUConsumption**, **ControlPlaneRequests** são os tipos de log disponíveis. |
| **Operationname** | **Operationname** | Nome da operação. Esse valor pode ser uma das seguintes operações: Create, Update, Read, ReadFeed, Delete, Replace, Execute, SqlQuery, Query, JSQuery, Head, HeadFeed ou Upsert.   |
| **Propriedades** | n/d | O conteúdo desse campo é descrito nas linhas a seguir. |
| **Activityid** | **activityId_g** | O GUID exclusivo da operação registrada. |
| **Useragent** | **userAgent_s** | Uma cadeia de caracteres que especifica o agente do usuário cliente que executa a solicitação. O formato é {nome do agente do usuário}/{versão}.|
| **requestResourceType** | **requestResourceType_s** | O tipo do recurso acessado. Esse valor pode ser um dos seguintes tipos de recursos: Database, Collection, Document, Attachment, User, Permission, StoredProcedure, Trigger, UserDefinedFunction ou Offer. |
| **statusCode** | **statusCode_s** | O status da resposta da operação. |
| **requestResourceId** | **Resourceid** | O resourceId referente à solicitação. O valor pode apontar para databaseRid, collectionRid ou documentRid, dependendo da operação executada.|
| **clientIpAddress** | **clientIpAddress_s** | Endereço IP do cliente. |
| **requestCharge** | **requestCharge_s** | O número de RUs usadas pela operação |
| **collectionRid** | **collectionId_s** | A ID exclusiva da coleção.|
| **Duração** | **duration_s** | A duração da operação, em milissegundos. |
| **requestLength** | **requestLength_s** | O tamanho da solicitação, em bytes. |
| **responseLength** | **responseLength_s** | O tamanho da resposta, em bytes.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Esse valor não fica vazio quando [tokens de recurso](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) são usados para autenticação. O valor aponta para a ID de recurso do usuário. |

Para obter uma lista de todas as categorias de log do Azure Monitor e links para esquemas associados, consulte [categorias e esquemas do Azure Monitor Logs](../azure-monitor/platform/diagnostic-logs-schema.md). 

## <a name="metrics"></a>Métricas
As tabelas a seguir listam as métricas da plataforma coletadas para o Azure CosmOS DB. Todas as métricas são armazenadas nas **métricas padrão cosmos DB**do namespace .

Para obter uma lista de todas as métricas de suporte ao Azure Monitor (incluindo o CosmosDB), consulte [métricas suportadas pelo Azure Monitor](../azure-monitor/platform/metrics-supported.md). 

#### <a name="request-metrics"></a>Métricas de solicitação
            
|Metric (Nome de exibição métrica)|Unidade (Tipo de Agregação) |Descrição|Dimensões| Granularidades de tempo| Mapeamento de métrica herdada | Uso |
|---|---|---|---| ---| ---| ---|
| Total de solicitações (Total de solicitações) | Contagem (Conde) | Número de solicitações feitas| DatabaseName, CollectionName, Region, StatusCode| Todos | TotalRequests, Http 2xx, Http 3xx, Http 400, Http 401, Erro interno do servidor, Serviço Não Disponível, Solicitações Limitadas, Média de Solicitações por Segundo | Usado para monitorar solicitações por código de status, contêiner a um minuto granularidade. Para obter a média de solicitações por segundo, use a agregação de contagem no minuto e divida por 60. |
| Solicitações de metadados (Solicitações de metadados) |Contagem (Conde) | Contagem de solicitações de metadados. O Azure Cosmos DB mantém o contêiner de metadados do sistema para cada conta, que permite enumerar coleções, bancos de dados, etc., e suas configurações, gratuitamente. | DatabaseName, CollectionName, Region, StatusCode| Todos| |Usada para monitorar os limitadores devido a solicitações de metadados.|
| MongoRequests (Solicitações mongo) | Contagem (Conde) | Número de Solicitações do Mongo Feitas | DatabaseName, CollectionName, Region, CommandName, ErrorCode| Todos |Taxa de Solicitação de Consulta do Mongo, Taxa de Solicitação de Atualização do Mongo, Taxa de Solicitação de Exclusão do Mongo, Taxa de Solicitação de Inserção do Mongo, Taxa de Solicitação de Contagem do Mongo| Usada para monitorar os erros de solicitação do Mongo, usos por tipo de comando. |

#### <a name="request-unit-metrics"></a>Métricas de unidade de solicitação

|Metric (Nome de exibição métrica)|Unidade (Tipo de Agregação)|Descrição|Dimensões| Granularidades de tempo| Mapeamento de métrica herdada | Uso |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Taxa de solicitação de Mongo) | Contagem (Total) |Unidades Solicitadas do Mongo Consumidas| DatabaseName, CollectionName, Region, CommandName, ErrorCode| Todos |Encargo de Solicitação de Consulta do Mongo, Encargo de Solicitação de Atualização do Mongo, Encargo de Solicitação de Exclusão do Mongo, Encargo de Solicitação de Inserção do Mongo, Encargo de Solicitação de Contagem do Mongo| Usada para monitorar RUs de recurso do Mongo em um minuto.|
| Totalde Unidades de Solicitação (Unidades de Solicitação Total)| Contagem (Total) | Unidades Solicitadas Consumidas| DatabaseName, CollectionName, Region, StatusCode |Todos| TotalRequestUnits| Usada para monitorar o uso de RU Total a uma granularidade de minuto. Para obter a média de RU consumida por segundo, use a agregação total no minuto e divida por 60.|
| ProvisionadoThroughput (Throughput provisionado)| Contagem (Máximo) |Throughput provisionado na granularidade do contêiner| Nome do banco de dados, nome do contêiner| 5 M| | Usado para monitorar o throughput provisionado por contêiner.|

#### <a name="storage-metrics"></a>Métricas de armazenamento

|Metric (Nome de exibição métrica)|Unidade (Tipo de Agregação)|Descrição|Dimensões| Granularidades de tempo| Mapeamento de métrica herdada | Uso |
|---|---|---|---| ---| ---| ---|
| Armazenamento disponível (armazenamento disponível) |Bytes (Total) | Armazenamento total disponível relatado em granularidade de 5 minutos por região| DatabaseName, CollectionName, Region| 5 M| Armazenamento disponível| Usada para monitorar a capacidade de armazenamento disponível (aplicável apenas para coleções de armazenamento fixas). A granularidade mínima deve ser de 5 minutos.| 
| Uso de dados (uso de dados) |Bytes (Total) |Uso total de dados relatado em granularidade de 5 minutos por região| DatabaseName, CollectionName, Region| 5 M |Tamanho dos dados | Usado para monitorar o uso total de dados em contêineres e região, a granularidade mínima deve ser de 5 minutos.|
| Utilização de índices (uso de índice) | Bytes (Total) |Uso total do índice relatado em granularidade de 5 minutos por região| DatabaseName, CollectionName, Region| 5 M| Tamanho do Índice| Usado para monitorar o uso total de dados em contêineres e região, a granularidade mínima deve ser de 5 minutos. |
| Cota de Documentos (Cota de Documentos) | Bytes (Total) | Cota total de armazenamento relatada em granularidade de 5 minutos por região.| DatabaseName, CollectionName, Region| 5 M |Capacidade de Armazenamento| Usado para monitorar a cota total em contêiner e região, a granularidade mínima deve ser de 5 minutos.|
| Contagem de documentos (contagem de documentos) | Contagem (Total) |Contagem total de documentos relatada em granularidade de 5 minutos por região| DatabaseName, CollectionName, Region| 5 M |Contagem de documentos|Usado para monitorar a contagem de documentos no contêiner e região, a granularidade mínima deve ser de 5 minutos.|

#### <a name="latency-metrics"></a>Métricas de latência

|Metric (Nome de exibição métrica)|Unidade (Tipo de Agregação)|Descrição|Dimensões| Granularidades de tempo| Uso |
|---|---|---|---| ---| ---|
| Amortização de replicação (Latência de replicação)| Milissegundos (Mínimo, Máximo, Médio) | Latência de replicação P99 nas regiões de origem e de destino para conta habilitada geograficamente| SourceRegion, TargetRegion| Todos | Usada para monitorar a latência de replicação P99 entre quaisquer duas regiões para uma conta com replicação geográfica. |
| Latência lateral do servidor| Milissegundos (Média) | Tempo levado pelo servidor para processar a solicitação. | Nome de coleção, modo de conexão, nome de banco de dados, tipo de operação, publicapitype, região | Todos | Usado para monitorar a latência de solicitação no servidor Azure Cosmos DB. |



#### <a name="availability-metrics"></a>Métricas de disponibilidade

|Metric (Nome de exibição métrica) |Unidade (Tipo de Agregação)|Descrição| Granularidades de tempo| Mapeamento de métrica herdada | Uso |
|---|---|---|---| ---| ---|
| Disponibilidade de serviços (Disponibilidade de serviço)| Percentual (Mínimo, Máximo) | Disponibilidade de solicitações de conta na granularidade de uma hora| 1H | Disponibilidade do serviço | Representa o percentual do total de pedidos aprovados. Uma solicitação será considerada com falha devido a erro de sistema se o código de status for 410, 500 ou 503. Usada para monitorar a disponibilidade da conta na granularidade de hora. |


#### <a name="cassandra-api-metrics"></a>Métricas da API do Cassandra

|Metric (Nome de exibição métrica)|Unidade (Tipo de Agregação)|Descrição|Dimensões| Granularidades de tempo| Uso |
|---|---|---|---| ---| ---|
| Pedidos de Cassandra (Pedidos de Cassandra) | Contagem (Conde) | Número de solicitações da API do Cassandra feitas| DatabaseName, CollectionName, ErrorCode, Region, OperationType, ResourceType| Todos| Usada para monitorar solicitações do Cassandra em uma granularidade de minuto. Para obter a média de solicitações por segundo, use a agregação de contagem no minuto e divida por 60.|
| CassandraRequestCharges (Acusações de pedido de Cassandra) | Conde (Soma, Min, Max, Avg) | Unidades de solicitação consumidas pelas solicitações de API do Cassandra| DatabaseName, CollectionName, Region, OperationType, ResourceType| Todos| Usada para monitorar as RUs usadas por minuto por uma conta de API do Cassandra.|
| CassandraConnectionClosures (Cassandra Connection Closures) |Contagem (Conde) |Número de conexões do Cassandra fechadas| ClosureReason, Region| Todos | Usada para monitorar a conectividade entre os clientes e a API do Cassandra do Azure Cosmos DB.|

## <a name="see-also"></a>Consulte também

- Consulte [Monitorando o Azure Cosmos DB](monitor-cosmos-db.md) para obter uma descrição do monitoramento do Azure Cosmos DB.
- Consulte [Monitorando os recursos do Azure com o Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) para obter detalhes sobre o monitoramento dos recursos do Azure.
