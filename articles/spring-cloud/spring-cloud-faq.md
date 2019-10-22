---
title: Perguntas frequentes sobre o Azure Spring Cloud | Microsoft Docs
description: Examine as perguntas frequentes do Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: d4edc634ad41042642b1d73f28ec61cb6dc15ac4
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72433328"
---
# <a name="frequently-asked-questions"></a>Perguntas frequentes

Este artigo aborda as perguntas frequentes sobre o Azure Spring Cloud. 

## <a name="general"></a>Geral

### <a name="why-azure-spring-cloud"></a>Por que o Azure Spring Cloud?

O Azure Spring Cloud oferece uma PaaS (plataforma como serviço) para desenvolvedores do Spring. O Azure Spring Cloud gerencia a infraestrutura do seu aplicativo para que você possa se concentrar no código do aplicativo e na lógica de negócios. Os principais recursos incorporados no Azure Spring Cloud incluem Eureka, Servidor de Configuração, Servidor de Registro de Serviço, Serviço de Build da Pivotal, implantações Blue-Green e muito mais. Este serviço também permite que os desenvolvedores associem seus aplicativos a serviços do Azure, como o CosmosDB, o MySQL e o Cache Redis do Azure.

O Azure Spring Cloud aprimora a experiência de diagnóstico de aplicativos para os desenvolvedores e operadores integrando o Azure Monitor, Application Insights e Log Analytics.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Quais planos de serviço o Azure Spring Cloud oferece?

O Azure Spring Cloud oferece um plano de serviço durante o período de versão prévia.

Recurso | Amount
------- | -------
vCPU | 4
Memória | 8 GBytes
Instâncias de aplicativo por aplicativo Spring | 20
Instâncias totais de aplicativo por instância de serviço do Azure Spring Cloud | 50*
Instâncias de serviço do Azure Spring Cloud por região por assinatura | 2*
Volumes persistentes | 10 x 50 GBytes

