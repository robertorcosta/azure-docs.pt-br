---
title: Cenários e design de aplicativos
description: Visão geral das categorias de aplicativos em nuvem no Service Fabric. Discute o design de aplicativo que usa serviços com e sem monitoração de estado.
ms.topic: conceptual
ms.date: 01/08/2020
ms.custom: sfrev
ms.openlocfilehash: 0aeb8ab2923915befdd11f96025687be3b3c4ff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76024748"
---
# <a name="service-fabric-application-scenarios"></a>Cenários de aplicativos do Service Fabric

O Azure Service Fabric oferece uma plataforma confiável e flexível onde você pode escrever e executar muitos tipos de aplicativos e serviços de negócios. Esses aplicativos e microserviços podem ser apátridas ou estatais, e são equilibrados em máquinas virtuais para maximizar a eficiência.

A arquitetura exclusiva da Malha do Serviço permite que você execute análise de dados, computação na memória, transações paralelas e processamento de eventos quase em tempo real em seus aplicativos. Você pode facilmente dimensionar seus aplicativos para dentro ou para fora, dependendo de suas necessidades de recursos em mudança.

Para obter orientação de design sobre aplicativos de construção, leia [arquitetura de microserviços no Azure Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) e práticas [recomendadas para design de aplicativos usando o Service Fabric](service-fabric-best-practices-applications.md).

Considere usar a plataforma Service Fabric para os seguintes tipos de aplicativos:

