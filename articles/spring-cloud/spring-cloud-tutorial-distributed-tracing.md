---
title: 'Tutorial: Usar o Rastreamento Distribuído com o Azure Spring Cloud | Microsoft Docs'
description: Saiba como usar o Rastreamento Distribuído do Spring Cloud por meio do Azure Application Insights
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: 95aae47bddffd102c5d6d6dac67f22e6777cc3e7
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038225"
---
# <a name="tutorial-using-distributed-tracing-with-azure-spring-cloud"></a>Tutorial: Usar o Rastreamento Distribuído com o Azure Spring Cloud

As ferramentas de Rastreamento Distribuído do Spring Cloud permitem depuração fácil e monitoramento de problemas complexos. O Azure Spring Cloud integra o [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) ao Azure [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) para fornecer uma funcionalidade avançada de rastreamento distribuído por meio do portal do Azure.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Habilitar o Rastreamento Distribuído no portal do Azure
> * Adicionar o Spring Cloud Sleuth ao aplicativo
> * Exibir mapas de dependências para seus aplicativos de microsserviço
> * Pesquisar dados de rastreamento com filtros diferentes

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* Um serviço do Azure Spring Cloud já provisionado e em execução.  Conclua [este início rápido](spring-cloud-quickstart-launch-app-cli.md) para provisionar e iniciar um serviço do Azure Spring Cloud.
    
## <a name="add-dependencies"></a>Adicionar dependências

Habilite o remetente zipkin para enviar à Web adicionando a seguinte linha ao arquivo application.properties:

```xml
spring.zipkin.sender.type = web
```

Você poderá ignorar a próxima etapa se tiver seguido nosso [guia para preparar um aplicativo do Azure Spring Cloud](spring-cloud-tutorial-prepare-app-deployment.md). Caso contrário, vá para seu ambiente de desenvolvimento local e edite seu arquivo `pom.xml` para incluir a dependência Sleuth Spring Cloud:

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

* Crie e implante novamente para o serviço do Azure Spring Cloud para refletir essas alterações. 

## <a name="modify-the-sample-rate"></a>Modificar a taxa de amostragem
Modificando a taxa de amostragem, você pode alterar a taxa na qual a telemetria é coletada. Por exemplo, se você quiser reduzir a frequência da amostragem pela metade, vá para o arquivo `application.properties` e altere a seguinte linha:

```xml
spring.sleuth.sampler.probability=0.5
```

Se já tiver um aplicativo criado e implantado, você poderá modificar a taxa de amostragem adicionando a linha acima como uma variável de ambiente no portal ou na CLI do Azure. 

## <a name="enable-application-insights"></a>Habilitar o Application Insights

1. Acesse sua página de serviços do Azure Spring Cloud no portal do Azure.
1. Na seção Monitoramento, selecione **Rastreamento Distribuído**.
1. Selecione **Editar configuração** para editar ou adicionar uma nova configuração.
1. Crie uma nova consulta do Application Insights ou selecione uma existente.
1. Escolha qual categoria de log você deseja monitorar e especifique o tempo de retenção (em dias).
1. Selecione **Aplicar** para aplicar o novo rastreamento.

## <a name="view-application-map"></a>Exibir mapa do aplicativo

Retorne à página do Rastreamento Distribuído e selecione **Exibir mapa do aplicativo**. Examine a representação visual do aplicativo das configurações de monitoramento. Para saber como usar o mapa do aplicativo, confira [este artigo](https://docs.microsoft.com/azure/azure-monitor/app/app-map).

## <a name="search"></a>Search

Use a função de pesquisa para consultar outros itens de telemetria específicos. Na página de **Rastreamento Distribuído**, selecione **Pesquisar**. Para saber mais sobre como usar a funcionalidade de pesquisa, veja [este artigo](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search).

## <a name="application-insights-page"></a>Página do Application Insights

Application Insights fornece recursos de monitoramento além do mapa do aplicativo e da pesquisa. Pesquise pelo nome do aplicativo no portal do Azure e, em seguida, inicie uma página do Application Insights para saber mais. Para obter mais diretrizes sobre como usar essas ferramentas, [confira a documentação](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).


## <a name="disable-application-insights"></a>Desabilitar o Application Insights

1. Acesse sua página de serviços do Azure Spring Cloud no portal do Azure.
1. Na seção Monitoramento, clique em **Rastreamento Distribuído**.
1. Clique em **Desabilitar** para desabilitar o Application Insights

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a habilitar e a entender o rastreamento distribuído no Azure Spring Cloud. Para saber como associar seu aplicativo a um Azure Cosmos DB, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Saiba como associar o aplicativo a um Azure Cosmos DB](spring-cloud-tutorial-bind-cosmos.md).
