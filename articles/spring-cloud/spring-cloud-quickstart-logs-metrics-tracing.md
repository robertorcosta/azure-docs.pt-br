---
title: Guia de início rápido – Monitoramento de aplicativos do Azure Spring Cloud com logs, métricas e rastreamento
description: Use o streaming de log, o Log Analytics, as métricas e o rastreamento para monitorar aplicativos de exemplo Piggymetrics no Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: f9f03c355e1e619d004c8ec8c1cc2f91932db744
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89046826"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>Início Rápido: Monitoramento de aplicativos do Azure Spring Cloud com logs, métricas e rastreamento

Com a funcionalidade de monitoramento integrado no Azure Spring Cloud, você pode depurar e monitorar problemas complexos. O Azure Spring Cloud integra o [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) ao [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) do Azure. Essa integração fornece uma funcionalidade avançada de logs, métricas e rastreamento distribuído no portal do Azure. Os procedimentos a seguir explicam como usar o Streaming de Log, o Log Analytics, as Métricas e o rastreamento distribuído com aplicativos PiggyMetrics implantados.

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas anteriores: 

* [Provisionar uma instância do Azure Spring Cloud](spring-cloud-quickstart-provision-service-instance.md)
* [Configurar o Config Server](spring-cloud-quickstart-setup-config-server.md)
* [Criar e implantar aplicativos](spring-cloud-quickstart-deploy-apps.md).

## <a name="logs"></a>Logs

Há duas maneiras de ver os logs no Azure Spring Cloud: O **Streaming de Log** de logs em tempo real por instância de aplicativo ou o **Log Analytics** para logs agregados com capacidade de consulta avançada.

### <a name="log-streaming"></a>Streaming de log

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Você pode usar o streaming de log na CLI do Azure com o comando a seguir.

```azurecli
az spring-cloud app logs -s <service instance name> -g <resource group name> -n gateway -f
```

Você verá logs como este:

[ ![Streaming de Log da CLI do Azure](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png#lightbox)

> [!TIP]
> Use `az spring-cloud app logs -h` para explorar mais parâmetros e funcionalidades de fluxo de log.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

Para obter os logs usando o Azure Toolkit for IntelliJ:

1. Selecione **Azure Explorer** e, em seguida, **Spring Cloud**.

1. Clique com o botão direito do mouse no aplicativo em execução.

1. Selecione **Logs de Streaming** na lista suspensa.

   ![Selecionar os logs de streaming](media/spring-cloud-intellij-howto/streaming-logs.png)
    
1. Selecione a **Instância**.

   ![Selecionar instância](media/spring-cloud-intellij-howto/select-instance.png)
    
1. O log de streaming ficará visível na janela de saída.

   ![Saída do log de streaming](media/spring-cloud-intellij-howto/streaming-log-output.png)

---
### <a name="log-analytics"></a>Log Analytics

1. Acesse a página **serviço | Visão geral** e selecione **Logs** na seção **Monitoramento**. Clique em **Executar** em uma das consultas de exemplo do Azure Spring Cloud. 

   [ ![Entrada do Logs Analytics](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Em seguida, você verá logs filtrados. Confira os [documentos do Azure Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries) para obter mais diretrizes sobre como escrever consultas.

   [ ![Consulta do Logs Analytics](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png#lightbox)

## <a name="metrics"></a>Métricas

1. Acesse a página **serviço | Visão geral** e selecione **Métricas** na seção **Monitoramento**. Adicione a sua primeira métrica selecionando `system.cpu.usage` para **Métrica** e `Avg` para **Agregação** para ver a linha do tempo do uso geral da CPU.

   [ ![Entrada de métricas](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png#lightbox)
    
1. Clique em **Adicionar filtro** na barra de ferramentas acima, selecione `App=Gateway` para ver o uso da CPU somente para o aplicativo de **gateway**.

   [ ![Usar filtro em métricas](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png#lightbox)

1. Ignore o filtro criado acima, clique em **Aplicar Divisão** e selecione `App` para **Valores** para ver o uso da CPU por aplicativos diferentes.

   [ ![Aplicar divisão em métricas](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png#lightbox)

## <a name="distributed-tracing"></a>Rastreamento distribuído

1. Acesse a página **serviço | Visão geral** e selecione **Rastreamento distribuído** na seção **Monitoramento**. Em seguida, clique na guia **Exibir o mapa do aplicativo** à direita.

   [ ![Entrada do Rastreamento Distribuído](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Agora você pode ver o status de chamadas entre os aplicativos Piggymetrics. 

   [ ![Visão geral do rastreamento distribuído](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png#lightbox)
    
1. Clique no link entre **gateway** e **account-service** para ver mais detalhes, como chamadas mais lentas por métodos HTTP.

   [ ![Rastreamento distribuído](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png#lightbox)
    
1. Por fim, clique em **Investigar Desempenho** para explorar uma análise de desempenho interna mais avançada.

   [ ![Desempenho de rastreamento distribuído](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png#lightbox)

## <a name="clean-up-resources"></a>Limpar os recursos

Nas etapas anteriores, você criou os recursos do Azure em um grupo de recursos. Se você acha que não precisará desses recursos no futuro, exclua o grupo de recursos por meio do portal ou executando o seguinte comando no Cloud Shell:

```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

Nas etapas anteriores, você também define o nome do grupo de recursos padrão. Para limpar o padrão, execute o seguinte comando no Cloud Shell:

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>Próximas etapas

Para explorar mais a funcionalidade de monitoramento pronto para uso para o Azure Spring Cloud, confira:

> [!div class="nextstepaction"]
> [Serviços de diagnóstico](diagnostic-services.md)
> [Rastreamento distribuído](spring-cloud-tutorial-distributed-tracing.md)
> [Transmitir logs em tempo real](spring-cloud-howto-log-streaming.md)
