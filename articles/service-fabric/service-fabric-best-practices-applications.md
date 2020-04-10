---
title: Práticas recomendadas de design de aplicativos do Azure Service Fabric
description: As melhores práticas e considerações de design para o desenvolvimento de aplicativos e serviços usando o Azure Service Fabric.
author: markfussell
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: mfussell
ms.openlocfilehash: 56df6e28940eb15597a3d6bccca3f85e5f690f89
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991647"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Práticas recomendadas de design de aplicativos do Azure Service Fabric

Este artigo fornece orientação de práticas recomendadas para a construção de aplicativos e serviços no Azure Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Familiarize-se com o Service Fabric
* Leia o [artigo So you quer aprender sobre Service Fabric?](service-fabric-content-roadmap.md)
* Leia sobre [os cenários de aplicativos do Service Fabric](service-fabric-application-scenarios.md).
* Entenda as escolhas do modelo de programação lendo [a visão geral do modelo de programação service fabric](service-fabric-choose-framework.md).



## <a name="application-design-guidance"></a>Orientação de design de aplicativos
Familiarize-se com a [arquitetura geral](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) dos aplicativos de Malha de Serviço e suas [considerações de design.](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations)

### <a name="choose-an-api-gateway"></a>Escolha um gateway de API
Use um serviço de gateway de API que se comunica com serviços back-end que podem ser dimensionados. Os serviços de gateway de API mais comuns utilizados são:

