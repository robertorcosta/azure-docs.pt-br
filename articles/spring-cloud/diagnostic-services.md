---
title: Analise registros e métricas no Azure Spring Cloud | Microsoft Docs
description: Saiba como analisar dados de diagnóstico no Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.openlocfilehash: adbcf28cfbbe2ea3b7cc9c7fd0d1c76246938344
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870399"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Analisar registros e métricas com configurações de diagnóstico

Usando a funcionalidade de diagnóstico do Azure Spring Cloud, você pode analisar logs e métricas com qualquer um dos seguintes serviços:

* Use o Azure Log Analytics, onde os dados são gravados no Azure Storage. Há um atraso na exportação de logs para o Log Analytics.
* Salve logs em uma conta de armazenamento para auditoria ou inspeção manual. Você pode especificar o tempo de retenção (em dias).
* Faça logs de stream no seu hub de eventos para ingestão por um serviço de terceiros ou uma solução de análise personalizada.

Escolha a categoria de log e a categoria métrica que deseja monitorar.

> [!TIP]
> Só quer transmitir seus registros? Confira este [comando Azure CLI](https://docs.microsoft.com/cli/azure/ext/spring-cloud/spring-cloud/app?view=azure-cli-latest#ext-spring-cloud-az-spring-cloud-app-logs)!

## <a name="logs"></a>Logs

|Log | Descrição |
|----|----|
| **Console de aplicativos** | Registro de console de todos os aplicativos do cliente. | 
| **Logs de sistema** | Atualmente, apenas [o Spring Config Server](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) faz lognesta categoria. |

## <a name="metrics"></a>Métricas

Para obter uma lista completa de métricas, consulte [Spring Cloud Metrics](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-concept-metrics#user-metrics-options).

Para começar, habilite um desses serviços para receber os dados. Para saber mais sobre a configuração do Log Analytics, consulte [Iniciar com o Log Analytics no Azure Monitor](../azure-monitor/log-query/get-started-portal.md). 

## <a name="configure-diagnostics-settings"></a>Configure as configurações de diagnóstico

1. No portal Azure, vá para a sua instância Azure Spring Cloud.
1. Selecione a opção **Configurações diagnósticos** e selecione **Adicionar configuração Diagnóstico**.
1. Digite um nome para a configuração e escolha para onde deseja enviar os logs. Você pode selecionar qualquer combinação das três opções a seguir:
    * **Arquivar em uma conta de armazenamento**
    * **Fluxo para um hub de eventos**
    * **Enviar para o Log Analytics**

1. Escolha qual categoria de log e categoria métrica você deseja monitorar e, em seguida, especifique o tempo de retenção (em dias). O tempo de retenção se aplica apenas à conta de armazenamento.
1. Clique em **Salvar**.

> [!NOTE]
> Pode haver uma lacuna de até 15 minutos entre quando logs ou métricas são emitidos e quando eles aparecem em sua conta de armazenamento, seu hub de eventos ou Log Analytics.

## <a name="view-the-logs-and-metrics"></a>Veja os registros e métricas
Existem vários métodos para visualizar logs e métricas conforme descrito nos seguintes títulos.

### <a name="use-logs-blade"></a>Use a lâmina Logs

1. No portal Azure, vá para a sua instância Azure Spring Cloud.
1. Para abrir o painel **Log Search,** selecione **Logs**.
1. Na caixa de pesquisa **Log**
   * Para exibir logs, digite uma consulta simples como:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * Para visualizar métricas, digite uma consulta simples como:

    ```sql
    AzureMetrics
    | limit 50
    ```
1. Para visualizar o resultado da pesquisa, **selecione Executar**.

### <a name="use-log-analytics"></a>Usar o Log Analytics

1. No portal Azure, no painel esquerdo, selecione **Log Analytics**.
1. Selecione o espaço de trabalho do Log Analytics que você escolheu ao adicionar suas configurações de diagnóstico.
1. Para abrir o painel **Log Search,** selecione **Logs**.
1. Na caixa de pesquisa **Log,**
   * para exibir logs, inserir uma consulta simples, como:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * para visualizar métricas, insira uma consulta simples, como:

    ```sql
    AzureMetrics
    | limit 50
    ```

1. Para visualizar o resultado da pesquisa, **selecione Executar**.
1. Você pode pesquisar os registros do aplicativo ou instância específico definindo uma condição de filtro:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]  
> `==`é sensível a `=~` casos, mas não é.

Para saber mais sobre o idioma de consulta usado no Log Analytics, consulte [consultas de log do Azure Monitor](../azure-monitor/log-query/query-language.md).

### <a name="use-your-storage-account"></a>Use sua conta de armazenamento 

1. No portal Azure, no painel esquerdo, selecione **Contas de armazenamento**.

1. Selecione a conta de armazenamento escolhida ao adicionar suas configurações de diagnóstico.
1. Para abrir o painel **do recipiente Blob,** selecione **Blobs**.
1. Para revisar os registros de aplicativos, procure por um contêiner chamado **insights-logs-applicationconsole**.
1. Para revisar as métricas do aplicativo, procure um contêiner chamado **insights-metrics-pt1m**.

Para saber mais sobre o envio de informações de diagnóstico para uma conta de armazenamento, consulte [Armazenar e visualizar dados de diagnóstico no Azure Storage](../storage/common/storage-introduction.md).

### <a name="use-your-event-hub"></a>Use seu hub de eventos

1. No portal Azure, no painel esquerdo, selecione **Event Hubs**.

1. Procure e selecione o centro de eventos que você escolheu ao adicionar suas configurações de diagnóstico.
1. Para abrir o painel **Event Hub List,** selecione **Event Hubs**.
1. Para revisar os registros de aplicativos, procure um hub de eventos chamado **insights-logs-applicationconsole**.
1. Para revisar as métricas do aplicativo, procure um hub de eventos chamado **insights-metrics-pt1m**.

Para saber mais sobre como enviar informações de diagnóstico para um hub de eventos, consulte [os dados do Streaming Azure Diagnostics no caminho quente usando hubs de eventos](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs).

## <a name="analyze-the-logs"></a>Analisar os registros

O Azure Log Analytics está sendo executado com um mecanismo Kusto para que você possa consultar seus registros para análise. Para uma rápida introdução aos registros de consulta usando Kusto, revise o [tutorial do Log Analytics](../azure-monitor/log-query/get-started-portal.md).

Os registros de aplicativos fornecem informações críticas e registros verbosos sobre a saúde, o desempenho e muito mais do seu aplicativo. Nas próximas seções há algumas consultas simples para ajudá-lo a entender os estados atuais e passados do seu aplicativo.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Mostrar registros de aplicativos do Azure Spring Cloud

Para rever uma lista de registros de aplicativos do Azure Spring Cloud, classificados por tempo com os registros mais recentes mostrados primeiro, execute a seguinte consulta:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Mostrar entradas de logs contendo erros ou exceções

Para revisar entradas de log não classificadas que mencionam um erro ou exceção, execute a seguinte consulta:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Use esta consulta para encontrar erros ou modificar os termos da consulta para encontrar códigos de erro ou exceções específicas. 

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Mostre o número de erros e exceções relatados pelo seu aplicativo na última hora

Para criar um gráfico de tortas que exibe o número de erros e exceções registrados pelo seu aplicativo na última hora, execute a seguinte consulta:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Saiba mais sobre a consulta de logs de aplicativos

O Azure Monitor oferece amplo suporte para consulta de logs de aplicativos usando o Log Analytics. Para saber mais sobre este serviço, consulte [Comece com consultas de log no Azure Monitor](../azure-monitor/log-query/get-started-queries.md). Para obter mais informações sobre consultas de construção para analisar os registros de aplicativos, consulte [Visão geral das consultas de log no Azure Monitor](../azure-monitor/log-query/log-query-overview.md).
