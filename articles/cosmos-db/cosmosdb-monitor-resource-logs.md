---
title: Monitore os dados do Azure Cosmos DB usando as configurações do Azure Diagnostic
description: Saiba como usar as configurações do Azure Diagnostic para monitorar o desempenho e a disponibilidade de dados armazenados no Azure Cosmos DB
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: sngun
ms.openlocfilehash: f5a0b0f71a72ea76940450f73354fda230e09c5c
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521039"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Monitore os dados do Azure Cosmos DB usando configurações de diagnóstico no Azure

As configurações de diagnóstico no Azure são usadas para coletar registros de recursos. Os logs de recursos do Azure são emitidos por um recurso e fornecem dados ricos e frequentes sobre o funcionamento desse recurso. Esses registros são capturados por solicitação e também são chamados de "registros de data plane". Alguns exemplos das operações do plano de dados incluem excluir, inserir e lerFeed. O conteúdo desses logs varia de acordo com o tipo de recurso.

As métricas da plataforma e os logs de atividade são coletados automaticamente, enquanto você deve criar uma configuração de diagnóstico para coletar logs de recursos ou encaminhá-los fora do Azure Monitor. Você pode ativar a configuração de diagnóstico para contas do Azure Cosmos usando as seguintes etapas:

1. Inscreva-se no [portal Azure](https://portal.azure.com).

1. Navegue até a conta do Azure Cosmos. Abra o painel **de configurações de diagnóstico** e, em seguida, selecione Adicionar opção de **configuração de diagnóstico.**

1. No painel **de configurações de diagnóstico,** preencha o formulário com os seguintes detalhes: 

    * **Nome**: insira um nome para os logs a serem criados.

    * Você pode armazenar os logs **do Archive para uma conta de armazenamento,** transmitir para um hub de **eventos** ou enviar para o **Log Analytics**

1. Quando você cria uma configuração de diagnóstico, você especifica qual categoria de logs a coletar. As categorias de logs suportados pelo Azure Cosmos DB estão listadas abaixo, juntamente com o registro de amostra coletado por eles:

 * **DataPlaneRequests**: Selecione esta opção para registrar solicitações de back-end para todas as APIs, que incluem contas SQL, Graph, MongoDB, Cassandra e Table API no Azure Cosmos DB. As principais propriedades `Requestcharge` `statusCode`a `clientIPaddress`serem `partitionID`observados são: , , e .

    ```json
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**: Selecione esta opção para registrar solicitações iniciadas pelo usuário a partir do front-end para atender solicitações à API do Azure Cosmos DB para MongoDB. Este tipo de log não está disponível para outras contas de API. As principais propriedades `Requestcharge` `opCode`a serem observados são: . Quando você habilita ristindo MongoRequests em registros de diagnósticos, certifique-se de desativar as Solicitações de DataPlane. Você veria um registro para cada solicitação feita na API.

    ```json
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **CassandraRequests**: Selecione esta opção para registrar solicitações iniciadas pelo usuário a partir do front-end para atender solicitações à API do Azure Cosmos DB para Cassandra. Este tipo de log não está disponível para outras contas de API. As propriedades-chave `operationName`a `requestCharge` `piiCommandText`serem anotadas são, . Quando você habilitar CassandraRequests em registros de diagnósticos, certifique-se de desativar as Solicitações de DataPlane. Você veria um registro para cada solicitação feita na API.

   ```json
   { "time": "2020-03-30T23:55:10.9579593Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "CassandraRequests", "operationName": "QuerySelect", "properties": {"activityId": "6b33771c-baec-408a-b305-3127c17465b6","opCode": "<empty>","errorCode": "-1","duration": "0.311900","requestCharge": "1.589237","databaseName": "system","collectionName": "local","retryCount": "<empty>","authorizationTokenType": "PrimaryMasterKey","address": "104.42.195.92","piiCommandText": "{"request":"SELECT key from system.local"}","userAgent": """"}}
   ```

* **QueryRuntimeStatistics**: Selecione esta opção para registrar o texto de consulta que foi executado. Este tipo de log está disponível apenas para contas API SQL.

    ```json
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**: Selecione esta opção para registrar as estatísticas das teclas de partição. Isso é atualmente representado com o tamanho de armazenamento (KB) das teclas de partição. Consulte os [problemas de solução de problemas usando a](#diagnostic-queries) seção de consultas do Azure Diagnostic deste artigo. Por exemplo, consultas que usam "PartitionKeyStatistics". O log é emitido contra as três primeiras chaves de partição que ocupam a maioria do armazenamento de dados. Este registro contém dados como ID de assinatura, nome da região, nome do banco de dados, nome da coleção, chave de partição e tamanho de armazenamento em KB.

    ```json
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **PartitionKeyRUConsumption**: Este registro relata o consumo agregado por segundo de RU/s de chaves de partição. Atualmente, o Azure Cosmos DB informa as chaves de partição apenas para contas SQL API e para operações de procedimento de leitura/gravação e armazenada de pontos. outras APIs e tipos de operação não são suportados. Para outras APIs, a coluna de tecla de partição na tabela de registro de diagnóstico estará vazia. Este registro contém dados como ID de assinatura, nome da região, nome do banco de dados, nome da coleção, chave de partição, tipo de operação e taxa de solicitação. Consulte os [problemas de solução de problemas usando a](#diagnostic-queries) seção de consultas do Azure Diagnostic deste artigo. Por exemplo, consultas que usam "PartitionKeyRUConsumption". 

* **ControlPlaneRequests**: Este registro contém detalhes sobre operações de plano de controle, como criar uma conta, adicionar ou remover uma região, atualizar as configurações de replicação da conta etc. Este tipo de log está disponível para todos os tipos de API que incluem SQL (Core), MongoDB, Gremlin, Cassandra, Table API.

* **Solicitações**: Selecione esta opção para coletar dados métricos do Azure Cosmos DB para os destinos na configuração de diagnóstico. Estes são os mesmos dados coletados automaticamente no Azure Metrics. Coletar dados métricos com registros de recursos para analisar os dois tipos de dados juntos e enviar dados métricos fora do Azure Monitor.

Para obter informações detalhadas sobre como criar uma configuração de diagnóstico usando o portal Azure, CLI ou PowerShell, consulte [Criar configuração de diagnóstico para coletar registros e métricas da plataforma no artigo do Azure.](../azure-monitor/platform/diagnostic-settings.md)


## <a name="troubleshoot-issues-with-diagnostics-queries"></a><a id="diagnostic-queries"></a>Solucionando problemas com consultas de diagnóstico

1. Como obter as taxas de solicitação para consultas caras?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 10.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
   AzureDiagnostics
   | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
   | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. Como descobrir quais operações estão tomando a maior parte dos RU/s?

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
1. Como obter a distribuição para diferentes operações?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. Qual é o throughput máximo que uma partição consumiu?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. Como obter as informações sobre as teclas de partição de consumo RU/s por segundo?

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. Como obter a taxa de solicitação de uma chave de partição específica

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. Como obter as chaves de partição superior com a maioria dos RU/s consumidos em um período específico? 

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
    ```

1. Como obter os logs das teclas de partição cujo tamanho de armazenamento é maior que 8 GB?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

1. Como obter partição Estatísticas-chave para avaliar distorções entre as três principais partições para conta de banco de dados?

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="next-steps"></a>Próximas etapas

* [Monitor Azure para Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [Monitorar e depurar com métricas no Azure Cosmos DB](use-metrics.md)
