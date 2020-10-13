---
title: Monitorando arquivos do Azure | Microsoft Docs
description: Saiba como monitorar o desempenho e a disponibilidade dos arquivos do Azure. Monitore os dados dos arquivos do Azure, saiba mais sobre a configuração e analise os dados de métrica e de log.
author: normesta
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 4b2f819edd875130c57d487536691b4588dcc71f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91772661"
---
# <a name="monitoring-azure-files"></a>Monitorando arquivos do Azure

Quando você tem aplicativos e processos de negócios críticos que dependem de recursos do Azure, recomendamos monitorar a disponibilidade, o desempenho e a operação desses recursos. Este artigo descreve os dados de monitoramento gerados pelos arquivos do Azure e como você pode usar os recursos do Azure Monitor para analisar alertas sobre esses dados.

> [!NOTE]
> Os logs do Armazenamento do Microsoft Azure no Azure Monitor estão em versão preliminar pública e disponíveis para teste de versão preliminar em todas as regiões de nuvem pública. Para se inscrever na versão preliminar, veja [esta página](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Essa visualização habilita logs para BLOBs (que inclui Azure Data Lake Storage Gen2), arquivos, filas e tabelas. Esse recurso está disponível para todas as contas de armazenamento criadas com o modelo de implantação Azure Resource Manager. Consulte [visão geral da conta de armazenamento](../common/storage-account-overview.md).

## <a name="monitor-overview"></a>Visão geral do Monitor

A página de **visão geral** no portal do Azure para cada recurso de arquivos do Azure inclui uma breve exibição do uso de recursos, como solicitações e cobrança por hora. Essas informações são úteis, mas apenas uma pequena quantidade de dados de monitoramento está disponível. Alguns desses dados são coletados automaticamente e estão disponíveis para análise assim que você criar o recurso. Você pode habilitar tipos adicionais de coleta de dados com algumas configurações.

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?
Os arquivos do Azure criam dados de monitoramento usando [Azure monitor](../../azure-monitor/overview.md), que é um serviço de monitoramento de pilha completo no Azure. O Azure Monitor oferece um conjunto completo de recursos para monitorar os recursos do Azure e recursos em outras nuvens e locais. 

Comece com o artigo [monitorando os recursos do Azure com o Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource), que descreve o seguinte:

- O que é o Azure Monitor?
- Custos associados ao monitoramento
- Monitoramento de dados coletados no Azure
- Configuração de coleta de dados
- Ferramentas padrão no Azure para analisar e alertar sobre dados de monitoramento

As seções a seguir se baseiam neste artigo descrevendo os dados específicos coletados dos arquivos do Azure. Os exemplos mostram como configurar a coleta de dados e analisar esses dados com as ferramentas do Azure.

## <a name="monitoring-data"></a>Dados de monitoramento

Os arquivos do Azure coletam os mesmos tipos de dados de monitoramento que outros recursos do Azure, que são descritos em [monitoramento de dados de recursos do Azure](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). 

Consulte [referência de dados de monitoramento de arquivo do Azure](storage-files-monitoring-reference.md) para obter informações detalhadas sobre as métricas de métricas e logs criadas pelos arquivos do Azure.

As métricas e os logs do Azure Monitor oferecem suporte apenas às contas de armazenamento do Azure Resource Manager. O Azure Monitor não tem suporte a contas de armazenamento clássicas. Se você quiser usar as métricas ou os logs nas contas de armazenamento clássicas, precisará migrar para a conta de armazenamento do Azure Resource Manager. Confira [Migrar para o Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

## <a name="collection-and-routing"></a>Coleta e roteamento

As métricas de plataforma e o log de atividades são coletados automaticamente, mas podem ser roteados para outros locais usando uma configuração de diagnóstico. Você deve criar uma configuração de diagnóstico para coletar logs de recursos. 

> [!NOTE]
> Os logs do Armazenamento do Microsoft Azure no Azure Monitor estão em versão preliminar pública e disponíveis para teste de versão preliminar em todas as regiões de nuvem pública. Para se inscrever na versão preliminar, veja [esta página](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Essa versão preliminar habilita logs para blobs (que incluem Azure Data Lake Storage Gen2), arquivos, filas, tabelas, contas de armazenamento Premium nas contas de armazenamento GPv1 e GPv2. Não há suporte para contas de armazenamento clássicas.

Para criar uma configuração de diagnóstico usando o portal do Azure, o CLI do Azure ou o PowerShell, consulte [criar configuração de diagnóstico para coletar logs e métricas de plataforma no Azure](../../azure-monitor/platform/diagnostic-settings.md). 

Para ver um modelo de Azure Resource Manager que cria uma configuração de diagnóstico, consulte [configuração de diagnóstico para o armazenamento do Azure](https://docs.microsoft.com/azure/azure-monitor/samples/resource-manager-diagnostic-settings#diagnostic-setting-for-azure-storage).

Ao criar uma configuração de diagnóstico, escolha o tipo de armazenamento para o qual você deseja habilitar os logs, como um blob, uma fila, uma tabela ou um arquivo. Para arquivos do Azure, escolha **arquivo**. 

Se você criar a configuração de diagnóstico no portal do Azure, poderá selecionar o recurso em uma lista. Se você usar o PowerShell ou o CLI do Azure, será necessário usar a ID de recurso do ponto de extremidade dos arquivos do Azure. Para encontrar o ID do recurso no portal do Azure, você pode abrir a página **Propriedades** da sua conta de armazenamento.

Você também precisa especificar uma das seguintes categorias de operações para as quais deseja coletar logs. 

| Categoria | Descrição |
|:---|:---|
| StorageRead | Operações de leitura em objetos. |
| StorageWrite | Operações de gravação em objetos. |
| StorageDelete | Excluir operações em objetos. |

Para obter a lista de operações de SMB e REST registradas, consulte [operações registradas de armazenamento e mensagens de status](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [referência de dados de monitoramento de arquivos do Azure](storage-files-monitoring-reference.md).

## <a name="analyzing-metrics"></a>Analisando métricas

Você pode analisar métricas para o Armazenamento do Microsoft Azure com métricas de outros serviços do Azure usando o Metrics Explorer. Para abrir o Metrics Explorer, selecione **Métricas** no menu **Azure Monitor**. Para informações sobre o uso dessa ferramenta, consulte [Introdução ao Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md). 

Para métricas com suporte para dimensões, você pode filtrar a métrica com valor da dimensão desejado.  Para ver uma lista completa das dimensões compatíveis com o Armazenamento do Microsoft Azure, consulte [Dimensões de métricas](storage-files-monitoring-reference.md#metrics-dimensions). As métricas para os arquivos do Azure estão nestes namespaces: 

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/fileServices

Para obter uma lista de todas as métricas de suporte de Azure Monitor, que inclui arquivos do Azure, consulte [Azure monitor métricas com suporte](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsfileservices).

### <a name="accessing-metrics"></a>Acessando métricas

> [!TIP]
> Para exibir os exemplos da CLI do Azure ou do .NET, escolha as guias correspondentes listadas aqui.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Listar a definição de métrica

Você pode listar a definição de métrica de sua conta de armazenamento ou o serviço de arquivos do Azure. Use o cmdlet [Get-AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition).

Neste exemplo, substitua o `<resource-ID>` espaço reservado pela ID de recurso de toda a conta de armazenamento ou a ID de recurso do serviço arquivos do Azure.  Você pode encontrar essas IDs de recurso nas página de **propriedades** da sua conta de armazenamento no portal do Azure.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Lendo valores de métrica

Você pode ler os valores de métrica de nível de conta da sua conta de armazenamento ou do serviço arquivos do Azure. Use o cmdlet [Get-AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric).

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Listar a definição de métrica no nível de conta

Você pode listar a definição de métrica de sua conta de armazenamento ou o serviço de arquivos do Azure. Use o comando [az monitor metrics list-definitions](https://docs.microsoft.com/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions).
 
Neste exemplo, substitua o `<resource-ID>` espaço reservado pela ID de recurso de toda a conta de armazenamento ou a ID de recurso do serviço arquivos do Azure. Você pode encontrar essas IDs de recurso nas página de **propriedades** da sua conta de armazenamento no portal do Azure.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Ler os valores de métrica no nível de conta

Você pode ler os valores de métrica da sua conta de armazenamento ou do serviço arquivos do Azure. Use o comando [az monitor metrics list](https://docs.microsoft.com/cli/azure/monitor/metrics#az-monitor-metrics-list).

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

O Azure Monitor fornece o [SDK do .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) para ler valores e definição de métricas. O [código de exemplo](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) mostra como usar o SDK com parâmetros diferentes. Você precisa usar `0.18.0-preview` ou uma versão posterior para métricas de armazenamento.
 
Nestes exemplos, substitua o `<resource-ID>` espaço reservado pela ID de recurso de toda a conta de armazenamento ou do serviço arquivos do Azure. Você pode encontrar essas IDs de recurso nas página de **propriedades** da sua conta de armazenamento no portal do Azure.

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

#### <a name="reading-account-level-metric-values"></a>Lendo valores de métrica no nível da conta

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

#### <a name="reading-multidimensional-metric-values"></a>Lendo valores de métrica multidimensional

Para métricas multidimensionais, você precisa definir os filtros de metadados se você quiser ler dados da métrica nos valores de dimensão específica.

O exemplo a seguir mostra como ler dados em uma métrica que suporta várias dimensões:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for Azure Files
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default";
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

## <a name="analyzing-logs"></a>Análise de logs

Você pode acessar os logs de recursos como um blob em uma conta de armazenamento, como dados de evento, ou por meio de consultas do Log Analytics.

Para obter a lista de operações de SMB e REST registradas, consulte [operações registradas de armazenamento e mensagens de status](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [referência de dados de monitoramento de arquivos do Azure](storage-files-monitoring-reference.md).

> [!NOTE]
> Os logs do Armazenamento do Microsoft Azure no Azure Monitor estão em versão preliminar pública e disponíveis para teste de versão preliminar em todas as regiões de nuvem pública. Para se inscrever na versão preliminar, veja [esta página](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Essa versão preliminar habilita logs para blobs (que incluem Azure Data Lake Storage Gen2), arquivos, filas, tabelas, contas de armazenamento Premium nas contas de armazenamento GPv1 e GPv2. Não há suporte para contas de armazenamento clássicas.

As entradas de log são criadas somente se há solicitações feitas no ponto de extremidade de serviço. Por exemplo, se uma conta de armazenamento tiver atividade em seu ponto de extremidade de arquivo, mas não em seus pontos de extremidades de tabela ou de fila, somente os logs pertencentes ao serviço de arquivo serão criados. Os logs do Armazenamento do Microsoft Azure contêm informações detalhadas sobre solicitações bem-sucedidas e com falha para um serviço de armazenamento. Essas informações podem ser usadas para monitorar solicitações individuais e diagnosticar problemas com um serviço de armazenamento. As solicitações são registradas em uma base de melhor esforço.

### <a name="log-authenticated-requests"></a>Registrar em log solicitações autenticadas

 Os seguintes tipos de solicitações autenticadas são registrados:

- Solicitações bem-sucedidas
- Solicitações com falha, incluindo tempo limite, limitação, rede, autorização e outros erros
- Solicitações que usam uma SAS (Assinatura de Acesso Compartilhado) ou OAuth, incluindo solicitações bem-sucedidas e com falha
- Solicitações para dados de análise (dados de log clássicos no contêiner **$logs** e dados de métrica clássicos nas tabelas **$metric**)

As solicitações feitas pelo próprio serviço de arquivos do Azure, como criação ou exclusão de log, não são registradas. Para obter uma lista completa das solicitações SMB e REST registradas, consulte [operações registradas de armazenamento e mensagens de status](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [referência de dados de monitoramento de arquivos do Azure](storage-files-monitoring-reference.md).

### <a name="log-anonymous-requests"></a>Registrar em log solicitações anônimas

 Os seguintes tipos de solicitações anônimas são registrados:

- Solicitações bem-sucedidas
- Erros do servidor
- Erros de tempo limite para o cliente e o servidor
- Solicitações GET com falha com o código de erro 304 (não modificado)

Nenhuma outra solicitação anônima com falha é registrada em log. Para obter uma lista completa das solicitações SMB e REST registradas, consulte [operações registradas de armazenamento e mensagens de status](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [referência de dados de monitoramento de arquivos do Azure](storage-files-monitoring-reference.md).

### <a name="accessing-logs-in-a-storage-account"></a>Acessando logs em uma conta de armazenamento

Os logs aparecem como blobs armazenados em um contêiner na conta de armazenamento de destino. Os dados são coletados e armazenados dentro de um único blob como um conteúdo JSON delimitado por linha. O nome do blob segue a convenção de nomenclatura:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/fileServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Aqui está um exemplo:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Acessando logs em um hub de eventos

Os logs enviados para um hub de eventos não são armazenados como um arquivo, mas você pode verificar se o hub de eventos recebeu as informações de log. No portal do Azure, navegue até seu hub de eventos e verifique se a contagem de **mensagens de entrada** é maior que zero. 

![Logs de auditoria](media/storage-files-monitoring/event-hub-log.png)

Você pode acessar e ler dados de log enviados ao seu hub de eventos usando informações de segurança e ferramentas de monitoramento e gerenciamento de eventos. Para mais informações, consulte [O que posso fazer com os dados de monitoramento que estão sendo enviados ao meu hub de eventos?](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub).

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Acessando logs em um espaço de trabalho Log Analytics

Você pode acessar os logs enviados a um workspace do Log Analytics usando consultas de log do Azure Monitor. Os dados são armazenados na tabela **StorageFileLogs** . 

Para obter mais informações, confira [Introdução ao Log Analytics no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

#### <a name="sample-kusto-queries"></a>Consultas de exemplo do Kusto

Aqui estão algumas consultas que você pode inserir na barra de **pesquisa de log** para ajudá-lo a monitorar seus arquivos do Azure. Essas consultas funcionam com a [nova linguagem](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

> [!IMPORTANT]
> Quando você seleciona **logs** no menu do grupo de recursos da conta de armazenamento, log Analytics é aberta com o escopo de consulta definido como o grupo de recursos atual. Isso significa que as consultas de log incluirão apenas os dados desse grupo de recursos. Se você quiser executar uma consulta que inclui dados de outros recursos ou dados de outros serviços do Azure, selecione **logs** no menu **Azure monitor** . Confira [Escopo da consulta de log e intervalo de tempo no Log Analytics do Azure Monitor](/azure/azure-monitor/log-query/scope/) para obter detalhes.

Use essas consultas para ajudá-lo a monitorar seus compartilhamentos de arquivos do Azure:

- Exibir erros de SMB na última semana

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) and StatusCode contains "-"
| sort by StatusCode
```
- Criar um gráfico de pizza de operações SMB na última semana

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

- Exibir erros REST na última semana

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) and StatusText !contains "Success"
| sort by StatusText asc
```

- Criar um gráfico de pizza de operações REST na última semana

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

Para exibir a lista de nomes de coluna e descrições de arquivos do Azure, consulte [StorageFileLogs](https://docs.microsoft.com/azure/azure-monitor/reference/tables/storagefilelogs).

Para obter mais informações sobre como escrever consultas, consulte [tutorial: introdução às consultas de log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

## <a name="alerts"></a>Alertas

Azure Monitor Alertas proativamente notificam você quando condições importantes são encontradas nos dados de monitoramento. Eles permitem que você identifique e resolva problemas em seu sistema antes que os clientes os percebam. Você pode definir alertas em [métricas](/azure/azure-monitor/platform/alerts-metric-overview), [logs](/azure/azure-monitor/platform/alerts-unified-log)e no [log de atividades](/azure/azure-monitor/platform/activity-log-alerts). 

A tabela a seguir lista alguns cenários de exemplo para monitorar e a métrica apropriada a ser usada para o alerta:

| Cenário | Métrica a ser usada para alerta |
|-|-|
| O compartilhamento de arquivos está limitado. | Métrica: transações<br>Nome da dimensão: tipo de resposta <br>Nome da dimensão: FileShare (somente compartilhamento de arquivos Premium) |
| O tamanho do compartilhamento de arquivos é de 80% da capacidade. | Métrica: capacidade de arquivo<br>Nome da dimensão: FileShare (somente compartilhamento de arquivos Premium) |
| A saída do compartilhamento de arquivos excedeu 500 GiB em um dia. | Métrica: saída<br>Nome da dimensão: FileShare (somente compartilhamento de arquivos Premium) |

### <a name="how-to-create-alerts-for-azure-files"></a>Como criar alertas para arquivos do Azure

1. Vá para sua **conta de armazenamento** no **portal do Azure**. 

2. Clique em **alertas** e, em seguida, clique em **+ nova regra de alerta**.

3. Clique em **Editar recurso**, selecione o **tipo de recurso arquivo** e, em seguida, clique em **concluído**. 

4. Clique em **Selecionar condição** e forneça as seguintes informações para o alerta: 

    - **Métrica**
    - **Nome da dimensão**
    - **Lógica de alerta**

5. Clique em **selecionar grupo de ações** e adicione um grupo de ações (email, SMS, etc.) ao alerta selecionando um grupo de ações existente ou criando um novo grupo de ação.

6. Preencha os **detalhes do alerta** , como nome da **regra de alerta**, **Descrição**e **severidade**.

7. Clique em **criar regra de alerta** para criar o alerta.

> [!NOTE]  
> Se você criar um alerta e ele estiver muito ruidosa, ajuste o valor de limite e a lógica de alerta.

### <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Como criar um alerta se um compartilhamento de arquivos for limitado

1. Vá para sua **conta de armazenamento** no **portal do Azure**.
2. Na seção **monitoramento** , clique em **alertas**e, em seguida, clique em **+ nova regra de alerta**.
3. Clique em **Editar recurso**, selecione o **tipo de recurso arquivo** para a conta de armazenamento e clique em **concluído**. Por exemplo, se o nome da conta de armazenamento for `contoso` , selecione o `contoso/file` recurso.
4. Clique em **Selecionar condição** para adicionar uma condição.
5. Você verá uma lista de sinais com suporte para a conta de armazenamento, selecione a métrica **Transações** .
6. Na folha **Configurar lógica de sinal** , clique na lista suspensa **nome da dimensão** e selecione **tipo de resposta**.
7. Clique na lista suspensa **valores de dimensão** e selecione **SUCCESSWITHTHROTTLING** (para SMB) ou **ClientThrottlingError** (para REST).

   > [!NOTE]
   > Se o valor da dimensão SuccessWithThrottling ou ClientThrottlingError não estiver listado, isso significará que o recurso não foi limitado. Para adicionar o valor de dimensão, clique em **adicionar valor personalizado** ao lado da lista suspensa **valores de dimensão** , digite **SuccessWithThrottling** ou **ClientThrottlingError**, clique em **OK** e repita a etapa #7.

8. Clique na lista suspensa **nome da dimensão** e selecione **compartilhamento de arquivos**.
9. Clique na lista suspensa **valores de dimensão** e selecione os compartilhamentos de arquivos que você deseja alertar.

   > [!NOTE]
   > Se o compartilhamento de arquivos for um compartilhamento de arquivos padrão, selecione **todos os valores atuais e futuros**. O menu suspenso valores de dimensão não listará os compartilhamentos de arquivos porque as métricas por compartilhamento não estão disponíveis para compartilhamentos de arquivos padrão. Os alertas de limitação para compartilhamentos de arquivos padrão serão disparados se algum compartilhamento de arquivos dentro da conta de armazenamento for limitado e o alerta não identificar qual compartilhamento de arquivos foi limitado. Como as métricas por compartilhamento não estão disponíveis para compartilhamentos de arquivos padrão, a recomendação é ter um compartilhamento de arquivos por conta de armazenamento.

10. Defina os **parâmetros de alerta** (valor de limite, operador, granularidade de agregação e frequência de avaliação) e clique em **concluído**.

    > [!TIP]
    > Se você estiver usando um limite estático, o gráfico de métrica poderá ajudar a determinar um valor de limite razoável se o compartilhamento de arquivos estiver sendo limitado no momento. Se você estiver usando um limite dinâmico, o gráfico de métrica exibirá os limites calculados com base nos dados recentes.

11. Clique em **selecionar grupo de ações** para adicionar um **grupo de ações** (email, SMS, etc.) ao alerta, seja selecionando um grupo de ações existente ou criando um novo grupo de ação.
12. Preencha os **detalhes do alerta** , como nome da **regra de alerta**, * * descrição e **severidade**.
13. Clique em **criar regra de alerta** para criar o alerta.

### <a name="how-to-create-an-alert-if-the-azure-file-share-size-is-80-of-capacity"></a>Como criar um alerta se o tamanho do compartilhamento de arquivos do Azure for de 80% da capacidade

1. Vá para sua **conta de armazenamento** no **portal do Azure**.
2. Na seção **monitoramento** , clique em **alertas** e, em seguida, clique em **+ nova regra de alerta**.
3. Clique em **Editar recurso**, selecione o **tipo de recurso arquivo** para a conta de armazenamento e clique em **concluído**. Por exemplo, se o nome da conta de armazenamento for `contoso` , selecione o `contoso/file` recurso.
4. Clique em **Selecionar condição** para adicionar uma condição.
5. Você verá uma lista de sinais com suporte para a conta de armazenamento, selecione a métrica de **capacidade de arquivo** .
6. Na folha **Configurar lógica de sinal** , clique na lista suspensa **nome da dimensão** e selecione **compartilhamento de arquivos**.
7. Clique na lista suspensa **valores de dimensão** e selecione os compartilhamentos de arquivos que você deseja alertar.

   > [!NOTE]
   > Se o compartilhamento de arquivos for um compartilhamento de arquivos padrão, selecione **todos os valores atuais e futuros**. O menu suspenso valores de dimensão não listará os compartilhamentos de arquivos porque as métricas por compartilhamento não estão disponíveis para compartilhamentos de arquivos padrão. Os alertas para compartilhamentos de arquivos padrão são baseados em todos os compartilhamentos de arquivos na conta de armazenamento. Como as métricas por compartilhamento não estão disponíveis para compartilhamentos de arquivos padrão, a recomendação é ter um compartilhamento de arquivos por conta de armazenamento.

8. Insira o **valor do limite** em bytes. Por exemplo, se o tamanho do compartilhamento de arquivos for 100 TiB e você quiser receber um alerta quando o tamanho do compartilhamento de arquivos for 80% da capacidade, o valor do limite em bytes será 87960930222080.
9. Defina o restante dos **parâmetros de alerta** (granularidade de agregação e frequência de avaliação) e clique em **concluído**.
10. Clique em selecionar grupo de ações para adicionar um grupo de ações (email, SMS, etc.) ao alerta, seja selecionando um grupo de ações existente ou criando um novo grupo de ação.
11. Preencha os **detalhes do alerta** , como nome da **regra de alerta**, * * descrição e **severidade**.
12. Clique em **criar regra de alerta** para criar o alerta.

### <a name="how-to-create-an-alert-if-the-azure-file-share-egress-has-exceeded-500-gib-in-a-day"></a>Como criar um alerta se a saída do compartilhamento de arquivos do Azure excedeu 500 GiB em um dia

1. Vá para sua **conta de armazenamento** no **portal do Azure**.
2. Na seção monitoramento, clique em **alertas** e, em seguida, clique em **+ nova regra de alerta**.
3. Clique em **Editar recurso**, selecione o **tipo de recurso arquivo** para a conta de armazenamento e clique em **concluído**. Por exemplo, se o nome da conta de armazenamento for contoso, selecione o recurso contoso/File.
4. Clique em **Selecionar condição** para adicionar uma condição.
5. Você verá uma lista de sinais com suporte para a conta de armazenamento, selecione a métrica de **saída** .
6. Na folha **Configurar lógica de sinal** , clique na lista suspensa **nome da dimensão** e selecione **compartilhamento de arquivos**.
7. Clique na lista suspensa **valores de dimensão** e selecione os compartilhamentos de arquivos que você deseja alertar.

   > [!NOTE]
   > Se o compartilhamento de arquivos for um compartilhamento de arquivos padrão, selecione **todos os valores atuais e futuros**. O menu suspenso valores de dimensão não listará os compartilhamentos de arquivos porque as métricas por compartilhamento não estão disponíveis para compartilhamentos de arquivos padrão. Os alertas para compartilhamentos de arquivos padrão são baseados em todos os compartilhamentos de arquivos na conta de armazenamento. Como as métricas por compartilhamento não estão disponíveis para compartilhamentos de arquivos padrão, a recomendação é ter um compartilhamento de arquivos por conta de armazenamento.

8. Insira **536870912000** bytes para o valor de limite. 
9. Clique na lista suspensa **agregação de granularidade** e selecione **24 horas**.
10. Selecione a **frequência de avaliação** e **clique em concluído**.
11. Clique em **selecionar grupo de ações** para adicionar um **grupo de ações** (email, SMS, etc.) ao alerta, seja selecionando um grupo de ações existente ou criando um novo grupo de ação.
12. Preencha os **detalhes do alerta** , como nome da **regra de alerta**, * * descrição e **severidade**.
13. Clique em **criar regra de alerta** para criar o alerta.

## <a name="next-steps"></a>Próximas etapas

- [Referência de dados de monitoramento de arquivos do Azure](storage-files-monitoring.md)
- [Monitorar recursos do Azure com o Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md)
- [Migração de métricas do armazenamento do Azure](../common/storage-metrics-migration.md)
- [Planejando uma implantação de Arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-planning)
- [Como implantar Arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)
- [Solucionar Problemas dos Arquivos do Azure no Windows](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems)
- [Solucionar Problemas dos Arquivos do Azure no Linux](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-linux-file-connection-problems)
