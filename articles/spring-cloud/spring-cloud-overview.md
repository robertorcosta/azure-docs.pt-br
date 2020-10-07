---
title: Introdução ao Azure Spring Cloud
description: Conheça os recursos e benefícios do Azure Spring Cloud para implantar e gerenciar aplicativos Spring Java no Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b03bedd9abbe59ed3bc1b0ec1439aa27af57e6da
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91758890"
---
# <a name="what-is-azure-spring-cloud"></a>O que é o Azure Spring Cloud?

O Azure Spring Cloud facilita a implantação de aplicativos de microsserviço baseados no Spring Boot no Azure com zero alterações de código.  O Azure Spring Cloud gerencia a infraestrutura dos aplicativos do Spring Cloud para que os desenvolvedores possam se concentrar no código.  O Spring Cloud fornece gerenciamento de ciclo de vida usando monitoramento e diagnóstico abrangentes, gerenciamento de configuração, descoberta de serviços, integração de CI/CD, implantações em “blue-green” e muito mais.

O Azure Spring Cloud dá suporte a aplicativos Java [Spring Boot](https://spring.io/projects/spring-boot) e ASP.NET Core [Steeltoe](https://steeltoe.io/). O suporte a Steeltoe é oferecido atualmente em versão prévia pública. As ofertas em versão prévia pública permitem que você experimente os novos recursos antes do lançamento oficial.  Os serviços e recursos de visualização pública não são destinados ao uso em produção.  Para obter mais informações, confira as [perguntas frequentes](https://azure.microsoft.com/support/faq/) ou registre uma [Solicitação de suporte](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

Como parte do ecossistema do Azure, o Azure Spring Cloud permite fácil associação a outros serviços do Azure, incluindo armazenamento, bancos de dados, monitoramento e mais.

Esta introdução descreve os seguintes recursos do Azure Spring Cloud:

* Servidor de configuração
* Implantações azul/verde
* Dimensionamento de aplicativos
* Integração com o Azure DevOps
* Monitoramento de aplicativo

## <a name="spring-cloud-config-server"></a>Servidor de Configuração do Spring Cloud

O Servidor de Configuração do Azure Spring Cloud oferece configuração externalizada em um sistema distribuído com suporte a servidor e cliente.  O Azure Spring Cloud Config Server é uma localização central para gerenciar propriedades de aplicativo em todos os ambientes. Para obter mais informações, confira [Referência do Spring Cloud Config Server](https://spring.io/projects/spring-cloud-config). 

## <a name="bluegreen-deployments"></a>Implantações azul/verde

O Azure Spring Cloud dá suporte a implantações blue/green para liberar e atualizar o código para ambientes de produção.  Com esse padrão de gerenciamento de alterações, os desenvolvedores podem implementar recursos e alterações de código com a segurança de um fallback imediato, quando necessário.  Os desenvolvedores podem se concentrar em escrever o código com vários ambientes de produção para atualizar ou reverter as alterações de código sem interromper o aplicativo.  Para saber mais ambientes de preparo e implantações blue/green, acesse este [artigo de instruções](spring-cloud-howto-staging-environment.md).

## <a name="cicd-pipeline-automation"></a>Automação de pipeline de CI/CD

O Azure Spring Cloud fornece integração com o Azure DevOps usando a CLI do Azure.  Usando o Azure DevOps, você pode automatizar a integração de código e a implantação no seu aplicativo do Spring.  Para saber mais, acesse este [artigo](spring-cloud-howto-cicd.md).

## <a name="application-scaling"></a>Dimensionamento de aplicativos

O Azure Spring Cloud permite que você dimensione facilmente os microsserviços em seu painel do Azure Spring Cloud.  O número de vCPUs e a quantidade de memória disponível para seus microsserviços pode ser expandida ou reduzida para atender às suas necessidades.  O dimensionamento entra em vigor em segundos e não requer alterações de código ou reimplantação.  Para saber mais, conclua este [tutorial](spring-cloud-tutorial-scale-manual.md).

## <a name="application-monitoring"></a>Monitoramento de aplicativo

As ferramentas de rastreamento distribuído do Spring Cloud permitem que os desenvolvedores depurem e monitorem as interconexões complexas entre microsserviços em um aplicativo.  Integrando o [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) ao [Application Insights](../azure-monitor/insights/insights-overview.md) do Azure, o Azure oferece uma funcionalidade avançada de rastreamento distribuído diretamente do portal do Azure.  Para saber mais, conclua este [tutorial](spring-cloud-tutorial-distributed-tracing.md).

## <a name="next-steps"></a>Próximas etapas

Para começar, conclua o [guia de início rápido do Spring Cloud](spring-cloud-quickstart.md)

As amostras estão disponíveis no GitHub: [Exemplos do Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/).
