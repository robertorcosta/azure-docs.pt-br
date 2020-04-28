---
title: Azure Cosmos DB de monitoramento | Microsoft Docs
description: Saiba como monitorar o desempenho e a disponibilidade do Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: ec0894818c0c246223749e1efcf7ea9e5ebee463
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82194526"
---
# <a name="monitoring-azure-cosmos-db"></a>Azure Cosmos DB de monitoramento
Quando você tem aplicativos críticos e processos de negócios que dependem de recursos do Azure, você deseja monitorar esses recursos para sua disponibilidade, desempenho e operação. Este artigo descreve os dados de monitoramento gerados pelos bancos de dado do Azure Cosmos e como você pode usar os recursos do Azure Monitor para analisar e alertar sobre esses dados.

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?
Azure Cosmos DB cria dados de monitoramento usando [Azure monitor](../azure-monitor/overview.md) que é um serviço de monitoramento de pilha completo no Azure que fornece um conjunto completo de recursos para monitorar os recursos do Azure, além de recursos em outras nuvens e locais. 

Se você ainda não estiver familiarizado com o monitoramento dos serviços do Azure, comece com o artigo [monitorando os recursos do Azure com Azure monitor](../azure-monitor/insights/monitor-azure-resource.md) que descreve o seguinte:

- O que é o Azure Monitor?
- Custos associados ao monitoramento
- Monitorando dados coletados no Azure
- Configurar a coleta de dados
- Ferramentas padrão no Azure para analisar e alertar sobre dados de monitoramento

As seções a seguir se baseiam neste artigo descrevendo os dados específicos coletados de Azure Cosmos DB e fornecendo exemplos para configurar a coleta de dados e analisar esses dados com as ferramentas do Azure.

## <a name="azure-monitor-for-cosmos-db-preview"></a>Azure Monitor para Cosmos DB (versão prévia)
[Azure monitor para Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md) baseia-se no [recurso de pastas de trabalho do Azure monitor](../azure-monitor/app/usage-workbooks.md) e usa os mesmos dados de monitoramento coletados para Cosmos DB descritos nas seções abaixo. Use essa ferramenta para ver um modo de exibição do desempenho geral, falhas, capacidade e integridade operacional de todos os seus Azure Cosmos DB recursos em uma experiência interativa unificada e aproveite os outros recursos do Azure Monitor para análise e alertas detalhados. 

![Azure Monitor para Cosmos DB](media/monitor-cosmos-db/azure-monitor-cosmos-db.png)

> [!NOTE]
> Ao criar contêineres, certifique-se de não criar dois contêineres com o mesmo nome, mas com maiúsculas e minúsculas diferentes. Isso porque algumas partes da plataforma Azure não diferenciam maiúsculas de minúsculas e isso pode resultar em confusão/colisão de telemetria e ações em contêineres com tais nomes.

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Exibir métricas de nível de operação para Azure Cosmos DB

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Selecione **Monitor** na barra de navegação à esquerda e selecione **métricas**.

   ![Painel de métricas no Azure Monitor](./media/monitor-cosmos-db/monitor-metrics-blade.png)

1. No painel de **métricas** > **selecione um recurso** > escolha a **assinatura**necessária e o **grupo de recursos**. Para o **tipo de recurso**, selecione **contas de Azure Cosmos DB**, escolha uma das contas existentes do Azure Cosmos e selecione **aplicar**.

   ![Escolha uma conta de Cosmos DB para exibir as métricas](./media/monitor-cosmos-db/select-cosmosdb-account.png)

