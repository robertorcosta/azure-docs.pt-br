---
title: Tutorial – Usar o Rastreamento Distribuído com o Azure Spring Cloud
description: Este tutorial mostra como usar o Rastreamento Distribuído do Spring Cloud por meio do Azure Application Insights
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: brendm
ms.openlocfilehash: 0815aa084462d1b829d64cd7c5d6fa7cebf534fc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78273204"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>Usar o rastreamento distribuído com o Azure Spring Cloud

Com as ferramentas de rastreamento distribuído do Azure Spring Cloud, você pode depurar e monitorar problemas complexos com facilidade. O Azure Spring Cloud integra o [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) ao [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) do Azure. Essa integração fornece uma funcionalidade avançada de rastreamento distribuído no portal do Azure.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Habilitar o rastreamento distribuído no portal do Azure.
> * Adicionar o Spring Cloud Sleuth ao seu aplicativo.
> * Exibir mapas de dependências para seus aplicativos de microsserviço.
> * Pesquisar dados de rastreamento com diferentes filtros.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará de um serviço do Azure Spring Cloud que já esteja provisionado e em execução. Conclua o [início rápido sobre como implantar um aplicativo por meio da CLI do Azure](spring-cloud-quickstart-launch-app-cli.md) para provisionar e executar um serviço do Azure Spring Cloud.
    
## <a name="add-dependencies"></a>Adicionar dependências

1. Adicione a seguinte linha ao arquivo application.properties:

   ```xml
   spring.zipkin.sender.type = web
   ```

   Após essa alteração, o remetente Zipkin pode fazer o envio para a Web.

1. Ignore esta etapa caso tenha seguido nosso [guia para preparar um aplicativo do Azure Spring Cloud](spring-cloud-tutorial-prepare-app-deployment.md). Caso contrário, acesse o ambiente de desenvolvimento local e edite o arquivo pom.xml para incluir a seguinte dependência do Spring Cloud Sleuth:

    ```xml
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-sleuth</artifactId>
                <version>${spring-cloud-sleuth.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-sleuth</artifactId>
        </dependency>
    </dependencies>
    ```

1. Crie e implante novamente para o serviço do Azure Spring Cloud para refletir essas alterações.

## <a name="modify-the-sample-rate"></a>Modificar a taxa de amostragem

Modificando a taxa de amostragem, você pode alterar a taxa na qual a telemetria é coletada. Por exemplo, se você quiser reduzir a frequência da amostragem pela metade, abra o arquivo application.properties e altere a seguinte linha:

```xml
spring.sleuth.sampler.probability=0.5
```

Caso já tenha criado e implantado um aplicativo, modifique a taxa de amostra. Faça isso adicionando a linha anterior como uma variável de ambiente na CLI do Azure ou no portal do Azure.

## <a name="enable-application-insights"></a>Habilitar o Application Insights

1. Acesse sua página de serviços do Azure Spring Cloud no portal do Azure.
1. Na página **Monitoramento**, selecione **Rastreamento Distribuído**.
1. Selecione **Editar configuração** para editar ou adicionar uma nova configuração.
1. Crie uma consulta do Application Insights ou selecione uma existente.
1. Escolha qual categoria de log deseja monitorar e especifique o tempo de retenção em dias.
1. Selecione **Aplicar** para aplicar o novo rastreamento.

## <a name="view-the-application-map"></a>Exibir o mapa do aplicativo

Retorne à página **Rastreamento Distribuído** e selecione **Exibir mapa do aplicativo**. Examine a representação visual do aplicativo das configurações de monitoramento. Para saber como usar o mapa do aplicativo, confira [Mapa do aplicativo: Triagem dos aplicativos distribuídos](https://docs.microsoft.com/azure/azure-monitor/app/app-map).

## <a name="use-search"></a>Usar a pesquisa

Use a função de pesquisa para consultar outros itens de telemetria específicos. Na página de **Rastreamento Distribuído**, selecione **Pesquisar**. Para obter mais informações sobre como usar a função de pesquisa, confira [Como usar a pesquisa no Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search).

## <a name="use-application-insights"></a>Usar o Application insights

O Application Insights fornece funcionalidades de monitoramento além do mapa do aplicativo e da função de pesquisa. Pesquise o nome do aplicativo no portal do Azure e, em seguida, abra uma página do Application Insights para encontrar informações de monitoramento. Para obter mais diretrizes sobre como usar essas ferramentas, confira [Consultas de log do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

## <a name="disable-application-insights"></a>Desabilitar o Application Insights

1. Acesse sua página de serviços do Azure Spring Cloud no portal do Azure.
1. Em **Monitoramento**, selecione **Rastreamento Distribuído**.
1. Selecione **Desabilitar** para desabilitar o Application Insights.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a habilitar e entender o rastreamento distribuído no Azure Spring Cloud. Para saber como associar seu aplicativo a um banco de dados do Azure Cosmos DB, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Saiba como fazer a associação a um banco de dados do Azure Cosmos DB](spring-cloud-tutorial-bind-cosmos.md)
