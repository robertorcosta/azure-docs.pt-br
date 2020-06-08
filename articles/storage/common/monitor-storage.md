---
title: Monitorar o Armazenamento do Microsoft Azure | Microsoft Docs
description: Saiba como monitorar o desempenho e a disponibilidade do Armazenamento do Microsoft Azure.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring
ms.openlocfilehash: 3ede22b5af942c3f0c0cd88d86b56a625c7656c0
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267606"
---
# <a name="monitor-azure-storage"></a>Monitorar o Armazenamento do Microsoft Azure

Quando você tem aplicativos e processos de negócios críticos que dependem de recursos do Azure, recomendamos monitorar a disponibilidade, o desempenho e a operação desses recursos. Este artigo descreve os dados de monitoramento gerados pelo Armazenamento do Microsoft Azure e como usar os recursos do Azure Monitor para analisar alertas sobre esses dados.

> [!NOTE]
> Os logs do Armazenamento do Microsoft Azure no Azure Monitor estão em versão preliminar pública e disponíveis para teste de versão preliminar em todas as regiões de nuvem pública. Para se inscrever na versão preliminar, veja [esta página](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Essa versão preliminar habilita logs para blobs (que incluem Azure Data Lake Storage Gen2), arquivos, filas, tabelas, contas de armazenamento Premium nas contas de armazenamento GPv1 e GPv2. Não há suporte para contas de armazenamento clássicas.

## <a name="monitor-overview"></a>Visão geral do Monitor

A página **Visão Geral** no portal do Azure de cada recurso de armazenamento contém um resumo do uso de recurso, como solicitações e cobrança por hora. Essas informações são úteis, mas apenas uma pequena quantidade de dados de monitoramento está disponível. Alguns desses dados são coletados automaticamente e ficam disponíveis para análise assim que você cria o recurso de armazenamento. Você pode habilitar tipos adicionais de coleta de dados com algumas configurações.

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?
O Armazenamento do Microsoft Azure cria dados de monitoramento usando o [Azure Monitor](../../azure-monitor/overview.md), que é um serviço de monitoramento de pilha completo no Azure. O Azure Monitor oferece um conjunto completo de recursos para monitorar os recursos do Azure e recursos em outras nuvens e locais. 

Para saber mais sobre o Azure Monitor, consulte [Como monitorar recursos do Azure com o Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md). O artigo descreve os seguintes assuntos:

- O que é o Azure Monitor?
- Custos associados ao monitoramento
- Monitoramento de dados coletados no Azure
- Configuração de coleta de dados
- Ferramentas padrão no Azure para analisar e alertar sobre dados de monitoramento

As seções a seguir se baseiam neste artigo para descrever os dados específicos coletados do Armazenamento do Azure. Os exemplos mostram como configurar a coleta de dados e analisar esses dados com as ferramentas do Azure.

## <a name="monitor-data-from-azure-storage"></a>Monitorar dados do Armazenamento do Microsoft Azure

O Armazenamento do Microsoft Azure coleta os mesmos tipos de dados de monitoramento que outros recursos do Azure, que são descritos em [Dados de monitoramento dos recursos do Azure](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Para mais informações sobre os logs e as métricas criados pelo Armazenamento do Azure, consulte a [referência de dados de monitoramento do Armazenamento do Microsoft Azure](monitor-storage-reference.md).

As métricas e os logs do Azure Monitor oferecem suporte apenas às contas de armazenamento do Azure Resource Manager. O Azure Monitor não tem suporte a contas de armazenamento clássicas. Se você quiser usar as métricas ou os logs nas contas de armazenamento clássicas, precisará migrar para a conta de armazenamento do Azure Resource Manager. Confira [Migrar para o Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

Você pode continuar usando logs e métricas clássicos se desejar. Na verdade, as métricas e os logs clássicos estão disponíveis em paralelo com as métricas e os logs no Azure Monitor. O suporte permanece disponível até que o Armazenamento do Microsoft Azure encerre o serviço nas métricas e nos logs herdados.

### <a name="logs-in-azure-monitor-preview"></a>Logs no Azure Monitor (versão prévia)

As entradas de log são criadas somente se há solicitações feitas no ponto de extremidade de serviço. Por exemplo, se uma conta de armazenamento tiver atividades no ponto de extremidade de blob, mas não em nos pontos de extremidade de tabela ou fila, somente os logs pertencentes ao serviço de armazenamento de blobs serão criados. Os logs do Armazenamento do Microsoft Azure contêm informações detalhadas sobre solicitações bem-sucedidas e com falha para um serviço de armazenamento. Essas informações podem ser usadas para monitorar solicitações individuais e diagnosticar problemas com um serviço de armazenamento. As solicitações são registradas em uma base de melhor esforço.

#### <a name="log-authenticated-requests"></a>Registrar em log solicitações autenticadas

 Os seguintes tipos de solicitações autenticadas são registrados:

- Solicitações bem-sucedidas
- Solicitações com falha, incluindo tempo limite, limitação, rede, autorização e outros erros
- Solicitações que usam uma SAS (Assinatura de Acesso Compartilhado) ou OAuth, incluindo solicitações bem-sucedidas e com falha
- Solicitações para dados de análise (dados de log clássicos no contêiner **$logs** e dados de métrica clássicos nas tabelas **$metric**)

As solicitações feitas pelo próprio serviço de armazenamento, como criação ou exclusão de log, não estão registradas. Para uma lista completa de dados registrados, consulte [Mensagens de operações e status registradas em logs](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [Formato de log de armazenamento](monitor-storage-reference.md).

#### <a name="log-anonymous-requests"></a>Registrar em log solicitações anônimas

 Os seguintes tipos de solicitações anônimas são registrados:

- Solicitações bem-sucedidas
- Erros do servidor
- Erros de tempo limite para o cliente e para o servidor
- Solicitações GET com falha com o código de erro 304 (não modificado)

Nenhuma outra solicitação anônima com falha é registrada em log. Para uma lista completa de dados registrados, consulte [Mensagens de operações e status registradas em logs](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [Formato de log de armazenamento](monitor-storage-reference.md).

## <a name="configuration"></a>Configuração

As métricas de plataforma e o log de atividades são coletados automaticamente, mas você precisa criar uma configuração de diagnóstico para coletar logs de recursos ou encaminhá-los para fora do Azure Monitor. Para ver o processo para criar uma configuração de diagnóstico usando o portal do Azure, a CLI do Azure ou o PowerShell, consulte [Criar uma configuração de diagnóstico para coletar logs e métricas de plataforma no Azure](../../azure-monitor/platform/diagnostic-settings.md).

Ao criar uma configuração de diagnóstico, escolha o tipo de armazenamento para o qual você deseja habilitar os logs, como um blob, uma fila, uma tabela ou um arquivo. Se você criar a configuração de diagnóstico no portal do Azure, poderá selecionar o recurso em uma lista. Se você usar o PowerShell ou a CLI do Azure, será necessário usar a ID de recurso do tipo de armazenamento. Para encontrar o ID do recurso no portal do Azure, você pode abrir a página **Propriedades** da sua conta de armazenamento.

Você também precisa especificar as categorias de operações para as quais deseja coletar logs. As categorias do Armazenamento do Microsoft Azure são listadas nesta tabela.

| Categoria | Descrição |
|:---|:---|
| StorageRead | Ler operações em blobs. |
| StorageWrite | Gravar operações em blobs. |
| StorageDelete | Exclui operações em blobs. |

## <a name="analyze-metric-data"></a>Analisar dados de métrica

Você pode analisar métricas para o Armazenamento do Microsoft Azure com métricas de outros serviços do Azure usando o Metrics Explorer. Para abrir o Metrics Explorer, selecione **Métricas** no menu **Azure Monitor**. Para informações sobre o uso dessa ferramenta, consulte [Introdução ao Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md). 

Este exemplo mostra como exibir **Transações** no nível da conta.

![Captura de tela de acesso às métricas no portal do Azure](./media/monitor-storage/access-metrics-portal.png)

Para métricas com suporte para dimensões, você pode filtrar a métrica com valor da dimensão desejado. Este exemplo mostra como exibir **transações** no nível de conta em uma operação específica, selecionando valores para a dimensão **Nome da API**.

![Captura de tela de acesso às métricas com dimensão no portal do Azure](./media/monitor-storage/access-metrics-portal-with-dimension.png)

Para ver uma lista completa das dimensões compatíveis com o Armazenamento do Microsoft Azure, consulte [Dimensões de métricas](monitor-storage-reference.md#metrics-dimensions).

Todas as métricas para o Armazenamento do Microsoft Azure estão localizadas nestes namespaces:

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices
- Microsoft.Storage/storageAccounts/fileServices
- Microsoft.Storage/storageAccounts/queueServices
- Microsoft.Storage/storageAccounts/tableServices

Para ver uma lista de todas as métricas de suporte do Azure Monitor, que inclui o Armazenamento do Microsoft Azure, consulte [Métricas com suporte do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported).


### <a name="access-metrics"></a>Métricas de acesso

> [!TIP]
> Para exibir os exemplos da CLI do Azure ou do .NET, escolha as guias correspondentes listadas aqui.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Listar a definição de métrica

Você pode listar a definição de métrica da sua conta de armazenamento ou do serviço de armazenamento individual, como o serviço de blob, arquivo, tabela ou fila. Use o cmdlet [Get-AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition?view=azps-3.3.0).

Neste exemplo, substitua o espaço reservado `<resource-ID>` pela ID do recurso de toda a conta de armazenamento ou a ID do recurso de um serviço de armazenamento individual, como o serviço de blob, arquivo, tabela ou fila. Você pode encontrar essas IDs de recurso nas página de **propriedades** da sua conta de armazenamento no portal do Azure.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="read-metric-values"></a>Ler valores de métrica

Você pode ler os valores de métrica no nível da sua conta de armazenamento ou do serviço de armazenamento individual, como o serviço de blob, arquivo, tabela ou fila. Use o cmdlet [Get-AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric?view=azps-3.3.0).

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Listar a definição de métrica no nível de conta

Você pode listar a definição de métrica da sua conta de armazenamento ou do serviço de armazenamento individual, como o serviço de blob, arquivo, tabela ou fila. Use o comando [az monitor metrics list-definitions](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list-definitions).
 
Neste exemplo, substitua o espaço reservado `<resource-ID>` pela ID do recurso de toda a conta de armazenamento ou a ID do recurso de um serviço de armazenamento individual, como o serviço de blob, arquivo, tabela ou fila. Você pode encontrar essas IDs de recurso nas página de **propriedades** da sua conta de armazenamento no portal do Azure.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Ler os valores de métrica no nível de conta

Você pode ler os valores de métrica da sua conta de armazenamento ou do serviço de armazenamento individual, como o serviço de blob, arquivo, tabela ou fila. Use o comando [az monitor metrics list](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list).

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

O Azure Monitor fornece o [SDK do .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) para ler valores e definição de métricas. O [código de exemplo](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) mostra como usar o SDK com parâmetros diferentes. Você precisa usar `0.18.0-preview` ou uma versão posterior para métricas de armazenamento.
 
Nestes exemplos, substitua o espaço reservado `<resource-ID>` pela ID do recurso de toda a conta de armazenamento ou pela ID do recurso de um serviço de armazenamento individual, como o serviço de blob, arquivo, tabela ou fila. Você pode encontrar essas IDs de recurso nas página de **propriedades** da sua conta de armazenamento no portal do Azure.

Substitua a variável `<subscription-ID>` pela ID da sua assinatura. Para saber como obter valores para `<tenant-ID>`, `<application-ID>` e `<AccessKey>`, consulte [Usar o portal para criar um aplicativo e uma entidade de serviço do Azure Active Directory que possa acessar recursos](https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/). 

#### <a name="list-the-account-level-metric-definition"></a>Listar a definição de métrica no nível de conta

Os exemplos a seguir mostram como listar a definição de uma métrica no nível da conta:

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

#### <a name="read-account-level-metric-values"></a>Ler os valores de métrica no nível de conta

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

#### <a name="read-multidimensional-metric-values"></a>Ler valores métricos multidimensionais

Para métricas multidimensionais, você precisa definir os filtros de metadados se você quiser ler dados da métrica nos valores de dimensão específica.

O exemplo a seguir mostra como ler dados em uma métrica que suporta várias dimensões:

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

## <a name="analyze-log-data"></a>Analisar dados de log

Você pode acessar os logs de recursos como um blob em uma conta de armazenamento, como dados de evento, ou por meio de consultas do Log Analytics.

Para obter uma referência detalhada dos campos que aparecem nesses logs, consulte a [referência de dados de monitoramento do Armazenamento do Microsoft Azure](monitor-storage-reference.md).

> [!NOTE]
> Os logs do Armazenamento do Microsoft Azure no Azure Monitor estão em versão preliminar pública e disponíveis para teste de versão preliminar em todas as regiões de nuvem pública. Para se inscrever na versão preliminar, veja [esta página](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Essa versão preliminar habilita logs para blobs (que incluem Azure Data Lake Storage Gen2), arquivos, filas, tabelas, contas de armazenamento Premium nas contas de armazenamento GPv1 e GPv2. Não há suporte para contas de armazenamento clássicas.

### <a name="access-logs-in-a-storage-account"></a>Acessar logs em uma conta de armazenamento

Os logs aparecem como blobs armazenados em um contêiner na conta de armazenamento de destino. Os dados são coletados e armazenados dentro de um único blob como um conteúdo JSON delimitado por linha. O nome do blob segue a convenção de nomenclatura:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Aqui está um exemplo:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="access-logs-in-an-event-hub"></a>Acessar logs em um hub de eventos

Os logs enviados para um hub de eventos não são armazenados como um arquivo, mas você pode verificar se o hub de eventos recebeu as informações de log. No portal do Azure, navegue até seu hub de eventos e verifique se a contagem de **mensagens de entrada** é maior que zero. 

![Logs de auditoria](media/monitor-storage/event-hub-log.png)

Você pode acessar e ler dados de log enviados ao seu hub de eventos usando informações de segurança e ferramentas de monitoramento e gerenciamento de eventos. Para mais informações, consulte [O que posso fazer com os dados de monitoramento que estão sendo enviados ao meu hub de eventos?](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub).

### <a name="access-logs-in-a-log-analytics-workspace"></a>Acessar logs em um workspace do Log Analytics

Você pode acessar os logs enviados a um workspace do Log Analytics usando consultas de log do Azure Monitor.

Para obter mais informações, confira [Introdução ao Log Analytics no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

Os dados são armazenados nestas tabelas.

| Tabela | Descrição |
|:---|:---|
|StorageBlobLogs | Logs que descrevem a atividade no armazenamento de blob. |
|StorageFileLogs | Logs que descrevem a atividade em compartilhamentos de arquivo. |
|StorageQueueLogs | Logs que descrevem a atividade em filas.|
|StorageTableLogs| Logs que descrevem a atividade em tabelas.|

### <a name="azure-storage-log-analytics-queries-in-azure-monitor"></a>Consultas do Log Analytics do Armazenamento do Microsoft Azure no Azure Monitor

Veja algumas consultas adicionais que você pode inserir na barra **Pesquisa de logs** para ajudar a monitorar suas contas do Armazenamento do Microsoft Azure. Essas consultas funcionam com a [nova linguagem](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

Use essas consultas para ajudar a monitorar suas contas do Armazenamento do Microsoft Azure:

* Para listar os 10 erros mais comuns nos últimos três dias.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* Para listar as 10 operações que causaram mais erros nos últimos três dias.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* Para listar as 10 operações com a latência de ponta a ponta mais longa nos últimos três dias.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* Para listar todas as operações que causaram erros de limitação da largura de banda do lado do servidor nos últimos três dias.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* Para listar todas as solicitações com acesso anônimo nos últimos três dias.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* Para criar um gráfico de pizza das operações usadas nos últimos três dias.
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>Perguntas frequentes

**Armazenamento do Azure dá suporte a métricas para gerenciados discos ou discos não gerenciado?**

Não. A Computação do Azure suporta as métricas em discos. Para obter mais informações, consulte [Métricas por dicso para discos gerenciados e não gerenciados](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

## <a name="next-steps"></a>Próximas etapas

- Para ver uma referência dos logs e das métricas criados pelo Armazenamento do Azure, consulte a [referência de dados de monitoramento do Armazenamento do Microsoft Azure](monitor-storage-reference.md).
- Para ver informações detalhadas sobre o monitoramento dos recursos do Azure, consulte [Monitorar recursos do Azure com o Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md).
- Para obter mais informações sobre a migração de métricas, consulte [Migração de métricas do Armazenamento do Microsoft Azure](./storage-metrics-migration.md).