1. Em seguida, você pode selecionar uma métrica na lista de métricas disponíveis. Você pode selecionar métricas específicas para unidades de solicitação, armazenamento, latência, disponibilidade, Cassandra e outros. Para saber mais detalhadamente sobre todas as métricas disponíveis nesta lista, consulte o artigo [métricas por categoria](monitor-cosmos-db-reference.md) . Neste exemplo, vamos selecionar unidades de **solicitação** e **Méd** como o valor de agregação.

   Além desses detalhes, você também pode selecionar o intervalo de **tempo** e a **granularidade de tempo** das métricas. No máximo, você pode exibir as métricas dos últimos 30 dias.  Depois de aplicar o filtro, um gráfico é exibido com base no seu filtro. Você pode ver o número médio de unidades de solicitação consumidas por minuto para o período selecionado.  

   ![Escolha uma métrica no portal do Azure](./media/monitor-cosmos-db/metric-types.png)

### <a name="add-filters-to-metrics"></a>Adicionar filtros a métricas

Você também pode filtrar as métricas e o gráfico exibidos por um **CollectionName**, **DatabaseName**, **OperationType**, **região**e **StatusCode**específicos. Para filtrar as métricas, selecione **Adicionar filtro** e escolha a propriedade necessária, como **OperationType** , e selecione um valor como **consulta**. Em seguida, o grafo exibe as unidades de solicitação consumidas para a operação de consulta para o período selecionado. As operações executadas por meio do procedimento armazenado não são registradas para que não estejam disponíveis na métrica OperationType.

![Adicionar um filtro para selecionar a granularidade da métrica](./media/monitor-cosmos-db/add-metrics-filter.png)

Você pode agrupar as métricas usando a opção **aplicar divisão** . Por exemplo, você pode agrupar as unidades de solicitação por tipo de operação e exibir o grafo para todas as operações de uma só vez, conforme mostrado na imagem a seguir:

