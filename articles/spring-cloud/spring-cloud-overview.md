---
title: Introdução ao Azure Spring Cloud
description: Conheça os recursos e benefícios do Azure Spring Cloud para implantar e gerenciar aplicativos Spring Java no Azure.
author: jpconnock
ms.service: spring-cloud
ms.topic: overview
ms.date: 10/04/2019
ms.author: jeconnoc
ms.openlocfilehash: c58e6b2a0c0de8295df65b44fbdeaeb2d4461948
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166527"
---
# <a name="what-is-azure-spring-cloud"></a>O que é o Azure Spring Cloud?

O Azure Spring Cloud facilita a implantação de aplicativos de microsserviço baseados no Spring Boot no Azure com zero alterações de código.  O Azure Spring Cloud permite que os desenvolvedores se concentrem em seu código gerenciando o ciclo de vida de aplicativos Spring Cloud.  O Spring Cloud fornece gerenciamento de ciclo de vida usando monitoramento e diagnóstico abrangentes, gerenciamento de configuração, descoberta de serviços, integração de CI/CD, implantações em “blue-green” e muito mais.

Como parte do ecossistema do Azure, o Azure Spring Cloud permite fácil associação a outros serviços do Azure, incluindo armazenamento, bancos de dados, monitoramento e mais.

No momento, o Azure Spring Cloud está em versão prévia.  Para ter acesso a este serviço enquanto ele estiver em versão prévia, [preencha este formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u
).

Para começar a usar, conclua o início rápido do Spring Cloud usando a [CLI do Azure](spring-cloud-quickstart-launch-app-cli.md), o [portal do Azure](spring-cloud-quickstart-launch-app-portal.md) ou [Maven](spring-cloud-quickstart-launch-app-maven.md).

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
