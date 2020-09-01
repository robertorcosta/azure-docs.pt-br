---
title: Perguntas frequentes sobre o Azure Spring Cloud | Microsoft Docs
description: Este artigo responde às perguntas frequentes sobre o Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: fcd9f0759ae885199347cc23d93e8f1c00251387
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89260519"
---
# <a name="azure-spring-cloud-faq"></a>FAQ do Azure Spring Cloud

Este artigo responde às perguntas frequentes sobre o Azure Spring Cloud.

## <a name="general"></a>Geral

### <a name="why-azure-spring-cloud"></a>Por que o Azure Spring Cloud?

O Azure Spring Cloud fornece uma PaaS (plataforma como serviço) para desenvolvedores de nuvem Spring. O Azure Spring Cloud gerencia sua infraestrutura de aplicativo para que você possa se concentrar no código do aplicativo e na lógica de negócios. Os principais recursos incorporados ao Azure Spring Cloud incluem Eureka, servidor de configuração, servidor de registro de serviço, serviço de compilação dinâmica, implantação em verde-azulado e muito mais. Esse serviço também permite que os desenvolvedores associem seus aplicativos a outros serviços do Azure, como Azure Cosmos DB, banco de dados do Azure para MySQL e cache do Azure para Redis.

O Azure Spring Cloud aprimora a experiência do Application Diagnostics para desenvolvedores e operadores integrando Azure Monitor, Application Insights e Log Analytics.

### <a name="how-secure-is-azure-spring-cloud"></a>Quão seguro é o Azure Spring Cloud?

A segurança e a privacidade estão entre as principais prioridades do Azure e dos clientes do Azure Spring Cloud. O Azure ajuda a garantir que somente os clientes tenham acesso a dados, logs ou configurações de aplicativos, criptografando com segurança todos esses dados. 

* As instâncias de serviço no Azure Spring Cloud são isoladas umas das outras.
* O Azure Spring Cloud fornece o TLS/SSL e o gerenciamento de certificados completos.
* Os patches de segurança críticos para OpenJDK e tempos de execução do Spring Cloud são aplicados ao Azure Spring Cloud o quanto antes.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Em quais regiões o Azure Spring Cloud está disponível?

Leste dos EUA, Oeste dos EUA 2, Oeste da Europa e Sudeste Asiático.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Quais são as limitações conhecidas do Azure Spring Cloud?

O Azure Spring Cloud tem as seguintes limitações conhecidas:
    
* `spring.application.name` será substituído pelo nome do aplicativo usado para criar cada aplicativo.
* `server.port` o padrão é a porta 1025. Se qualquer outro valor for aplicado, ele será substituído para 1025.
* Os modelos portal do Azure e Azure Resource Manager não dão suporte ao carregamento de pacotes de aplicativos. Você pode carregar pacotes de aplicativos somente implantando o aplicativo por meio do CLI do Azure.