![Adicionar filtro de divisão de aplicação](./media/monitor-cosmos-db/apply-metrics-splitting.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>Dados de monitoramento coletados do Azure Cosmos DB

Azure Cosmos DB coleta os mesmos tipos de dados de monitoramento que outros recursos do Azure, que são descritos em [monitoramento de dados de recursos do Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Veja [Azure Cosmos DB referência de dados de monitoramento](monitor-cosmos-db-reference.md) para obter uma referência detalhada dos logs e métricas criados por Azure Cosmos DB.

A página de **visão geral** no portal do Azure para cada banco de dados Cosmos do Azure inclui uma breve exibição do uso do banco de dados, incluindo sua solicitação e o uso de cobrança por hora. Essas são informações úteis, mas apenas uma pequena quantidade de dados de monitoramento disponíveis. Alguns desses dados são coletados automaticamente e disponíveis para análise assim que você cria o banco de dados, enquanto você pode habilitar a coleta de dado adicional com algumas configurações.

![Página de visão geral](media/monitor-cosmos-db/overview-page.png)

## <a name="analyzing-metric-data"></a>Analisando dados de métrica

Azure Cosmos DB fornece uma experiência personalizada para trabalhar com métricas. Consulte [monitorar e depurar Azure Cosmos DB métricas de Azure monitor](cosmos-db-azure-monitor-metrics.md) para obter detalhes sobre como usar essa experiência e para analisar diferentes cenários de Azure Cosmos DB.

Você pode analisar métricas para Azure Cosmos DB com métricas de outros serviços do Azure usando o Metrics Explorer abrindo **métricas** no menu **Azure monitor** . Consulte [introdução ao Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md) para obter detalhes sobre como usar essa ferramenta. Todas as métricas para Azure Cosmos DB estão no namespace **Cosmos DB métricas padrão**. Você pode usar as seguintes dimensões com essas métricas ao adicionar um filtro a um gráfico:

- CollectionName
- DatabaseName
- OperationType
- Região
- StatusCode


## <a name="analyzing-log-data"></a>Analisando dados de log
Os dados em logs de Azure Monitor são armazenados em tabelas que cada tabela tem seu próprio conjunto de propriedades exclusivas. Azure Cosmos DB armazena dados nas tabelas a seguir.

| Tabela | Descrição |
|:---|:---|
| AzureDiagnostics | Tabela comum usada por vários serviços para armazenar logs de recursos. Os logs de recursos de Azure Cosmos DB podem ser `MICROSOFT.DOCUMENTDB`identificados com.   |
| AzureActivity    | Tabela comum que armazena todos os registros do log de atividades. 


> [!IMPORTANT]
> Quando você seleciona **logs** no menu Azure Cosmos DB, log Analytics é aberto com o escopo de consulta definido para o banco de dados atual do Azure Cosmos. Isso significa que as consultas de log incluirão apenas os dados desse recurso. Se você quiser executar uma consulta que inclua dados de outros bancos de dados ou de outros serviços do Azure, selecione **logs** no menu **Azure monitor** . Consulte [escopo de consulta de log e intervalo de tempo em Azure Monitor log Analytics](../azure-monitor/log-query/scope.md) para obter detalhes.

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Azure Cosmos DB Log Analytics consultas no Azure Monitor

Aqui estão algumas consultas que você pode inserir na barra de pesquisa da **pesquisa de logs** para ajudá-lo a monitorar seus contêineres de Cosmos do Azure. Essas consultas funcionam com a [nova linguagem](../log-analytics/log-analytics-log-search-upgrade.md).

Veja a seguir as consultas que você pode usar para ajudá-lo a monitorar seus bancos de dados do Azure Cosmos.

* Para consultar todos os logs de diagnóstico do Azure Cosmos DB para um período especificado:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"

    ```

* Para consultar os 10 eventos registrados em log mais recentemente:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | limit 10
    ```

* Para consultar todas as operações, agrupadas por tipo de operação:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName
    ```

* Para consultar todas as operações, agrupadas por recurso:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* Para consultar toda a atividade do usuário, agrupada por recurso:

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```
* Para obter todas as consultas com mais de 100 RUs Unidas com dados de **DataPlaneRequests** e **QueryRunTimeStatistics**.

    ```Kusto
    AzureDiagnostics
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
    | project activityId_g, requestCharge_s
    | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
    ) on $left.activityId_g == $right.activityId_g
    | order by requestCharge_s desc
    | limit 100
    ```

* Para consultar quais operações demoram mais de 3 milissegundos:

    ```Kusto
    AzureDiagnostics 
    | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Para consultar qual agente está executando as operações:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName, userAgent_s
    ```

* Para consultar quando foram executadas as operações de longa execução:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | project TimeGenerated , duration_s 
    | summarize count() by bin(TimeGenerated, 5s)
    | render timechart
    ```
    
* Para obter estatísticas de chave de partição para avaliar a distorção entre as três principais partições para a conta de banco de dados:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="monitor-azure-cosmos-db-programmatically"></a>Monitorar o Azure Cosmos DB de forma programática
As métricas no nível da conta disponíveis no portal, como o uso de armazenamento da conta e o total de solicitações, não estão disponíveis por meio das APIs do SQL. No entanto, você pode recuperar os dados de uso no nível da coleção usando as APIs do SQL. Para recuperar os dados do nível de coleção, faça o seguinte:

* Para usar a API REST, [execute um GET na coleção](https://msdn.microsoft.com/library/mt489073.aspx). As informações de cota e de uso da coleção são retornadas nos cabeçalhos x-ms-resource-quota e x-ms-resource-usage na resposta.
* Para usar o SDK do .NET, use o método [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx), que retorna uma [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) que contém diversas propriedades de uso como **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage** e muito mais.

Para acessar outras métricas, use o [SDK do Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Insights). As definições de métricas disponíveis podem ser recuperadas chamando:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

As consultas para recuperar métricas individuais usam o seguinte formato:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z



## <a name="next-steps"></a>Próximas etapas

- Veja [Azure Cosmos DB referência de dados de monitoramento](monitor-cosmos-db-reference.md) para obter uma referência dos logs e métricas criados por Azure Cosmos DB.
- Consulte [monitorando recursos do Azure com Azure monitor](../azure-monitor/insights/monitor-azure-resource.md) para obter detalhes sobre como monitorar recursos do Azure.
