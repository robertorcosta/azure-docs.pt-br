---
title: Monitoramento do Azure Cosmos DB | Microsoft Docs
description: Saiba como monitorar o desempenho e a disponibilidade do Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: b9b66c379714c2f4fa2421876fda3bdb500ce6c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250365"
---
# <a name="monitoring-azure-cosmos-db"></a>Monitoramento Azure Cosmos DB
Quando você tem aplicativos críticos e processos de negócios que dependem dos recursos do Azure, você deseja monitorar esses recursos para sua disponibilidade, desempenho e operação. Este artigo descreve os dados de monitoramento gerados pelos bancos de dados do Azure Cosmos e como você pode usar os recursos do Azure Monitor para analisar e alertar sobre esses dados.

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?
O Azure Cosmos DB cria dados de monitoramento usando [o Azure Monitor,](../azure-monitor/overview.md) que é um serviço de monitoramento de pilha completa no Azure que fornece um conjunto completo de recursos para monitorar seus recursos do Azure, além de recursos em outras nuvens e no local. 

Se você ainda não estiver familiarizado com o monitoramento dos serviços do Azure, comece com o artigo [Monitorando os recursos do Azure com o Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) que descreve o seguinte:

- O que é o Azure Monitor?
- Custos associados ao monitoramento
- Monitoramento de dados coletados no Azure
- Configurar a coleta de dados
- Ferramentas padrão no Azure para análise e alerta sobre dados de monitoramento

As seções a seguir se baseiam neste artigo descrevendo os dados específicos coletados do Azure Cosmos DB e fornecendo exemplos para configurar a coleta de dados e analisar esses dados com ferramentas do Azure.

## <a name="azure-monitor-for-cosmos-db-preview"></a>Monitor Azure para Cosmos DB (Visualização)
[O Azure Monitor for Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md) é baseado no [recurso de livros de trabalho do Azure Monitor](../azure-monitor/app/usage-workbooks.md) e usa os mesmos dados de monitoramento coletados para cosmos DB descritos nas seções abaixo. Use esta ferramenta para uma visão do desempenho geral, falhas, capacidade e saúde operacional de todos os seus recursos do Azure Cosmos DB em uma experiência interativa unificada e aproveite os outros recursos do Azure Monitor para análise detalhada e alerta. 

![Monitor Azure para Cosmos DB](media/monitor-cosmos-db/azure-monitor-cosmos-db.png)

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Ver métricas de nível de operação para Azure Cosmos DB