- [Gerenciamento de API do Azure,](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview)que é [integrado com o Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management).
- [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) ou [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/), usando o [ServiceFabricProcessor](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.ServiceFabricProcessor) para ler as partições do Event Hub.
- [Træfik proxy reverso](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/), usando o [provedor de malha de serviço azure](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/).
- [Gateway de Aplicativo do Azure](https://docs.microsoft.com/azure/application-gateway/).

   > [!NOTE] 
   > O Gateway de aplicativo do Azure não está diretamente integrado ao Service Fabric. O Azure API Management é tipicamente a escolha preferida.
- Seu próprio gateway de aplicativo web [ASP.NET](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) personalizado.

### <a name="stateless-services"></a>Serviços apátridas
Recomendamos que você sempre comece construindo serviços apátridas usando [Serviços Confiáveis](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) e armazenando estado em um banco de dados Azure, Azure Cosmos DB ou Azure Storage. O estado externo é a abordagem mais familiar para a maioria dos desenvolvedores. Essa abordagem também permite que você aproveite os recursos de consulta na loja.  

### <a name="when-to-use-stateful-services"></a>Quando usar serviços estaduais
Considere serviços estatais quando você tem um cenário de baixa latência e precisa manter os dados próximos ao cálculo. Alguns cenários de exemplo incluem dispositivos gêmeos digitais ioT, estado de jogo, estado de sessão, cache de dados de um banco de dados e fluxos de trabalho de longa duração para rastrear chamadas para outros serviços.

Decida sobre o período de tempo de retenção de dados:

- **Dados armazenados em cache**. Use cache quando a latência em lojas externas é um problema. Use um serviço de estado como cache de dados próprio ou considere usar o [cache distribuído de malha de malha de malha do SoCreate de código aberto.](https://github.com/SoCreate/service-fabric-distributed-cache) Neste cenário, você não precisa se preocupar se você perder todos os dados no cache.
- **Dados vinculados ao tempo**. Neste cenário, você precisa manter os dados próximos ao cálculo por um período de tempo de latência, mas você pode se dar ao luxo de perder os dados em um *desastre*. Por exemplo, em muitas soluções de IoT, os dados precisam estar próximos do cálculo, como quando a temperatura média dos últimos dias está sendo calculada, mas se esses dados forem perdidos, os pontos de dados específicos registrados não são tão importantes. Além disso, neste cenário, você normalmente não se preocupa em fazer backup dos pontos de dados individuais. Você só faz backup de valores médios computados que são periodicamente escritos para armazenamento externo.  
- **Dados de longo prazo**. Coleções confiáveis podem armazenar seus dados permanentemente. Mas, neste caso, você precisa se preparar para a [recuperação de desastres,](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery)incluindo [a configuração de políticas de backup periódicas](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) para seus clusters. Com efeito, você configura o que acontece se o cluster for destruído em um desastre, onde você precisará criar um novo cluster e como implantar novas instâncias de aplicativo e recuperar do backup mais recente.

Economize custos e melhore a disponibilidade:
- Você pode reduzir custos usando serviços estatais porque você não incorre em acesso a dados e custos de transações da loja remota, e porque você não precisa usar outro serviço, como o Cache Do Azure para Redis.
- Usar serviços estatais principalmente para armazenamento e não para computação é caro, e não recomendamos isso. Pense em serviços estatais como computação com armazenamento local barato.
- Ao remover dependências de outros serviços, você pode melhorar a disponibilidade do seu serviço. Gerenciar o estado com HA no cluster isola você de outros tempos de inatividade de serviço ou problemas de latência.

## <a name="how-to-work-with-reliable-services"></a>Como trabalhar com serviços confiáveis
Service Fabric Reliable Services permite criar facilmente serviços apátridas e estatais. Para obter mais informações, consulte a [introdução de Serviços Confiáveis](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction).
- Sempre honre o [token de cancelamento](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) no `RunAsync()` método `ChangeRole()` para serviços apátridas e estatais e o método para serviços estatais. Se você não fizer isso, a Service Fabric não sabe se seu serviço pode ser fechado. Por exemplo, se você não honrar o token de cancelamento, muito mais tempo de atualização do aplicativo pode ocorrer.
-    Abrir e fechar [os ouvintes](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) de comunicação em tempo háprésimo, e honrar os tokens de cancelamento.
-    Nunca misture código de sincronização com código assincronte. Por exemplo, não `.GetAwaiter().GetResult()` use em suas chamadas assíncronas. Use assincronizar *todo o caminho* através da pilha de chamadas.

## <a name="how-to-work-with-reliable-actors"></a>Como trabalhar com atores confiáveis
Service Fabric Reliable Actors permite criar facilmente atores virtuais e imponentes. Para obter mais informações, consulte a [introdução de Atores Confiáveis](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction).

- Considere seriamente usar mensagens pub/sub entre seus atores para dimensionar seu aplicativo. As ferramentas que fornecem este serviço incluem o [SoCreate Service Fabric Pub/Sub](https://service-fabric-pub-sub.socreate.it/) de código aberto e [o Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).
- Faça o estado do ator o [mais granular possível.](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)
- Gerencie o [ciclo de vida do ator.](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices) Exclua atores se você não vai usá-los novamente. Excluir atores desnecessários é especialmente importante quando você está usando o [provedor de estado volátil,](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication)porque todo o estado é armazenado na memória.
- Por causa de sua [concorrência baseada em turnos,](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency)os atores são mais usados como objetos independentes. Não crie gráficos de chamadas de métodos síncronos e multiatores (cada um dos quais provavelmente se torna uma chamada de rede separada) ou crie solicitações circulares de atores. Isso afetará significativamente o desempenho e a escala.
- Não misture código de sincronização com código assincronte. Use assincronizar consistentemente para evitar problemas de desempenho.
- Não faça chamadas longas em atores. Chamadas de longa duração bloquearão outras chamadas para o mesmo ator, devido à concorrência baseada em turnos.
- Se você está se comunicando com outros serviços usando o `ServiceProxyFactory`Service Fabric [remoting](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) e você está criando um , crie a fábrica no nível [ator-serviço](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) e *não* no nível de ator.


## <a name="application-diagnostics"></a>Diagnóstico de aplicativo
Seja minucioso sobre a adição [de login](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) de aplicativos em chamadas de serviço. Ele vai ajudá-lo a diagnosticar cenários em que os serviços se chamam. Por exemplo, quando A chama B chamadas C chamadas D, a chamada pode falhar em qualquer lugar. Se você não tem registro suficiente, falhas são difíceis de diagnosticar. Se os serviços estiverem registrando muito por causa dos volumes de chamadas, certifique-se de pelo menos registrar erros e avisos.

## <a name="iot-and-messaging-applications"></a>Aplicativos de IoT e mensagens
Quando estiver lendo mensagens do [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) ou [do Azure Event Hubs,](https://docs.microsoft.com/azure/event-hubs/)use [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/ServiceFabricProcessor). ServiceFabricProcessor integra-se com serviços confiáveis de malha de serviço para manter o estado de `IEventProcessor::ProcessEventsAsync()` leitura das partições do hub de eventos e empurra novas mensagens para seus serviços através do método.


## <a name="design-guidance-on-azure"></a>Orientação de design no Azure
* Visite o [centro de arquitetura Azure](https://docs.microsoft.com/azure/architecture/microservices/) para obter orientações sobre [a construção de microsserviços no Azure](https://docs.microsoft.com/azure/architecture/microservices/).

* Visite [Get Started com o Azure for Gaming para](https://docs.microsoft.com/gaming/azure/) obter orientações de design sobre o uso de Malha de Serviço em [serviços de jogos.](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)
