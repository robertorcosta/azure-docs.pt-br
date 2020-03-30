---
title: Perguntas frequentes sobre Azure Spring Cloud | Microsoft Docs
description: Este artigo responde perguntas frequentes sobre a Nuvem de Primavera do Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: 62623bcadb35f21117ddc2601195e34598c2dff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298766"
---
# <a name="azure-spring-cloud-faq"></a>Faq da Nuvem da Primavera do Azure

Este artigo responde perguntas frequentes sobre a Nuvem de Primavera do Azure. 

## <a name="general"></a>Geral

### <a name="why-azure-spring-cloud"></a>Por que o Azure Spring Cloud?

O Azure Spring Cloud fornece uma plataforma como serviço (PaaS) para desenvolvedores da Spring Cloud. O Azure Spring Cloud gerencia sua infra-estrutura de aplicativos para que você possa se concentrar no código de aplicativos e na lógica de negócios. Os principais recursos incorporados no Azure Spring Cloud incluem Eureka, Config Server, Service Registry Server, Pivotal Build Service, implantações em verde azul e muito mais. Esse serviço também permite que os desenvolvedores vinculem seus aplicativos a outros serviços do Azure, como o Azure Cosmos DB, o Azure Database for MySQL e o Azure Cache for Redis.

O Azure Spring Cloud melhora a experiência de diagnóstico de aplicativos para desenvolvedores e operadores, integrando o Azure Monitor, Application Insights e Log Analytics.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Quais planos de serviço o Azure Spring Cloud oferece?

O Azure Spring Cloud oferece um plano de serviço durante o período de versão prévia.  Uma implantação da Spring Cloud contém 16 núcleos de vCPU e 32 gigabytes (GB) de memória.  O limite superior para cada instância de microserviço dentro de uma implantação é de 4 núcleos de vCPU com 8 GB de memória.

Recurso | Amount
------- | -------
Instâncias de aplicativo por aplicativo Spring | 20
Instâncias totais de aplicativo por instância de serviço do Azure Spring Cloud | 500
Instâncias de serviço do Azure Spring Cloud por região por assinatura | 10
Volumes persistentes | 10 x 50 GBytes

