---
title: Visão geral do modelo de programação do Service Fabric
description: 'A Service Fabric oferece duas estruturas para criação de serviços: a estrutura de atores e a estrutura de serviços. Elas oferecem vantagens e desvantagens distintas com relação à simplicidade e ao controle.'
author: vturecek
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: sfrev
ms.openlocfilehash: 11e32c9d1290227e638a314ed8417b1bed906842
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749540"
---
# <a name="service-fabric-programming-model-overview"></a>Visão geral do modelo de programação do Service Fabric

O Service Fabric oferece várias maneiras de escrever e gerenciar seus serviços. Os serviços podem escolher usar as APIs do Service Fabric para se beneficiarem dos recursos e estruturas de aplicativo da plataforma. Os serviços também podem ser um programa executável compilado, escrito em qualquer linguagem ou código executado em um contêiner hospedado em um cluster do Service Fabric.

## <a name="guest-executables"></a>Executáveis de convidado

Um [executável do convidado](service-fabric-guest-executables-introduction.md) é um executável arbitrário existente (escrito em qualquer linguagem) que pode ser executado como um serviço em seu aplicativo. Os executáveis do convidado não chamam diretamente as APIs do SDK do Service Fabric. No entanto, eles ainda se beneficiam dos recursos que a plataforma oferece, como a descoberta de serviço, os relatórios personalizados de integridade e carregamento por meio da chamada às APIs REST expostas pelo Service Fabric. Eles também têm suporte completo do ciclo de vida do aplicativo.

Comece a usar executáveis convidados implantando seu primeiro [aplicativo executável convidado](service-fabric-deploy-existing-app.md).

## <a name="containers"></a>Contêineres

Por padrão, o Service Fabric implanta e ativa esses serviços como processos. O Service Fabric também pode implantar serviços em [contêineres](service-fabric-containers-overview.md). Service Fabric suporta a implantação de contêineres Linux e contêineres Windows no Windows Server 2016 e posteriores. As imagens de contêiner podem ser extraídas de qualquer repositório de contêiner e implantadas no computador. Você pode implantar aplicativos existentes como executáveis do convidado, serviços confiáveis ou Reliable Actors do Service Fabric com ou sem estado em contêineres e você pode combinar serviços em processos e serviços em contêineres no mesmo aplicativo.

[Saiba mais sobre colocação de seus serviços em contêineres no Windows ou Linux](service-fabric-deploy-container.md)

## <a name="reliable-services"></a>Reliable Services

O Reliable Services é uma estrutura leve para o desenvolvimento de serviços que se integram com a plataforma do Service Fabric e aproveitam o conjunto completo de recursos de plataforma. O Reliable Services fornece um conjunto mínimo de APIs que permite que o runtime do Service Fabric gerencie o ciclo de vida de seus serviços e que seus serviços interajam com o runtime. A estrutura do aplicativo é mínima, dando a você total controle sobre as opções de design e implementação e pode ser usada para hospedar qualquer outra estrutura de aplicativo, como ASP.NET Core.

Os Reliable Services podem ser sem estado, semelhantes à maioria das plataformas de serviço, como servidores Web, nas quais todas as instâncias do serviço são criadas de forma igual e o estado é persistido em uma solução externa, como o BD do Azure ou o Armazenamento de Tabelas do Azure.

Exclusivo s a Service Fabric, os Serviços Confiáveis também podem ser estaduais, onde o estado persiste diretamente no próprio serviço usando Coleções Confiáveis. O estado fica altamente disponível por meio de replicação e é distribuído por meio de particionamento, tudo gerenciado automaticamente pelo Service Fabric.

[Saiba mais sobre os Reliable Services](service-fabric-reliable-services-introduction.md) ou comece [escrevendo o seu primeiro Reliable Service](service-fabric-reliable-services-quick-start.md).

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core é uma estrutura de código aberto e multiplataforma para a construção de aplicativos modernos conectados à Internet baseados em nuvem, como aplicativos web, aplicativos IoT e backends móveis. O Service Fabric integra-se com o ASP.NET Core portanto você pode escrever aplicativos ASP.NET Core com e sem estado que se beneficiam das Reliable Collections e das funcionalidades de orquestração avançadas do Service Fabric.

[Saiba mais sobre o ASP.NET Core no Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md) ou comece [escrevendo seu primeiro aplicativo do Service Fabric do ASP.NET Core](service-fabric-tutorial-create-dotnet-app.md).

## <a name="reliable-actors"></a>Reliable Actors

Construído em cima de Serviços Confiáveis, o framework Reliable Actor é uma estrutura de aplicação que implementa o padrão [ator virtual,](https://research.microsoft.com/en-us/projects/orleans/) baseado no [modelo de ator](https://en.wikipedia.org/wiki/Actor_model)computacional . A estrutura do Ator Confiável usa unidades independentes de computação e estado com execução de um único segmento chamados *atores*. A estrutura Reliable Actor fornece comunicação interna para atores e configurações de expansão e persistência de estado predefinido.

Como o Reliable Actors é uma estrutura de aplicativo baseada no Reliable Services, ele é totalmente integrado com a plataforma Service Fabric e se beneficia do conjunto completo de recursos oferecidos pela plataforma.

[Saiba mais sobre os Reliable Actors](service-fabric-reliable-actors-introduction.md) ou comece escrevendo [o seu primeiro serviço do Reliable Actor](service-fabric-reliable-actors-get-started.md)

[Criar um serviço de front-end usando o ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="next-steps"></a>Próximas etapas

[Visão geral do Service Fabric e contêineres](service-fabric-containers-overview.md)

[Visão geral dos Reliable Services](service-fabric-reliable-services-introduction.md)

[Visão geral dos Reliable Actors](service-fabric-reliable-actors-introduction.md)

[Tecido de serviço e núcleo de ASP.NET](service-fabric-reliable-services-communication-aspnetcore.md)