1. Faça login no [portal Azure](https://portal.azure.com/).

1. Selecione **Monitor** na barra de navegação à esquerda e selecione **Métricas**.

   ![Painel de métricas no Monitor Azure](./media/monitor-cosmos-db/monitor-metrics-blade.png)

1. No painel **Métricas** > **Selecione um recurso** > escolha a **assinatura**necessária e o grupo **de recursos**. Para o **tipo Recurso,** selecione **contas Azure Cosmos DB,** escolha uma de suas contas azure Cosmos existentes e **selecione Aplicar**.

   ![Escolha uma conta Cosmos DB para visualizar métricas](./media/monitor-cosmos-db/select-cosmosdb-account.png)

1. Em seguida, você pode selecionar uma métrica da lista de métricas disponíveis. Você pode selecionar métricas específicas para solicitar unidades, armazenamento, latência, disponibilidade, Cassandra e outros. Para saber em detalhes todas as métricas disponíveis nesta lista, consulte o artigo [Métricas por categoria.](monitor-cosmos-db-reference.md) Neste exemplo, vamos selecionar Unidades de **Solicitação** e **Avg** como o valor de agregação.

   Além desses detalhes, você também pode selecionar o **intervalo de tempo** e a **granularidade** de tempo das métricas. No máximo, você pode visualizar métricas para os últimos 30 dias.  Depois de aplicar o filtro, um gráfico é exibido com base no seu filtro. Você pode ver o número médio de unidades de solicitação consumidas por minuto para o período selecionado.  

   ![Escolha uma métrica no portal Azure](./media/monitor-cosmos-db/metric-types.png)

### <a name="add-filters-to-metrics"></a>Adicionar filtros às métricas

Você também pode filtrar métricas e o gráfico exibido por um Nome de **Coleção**específico, **Nome do banco de dados,** Tipo de **Operação,** **Região**e **StatusCode**. Para filtrar as métricas, selecione **Adicionar filtro** e escolha a propriedade necessária, como **OperationType** e selecione um valor como **Consulta**. Em seguida, o gráfico exibe as unidades de solicitação consumidas para a operação de consulta para o período selecionado. As operações executadas via procedimento armazenado não são registradas, portanto não estão disponíveis sob a métrica OperationType.

![Adicione um filtro para selecionar a granularidade métrica](./media/monitor-cosmos-db/add-metrics-filter.png)

Você pode agrupar métricas usando a opção **Aplicar divisão.** Por exemplo, você pode agrupar as unidades de solicitação por tipo de operação e visualizar o gráfico de todas as operações de uma só vez, conforme mostrado na imagem a seguir:

![Adicionar aplicar filtro de divisão](./media/monitor-cosmos-db/apply-metrics-splitting.png)

Aqui está outro exemplo para visualizar as métricas de latência do lado do servidor para um banco de dados específico, contêiner ou uma operação:

![Métricas de latência do lado do servidor](./media/monitor-cosmos-db/serverside-latency-metric.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>Dados de monitoramento coletados do Azure Cosmos DB

O Azure Cosmos DB coleta os mesmos tipos de dados de monitoramento que outros recursos do Azure descritos nos [dados de monitoramento dos recursos do Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Consulte [a referência de dados de monitoramento do Azure Cosmos DB](monitor-cosmos-db-reference.md) para obter uma referência detalhada dos logs e métricas criados pelo Azure Cosmos DB.

A página **Visão Geral** no portal Azure para cada banco de dados do Azure Cosmos inclui uma breve visualização do uso do banco de dados, incluindo sua solicitação e uso de faturamento por hora. Esta é uma informação útil, mas apenas uma pequena quantidade dos dados de monitoramento disponíveis. Alguns desses dados são coletados automaticamente e disponíveis para análise assim que você cria o banco de dados, enquanto você pode habilitar a coleta adicional de dados com alguma configuração.

![Página de visão geral](media/monitor-cosmos-db/overview-page.png)

## <a name="analyzing-metric-data"></a>Analisando dados métricos

O Azure Cosmos DB oferece uma experiência personalizada para trabalhar com métricas. Consulte [monitorar e depurar métricas do Azure Cosmos DB do Azure Monitor](cosmos-db-azure-monitor-metrics.md) para obter detalhes sobre o uso dessa experiência e para analisar diferentes cenários do Azure Cosmos DB.

Você pode analisar métricas para o Azure Cosmos DB com métricas de outros serviços do Azure usando o explorador Metrics abrindo **métricas** do menu **do Monitor Do Azure.** Consulte [Como começar com o Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md) para obter detalhes sobre o uso desta ferramenta. Todas as métricas para Azure Cosmos DB estão nas **métricas padrão do Namespace Cosmos DB**. Você pode usar as seguintes dimensões com essas métricas ao adicionar um filtro a um gráfico:

- CollectionName
- DatabaseName
- OperationType
- Região
- StatusCode


## <a name="analyzing-log-data"></a>Analisando dados de log
Os dados no Azure Monitor Logs são armazenados em tabelas que cada tabela tem seu próprio conjunto de propriedades únicas. O Azure Cosmos DB armazena dados nas tabelas a seguir.

| Tabela | Descrição |
|:---|:---|
| AzureDiagnostics | Tabela comum usada por vários serviços para armazenar registros de recursos. Os registros de recursos do Azure `MICROSOFT.DOCUMENTDB`Cosmos DB podem ser identificados com .   |
| AzureActivity    | Tabela comum que armazena todos os registros do registro de atividades. 


> [!IMPORTANT]
> Quando você **seleciona Logs** no menu Azure Cosmos DB, o Log Analytics é aberto com o escopo de consulta definido no banco de dados azure Cosmos atual. Isso significa que as consultas de log incluirão apenas dados desse recurso. Se você quiser executar uma consulta que inclua dados de outros bancos de dados ou dados de outros serviços do Azure, **selecione Logs** no menu Do Monitor do **Azure.** Consulte [o escopo de consulta de log e o intervalo de tempo no Azure Monitor Log Analytics](../azure-monitor/log-query/scope.md) para obter detalhes.

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Consultas do Azure Cosmos DB Log Analytics no Azure Monitor

Aqui estão algumas perguntas que você pode inserir na barra de **pesquisa log** para ajudá-lo a monitorar seus contêineres Do Azure Cosmos. Essas consultas funcionam com a [nova linguagem](../log-analytics/log-analytics-log-search-upgrade.md).

A seguir estão as consultas que você pode usar para ajudá-lo a monitorar seus bancos de dados do Azure Cosmos.

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
* Para obter todas as consultas maiores que 100 RUs unidas com dados de **DataPlaneRequests** e **QueryRunTimeStatistics**.

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
    
* Para obter estatísticas de base de partição para avaliar a distorção entre as 3 principais partições para conta de banco de dados:

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

- Consulte [a referência de dados de monitoramento do Azure Cosmos DB](monitor-cosmos-db-reference.md) para obter uma referência dos logs e métricas criados pelo Azure Cosmos DB.
- Consulte [Monitorando os recursos do Azure com o Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) para obter detalhes sobre o monitoramento dos recursos do Azure.
