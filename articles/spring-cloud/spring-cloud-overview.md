---
title: Introdução ao Azure Spring Cloud
description: Conheça os recursos e benefícios do Azure Spring Cloud para implantar e gerenciar aplicativos Spring Java no Azure.
author: jpconnock
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: jeconnoc
ms.openlocfilehash: d05ce8f94862d737b3514bd1116e431bcba5dedd
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75891692"
---
# <a name="what-is-azure-spring-cloud"></a>O que é o Azure Spring Cloud?

O Azure Spring Cloud facilita a implantação de aplicativos de microsserviço baseados no Spring Boot no Azure com zero alterações de código.  O Azure Spring Cloud gerencia o ciclo de vida dos aplicativos Spring Cloud para que os desenvolvedores possam se concentrar no código.  O Spring Cloud fornece gerenciamento de ciclo de vida usando monitoramento e diagnóstico abrangentes, gerenciamento de configuração, descoberta de serviços, integração de CI/CD, implantações em “blue-green” e muito mais.

Como parte do ecossistema do Azure, o Azure Spring Cloud permite fácil associação a outros serviços do Azure, incluindo armazenamento, bancos de dados, monitoramento e mais.

Atualmente, o Azure Spring Cloud é oferecido como visualização pública. As ofertas de visualização pública permitem que os clientes experimentem os novos recursos antes do lançamento oficial.  Os serviços e recursos de visualização pública não são destinados ao uso em produção.  Para saber mais sobre o suporte durante as visualizações, confira as [Perguntas frequentes](https://azure.microsoft.com/support/faq/) ou envie uma [Solicitação de suporte](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) para saber mais.

Para começar a usar, conclua o início rápido do Spring Cloud usando a [CLI do Azure](spring-cloud-quickstart-launch-app-cli.md), o [portal do Azure](spring-cloud-quickstart-launch-app-portal.md) ou [Maven](spring-cloud-quickstart-launch-app-maven.md).

Mais exemplos estão disponíveis no GitHub: [Exemplos do Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).

## <a name="application-configuration"></a>Configuração de aplicativo

### <a name="spring-cloud-config-server"></a>Servidor de Configuração do Spring Cloud

O Servidor de Configuração do Azure Spring Cloud oferece configuração externalizada em um sistema distribuído com suporte a servidor e cliente.  O Servidor de Configuração oferece uma localização central para gerenciar propriedades de aplicativo em todos os ambientes.  Para saber mais, acesse [referência do Servidor de Configuração do Spring Cloud](https://spring.io/projects/spring-cloud-config.md) e conclua este tutorial.

### <a name="enable-bluegreen-deployments"></a>Habilitar implantações blue/green

O Azure Spring Cloud dá suporte a implantações blue/green para liberar e atualizar o código para ambientes de produção.  Aproveitar esse padrão de gerenciamento de alterações permite que os desenvolvedores implementem recursos e alterações de código com a segurança de um fallback imediato quando necessário.  O Azure permite que os desenvolvedores se concentrem em escrever código gerenciando vários ambientes de produção e facilitando a atualização ou a reversão das alterações de código sem interromper o aplicativo.  Para saber mais ambientes de preparo e implantações blue/green, acesse este [artigo de instruções](spring-cloud-howto-staging-environment.md).

### <a name="automate-cicd-pipelines"></a>Automatizar pipelines CI/CD

O Azure Spring Cloud fornece integração com o Azure DevOps usando a CLI do Azure.  Usando o Azure DevOps, você pode automatizar a integração de código e a implantação no seu aplicativo do Spring.  Para saber mais, acesse este [artigo](spring-cloud-howto-cicd.md).

### <a name="scale-your-application"></a>Dimensionar o aplicativo

O Azure Spring Cloud permite que você dimensione facilmente os microsserviços em seu painel do Azure Spring Cloud.  O número de vCPUs e a quantidade de memória disponível para seus microsserviços pode ser expandida ou reduzida para atender às suas necessidades.  O dimensionamento entra em vigor em segundos e não requer alterações de código ou reimplantação.  Para saber mais, conclua este [tutorial](spring-cloud-tutorial-scale-manual.md).

## <a name="application-monitoring"></a>Monitoramento de Aplicativos

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>Monitorar seu aplicativo usando o rastreamento distribuído e o Azure App Insights

As ferramentas de rastreamento distribuído do Spring Cloud permitem que os desenvolvedores depurem e monitorem as interconexões complexas entre microsserviços em seu aplicativo.  Integrando o [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) ao [Application Insights](../azure-monitor/insights/insights-overview.md) do Azure, o Azure oferece uma funcionalidade avançada de rastreamento distribuído diretamente do portal do Azure.  Para saber mais, conclua este [tutorial](spring-cloud-tutorial-distributed-tracing.md).

## <a name="next-steps"></a>Próximas etapas

- [Iniciar o aplicativo Spring Cloud da CLI](spring-cloud-quickstart-launch-app-cli.md)