### <a name="what-pricing-tiers-are-available"></a>Quais tipos de preço estão disponíveis? 
Qual deles devo usar e quais são os limites em cada camada?
* O Azure Spring Cloud oferece dois tipos de preço: básico e Standard. A camada básica é destinada a desenvolvimento/teste e à experimentação do Azure Spring Cloud. A camada Standard é otimizada para executar o tráfego de produção de uso geral. Consulte [detalhes de preços do Azure Spring Cloud](https://azure.microsoft.com/pricing/details/spring-cloud/) para obter limites e comparação de nível de recurso.

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Como eu posso fornecer comentários e relato problemas?

Se você encontrar problemas com o Azure Spring Cloud, crie uma [solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Para enviar uma solicitação de recurso ou fornecer comentários, acesse [comentários do Azure](https://feedback.azure.com/forums/34192--general-feedback).

## <a name="development"></a>Desenvolvimento

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Sou um desenvolvedor de nuvem Spring, mas novo no Azure. Qual é a maneira mais rápida de aprender a desenvolver um aplicativo de nuvem Spring do Azure?

Para obter a maneira mais rápida de começar a usar o Azure Spring Cloud, siga as instruções em [início rápido: iniciar um aplicativo do Azure Spring Cloud usando o portal do Azure](spring-cloud-quickstart-launch-app-portal.md).

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>A qual Java Runtime o Azure Spring Cloud dá suporte?

O Azure Spring Cloud dá suporte ao Java 8 e 11. Consulte [Java Runtime e versões do sistema operacional](#java-runtime-and-os-versions)

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Onde posso exibir meus logs e métricas de aplicativos de nuvem Spring?

Encontre métricas na guia Visão Geral do Aplicativo e na guia [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics).

O Azure Spring Cloud dá suporte à exportação de métricas e logs de aplicativos de nuvem Spring para o armazenamento do Azure, o EventHub e o [log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). O nome da tabela em Log Analytics é *AppPlatformLogsforSpring*. Para saber como habilitá-lo, consulte [serviços de diagnóstico](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>O Azure Spring Cloud dá suporte ao rastreamento distribuído?

Sim. Para obter mais informações, consulte [tutorial: usar o rastreamento distribuído com o Azure Spring Cloud](spring-cloud-tutorial-distributed-tracing.md).

### <a name="what-resource-types-does-service-binding-support"></a>A quais tipos de recurso a Associação de Serviço dá suporte?

No momento, há suporte a três serviços:
* Azure Cosmos DB
* Banco de Dados do Azure para MySQL
* Cache Redis do Azure.

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Posso exibir, adicionar ou mover volumes persistentes de dentro de meus aplicativos?

Sim.

### <a name="when-i-deletemove-an-azure-spring-cloud-service-instance-will-its-extension-resources-be-deletedmoved-as-well"></a>Quando eu excluir/mover uma instância do serviço de nuvem do Azure Spring, seus recursos de extensão também serão excluídos/movidos?

Depende da lógica dos provedores de recursos que possuem os recursos de extensão. Os recursos de extensão de uma `Microsoft.AppPlatform` instância não pertencem ao mesmo namespace, portanto, o comportamento varia de acordo com o provedor de recursos. Por exemplo, a operação de exclusão/movimentação não será em cascata para os recursos de **configurações de diagnóstico** . Se uma nova instância do Azure Spring Cloud for provisionada com a mesma ID de recurso que a excluída, ou se a instância anterior do Azure Spring Cloud for movida de volta, os recursos anteriores de **configurações de diagnóstico** continuarão a estendê-la.

Você pode excluir as configurações de diagnóstico da Spring Cloud usando CLI do Azure:

```azurecli
 az monitor diagnostic-settings delete --name $diagnosticSettingName --resource $azureSpringCloudResourceId
```

## <a name="java-runtime-and-os-versions"></a>Tempo de execução Java e versões do sistema operacional

### <a name="which-versions-of-java-runtime-are-supported-in-azure-spring-cloud"></a>Quais versões do tempo de execução Java têm suporte no Azure Spring Cloud?

O Azure Spring Cloud dá suporte a versões do Java LTS com as compilações mais recentes, atualmente em junho de 2020, o Java 8 Build 252 e o Java 11 Build 7 têm suporte. Consulte [instalar o JDK para Azure e Azure Stack](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install)

### <a name="who-built-these-java-runtimes"></a>Quem construiu esses tempos de execução Java?

Sistemas azul. Os builds do JDK do Azul Zulu para Azure – Edição Enterprise são uma distribuição sem custo, multiplataforma e pronta para produção do OpenJDK para Azure e Azure Stack da Microsoft e da Azul Systems. Eles contêm todos os componentes para criar e executar aplicativos Java SE.

### <a name="how-often-will-java-runtimes-get-updated"></a>Com que frequência os tempos de execução do Java são atualizados?

As versões LTS e MTS do JDK têm atualizações de segurança trimestrais, correções de bug, atualizações críticas fora da faixa e patches conforme necessário. Esse suporte inclui backports para o Java 7 e 8 de atualizações de segurança e correções de bug que são relatadas em versões mais recentes do Java, como Java 11.

### <a name="how-long-will-java-8-and-java-11-lts-versions-be-supported"></a>Quanto tempo as versões do Java 8 e do Java 11 LTS terão suporte?

Consulte [suporte a longo prazo Java para Azure e Azure Stack](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support).

* O Java 8 LTS terá suporte até 2030 de dezembro.
* O Java 11 LTS terá suporte até 2027 de setembro.

### <a name="how-can-i-download-a-supported-java-runtime-for-local-development"></a>Como posso baixar um tempo de execução Java com suporte para o desenvolvimento local?

Consulte [instalar o JDK para Azure e Azure Stack](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install).

### <a name="what-is-the-retire-policy-for-older-java-runtimes"></a>Qual é a política de desativação para tempos de execução Java mais antigos?

O aviso público será enviado a cada 12 meses antes que qualquer versão de tempo de execução antiga seja desativada. Você terá 12 meses para migrar para uma versão posterior.

* Os administradores de assinatura receberão uma notificação por email quando desativarmos uma versão do Java.
* As informações de desativação serão publicadas na documentação do.

### <a name="how-can-i-get-support-for-issues-at-the-java-runtime-level"></a>Como posso obter suporte para problemas no nível do tempo de execução Java?

Você pode abrir um tíquete de suporte com o suporte do Azure.  Consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

### <a name="what-is-the-operation-system-to-run-my-apps"></a>Qual é o sistema operacional para executar meus aplicativos?

A versão mais recente do Ubuntu LTS é usada, atualmente o [ubuntu 20, 4 LTS (focal fossa)](https://releases.ubuntu.com/focal/) é o sistema operacional padrão.

### <a name="how-often-will-os-security-patches-be-applied"></a>Com que frequência OS patches de segurança do sistema operacional serão aplicados?

Os patches de segurança aplicáveis ao Azure Spring Cloud serão distribuídos para produção mensalmente.
Os patches de segurança críticos (CVE Score >= 9) aplicáveis ao Azure Spring Cloud serão implantados assim que possível.

## <a name="deployment"></a>Implantação

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>O Azure Spring Cloud dá suporte à implantação azul-verde?
Sim. Para obter mais informações, consulte [configurar um ambiente de preparo](spring-cloud-howto-staging-environment.md).

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Posso acessar o Kubernetes para manipular meus contêineres de aplicativos?

Não.  O Azure Spring Cloud abstrai o desenvolvedor da arquitetura subjacente, permitindo que você se concentre no código do aplicativo e na lógica de negócios.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>O Azure Spring Cloud dá suporte à associação de contêineres da origem?

Sim. Para obter mais informações, consulte [Iniciar seu aplicativo Spring Cloud do código-fonte](spring-cloud-launch-from-source.md).

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>O Azure Spring Cloud dá suporte ao dimensionamento automático nas instâncias do aplicativo?

Não.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Quais são as práticas recomendadas para migrar os microserviços existentes do Spring Cloud para o Azure Spring Cloud?

À medida que estiver migrando os microserviços existentes do Spring Cloud para o Azure Spring Cloud, é uma boa ideia observar as seguintes práticas recomendadas:
* todas as dependências do aplicativo precisam ser resolvidas.
* Prepare suas entradas de configuração, variáveis de ambiente e parâmetros JVM para que você possa compará-las com a implantação no Azure Spring Cloud.
* Se você quiser usar a associação de serviço, passe pelos serviços do Azure e verifique se você definiu as permissões de acesso apropriadas.
* Recomendamos que você remova ou desabilite os serviços inseridos que podem entrar em conflito com os serviços gerenciados pelo Azure Spring Cloud, como nosso serviço de descoberta de serviços, servidor de configuração e assim por diante.
* Recomendamos que você use bibliotecas dinâmicas e estáveis do Spring pivot. Versões não oficiais, beta ou bifurcadas de bibliotecas de Spring dinâmicos não têm suporte de SLA (contrato de nível de serviço).

Após a migração, monitore suas métricas de CPU/RAM e o tráfego de rede para garantir que as instâncias do aplicativo sejam dimensionadas adequadamente.

## <a name="trouble-shooting"></a>Solução de problemas

### <a name="what-are-the-impacts-of-service-registry-rarely-unavailable"></a>Quais são os impactos do registro de serviço raramente indisponíveis?

Em alguns cenários raramente ocorridos, você poderá ver alguns erros como 
```
RetryableEurekaHttpClient: Request execution failure with status code 401; retrying on another server if available
```
de seus logs de aplicativos. Esse problema foi introduzido pelo Spring Framework com uma taxa muito baixa devido à rede instável ou a outros problemas de rede. 

Não deve haver nenhum impacto na experiência do usuário, o cliente Eureka tem uma política de pulsação e de repetição para cuidar disso. Você pode considerá-lo como um erro transitório e ignorá-lo com segurança.

Aprimoraremos essa parte e evitaremos esse erro dos aplicativos dos usuários em breve.


## <a name="next-steps"></a>Próximas etapas

Se você tiver outras dúvidas, consulte o [Guia de solução de problemas do Azure Spring Cloud](spring-cloud-troubleshoot.md).