\*_Para aumentar o limite, abra um [bilhete de suporte.](https://azure.microsoft.com/support/faq/)_

Para obter mais informações, consulte [Azure Support FAQ](https://azure.microsoft.com/support/faq/).

### <a name="how-secure-is-azure-spring-cloud"></a>Quão seguro é o Azure Spring Cloud?

Segurança e privacidade estão entre as principais prioridades para os clientes do Azure e do Azure Spring Cloud. O Azure ajuda a garantir que apenas os clientes tenham acesso a dados, logs ou configurações de aplicativos, criptografando todos esses dados com segurança. Todas as instâncias de serviço no Azure Spring Cloud estão isoladas umas das outras.

O Azure Spring Cloud fornece TLS/SSL completo e gerenciamento de certificados.

Os patches de segurança críticos para OpenJDK e tempos de execução do Spring Cloud são aplicados ao Azure Spring Cloud o quanto antes.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Em quais regiões o Azure Spring Cloud está disponível?

Leste dos EUA, Oeste dos EUA 2, Oeste da Europa e Sudeste Asiático.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Quais são as limitações conhecidas do Azure Spring Cloud?

Durante o lançamento da pré-visualização, o Azure Spring Cloud tem as seguintes limitações conhecidas:

* `spring.application.name`será substituído pelo nome do aplicativo que é usado para criar cada aplicativo.
* `server.port`não é permitido no arquivo de configuração do repo Git. Adicioná-lo ao arquivo de configuração provavelmente tornará seu aplicativo inalcançável de outros aplicativos ou da internet.
* Os modelos do portal Azure e do Azure Resource Manager não suportam o upload de pacotes de aplicativos. Você só pode carregar pacotes de aplicativos implantando o aplicativo através do Azure CLI.
* Para saber mais sobre as limitações de cotas, veja [quais planos de serviço o Azure Spring Cloud oferece?](#what-service-plans-does-azure-spring-cloud-offer).

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Como eu posso fornecer comentários e relato problemas?

Se você encontrar algum problema com o Azure Spring Cloud, crie [uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Para enviar uma solicitação de recurso ou fornecer feedback, vá para [o Azure Feedback](https://feedback.azure.com/forums/34192--general-feedback).

## <a name="development"></a>Desenvolvimento

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Sou um desenvolvedor da Spring Cloud, mas novo no Azure. Qual é a maneira mais rápida de aprender como desenvolver um aplicativo Azure Spring Cloud?

Para obter a maneira mais rápida de começar com o Azure Spring Cloud, siga as instruções em [Quickstart: Inicie um aplicativo Azure Spring Cloud usando o portal Azure](spring-cloud-quickstart-launch-app-portal.md).

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>A qual Java Runtime o Azure Spring Cloud dá suporte?

O Azure Spring Cloud dá suporte ao Java 8 e 11.

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Onde posso ver meus registros e métricas de aplicativos da Spring Cloud?

Encontre métricas na guia Visão Geral do Aplicativo e na guia [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics).

O Azure Spring Cloud suporta a exportação de registros e métricas de aplicativos da Nuvem de Primavera para o Azure Storage, EventHub e [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). O nome da tabela no Log Analytics é *AppPlatformLogsforSpring*. Para saber como habilitá-lo, consulte [serviços de diagnóstico](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>O Azure Spring Cloud dá suporte ao rastreamento distribuído?

Sim. Para obter mais informações, consulte [Tutorial: Use Distributed Tracing with Azure Spring Cloud](spring-cloud-tutorial-distributed-tracing.md).

### <a name="what-resource-types-does-service-binding-support"></a>A quais tipos de recurso a Associação de Serviço dá suporte?

Atualmente, três serviços são suportados: Azure Cosmos DB, Azure Database for MySQL e Azure Cache for Redis.

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Posso visualizar, adicionar ou mover volumes persistentes de dentro dos meus aplicativos?

Sim.

## <a name="deployment"></a>Implantação

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>O Azure Spring Cloud suporta a implantação azul-verde?
Sim. Para obter mais informações, consulte [Configurar um ambiente de encenação](spring-cloud-howto-staging-environment.md).

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Posso acessar o Kubernetes para manipular meus contêineres de aplicativos?

Não.  O Azure Spring Cloud abstrai o desenvolvedor da arquitetura subjacente, permitindo que você se concentre no código do aplicativo e na lógica de negócios.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>O Azure Spring Cloud dá suporte à associação de contêineres da origem?

Sim. Para obter mais informações, consulte [Inicie seu aplicativo Spring Cloud a partir do código-fonte](spring-cloud-launch-from-source.md).

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>O Azure Spring Cloud dá suporte ao dimensionamento automático nas instâncias do aplicativo?

Não.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Quais são as melhores práticas para migrar os microsserviços existentes da Spring Cloud para o Azure Spring Cloud?

Como você está migrando os microsserviços existentes da Spring Cloud para o Azure Spring Cloud, é uma boa idéia observar as seguintes práticas recomendadas:
* todas as dependências do aplicativo precisam ser resolvidas.
* Prepare suas entradas de configuração, variáveis de ambiente e parâmetros JVM para que você possa compará-los com a implantação no Azure Spring Cloud.
* Se você quiser usar o Service Binding, passe por seus serviços do Azure e certifique-se de que você definiu as permissões de acesso apropriadas.
* Recomendamos que você remova ou desative quaisquer serviços incorporados que possam entrar em conflito com serviços gerenciados pelo Azure Spring Cloud, como nosso serviço de detecção de serviços, Config Server e assim por diante.
* Recomendamos que você use bibliotecas oficiais e estáveis da Primavera Central. Versões não oficiais, beta ou bifurcadas de bibliotecas de Mola Crucial não têm suporte a sla (service-level agreement).

Após a migração, monitore as métricas da CPU/RAM e o tráfego de rede para garantir que as instâncias do aplicativo sejam dimensionadas adequadamente.

## <a name="next-steps"></a>Próximas etapas

Se você tiver mais perguntas, consulte o [guia de solução de problemas da Nuvem de Primavera do Azure](spring-cloud-troubleshoot.md).
