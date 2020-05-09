---
title: Monitorando o armazenamento do Azure | Microsoft Docs
description: Saiba como monitorar o desempenho e a disponibilidade do armazenamento do Azure.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 5564634471045838dae3344dc883b6fdc203711e
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82722915"
---
# <a name="monitoring-azure-storage"></a>Monitoramento do armazenamento do Azure

Quando você tem aplicativos críticos e processos de negócios que dependem de recursos do Azure, você deseja monitorar esses recursos para sua disponibilidade, desempenho e operação. Este artigo descreve os dados de monitoramento gerados pelo armazenamento do Azure e como você pode usar os recursos do Azure Monitor para analisar o alerta sobre esses dados.

> [!NOTE]
> Os logs de armazenamento do Azure no Azure Monitor estão em visualização pública e estão disponíveis para teste de visualização em todas as regiões de nuvem pública. Para se registrar na versão prévia, consulte [esta página](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).  Essa visualização habilita logs para BLOBs (incluindo Azure Data Lake Storage Gen2), arquivos, filas, tabelas, contas de armazenamento Premium nas contas de armazenamento de uso geral v1 e de uso geral v2. Não há suporte para contas de armazenamento clássicas.

## <a name="monitor-overview"></a>Visão geral do monitor

A página de **visão geral** no portal do Azure de cada recurso de armazenamento inclui uma breve exibição do uso de recursos, incluindo sua solicitação e o uso de cobrança por hora. Essas são informações úteis, mas apenas uma pequena quantidade de dados de monitoramento disponíveis. Alguns desses dados são coletados automaticamente e estão disponíveis para análise assim que você cria o recurso de armazenamento. Você pode habilitar tipos adicionais de coleta de dados com algumas configurações.

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?
O armazenamento do Azure cria dados de monitoramento usando [Azure monitor](../../azure-monitor/overview.md) que é um serviço de monitoramento de pilha completo no Azure que fornece um conjunto completo de recursos para monitorar seus recursos do Azure, além de recursos em outras nuvens e locais. 

Comece com o artigo [monitorando os recursos do Azure com Azure monitor](../../azure-monitor/insights/monitor-azure-resource.md) que descreve o seguinte:

- O que é o Azure Monitor?
- Custos associados ao monitoramento
- Monitorando dados coletados no Azure
- Configurar a coleta de dados
- Ferramentas padrão no Azure para analisar e alertar sobre dados de monitoramento

As seções a seguir se baseiam neste artigo descrevendo os dados específicos coletados do armazenamento do Azure e fornecendo exemplos para configurar a coleta de dados e analisar esses dados com as ferramentas do Azure.

## <a name="monitoring-data-from-azure-storage"></a>Monitorando dados do armazenamento do Azure

