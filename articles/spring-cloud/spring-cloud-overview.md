---
title: Introdução ao Azure Spring Cloud
description: Conheça os recursos e benefícios do Azure Spring Cloud para implantar e gerenciar aplicativos Spring Java no Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: aa3f1032301224701f5bfc08807e89194f263da6
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89255232"
---
# <a name="what-is-azure-spring-cloud"></a>O que é o Azure Spring Cloud?

O Azure Spring Cloud facilita a implantação de aplicativos de microsserviço baseados no Spring Boot no Azure com zero alterações de código.  O Azure Spring Cloud gerencia a infraestrutura dos aplicativos do Spring Cloud para que os desenvolvedores possam se concentrar no código.  O Spring Cloud fornece gerenciamento de ciclo de vida usando monitoramento e diagnóstico abrangentes, gerenciamento de configuração, descoberta de serviços, integração de CI/CD, implantações em “blue-green” e muito mais.

Como parte do ecossistema do Azure, o Azure Spring Cloud permite fácil associação a outros serviços do Azure, incluindo armazenamento, bancos de dados, monitoramento e mais.

Esta introdução descreve o servidor de configuração do Azure Spring Cloud, como habilitar implantações azuis/verdes, escalar aplicativos e monitorar o desempenho do aplicativo.

## <a name="spring-cloud-config-server"></a>Servidor de Configuração do Spring Cloud

O Servidor de Configuração do Azure Spring Cloud oferece configuração externalizada em um sistema distribuído com suporte a servidor e cliente.  O Azure Spring Cloud Config Server é uma localização central para gerenciar propriedades de aplicativo em todos os ambientes. Para obter mais informações, confira [Referência do Spring Cloud Config Server](https://spring.io/projects/spring-cloud-config.md). 

## <a name="enable-bluegreen-deployments"></a>Habilitar implantações blue/green

O Azure Spring Cloud dá suporte a implantações blue/green para liberar e atualizar o código para ambientes de produção.  Com esse padrão de gerenciamento de alterações, os desenvolvedores podem implementar recursos e alterações de código com a segurança de um fallback imediato, quando necessário.  Os desenvolvedores podem se concentrar em escrever o código com vários ambientes de produção para atualizar ou reverter as alterações de código sem interromper o aplicativo.  Para saber mais ambientes de preparo e implantações blue/green, acesse este [artigo de instruções](spring-cloud-howto-staging-environment.md).

## <a name="automate-cicd-pipelines"></a>Automatizar pipelines CI/CD

O Azure Spring Cloud fornece integração com o Azure DevOps usando a CLI do Azure.  Usando o Azure DevOps, você pode automatizar a integração de código e a implantação no seu aplicativo do Spring.  Para saber mais, acesse este [artigo](spring-cloud-howto-cicd.md).

## <a name="scale-your-application"></a>Dimensionar o aplicativo

O Azure Spring Cloud permite que você dimensione facilmente os microsserviços em seu painel do Azure Spring Cloud.  O número de vCPUs e a quantidade de memória disponível para seus microsserviços pode ser expandida ou reduzida para atender às suas necessidades.  O dimensionamento entra em vigor em segundos e não requer alterações de código ou reimplantação.  Para saber mais, conclua este [tutorial](spring-cloud-tutorial-scale-manual.md).

## <a name="application-monitoring"></a>Monitoramento de aplicativo

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>Monitorar seu aplicativo usando o rastreamento distribuído e o Azure App Insights

As ferramentas de rastreamento distribuído do Spring Cloud permitem que os desenvolvedores depurem e monitorem as interconexões complexas entre microsserviços em um aplicativo.  Integrando o [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) ao [Application Insights](../azure-monitor/insights/insights-overview.md) do Azure, o Azure oferece uma funcionalidade avançada de rastreamento distribuído diretamente do portal do Azure.  Para saber mais, conclua este [tutorial](spring-cloud-tutorial-distributed-tracing.md).

## <a name="next-steps"></a>Próximas etapas
Para começar, conclua o guia de início rápido do Spring Cloud:
> [!div class="nextstepaction"]
> [Início Rápido: Implantar seu primeiro aplicativo do Azure Spring Cloud](spring-cloud-quickstart.md)

Mais exemplos estão disponíveis no GitHub: [Exemplos do Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