*_Abra um [tíquete de suporte](https://azure.microsoft.com/support/faq/) para aumentar o limite._

Para obter mais detalhes, confira as [Perguntas frequentes sobre o Suporte do Azure](https://azure.microsoft.com/support/faq/).

### <a name="how-secure-is-azure-spring-cloud"></a>Quão seguro é o Azure Spring Cloud?

A segurança e a privacidade são uma das prioridades dos clientes do Azure e do Azure Spring Cloud. O Azure verifica se apenas o cliente tem acesso a dados, logs ou configurações de aplicativo criptografando com segurança todos esses dados. Todas as instâncias de serviço no Azure Spring Cloud estão isoladas umas das outras.

O Azure Spring Cloud oferece gerenciamento completo de certificados e SSL.

Os patches de segurança críticos para OpenJDK e tempos de execução do Spring Cloud são aplicados ao Azure Spring Cloud o quanto antes.

### <a name="which-regions-azure-spring-cloud-are-available"></a>Quais regiões do Azure Spring Cloud estão disponíveis?

Leste dos EUA, Oeste dos EUA 2, Oeste da Europa e Sudeste Asiático.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Quais são as limitações conhecidas do Azure Spring Cloud

Veja a seguir as limitações conhecidas do Azure Spring Cloud enquanto o serviço está em versão prévia.

* `spring.application.name` será substituído pelo nome do aplicativo usado para criar cada aplicativo.
* `server.port` não é permitido no arquivo de configuração do repositório Git. Adicioná-lo ao arquivo de configuração provavelmente fará com que seu aplicativo não possa ser acessado de outros aplicativos ou da Internet.
* O portal do Azure e os modelos do Resource Manager não dão suporte ao carregamento de pacotes de aplicativos. Isso só pode ser feito por meio da implantação de aplicativos ou pela CLI do Azure.
* Para limitações de cota, confira [Qual plano de serviço o Azure Spring Cloud oferece](#what-service-plans-does-azure-spring-cloud-offer).

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Como eu posso fornecer comentários e relato problemas?

Se você tiver criado suas instâncias de serviço do Spring no Azure Spring Cloud, poderá criar uma [Solicitação de Suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Se você ainda não se integrou ao Azure Spring Cloud, pode acessar os [Comentários do Azure](https://feedback.azure.com/) para solicitar recursos ou fornecer comentários.

## <a name="development"></a>Desenvolvimento

### <a name="i-am-a-spring-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-aazure-spring-cloud-application"></a>Sou desenvolvedor do Spring, mas novo no Azure. Qual é a maneira mais rápido para eu aprender como desenvolver um aplicativo Azure Spring Cloud?

A maneira mais rápida de começar a usar o Azure Spring Cloud é seguir [este início rápido](spring-cloud-quickstart-launch-app-portal.md).

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>A qual Java Runtime o Azure Spring Cloud dá suporte?

O Azure Spring Cloud dá suporte ao Java 8 e 11.

### <a name="where-can-i-see-my-spring-application-logs-and-metrics"></a>Onde posso ver meus logs e métricas de aplicativos do Spring?

Encontre métricas na guia Visão Geral do Aplicativo e na guia [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics).

O Azure Spring Cloud dá suporte à exportação dos seus logs e métricas de aplicativos do Spring para o Armazenamento do Azure, EventHub e [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). O nome da tabela no Log Analytics é `AppPlatformLogsforSpring`. Para habilitá-lo, examine este artigo sobre nossos [Serviços de diagnóstico](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>O Azure Spring Cloud dá suporte ao rastreamento distribuído?

Sim, acesse [Rastreamento Distribuído](spring-cloud-tutorial-distributed-tracing.md) para obter mais detalhes.

### <a name="what-resource-types-does-service-binding-support"></a>A quais tipos de recurso a Associação de Serviço dá suporte?

No momento, há suporte a três serviços: Azure Cosmos DB, Banco de Dados do Azure para MySQL e Cache Redis do Azure.

### <a name="can-i-viewaddmove-persistent-volumes-from-inside-my-applications"></a>Posso exibir/adicionar/mover volumes persistentes de dentro de meus aplicativos?
Sim.

## <a name="deployment"></a>Implantação

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>O Azure Spring Cloud dá suporte à implantação blue-green?
Sim, acesse o [guia de ambiente de preparo](spring-cloud-howto-staging-environment.md) para obter mais detalhes.

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Posso acessar o Kubernetes para manipular meus contêineres de aplicativos?

Não.  O Azure Spring Cloud abstrai o desenvolvedor da arquitetura subjacente, permitindo que você se concentre no código do aplicativo e na lógica de negócios.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>O Azure Spring Cloud dá suporte à associação de contêineres da origem?

Sim, acesse [Implantar da origem](spring-cloud-launch-from-source.md) para obter mais detalhes.

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>O Azure Spring Cloud dá suporte ao dimensionamento automático nas instâncias do aplicativo?

Não.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-microservices-to-azure-spring-cloud"></a>Quais são as melhores práticas para migrar microsserviços do Spring existentes para o Azure Spring Cloud?

Antes de migrar microsserviços do Spring existentes para o Azure Spring Cloud,
* todas as dependências do aplicativo precisam ser resolvidas.
* Prepare suas entradas de configuração, variáveis de ambiente e parâmetros JVM para poder compará-los com a implantação no Azure Spring Cloud.
* Se desejar usar a Associação de Serviço, percorra os serviços do Azure e verifique se você definiu as permissões de acesso adequadas.
* Recomendamos remover ou desabilitar serviços inseridos que possam entrar em conflito com os serviços gerenciados pelo Azure Spring Cloud, como nosso serviço de Descoberta de Serviços, Servidor de Configuração, etc.
*-* Recomendamos usar bibliotecas Spring da Pivotal oficiais e estáveis. Versões não oficiais, beta ou fork de bibliotecas Spring da Pivotal não têm suporte a SLA.

Após a migração, monitore as métricas de CPU/RAM e o tráfego de rede para verificar se as instâncias de aplicativo foram devidamente dimensionadas.

## <a name="next-steps"></a>Próximas etapas

[Confira o guia de solução de problemas se tiver mais dúvidas](spring-cloud-troubleshoot.md).