---
title: Coletar dados de log
titleSuffix: Azure Cognitive Search
description: Coletar e analisar dados de log, habilitando uma configuração de diagnóstico e, em seguida, use o Kusto Query Language para explorar resultados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 86e869bc08552ea11728c508486a4784eccf4042
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462337"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Coletar e analisar dados de log para pesquisa cognitiva do Azure

Os registros diagnósticos ou operacionais fornecem informações sobre as operações detalhadas da Pesquisa Cognitiva do Azure e são úteis para monitorar os processos de serviço e carga de trabalho. Internamente, os logs existem no back-end por um curto período de tempo, o suficiente para investigação e análise, caso você registre um tíquete de suporte. No entanto, se você quiser auto-direção sobre dados operacionais, você deve configurar uma configuração de diagnóstico para especificar onde as informações de registro são coletadas.

A configuração de registros é útil para diagnósticos e preservação do histórico operacional. Depois de habilitar o registro, você pode executar consultas ou construir relatórios para análise estruturada.

A tabela a seguir enumera opções de coleta e persistência de dados.

| Recurso | Usado para |
|----------|----------|
| [Enviar para o espaço de trabalho do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | Eventos e métricas são enviados para um espaço de trabalho do Log Analytics, que pode ser consultado no portal para retornar informações detalhadas. Para uma introdução, consulte [Comece com os registros do Monitor do Azure](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Arquivo com armazenamento Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Eventos e métricas são arquivados em um contêiner Blob e armazenados em arquivos JSON. Os registros podem ser bastante granulares (por hora/minuto), úteis para pesquisar um incidente específico, mas não para uma investigação aberta. Use um editor JSON para exibir um arquivo de log bruto ou Power BI para agregar e visualizar dados de log.|
| [Fluxo para Hub de Eventos](https://docs.microsoft.com/azure/event-hubs/) | Eventos e métricas são transmitidos para um serviço Azure Event Hubs. Escolha esta opção como um serviço de coleta de dados alternativo para logs muito grandes. |

Tanto os registros do Monitor Do Azure quanto o armazenamento Blob estão disponíveis como um serviço gratuito para que você possa experimentá-lo gratuitamente durante toda a vida útil da sua assinatura do Azure. O Application Insights é gratuito para se inscrever e usar, desde que o tamanho de dados do aplicativo esteja abaixo de certos limites (confira a [página de preços](https://azure.microsoft.com/pricing/details/monitor/) para saber mais).

## <a name="prerequisites"></a>Pré-requisitos

Se você estiver usando o Log Analytics ou o Azure Storage, você pode criar recursos com antecedência.

+ [Crie um espaço de trabalho de análise de log](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ [Criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="enable-data-collection"></a>Habilitar coleta de dados

As configurações de diagnóstico especificam como eventos e métricas registradas são coletados.

1. Em **Monitoramento**, selecione **Configurações de diagnóstico**.

   ![Configurações de diagnóstico](./media/search-monitor-usage/diagnostic-settings.png "Configurações de Diagnóstico")

1. Selecione **+ Adicione a configuração de diagnóstico**

1. Verifique **o Log Analytics,** selecione seu espaço de trabalho e selecione **OperationLogs** e **AllMetrics**.

   ![Configurar a coleta de dados](./media/search-monitor-usage/configure-storage.png "Configurar a coleta de dados")

1. Salve a configuração.

1. Depois que o registro for ativado, use seu serviço de pesquisa para começar a gerar logs e métricas. Levará tempo até que eventos e métricas registradas estejam disponíveis.

Para o Log Analytics, levará vários minutos até que os dados estejam disponíveis, depois disso você pode executar consultas kusto para retornar dados. Para obter mais informações, consulte [Solicitações de consulta do Monitor](search-monitor-logs.md).

Para armazenamento Blob, leva uma hora até que os recipientes apareçam no armazenamento Blob. Haverá um blob por hora, por contêiner. Os contêineres são criados apenas quando há uma atividade para registrar ou medir. Quando os dados são copiados para uma conta de armazenamento, eles são formatados como JSON e são colocados em dois contêineres:

+ insights-logs-operationlogs: para logs de tráfego de pesquisa
+ insights-metrics-pt1m: para métrica

## <a name="query-log-information"></a>Informações do registro de consulta

Nos registros de diagnóstico, duas tabelas contêm registros e métricas para a Pesquisa Cognitiva do Azure: **AzureDiagnostics** e **AzureMetrics**.

1. Em **Monitoramento,** **selecione Logs**.

1. Digite **AzureMetrics** na janela de consulta. Execute esta consulta simples para se familiarizar com os dados coletados nesta tabela. Role a tabela para ver métricas e valores. Observe a contagem de registros no topo, e se o seu serviço estiver coletando métricas por um tempo, você pode querer ajustar o intervalo de tempo para obter um conjunto de dados gerenciável.

   ![Tabela AzureMetrics](./media/search-monitor-usage/azuremetrics-table.png "Tabela AzureMetrics")

1. Digite a seguinte consulta para retornar um conjunto de resultados tabular.

   ```
   AzureMetrics
    | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. Repita as etapas anteriores, começando **pelo AzureDiagnostics** para retornar todas as colunas para fins informativos, seguido por uma consulta mais seletiva que extrai informações mais interessantes.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![Tabela AzureDiagnostics](./media/search-monitor-usage/azurediagnostics-table.png "Tabela AzureDiagnostics")

## <a name="log-schema"></a>Esquema do log

Estruturas de dados que contêm dados de registro de pesquisa cognitiva do Azure estão em conformidade com o esquema abaixo. 

Para armazenamento Blob, cada bolha tem um objeto raiz chamado **registros** contendo uma matriz de objetos de log. Cada blob contém registros de todas as operações que ocorreram durante a mesma hora.

A tabela a seguir é uma lista parcial de campos comuns ao registro diagnóstico.

| Nome | Type | Exemplo | Observações |
| --- | --- | --- | --- |
| tempoGerado |DATETIME |"2018-12-07T00:00:43.6872559Z" |Carimbo de data/hora da operação |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>  MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |Seu ResourceId |
| operationName |string |"Query.Search" |O nome da operação |
| operationVersion |string |"2019-05-06" |A api-version usada |
| category |string |"OperationLogs" |constante |
| resultType |string |"Success" |Valores possíveis: Success ou Failure |
| resultSignature |INT |200 |Código do resultado HTTP |
| durationMS |INT |50 |Duração da operação em milissegundos |
| properties |objeto |confira a seguinte tabela |Objeto que contém os dados específicos da operação |

### <a name="properties-schema"></a>Esquema de propriedades

As propriedades abaixo são específicas para a Pesquisa Cognitiva do Azure.

| Nome | Type | Exemplo | Observações |
| --- | --- | --- | --- |
| Description_s |string |"GET /indexes('content')/docs" |Ponto de extremidade da operação |
| Documents_d |INT |42 |Número de documentos processados |
| IndexName_s |string |"Índice de teste" |Nome do índice associado à operação |
| Query_s |string |"?search=AzureSearch&$count=true&api-version=2019-05-06" |Parâmetros da consulta |

## <a name="metrics-schema"></a>Esquema de métricas

As métricas são capturadas para solicitações de consulta e medidas em intervalos de um minuto. Cada métrica expõe valores mínimo, máximo e médios por minuto. Para obter mais informações, consulte [Solicitações de consulta do Monitor](search-monitor-queries.md).

| Nome | Type | Exemplo | Observações |
| --- | --- | --- | --- |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |seu ID de recurso |
| metricName |string |"Latency" |o nome da métrica |
| time |DATETIME |"2018-12-07T00:00:43.6872559Z" |carimbo de data/hora da operação |
| média |INT |64 |O valor médio das amostras brutas no intervalo de tempo métrico, unidades em segundos ou porcentagem, dependendo da métrica. |
| mínimo |INT |37 |O valor mínimo das amostras brutas no intervalo de tempo métrico, unidades em segundos. |
| máximo |INT |78 |O valor máximo das amostras brutas no intervalo de tempo métrico, unidades em segundos.  |
| total |INT |258 |O valor total das amostras brutas no intervalo de tempo métrico, unidades em segundos.  |
| count |INT |4 |O número de métricas emitidas de um nó para o log dentro do intervalo de um minuto.  |
| intervalo de tempo |string |"PT1M" |O grão de tempo da métrica na ISO 8601. |

É comum que consultas sejam executadas em milissegundos, então apenas consultas que medem como segundos aparecerão em métricacomo QPS.

Para a métrica **Consultas de Pesquisa por Segundo,** o mínimo é o menor valor para consultas de pesquisa por segundo que foi registrado durante esse minuto. O mesmo se aplica ao valor máximo. O valor médio é a agregação durante todo o minuto. Por exemplo, dentro de um minuto, você pode ter um padrão como este: um segundo de alta carga que é o máximo para SearchQueriesPerSecond, seguido por 58 segundos de carga média, e finalmente um segundo com apenas uma consulta, que é o mínimo.

Para **a Porcentagem de Consultas de Pesquisa Estrangulada,** mínimo, máximo, médio e total, todos têm o mesmo valor: a porcentagem de consultas de pesquisa que foram estranguladas, a partir do número total de consultas de pesquisa durante um minuto.

## <a name="view-raw-log-files"></a>Exibir arquivos de log bruto

O armazenamento Blob é usado para arquivar arquivos de log. Você pode usar qualquer editor de JSON para exibir o arquivo de log. Se você não tem um, recomendamos usar o [Visual Studio Code](https://code.visualstudio.com/download).

1. No portal do Azure, abra sua conta de armazenamento. 

2. No painel de navegação à esquerda, clique em **Blobs**. Você deve ver **insights-logs-operationlogs** e **insights-metrics-pt1m**. Esses contêineres são criados pela Azure Cognitive Search quando os dados de log são exportados para o armazenamento Blob.

3. Clique para baixo na hierarquia de pastas até alcançar o arquivo .json.  Use o menu de contexto para baixar o arquivo.

Depois de baixar o arquivo, abra-o em um editor de JSON para exibir o conteúdo.

## <a name="next-steps"></a>Próximas etapas

Se você ainda não fez isso, revise os fundamentos do monitoramento do serviço de busca para saber sobre toda a gama de recursos de supervisão.

> [!div class="nextstepaction"]
> [Monitorar operações e atividades na Pesquisa Cognitiva do Azure](search-monitor-usage.md)