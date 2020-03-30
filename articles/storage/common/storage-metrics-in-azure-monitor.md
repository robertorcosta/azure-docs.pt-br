---
title: Métricas do Armazenamento do Azure no Azure Monitor | Microsoft Docs
description: Saiba mais sobre as novas métricas oferecidas pelo Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 73181222bf3f15bbbac24fc253eddfea1c57bc6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247088"
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Métricas do Armazenamento do Azure no Azure Monitor

Com as métricas do Armazenamento do Azure, você pode analisar tendências de uso, rastrear solicitações e diagnosticar problemas com a sua conta de armazenamento.

O Azure Monitor fornece interfaces de usuário unificadas para monitoramento em diferentes serviços do Azure. Para saber mais, consulte [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md). O Armazenamento do Azure integra-se ao Azure Monitor, enviando dados de métrica para a plataforma do Azure Monitor.

## <a name="access-metrics"></a>Métricas de acesso

O Azure Monitor fornece várias maneiras de acessar as métricas. Você pode acessá-los a partir do [portal Azure,](https://portal.azure.com)as APIs do Monitor Azure (REST e .NET) e soluções de análise, como Event Hubs. Para saber mais, consulte [Métricas do Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

As métricas são ativadas por padrão e você pode acessar os últimos 93 dias de dados. Se você precisar manter os dados por um período de tempo maior, você pode arquivar os dados de métrica em uma conta de Armazenamento do Azure. Isso pode ser configurado em [configurações de diagnóstico](../../azure-monitor/platform/platform-logs-overview.md) no Azure Monitor.

### <a name="access-metrics-in-the-azure-portal"></a>Acessar as métricas no portal do Azure

Você pode monitorar as métricas ao longo do tempo no portal do Azure. O exemplo a seguir mostra como exibir **Transações** no nível da conta.

![captura de tela de acesso às métricas no portal do Azure](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

Para métricas com suporte para dimensões, você pode filtrar a métrica com valor da dimensão desejado. O exemplo a seguir mostra como exibir **transações** no nível de conta em uma operação específica, selecionando valores para a dimensão **nome da API**.

![captura de tela de acesso às métricas com dimensão no portal do Azure](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Acessar métricas com a API REST

O Azure Monitor fornece [APIs REST](/rest/api/monitor/) para ler valores e definição de métricas. Esta seção mostra como ler as métricas de armazenamento. A ID do recurso é usada em todas as APIs REST. Para obter mais informações, leia Noções básicas sobre a ID de recurso para serviços no Armazenamento.

O exemplo a seguir mostra como usar [ArmClient](https://github.com/projectkudu/ARMClient) na linha de comando para simplificar o teste com a API REST.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Listar a definição de métrica no nível conta com a API REST

O exemplo a seguir mostra como listar a definição de métrica no nível da conta:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2018-01-01

```

Se você deseja listar as definições de métrica para o blob, tabela, arquivo ou fila, você deve especificar IDs de recurso diferentes para cada serviço com a API.

A resposta contém a definição da métrica no formato JSON:

```Json
{
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions/UsedCapacity",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
      "category": "Capacity",
      "name": {
        "value": "UsedCapacity",
        "localizedValue": "Used capacity"
      },
      "isDimensionRequired": false,
      "unit": "Bytes",
      "primaryAggregationType": "Average",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D"
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D"
        }
      ]
    },
    ... next metric definition
  ]
}

```

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Ler os valores de métrica no nível conta com a API REST

O exemplo a seguir mostra como ler os dados de métrica no nível da conta:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metricnames=Availability&api-version=2018-01-01&aggregation=Average&interval=PT1H"

```

No exemplo acima, se você deseja ler os valores de métrica para o blob, tabela, arquivo ou fila, você deve especificar IDs de recurso diferentes para cada serviço com a API.

A resposta a seguir contém os valores da métrica no formato JSON:

```Json
{
  "cost": 0,
  "timespan": "2017-09-07T17:27:41Z/2017-09-07T18:27:41Z",
  "interval": "PT1H",
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/Microsoft.Insights/metrics/Availability",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Availability",
        "localizedValue": "Availability"
      },
      "unit": "Percent",
      "timeseries": [
        {
          "metadatavalues": [],
          "data": [
            {
              "timeStamp": "2017-09-07T17:27:00Z",
              "average": 100.0
            }
          ]
        }
      ]
    }
  ]
}

```

### <a name="access-metrics-with-the-net-sdk"></a>Métricas de acesso com o .NET SDK