O armazenamento do Azure coleta os mesmos tipos de dados de monitoramento que outros recursos do Azure, que são descritos em [monitoramento de dados de recursos do Azure](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Consulte [referência de dados de monitoramento do armazenamento do Azure](monitor-storage-reference.md) para obter uma referência detalhada dos logs e métricas criados pelo armazenamento do Azure.

As métricas e os logs no Azure Monitor dão suporte apenas a contas de armazenamento Azure Resource Manager. Azure Monitor não dá suporte a contas de armazenamento clássicas. Se você quiser usar métricas ou logs em uma conta de armazenamento clássico, será necessário migrar para Azure Resource Manager conta de armazenamento. Confira [Migrar para o Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

Você pode continuar usando logs e métricas clássicos se desejar. Na verdade, as métricas e os logs clássicos estão disponíveis em paralelo com métricas e logs em Azure Monitor. O suporte permanece em vigor até que o armazenamento do Azure encerre o serviço em logs e métricas herdados. 

### <a name="logs-in-azure-monitor-preview"></a>Logs em Azure Monitor (visualização)

As entradas de log são criadas somente se há solicitações feitas no ponto de extremidade de serviço. Por exemplo, se uma conta de armazenamento tiver atividade em seu ponto de extremidade de BLOB, mas não em seus pontos de extremidades de tabela ou de fila, somente os logs pertencentes ao serviço blob serão criados. Os logs de armazenamento do Azure contêm informações detalhadas sobre solicitações bem-sucedidas e com falha para um serviço de armazenamento. Essas informações podem ser usadas para monitorar solicitações individuais e diagnosticar problemas com um serviço de armazenamento. As solicitações são registradas em uma base de melhor esforço.

#### <a name="logging-authenticated-requests"></a>Solicitações de registro em log autenticadas

 Os seguintes tipos de solicitações autenticadas são registrados:

- Solicitações bem-sucedidas
- Solicitações com falha, incluindo tempo limite, limitação, rede, autorização e outros erros
- Solicitações que usam uma SAS (Assinatura de Acesso Compartilhado) ou OAuth, incluindo solicitações bem-sucedidas e com falha
- Solicitações para dados de análise (dados de log clássicos no contêiner de **$logs** e dados de métrica de classe nas tabelas de **$Metric** )

As solicitações feitas pelo próprio serviço de armazenamento, como criação ou exclusão de log, não são registradas. Uma lista completa dos dados registrados em log está documentada nos tópicos [operações registradas de armazenamento e mensagens de status](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [formato de log de armazenamento](monitor-storage-reference.md) .

#### <a name="logging-anonymous-requests"></a>Registro em log de solicitações anônimas

 Os seguintes tipos de solicitações anônimas são registrados:

- Solicitações bem-sucedidas
- Erros do servidor
- Erros de tempo limite para o cliente e o servidor
- Solicitações GET com falha com o código de erro 304 (Não Modificado)

Todas as outras solicitações anônimas com falha não estão conectadas. Uma lista completa dos dados registrados em log está documentada nos tópicos [operações registradas de armazenamento e mensagens de status](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [formato de log de armazenamento](monitor-storage-reference.md) .

## <a name="configuration"></a>Configuração

As métricas de plataforma e o log de atividades são coletados automaticamente, mas você deve criar uma configuração de diagnóstico para coletar logs de recursos ou encaminhá-los fora do Azure Monitor. Consulte [criar configuração de diagnóstico para coletar logs e métricas de plataforma no Azure](../../azure-monitor/platform/diagnostic-settings.md) para o processo detalhado para criar uma configuração de diagnóstico usando o portal do Azure, a CLI ou o PowerShell.

Ao criar uma configuração de diagnóstico, você precisará escolher o tipo de armazenamento para o qual deseja habilitar os logs (BLOB, fila, tabela, arquivo). Se você criar a configuração de diagnóstico no portal do Azure, poderá selecionar o recurso em uma lista. Se você usar o PowerShell ou o CLI do Azure, precisará usar a ID de recurso do tipo de armazenamento. Você pode encontrar a ID do recurso no portal do Azure abrindo a página **Propriedades** da sua conta de armazenamento.

Você também precisará especificar a quais categorias de operações os logs serão coletados. As categorias para o armazenamento do Azure são listadas na tabela a seguir:

| Categoria | Descrição |
|:---|:---|
| StorageRead | Operações de leitura em BLOBs.  |
| StorageWrite | Operações de gravação em BLOBs. |
| StorageDelete | Excluir operações em BLOBs. |

## <a name="analyzing-metric-data"></a>Analisando dados de métrica

Você pode analisar métricas para o armazenamento do Azure com métricas de outros serviços do Azure usando o Metrics Explorer. Abra o Metrics Explorer escolhendo **métricas** no menu **Azure monitor** . Consulte [introdução ao Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md) para obter detalhes sobre como usar essa ferramenta. 

O exemplo a seguir mostra como exibir **Transações** no nível da conta.

![captura de tela de acesso às métricas no portal do Azure](./media/monitor-storage/access-metrics-portal.png)

Para métricas com suporte para dimensões, você pode filtrar a métrica com valor da dimensão desejado. O exemplo a seguir mostra como exibir **transações** no nível de conta em uma operação específica, selecionando valores para a dimensão **nome da API**.

![captura de tela de acesso às métricas com dimensão no portal do Azure](./media/monitor-storage/access-metrics-portal-with-dimension.png)

Para obter uma lista completa das dimensões que o armazenamento do Azure dá suporte, consulte [dimensões de métricas](monitor-storage-reference.md#metrics-dimensions).

Todas as métricas para o armazenamento do Azure estão localizadas nos seguintes namespaces:

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices
- Microsoft.Storage/storageAccounts/fileServices
- Microsoft.Storage/storageAccounts/queueServices
- Microsoft.Storage/storageAccounts/tableServices

Para obter uma lista de todas as métricas de suporte de Azure Monitor (incluindo o armazenamento do Azure), consulte [Azure monitor métricas com suporte](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported).


### <a name="access-metrics"></a>Métricas de acesso

> [!TIP]
> Para exibir os exemplos do CLI do Azure ou do .NET, escolha a guia correspondente abaixo.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Listar a definição de métrica

Você pode listar a definição de métrica de sua conta de armazenamento ou o serviço de armazenamento individual, como o serviço BLOB, arquivo, tabela ou fila. Use o cmdlet [Get-AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition?view=azps-3.3.0) .

Neste exemplo, substitua o `<resource-ID>` espaço reservado pela ID de recurso de toda a conta de armazenamento ou a ID de recurso de um serviço de armazenamento individual, como o como o serviço BLOB, arquivo, tabela ou fila. Você pode encontrar essas IDs de recurso nas páginas de **Propriedades** de sua conta de armazenamento no portal do Azure.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="read-metric-values"></a>Ler valores de métrica

Você pode ler os valores de métrica de nível de conta de sua conta de armazenamento ou o serviço de armazenamento individual, como o serviço BLOB, arquivo, tabela ou fila. Use o cmdlet [Get-AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric?view=azps-3.3.0) .

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

#### <a name="list-account-level-metric-definition"></a>Listar definição de métrica de nível de conta

Você pode listar a definição de métrica de sua conta de armazenamento ou o serviço de armazenamento individual, como o serviço BLOB, arquivo, tabela ou fila. Use o comando [AZ monitor da lista de métricas-definições](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list-definitions) .
 
Neste exemplo, substitua o `<resource-ID>` espaço reservado pela ID de recurso de toda a conta de armazenamento ou a ID de recurso de um serviço de armazenamento individual, como o como o serviço BLOB, arquivo, tabela ou fila. Você pode encontrar essas IDs de recurso nas páginas de **Propriedades** de sua conta de armazenamento no portal do Azure.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Ler valores de métrica no nível da conta

Você pode ler os valores de métrica de sua conta de armazenamento ou o serviço de armazenamento individual, como o serviço BLOB, arquivo, tabela ou fila. Use o comando [AZ monitor Metrics List](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) .

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Azure Monitor fornece o [SDK do .net](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) para ler a definição e os valores da métrica. O [código de exemplo](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) mostra como usar o SDK com parâmetros diferentes. Você precisa usar `0.18.0-preview` ou versão posterior para métricas de armazenamento.
 
Nesses exemplos, substitua o `<resource-ID>` espaço reservado pela ID de recurso de toda a conta de armazenamento ou a ID de recurso de um serviço de armazenamento individual, como o como o serviço BLOB, arquivo, tabela ou fila. Você pode encontrar essas IDs de recurso nas páginas de **Propriedades** de sua conta de armazenamento no portal do Azure.

Substitua a `<subscription-ID>` variável pela ID da sua assinatura.  Para obter orientação sobre como obter valores para o `<tenant-ID>`, `<application-ID>`e `<AccessKey>`, consulte [como: usar o portal para criar um aplicativo do Azure AD e uma entidade de serviço que pode acessar recursos](https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/). 

#### <a name="list-account-level-metric-definition"></a>Listar definição de métrica de nível de conta

Os exemplos a seguir mostram como listar a definição de métrica no nível da conta:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";


        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
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

#### <a name="read-account-level-metric-values"></a>Ler valores de métrica no nível da conta

O exemplo a seguir mostra como ler os dados `UsedCapacity` no nível da conta:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

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
            // Enumrate metric value
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

#### <a name="read-multi-dimensional-metric-values"></a>Ler valores de métrica multidimensionais

Para métricas multidimensionais, você precisa definir o filtro de metadados se você quiser ler dados da métrica no valor de dimensão específica.

O exemplo a seguir mostra como ler dados da métrica na métrica que suportam várias dimensões:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

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

---

## <a name="analyzing-log-data"></a>Analisando dados de log

Você pode acessar os logs de recursos como um blob em uma conta de armazenamento, como dados de evento ou por meio de consultas analíticas de log.

Consulte [referência de dados de monitoramento do armazenamento do Azure](monitor-storage-reference.md) para obter uma referência detalhada dos campos que aparecem nesses logs.

> [!NOTE]
> Os logs de armazenamento do Azure no Azure Monitor estão em visualização pública e estão disponíveis para teste de visualização em todas as regiões de nuvem pública. Para se registrar na versão prévia, consulte [esta página](https://www.microsoft.com).  Essa visualização habilita logs para BLOBs (incluindo Azure Data Lake Storage Gen2), arquivos, filas, tabelas, contas de armazenamento Premium nas contas de armazenamento de uso geral v1 e de uso geral v2. Não há suporte para contas de armazenamento clássicas.

### <a name="access-logs-in-a-storage-account"></a>Acessar logs em uma conta de armazenamento

Os logs aparecem como BLOBs armazenados em um contêiner na conta de armazenamento de destino. Os dados são coletados e armazenados dentro de um único blob como uma carga JSON delimitada por linha. O nome do blob segue a convenção de nomenclatura abaixo:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Aqui está um exemplo:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="access-logs-in-event-hub"></a>Acessar logs no Hub de eventos

Os logs enviados para um hub de eventos não são armazenados como um arquivo, mas você pode verificar se o Hub de eventos recebeu as informações de log. No portal do Azure, navegue até o Hub de eventos e verifique se a contagem de **mensagens de entrada** é maior que zero. 

![Logs de auditoria](media/monitor-storage/event-hub-log.png)

Você pode acessar e ler dados de log que são enviados ao seu hub de eventos usando as ferramentas de gerenciamento e monitoramento de eventos e informações de segurança. Para obter mais informações, consulte [o que posso fazer com os dados de monitoramento que estão sendo enviados para meu Hub de eventos?](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub).

### <a name="access-logs-in-log-analytics-workspace"></a>Acessar logs no espaço de trabalho Log Analytics

Você pode acessar os logs enviados a um espaço de trabalho do Log Analytics, usando consultas de log de Azure Monitor.

Consulte [introdução ao log Analytics no Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

Os dados são armazenados nas tabelas a seguir.

| Tabela | Descrição |
|:---|:---|
|StorageBlobLogs | Logs que descrevem a atividade no armazenamento de BLOBs. |
|StorageFileLogs | Logs que descrevem a atividade em compartilhamentos de arquivos. |
|StorageQueueLogs | Logs que descrevem a atividade em filas.|
|StorageTableLogs| Logs que descrevem a atividade em tabelas.|

### <a name="azure-storage-log-analytics-queries-in-azure-monitor"></a>Consultas de Log Analytics de armazenamento do Azure no Azure Monitor

Aqui estão algumas consultas que você pode inserir na barra de pesquisa da **pesquisa de logs** para ajudá-lo a monitorar suas contas de armazenamento do Azure. Essas consultas funcionam com a [nova linguagem](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

Veja a seguir as consultas que você pode usar para ajudá-lo a monitorar suas contas de armazenamento do Azure.

* Para listar 10 erros mais comuns nos últimos 3 dias.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* Para listar as 10 principais operações que causam mais erros nos últimos 3 dias.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* Para listar as 10 principais operações com a latência de ponta a ponta mais longa nos últimos 3 dias.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* Para listar todas as operações que causam erros de limitação do servidor nos últimos 3 dias.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* Para listar todas as solicitações com acesso anônimo nos últimos 3 dias.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* Para criar um gráfico de pizza de operações usadas nos últimos 3 dias.
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>Perguntas frequentes

**Armazenamento do Azure dá suporte a métricas para gerenciados discos ou discos não gerenciado?**

Não, a computação do Azure suporta as métricas em discos. Consulte [artigo](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/) para obter mais detalhes.

## <a name="next-steps"></a>Próximas etapas

- [Referência de dados de monitoramento do armazenamento do Azure](monitor-storage-reference.md) para obter uma referência dos logs e métricas criados pelo armazenamento do Azure.
- [Monitoramento de recursos do Azure com Azure monitor](../../azure-monitor/insights/monitor-azure-resource.md) para obter detalhes sobre como monitorar recursos do Azure.
- [Migração de métricas de Armazenamento do Azure](./storage-metrics-migration.md)

