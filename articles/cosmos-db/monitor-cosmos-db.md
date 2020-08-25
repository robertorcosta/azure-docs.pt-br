---
title: Monitoramento do Azure Cosmos DB | Microsoft Docs
description: Saiba como monitorar o desempenho e a disponibilidade do Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/24/2020
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: 12bf87e16bf4506f2015dd75fb360f8de8399902
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797812"
---
# <a name="monitoring-azure-cosmos-db"></a>Como monitorar o Azure Cosmos DB

Quando você tem aplicativos e processos de negócios críticos que dependem de recursos do Azure, recomendamos monitorar a disponibilidade, o desempenho e a operação desses recursos. Este artigo descreve os dados de monitoramento gerados pelos bancos de dados do Azure Cosmos e como você pode usar os recursos do Azure Monitor para analisar e alertar sobre esses dados.

Você pode monitorar seus dados com as métricas do lado do cliente e do servidor. Ao usar as métricas do lado do servidor, você pode monitorar os dados armazenados no Azure Cosmos DB com as seguintes opções:

* **Monitorar do portal de Azure Cosmos DB:** Você pode monitorar com as métricas disponíveis na guia **métricas** da conta do Azure Cosmos. As métricas nessa guia incluem taxa de transferência, armazenamento, disponibilidade, latência, consistência e métricas de nível de sistema. Por padrão, essas métricas têm um período de retenção de 7 dias. Para saber mais, consulte a seção [monitoramento de dados coletados de Azure Cosmos DB](#monitoring-from-azure-cosmos-db) deste artigo.

* **Monitorar com métricas no Azure monitor:** Você pode monitorar as métricas de sua conta do Azure Cosmos e criar painéis do Azure Monitor. Azure Monitor coleta as métricas de Azure Cosmos DB por padrão, você não configurou nada explicitamente. Essas métricas são coletadas com granularidade de um minuto, a granularidade pode variar com base na métrica que você escolher. Por padrão, essas métricas têm um período de retenção de 30 dias. A maioria das métricas que estão disponíveis nas opções anteriores também estão disponíveis nessas métricas. Os valores de dimensão para as métricas, como o nome do contêiner, não diferenciam maiúsculas de minúsculas. Portanto, você precisa usar a comparação de maiúsculas e minúsculas ao fazer comparações de cadeias de caracteres nesses valores de dimensão. Para saber mais, consulte a seção [analisar dados de métrica](#analyze-metric-data) deste artigo.

* **Monitorar com os logs de diagnóstico no Azure monitor:** Você pode monitorar os logs da sua conta do Azure Cosmos e criar painéis do Azure Monitor. A telemetria, como eventos e rastreamentos que ocorrem em uma granularidade de segundo, são armazenados como logs. Por exemplo, se a taxa de transferência de um contêiner for alterada, as propriedades de uma conta do cosmos serão alteradas, esses eventos serão capturados nos logs. Você pode analisar esses logs executando consultas nos dados coletados. Para saber mais, consulte a seção [analisar dados de log](#analyze-log-data) deste artigo.

* **Monitorar programaticamente com SDKs:** Você pode monitorar sua conta do Azure Cosmos por meio de programação usando .NET, Java, Python, Node.js SDKs e os cabeçalhos na API REST. Para saber mais, consulte a seção [monitoramento Azure Cosmos DB programaticamente](#monitor-cosmosdb-programmatically) deste artigo.

A imagem a seguir mostra opções diferentes disponíveis para monitorar Azure Cosmos DB conta por meio de portal do Azure:

:::image type="content" source="media/monitor-cosmos-db/monitoring-options-portal.png" alt-text="Opções de monitoramento disponíveis no portal do Azure" border="false":::

Ao usar Azure Cosmos DB, no lado do cliente, você pode coletar os detalhes de cobrança de solicitação, ID da atividade, informações de rastreamento de exceção/pilha, código de status/substatus HTTP, Cadeia de caracteres de diagnóstico para depurar qualquer problema que possa ocorrer. Essas informações também serão necessárias se você precisar entrar na equipe de suporte do Azure Cosmos DB.  

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?

O Azure Cosmos DB cria dados de monitoramento usando o [Azure Monitor](../azure-monitor/overview.md), que é um serviço de monitoramento de pilha completo no Azure que fornece um conjunto completo de recursos para monitorar seus recursos do Azure, além de recursos em outras nuvens e locais.

Se você ainda não está familiarizado com o monitoramento de serviços do Azure, comece com o artigo [Como monitorar os recursos do Azure com o Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) que descreve o seguinte:

* O que é o Azure Monitor?
* Custos associados ao monitoramento
* Monitoramento de dados coletados no Azure
* Configuração de coleta de dados
* Ferramentas padrão no Azure para analisar e alertar sobre dados de monitoramento

As seções a seguir se baseiam neste artigo descrevendo os dados específicos coletados do Azure Cosmos DB e fornecendo exemplos para configurar a coleta de dados e analisar esses dados com as ferramentas do Azure.

## <a name="azure-monitor-for-azure-cosmos-db"></a>Azure Monitor para o Azure Cosmos DB

O Azure Monitor para Azure Cosmos DB baseia-se no [recurso pastas de trabalho do Azure Monitor](../azure-monitor/platform/workbooks-overview.md) e usa os mesmos dados de monitoramento coletados para o Cosmos DB descritos nas seções abaixo. Use Azure Monitor para exibir o desempenho geral, as falhas, a capacidade e a integridade operacional de todos os seus recursos do Azure Cosmos DB em uma experiência interativa unificada e aproveitar os outros recursos do Azure Monitor para análise e alertas detalhados. Para saber mais, confira o artigo [Explorar o Azure Monitor para o Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md).

> [!NOTE]
> Ao criar contêineres, não crie dois contêineres com o mesmo nome, mas com maiúsculas e minúsculas diferentes. Isso porque algumas partes da plataforma Azure não diferenciam maiúsculas de minúsculas, o que pode resultar em confusão/colisão de telemetria e ações em contêineres com tais nomes.

## <a name="monitor-data-collected-from-azure-cosmos-db-portal"></a><a id="monitoring-from-azure-cosmos-db"></a> Monitorar dados coletados do portal Azure Cosmos DB

O Azure Cosmos DB coleta os mesmos tipos de dados de monitoramento que outros recursos do Azure, que são descritos em [Dados de monitoramento dos recursos do Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Confira [Referência de dados de monitoramento do Azure Cosmos DB](monitor-cosmos-db-reference.md) para obter uma referência detalhada dos logs e métricas criados pelo Azure Cosmos DB.

A página **Visão Geral** no portal do Azure para cada banco de dados do Azure Cosmos inclui uma visão breve do uso do banco de dados, incluindo sua solicitação e o uso de cobrança por hora. Essas informações são úteis, mas apenas uma pequena quantidade de dados de monitoramento está disponível. Alguns desses dados são coletados automaticamente e ficam disponíveis para análise assim que você cria o banco de dados, embora você possa habilitar a coleta de dado adicional com algumas configurações.

:::image type="content" source="media/monitor-cosmos-db/overview-page.png" alt-text="Página de visão geral":::

## <a name="analyzing-metric-data"></a><a id="analyze-metric-data"></a> Analisando dados de métrica

O Azure Cosmos DB fornece uma experiência personalizada para trabalhar com métricas. Confira [Monitorar e depurar as métricas do Azure Cosmos DB usando o Azure Monitor](cosmos-db-azure-monitor-metrics.md) para obter detalhes sobre como usar essa experiência e para analisar diferentes cenários do Azure Cosmos DB.

Você pode analisar as métricas para o Azure Cosmos DB com métricas de outros serviços do Azure usando o Metrics Explorer abrindo **Métricas** no menu **Azure Monitor**. Consulte [Introdução ao Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md) para obter detalhes sobre como usar essa ferramenta. Todas as métricas para o Azure Cosmos DB estão no namespace **Métricas padrão do Cosmos DB**. Você pode usar as seguintes dimensões com essas métricas ao adicionar um filtro a um gráfico:

* CollectionName
* DatabaseName
* OperationType
* Região
* StatusCode

### <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Exibir métricas de nível de operação para o Azure Cosmos DB

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Selecione **Monitor** na barra de menus de navegação à esquerda e selecione **Métricas**.

   :::image type="content" source="./media/monitor-cosmos-db/monitor-metrics-blade.png" alt-text="Painel Métricas no Azure Monitor":::

1. No painel **Métricas** > **Selecionar um recurso** > escolha a **assinatura** e o **grupo de recursos** necessários. Para o **Tipo de recurso**, selecione **Contas do Azure Cosmos DB**, escolha uma das contas existentes do Azure Cosmos e selecione **Aplicar**.

   :::image type="content" source="./media/monitor-cosmos-db/select-cosmosdb-account.png" alt-text="Escolha uma conta do Cosmos DB para exibir as métricas":::

1. Em seguida, você pode selecionar uma métrica na lista de métricas disponíveis. Você pode selecionar métricas específicas para unidades de solicitação, armazenamento, latência, disponibilidade, Cassandra e outros. Para saber mais detalhadamente sobre todas as métricas disponíveis nesta lista, consulte o artigo [Métricas por categoria](monitor-cosmos-db-reference.md). Neste exemplo, vamos selecionar **Unidades de solicitação** e **Média** como o valor de agregação.

   Além desses detalhes, você também pode selecionar o **Intervalo de tempo** e a **Granularidade de tempo** das métricas. Você pode exibir as métricas de, no máximo, os últimos 30 dias.  Depois que você aplicar o filtro, um gráfico será exibido com base no seu filtro. Você pode ver o número médio de unidades de solicitação consumidas por minuto pelo período selecionado.  

   :::image type="content" source="./media/monitor-cosmos-db/metric-types.png" alt-text="Escolha uma métrica no portal do Azure":::

### <a name="add-filters-to-metrics"></a>Adicionar filtros a métricas

Você também pode filtrar as métricas e os gráficos exibidos por um **CollectionName**, **DatabaseName**, **OperationType**, **Region** e **StatusCode** específicos. Para filtrar as métricas, selecione **Adicionar filtro** e escolha a propriedade necessária, como **OperationType** e selecione um valor como **Consulta**. Em seguida, o grafo exibe as unidades de solicitação consumidas para a operação de consulta para o período selecionado. As operações executadas por meio do procedimento armazenado não são registradas em log, de modo que não estão disponíveis na métrica OperationType.

:::image type="content" source="./media/monitor-cosmos-db/add-metrics-filter.png" alt-text="Adicionar um filtro para selecionar a granularidade da métrica":::

Você pode agrupar as métricas usando a opção **Aplicar divisão**. Por exemplo, você pode agrupar as unidades de solicitação por tipo de operação e exibir o grafo para todas as operações de uma só vez, conforme mostrado na imagem a seguir:

:::image type="content" source="./media/monitor-cosmos-db/apply-metrics-splitting.png" alt-text="Adicionar filtro de divisão de aplicação":::

## <a name="analyzing-log-data"></a><a id="analyze-log-data"></a> Analisando dados de log

Os dados em Logs do Azure Monitor são armazenados em tabelas, cada uma com o próprio conjunto de propriedades exclusivas. O Azure Cosmos DB armazena dados nas tabelas a seguir.

| Tabela | Descrição |
|:---|:---|
| AzureDiagnostics | Tabela comum usada por vários serviços para armazenar logs de recursos. Os logs de recursos do Azure Cosmos DB podem ser identificados com `MICROSOFT.DOCUMENTDB`.   |
| AzureActivity    | Tabela comum que armazena todos os registros do log de Atividades.

> [!IMPORTANT]
> Quando você seleciona **Logs** no menu do Azure Cosmos DB, o Log Analytics é aberto com o escopo de consulta definido para o banco de dados do Azure Cosmos atual. Isso significa que as consultas de log incluirão apenas os dados desse recurso. Se você quiser executar uma consulta que inclua dados de outros bancos de dados ou de outros serviços do Azure, selecione **Logs** no menu **Azure Monitor**. Confira [Escopo da consulta de log e intervalo de tempo no Log Analytics do Azure Monitor](../azure-monitor/log-query/scope.md) para obter detalhes.

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Consultas do Log Analytics do Azure Cosmos DB no Azure Monitor

Aqui estão algumas consultas que você pode inserir na barra de pesquisa **Pesquisa de logs** para ajudar na monitoração de seus contêineres do Azure Cosmos. Essas consultas funcionam com a [nova linguagem](../log-analytics/log-analytics-log-search-upgrade.md).

Veja a seguir as consultas que podem ser usadas para ajudar você a monitorar seus bancos de dados do Azure Cosmos.

* Para consultar todos os logs de diagnóstico do Azure Cosmos DB para um período especificado:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests"

    ```

* Para consultar todas as operações, agrupadas por recurso:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* Para consultar toda a atividade do usuário, agrupada por recurso:

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```

## <a name="monitor-azure-cosmos-db-programmatically"></a><a id="monitor-cosmosdb-programmatically"></a> Monitorar Azure Cosmos DB programaticamente

As métricas no nível da conta disponíveis no portal, como o uso de armazenamento da conta e o total de solicitações, não estão disponíveis por meio das APIs do SQL. No entanto, você pode recuperar os dados de uso no nível da coleção usando as APIs do SQL. Para recuperar os dados do nível de coleção, faça o seguinte:

* Para usar a API REST, [execute um GET na coleção](https://msdn.microsoft.com/library/mt489073.aspx). As informações de cota e de uso da coleção são retornadas nos cabeçalhos x-ms-resource-quota e x-ms-resource-usage na resposta.

* Para usar o SDK do .NET, use o método [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx), que retorna uma [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) que contém diversas propriedades de uso como **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage** e muito mais.

Para acessar outras métricas, use o [SDK do Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Insights). As definições de métricas disponíveis podem ser recuperadas chamando:

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01
```

Para recuperar métricas individuais, use o seguinte formato:

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metrics?timespan={StartTime}/{EndTime}&interval={AggregationInterval}&metricnames={MetricName}&aggregation={AggregationType}&`$filter={Filter}&api-version=2018-01-01
```

Para saber mais, confira o artigo da [API REST de monitoramento do Azure](../azure-monitor/platform/rest-api-walkthrough.md) .

## <a name="next-steps"></a>Próximas etapas

* Confira [Referência de dados de monitoramento do Azure Cosmos DB](monitor-cosmos-db-reference.md) para obter uma referência dos logs e métricas criados pelo Azure Cosmos DB.
* Confira [Como monitorar os recursos do Azure com o Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) para obter detalhes sobre o monitoramento de recursos do Azure.