O Azure Monitor fornece [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) para ler definição métrica e valores. O [código de exemplo](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) mostra como usar o SDK com parâmetros diferentes. Você precisa usar `0.18.0-preview` ou versão posterior para métricas de armazenamento. O ID de recurso é usado no .NET SDK. Para obter mais informações, leia Noções básicas sobre a ID de recurso para serviços no Armazenamento.

O exemplo a seguir mostra como usar o Azure Monitor .NET SDK para ler métricas de armazenamento.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Listar definição métrica de nível de conta com o .NET SDK

O exemplo a seguir mostra como listar a definição de métrica no nível da conta:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        // Using metrics in Azure Monitor is currently free. However, if you use additional solutions ingesting metrics data, you may be billed by these solutions. For example, you are billed by Azure Storage if you archive metrics data to an Azure Storage account. Or you are billed by Operation Management Suite (OMS) if you stream metrics data to OMS for advanced analysis.
        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            //Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

Se você deseja listar as definições de métrica para o blob, tabela, arquivo ou fila, você deve especificar IDs de recurso diferentes para cada serviço com a API.

#### <a name="read-metric-values-with-the-net-sdk"></a>Leia os valores métricos com o .NET SDK

O exemplo a seguir mostra como ler os dados `UsedCapacity` no nível da conta:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

No exemplo acima, se você deseja ler os valores de métrica para o blob, tabela, arquivo ou fila, você deve especificar IDs de recurso diferentes para cada serviço com a API.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Leia valores métricos multidimensionais com o .NET SDK

Para métricas multidimensionais, você precisa definir o filtro de metadados se você quiser ler dados da métrica no valor de dimensão específica.

O exemplo a seguir mostra como ler dados da métrica na métrica que suportam várias dimensões:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Noções básicas sobre a ID de recurso para serviços no Armazenamento do Azure

A ID de recurso é um identificador exclusivo de um recurso no Azure. Quando você usa a API REST do Azure Monitor para ler definições ou valores de métricas, você deve usar a ID de recurso para o recurso no qual você pretende operar. O modelo de ID de recurso segue este formato:

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

O Armazenamento fornece métricas no nível da conta de armazenamento e no nível de serviço com o Azure Monitor. Por exemplo, você pode recuperar as métricas somente para armazenamento de Blob. Cada nível tem sua própria ID de recurso, que é usada para recuperar as métricas somente para aquele nível.

### <a name="resource-id-for-a-storage-account"></a>ID de recurso para uma conta de armazenamento