* **Coleta de dados, processamento e IoT**: A Service Fabric lida com grande escala e possui baixa latência por meio de seus serviços estaduais. Ele pode ajudar a processar dados em milhões de dispositivos onde os dados para o dispositivo e a computação são localizados.

    Os clientes que construíram serviços de IoT usando o Service Fabric incluem [Honeywell,](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure) [PCL Construction,](https://customers.microsoft.com/story/pcl-construction-professional-services-azure) [Crestron,](https://customers.microsoft.com/story/crestron-partner-professional-services-azure) [BMW,](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/) [Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric)e [Mesh Systems.](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions)

* **Aplicativos interativos baseados em jogos e sessões**: O Service Fabric é útil se o seu aplicativo exigir leituras e gravações de baixa latência, como em jogos online ou mensagens instantâneas. O Service Fabric permite que você construa esses aplicativos interativos e imponentes sem ter que criar uma loja ou cache separado. Visite [as soluções de jogos do Azure](https://azure.microsoft.com/solutions/gaming/) para obter orientações sobre [o uso de Fabric de Serviço em serviços de jogos.](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)

    Os clientes que construíram serviços de jogos incluem [Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) e [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/). Os clientes que construíram sessões interativas incluem [honeywell com Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens).

* **Análise de dados e processamento de fluxo de trabalho**: Aplicativos que devem processar de forma confiável eventos ou fluxos de dados beneficiam-se das leituras e gravações otimizadas no Service Fabric. A Service Fabric também suporta pipelines de processamento de aplicativos, onde os resultados devem ser confiáveis e repassados para a próxima etapa de processamento sem qualquer perda. Esses pipelines incluem sistemas transacionais e financeiros, onde a consistência dos dados e as garantias de computação são essenciais.

    Os clientes que construíram serviços de fluxo de trabalho de negócios incluem [Zeiss Group,](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform) [Quorum Business Solutions](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric)e [Société General.](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric)

* **Computação em dados**: O Service Fabric permite que você crie aplicativos imponentes que façam computação intensiva de dados. Service Fabric permite a colocalização de processamento (computação) e dados em aplicativos. 

   Normalmente, quando seu aplicativo requer acesso a dados, a latência da rede associada a um cache de dados externo ou nível de armazenamento limita o tempo de computação. Os serviços stateful Service Fabric eliminam essa latência, permitindo leituras e gravações mais otimizadas.

   Por exemplo, considere um aplicativo que realize seleções de recomendação quase em tempo real para os clientes, com um requisito de tempo de ida e volta de menos de 100 milissegundos. As características de latência e desempenho dos serviços service fabric fornecem uma experiência responsiva ao usuário, em comparação com o modelo padrão de implementação de ter que buscar os dados necessários do armazenamento remoto. O sistema é mais responsivo porque o cálculo da seleção de recomendações é localizado com os dados e regras.

    Os clientes que construíram serviços de computação incluem [solidsoft reply](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) e [infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura).

* **Serviços altamente disponíveis**: O Service Fabric fornece failover rápido criando várias réplicas de serviço secundárias. Se um nó, processo ou serviço individual falhar devido a uma falha de hardware, ou outra falha, uma das réplicas secundárias é promovida a uma réplica primária com perda mínima de serviço.

* **Serviços escalonáveis**: serviços individuais podem ser particionados, permitindo que seu estado seja escalado horizontalmente no cluster. Serviços individuais também podem ser criados e removidos na hora. Você pode dimensionar os serviços de algumas instâncias em alguns nós para milhares de instâncias em muitos nós e, em seguida, dimensioná-los novamente conforme necessário. Você pode usar o Service Fabric para construir esses serviços e gerenciar seus ciclos de vida completos.

## <a name="application-design-case-studies"></a>Estudos de caso de design do aplicativo

Estudos de caso que mostram como o Service Fabric é usado para projetar aplicativos são publicados nos [stories do cliente](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=2&so=story_publish_date%20desc) e [microserviços em sites do Azure.](https://azure.microsoft.com/solutions/microservice-applications/)

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>Projetando aplicações compostas por microsserviços apátridas e estatais

Construir aplicativos com funções de trabalhadores do Azure Cloud Services é um exemplo de um serviço apátrida. Por outro lado, os microsserviços com monitoração de estado mantêm o estado autoritário além da solicitação e de sua resposta. Essa funcionalidade oferece alta disponibilidade e consistência do estado por meio de APIs simples que fornecem garantias transacionais apoiadas pela replicação.

Os serviços estaduais em Malha de Serviço saem com alta disponibilidade para todos os tipos de aplicativos, não apenas bancos de dados e outros armazenamentos de dados. Essa é uma progressão natural. Os aplicativos já passaram do uso de bancos de dados totalmente relacionais para bancos de dados NoSQL de alta disponibilidade. Agora os próprios aplicativos podem ter seu estado e dados "hot" gerenciados dentro deles para ganhos de desempenho adicionais sem sacrificar a disponibilidade, a consistência e a confiabilidade.

Quando você está construindo aplicativos que consistem em microsserviços, você normalmente tem uma combinação de aplicativos web apátridas (como ASP.NET e Node.js) chamando para serviços estatais e estatais de nível intermediário. Os aplicativos e serviços são todos implantados no mesmo cluster Service Fabric através dos comandos de implantação do Service Fabric. Cada um desses serviços é independente no que diz respeito à escala, confiabilidade e uso de recursos. Essa independência melhora a agilidade e a flexibilidade no desenvolvimento e no gerenciamento do ciclo de vida.

Os microsserviços com monitoração de estado simplificam o design dos aplicativos porque eliminam a necessidade de filas e caches adicionais que têm sido tradicionalmente necessários para abordar os requisitos de disponibilidade e de latência de um aplicativo totalmente sem monitoração de estado. Como os serviços estaduais têm alta disponibilidade e baixa latência, há menos detalhes para gerenciar em sua aplicação.

Os diagramas a seguir ilustram as diferenças entre projetar um aplicativo que é apátrida e um que é imponente. Aproveitando os modelos de programação [de Serviços Confiáveis](service-fabric-reliable-services-introduction.md) e [Atores Confiáveis,](service-fabric-reliable-actors-introduction.md) os serviços estaduais reduzem a complexidade dos aplicativos e, ao mesmo tempo, alcançam alta taxa de acesso e baixa latência.

Aqui está um aplicativo de exemplo ![que usa serviços apátridas: Aplicativo que usa serviços apátridas][Image1]

Aqui está um aplicativo de exemplo ![que usa serviços estaduais: Aplicativo que usa serviços estaduais][Image2]

## <a name="next-steps"></a>Próximas etapas

* Comece a construir serviços estatais e estatais com os [modelos de](service-fabric-reliable-services-quick-start.md) programação Service Fabric Reliable Services e [Reliable Actors.](service-fabric-reliable-actors-get-started.md)
* Visite o Azure Architecture Center para obter orientações sobre [a construção de microsserviços no Azure](https://docs.microsoft.com/azure/architecture/microservices/).
* Vá para [o aplicativo Azure Service Fabric e cluster práticas recomendadas](service-fabric-best-practices-overview.md) para orientação de design de aplicativos.

* Consulte também:
  * [Noções básicas sobre microsserviços](service-fabric-overview-microservices.md)
  * [Definir e gerenciar o estado do serviço](service-fabric-concepts-state.md)
  * [Disponibilidade de serviços](service-fabric-availability-services.md)
  * [Serviços de dimensionamento](service-fabric-concepts-scalability.md)
  * [Serviços de partição](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.png
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.png