O exemplo a seguir mostra o formato para especificar a ID de recurso para uma conta de armazenamento.

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
```

### <a name="resource-id-for-the-storage-services"></a>ID de recurso para os serviços de armazenamento

O exemplo a seguir mostra o formato para especificar a ID de recurso para cada um dos serviços de armazenamento.

* ID de recurso do serviço Blob 
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
```
* ID de recurso do serviço Tabela 
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
```
* ID de recurso do serviço Fila 
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
```
* ID de recurso do serviço Arquivo 
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
```

### <a name="resource-id-in-azure-monitor-rest-api"></a>ID de recurso na API REST do Azure Monitor

O exemplo a seguir mostra o padrão usado ao chamar a API REST do Azure Monitor.

```
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
```

## <a name="capacity-metrics"></a>Métricas de capacidade
Os valores de métricas de capacidade são enviados para o Azure Monitor a cada hora. Os valores são atualizados diariamente. O intervalo de agregação define o intervalo de tempo para o qual os valores das métricas são apresentados. O intervalo de agregação compatível com todas as métricas de capacidade é uma hora (PT1H).

O Armazenamento do Azure fornece as seguintes métricas de capacidade no Azure Monitor.

### <a name="account-level"></a>Nível de conta

| Nome da métrica | Descrição |
| ------------------- | ----------------- |
| UsedCapacity | A quantidade de armazenamento utilizada pela conta de armazenamento. Para contas de armazenamento Standard, é a soma da capacidade usada por blob, tabela, arquivo e fila. Para contas de armazenamento Premium e contas de armazenamento de Blob, é o mesmo que Capacidade de Blob. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

### <a name="blob-storage"></a>Armazenamento de blob

| Nome da métrica | Descrição |
| ------------------- | ----------------- |
| BlobCapacity | O total de armazenamento de Blob usado na conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 <br/> Dimensões: **BlobType**e **BlobTier** [(Definição)](#metrics-dimensions) |
| BlobCount    | O número de objetos blob armazenados na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 <br/> Dimensões: **BlobType**e **BlobTier** [(Definição)](#metrics-dimensions) |
| ContainerCount    | O número de contêineres na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| IndexCapacity     | A quantidade de armazenamento usado pelo Índice ADLS Gen2 Hierárquico <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

### <a name="table-storage"></a>Armazenamento de tabela

| Nome da métrica | Descrição |
| ------------------- | ----------------- |
| TableCapacity | A quantidade de armazenamento de Tabelas usada pela conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| TableCount   | O número de tabelas em uma conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| TableEntityCount | O número de entidades de tabela na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

### <a name="queue-storage"></a>Armazenamento de filas

| Nome da métrica | Descrição |
| ------------------- | ----------------- |
| QueueCapacity | A quantidade de armazenamento de fila usada pela conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| QueueCount   | O número de filas em uma conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| QueueMessageCount | O número de mensagens da fila não expiradas na conta de armazenamento. <br/><br/>Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

### <a name="file-storage"></a>Armazenamento de arquivos

| Nome da métrica | Descrição |
| ------------------- | ----------------- |
| FileCapacity | A quantidade de armazenamento de Arquivos usada pela conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| FileCount   | O número de arquivos na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| FileShareCount | O número de compartilhamentos de arquivos na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

## <a name="transaction-metrics"></a>Métricas de transação

As métricas de transação são emitidas, do Armazenamento do Azure para o Azure Monitor, em cada solicitação a uma conta de armazenamento. Caso não haja nenhuma atividade em sua conta de armazenamento, não haverá nenhum dado nas métricas de transações no período. Todas as métricas de transação estão disponíveis no nível de conta e de serviço (armazenamento de Blob, armazenamento de Tabelas, Azure Files e armazenamento de fila). O intervalo de agregação define o intervalo de tempo para o qual os valores das métricas são apresentados. Os intervalos de agregação para todas as métricas de transação são PT1H e PT1M.

O Armazenamento do Azure fornece as seguintes métricas de transação no Azure Monitor.

| Nome da métrica | Descrição |
| ------------------- | ----------------- |
| Transactions | O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensões aplicáveis: ResponseType, GeoType, ApiName e autenticação ([definição](#metrics-dimensions))<br/> Exemplo de valor: 1024 |
| Entrada | A quantidade de dados de entrada. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensões aplicáveis: GeoType, ApiName e autenticação ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| Saída | A quantidade de dados de saída. Esse número inclui saída para um cliente externo do Azure Storage, bem como saída dentro do Azure. Como resultado, esse número não reflete a saída faturável. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensões aplicáveis: GeoType, ApiName e autenticação ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| SuccessServerLatency | O tempo médio usado para processar uma solicitação bem-sucedida pelo Armazenamento do Azure. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency. <br/><br/> Unidade: Milissegundos <br/> Tipo de agregação: Média <br/> Dimensões aplicáveis: GeoType, ApiName e autenticação ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| SuccessE2ELatency | A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta. <br/><br/> Unidade: Milissegundos <br/> Tipo de agregação: Média <br/> Dimensões aplicáveis: GeoType, ApiName e autenticação ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| Disponibilidade | O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor total de solicitações faturáveis e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada. <br/><br/> Unidade: Porcentagem <br/> Tipo de agregação: Média <br/> Dimensões aplicáveis: GeoType, ApiName e autenticação ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 99.99 |

## <a name="metrics-dimensions"></a>Dimensões das métricas

O Armazenamento do Azure oferece suporte às seguintes dimensões para métricas no Azure Monitor.

| Nome da dimensão | Descrição |
| ------------------- | ----------------- |
| **BlobType** | O tipo de blob somente para métricas de Blob. Os valores suportados são **BlockBlob,** **PageBlob**e **Azure Data Lake Storage**. Acrescentar Blob está incluído no BlockBlob. |
| **BlobTier** | O armazenamento do Azure oferece diferentes níveis de acesso, que permitem armazenar dados de objetos blob da maneira mais econômica. Veja mais em [Azure Storage blob tier](../blobs/storage-blob-storage-tiers.md). Os valores suportados incluem: <br/> <li>**Quente**: Nível quente</li> <li>**Legal**: Nível legal</li> <li>**Arquivo**: Nível de arquivamento</li> <li>**Premium**: Nível premium para bolha de bloco</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**: Tipos de níveis para blob de página premium</li> <li>**Padrão**: Tipo de camada para a página padrão Blob</li> <li>**Untiered**: Tipo de camada para a conta de armazenamento v1 de propósito geral</li> |
| **GeoType** | Transação de cluster primário ou secundário. Os valores disponíveis incluem **primário** e **secundário.** Aplica-se ao acesso de leitura ao armazenamento com redundância geográfica (RA-GRS) ao ler objetos de um locatário secundário. |
| **Tipo de resposta** | Tipo de resposta da transação. Os valores disponíveis incluem: <br/><br/> <li>**ServerOtherError**: Todos os outros erros do lado do servidor, exceto os descritos </li> <li>**ServerBusyError**: Solicitação autenticada que retornou um código de status HTTP 503. </li> <li>**ServerTimeoutError**: Solicitação autenticada com tempo de saída que retornou um código de status HTTP 500. O tempo limite foi ultrapassado devido a um erro no servidor. </li> <li>**AutorizaçãoErro**: Solicitação autenticada que falhou devido ao acesso não autorizado de dados ou uma falha de autorização. </li> <li>**Erro de rede**: Solicitação autenticada que falhou devido a erros de rede. Geralmente ocorre quando um cliente fecha prematuramente uma conexão antes da expiração do tempo limite. </li>  <li>**ClientAccountBandwidthThrottlingError**: A solicitação é estrangulada na largura de banda para exceder os [limites de escalabilidade da conta de armazenamento](scalability-targets-standard-account.md).</li><li>**ClientAccountRequestThrottlingError**: A solicitação é estrangulada na taxa de solicitação para exceder os [limites de escalabilidade da conta de armazenamento](scalability-targets-standard-account.md).<li>**ClientThrottlingError**: Outro erro de estrangulamento do lado do cliente. ClientAccountBandwidthThrottlingError e ClientAccountRequestThrottlingError são excluídos.</li> <li>**ClientTimeoutError**: Solicitação autenticada com tempo de saída que retornou um código de status HTTP 500. Se o tempo limite de rede do cliente ou o tempo limite da solicitação for definido como um valor menor do que o esperado pelo serviço de armazenamento, ele é considerado um tempo limite esperado. Caso contrário, ele será relatado como um ServerTimeoutError.</li> </li> <li>**ClientOtherError**: Todos os outros erros do lado do cliente, exceto os descritos. </li> <li>**Sucesso**: Pedido de sucesso</li> <li> **SucessoComThrottling**: Solicitação bem sucedida quando um cliente SMB é estrangulado na primeira tentativa, mas é bem sucedido após tentativas.</li> |
| **ApiName** | O nome da operação. Por exemplo:  <br/> <li>**Criar contêiner**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> Para saber todos os nomes de operação, consulte [documento](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
| **Autenticação** | Tipo de autenticação usado em transações. Os valores disponíveis incluem: <br/> <li>**AccountKey**: A transação é autenticada com a chave da conta de armazenamento.</li> <li>**SAS**: A transação é autenticada com assinaturas de acesso compartilhadas.</li> <li>**OAuth**: A transação é autenticada com tokens de acesso OAuth.</li> <li>**Anônimo**: A transação é solicitada anonimamente. Não inclui pedidos de pré-vôo.</li> <li>**Flexflight Anônimo**: A transação é solicitação de pré-vôo.</li> |

Para saber as dimensões de métricas com suporte, você precisa especificar o valor da dimensão para exibir os valores correspondentes das métricas. Por exemplo, se você pesquisar o valor de **Transações** para respostas com sucesso, você precisa filtrar a dimensão **ResponseType** por **Sucesso**. Ou se você pesquisar o valor de **BlobCount** para o Blob de Blocos, você precisa filtrar a dimensão **BlobType** por **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>Continuidade do serviço de métricas herdadas

As métricas herdadas estão disponíveis paralelamente às métricas gerenciadas do Azure Monitor. O suporte é o mesmo até que o Armazenamento do Azure conclua o serviço nas métricas herdados.

## <a name="faq"></a>Perguntas frequentes

**As novas métricas dão suporte à conta de armazenamento clássica?**

Não, as novas métricas do Azure Monitor oferecem suporte apenas às contas de armazenamento do Azure Resource Manager. Se você quiser usar as métricas nas contas de armazenamento, precisará migrar para a conta de armazenamento do Azure Resource Manager. Confira [Migrar para o Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

**Armazenamento do Azure dá suporte a métricas para gerenciados discos ou discos não gerenciado?**

Não, a computação do Azure suporta as métricas em discos. Consulte [artigo](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/) para obter mais detalhes.

**Como mapear e migrar métricas clássicas com novas métricas?**

Você pode encontrar um mapeamento detalhado entre métricas clássicas e novas métricas na [Migração de métricas de Armazenamento do Azure](./storage-metrics-migration.md).

## <a name="next-steps"></a>Próximas etapas

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
