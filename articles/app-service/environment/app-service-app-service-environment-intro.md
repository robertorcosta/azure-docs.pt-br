---
title: Introdução ao ASE v1
description: Saiba mais sobre os recursos do Ambiente do Serviço de Aplicativo v1. Este documento é fornecido somente para clientes que usam o ASE v1 herdado.
author: stefsch
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: f655b7793bfbb5bbeddfc2f1f8e7bc973dabeb4c
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687373"
---
# <a name="introduction-to-app-service-environment-v1"></a>Introdução ao Ambiente do Serviço de Aplicativo v1

> [!NOTE]
> Este artigo é sobre o Ambiente do Serviço de Aplicativo v1.  Há uma versão mais recente do Ambiente do Serviço de Aplicativo que é mais fácil de usar e é executada em infraestrutura mais avançada. Para saber mais sobre o novo início de versão com o [Introdução ao Ambiente do Serviço de Aplicativo](intro.md).

## <a name="overview"></a>Visão Geral

Um Ambiente do Serviço de Aplicativo é uma opção de plano de serviço [Premium][PremiumTier] do [serviço de Azure app](../overview.md) que fornece um ambiente totalmente isolado e dedicado para execução segura de aplicativos de serviço do Azure app em alta escala, incluindo aplicativos Web, aplicativos móveis e aplicativos de API.  

Os Ambientes de Serviço de Aplicativo são ideais para cargas de trabalho de aplicativos que exigem:

* Escala muito alta
* Isolamento e acesso seguro à rede

Os clientes podem criar vários Ambientes de Serviço de Aplicativo dentro de uma única região do Azure, bem como entre várias regiões do Azure.  Isso faz dos Ambientes de Serviço de Aplicativo ideais para dimensionar horizontalmente camadas de aplicativo sem estado para dar suporte a cargas de trabalho RPS altas.

Ambientes de Serviço de Aplicativo são isolados para executar somente aplicativos de um único cliente, e sempre são implantados em uma rede virtual.  Os clientes têm um controle refinado sobre o tráfego de entrada e saída da rede de aplicativos, e os aplicativos podem estabelecer conexões seguras de alta velocidade por meio de redes virtuais com recursos corporativos locais.

Para obter uma visão geral de como os ambientes do serviço de aplicativo permitem o acesso de alta escala e segurança à rede, consulte o aprofundamento [AzureCon][AzureConDeepDive] nos ambientes do serviço de aplicativo!

Para uma análise aprofundada sobre o dimensionamento horizontal usando vários Ambientes de Serviço de Aplicativo, veja o artigo sobre como configurar uma [área de aplicativo distribuído geograficamente][GeodistributedAppFootprint].

Para ver como a arquitetura de segurança exibida no AzureCon Deep Dive foi configurada, consulte o artigo sobre a implementação de uma [arquitetura de segurança em camadas](app-service-app-service-environment-layered-security.md) com os Ambientes do Serviço de Aplicativo.

Os aplicativos executados nos Ambientes do Serviço de Aplicativo podem ter seu acesso restrito por dispositivos upstream como WAF (firewalls do aplicativo Web).  O artigo sobre como [configurar um WAF para Ambientes do Serviço de Aplicativo](app-service-app-service-environment-web-application-firewall.md) aborda esse cenário.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Recursos de computação dedicados

Todos os recursos de computação em um Ambiente de Serviço de Aplicativo são dedicados exclusivamente a uma única assinatura, e um Ambiente de Serviço de Aplicativo pode ser configurado com até 50 (cinquenta) recursos de computação para uso exclusivo por um único aplicativo.

Um Ambiente de Serviço de Aplicativo é composto de um pool de recursos de computação de front-end, bem como um a três pools de recursos de computação de trabalho.

O pool de front-end contém recursos de computação responsáveis pela terminação SSL, bem como balanceamento automático de carga de solicitações do aplicativo em um ambiente de serviço de aplicativo.

Cada pool de trabalho contém recursos de computação alocados para [planos do serviço de aplicativo][AppServicePlan]que, por sua vez, contêm um ou mais aplicativos de serviço de Azure app.  Como pode haver até três pools de trabalho diferentes em um ambiente de serviço de aplicativo, você tem a flexibilidade de escolher os recursos de computação diferentes para cada pool de trabalho.  

Por exemplo, isso permite a criação de um pool de trabalho com menos recursos de computação poderosos para Plano do Serviço de Aplicativo destinados a aplicativos de desenvolvimento ou teste.  Um segundo (ou até mesmo terceiro) pool de trabalho de pode usar recursos de computação mais poderosos para planos de serviço de aplicativo executando aplicativos de produção.

Para obter mais detalhes sobre a quantidade de recursos de computação disponíveis para os pools de front-end e de trabalho, consulte [como configurar um ambiente do serviço de aplicativo][HowToConfigureanAppServiceEnvironment].  

Para obter detalhes sobre os tamanhos de recursos de computação disponíveis com suporte em um Ambiente do Serviço de Aplicativo, consulte a página de [preços do serviço de aplicativo][AppServicePricing] e examine as opções disponíveis para ambientes do serviço de aplicativo no tipo de preço premium.

## <a name="virtual-network-support"></a>Suporte a Rede Virtual

Um Ambiente do Serviço de Aplicativo pode ser criado em uma rede virtual Azure Resource Manager **ou** em uma rede **virtual do modelo** de implantação clássico ([mais informações sobre redes virtuais][MoreInfoOnVirtualNetworks]).  Como um Ambiente de Serviço de Aplicativo sempre existe em uma rede virtual, mais precisamente em uma sub-rede de uma rede virtual, você pode aproveitar os recursos de segurança de redes virtuais para controlar tanto a comunicação de rede de entrada quanto a de saída.  

Um Ambiente de Serviço de Aplicativo pode ser voltado para a Internet com um endereço IP público ou voltado para o interior com apenas um endereço ILB (Balanceador de Carga Interno).

Você pode usar [grupos de segurança de rede][NetworkSecurityGroups] para restringir as comunicações de rede de entrada para a sub-rede onde reside um ambiente do serviço de aplicativo.  Isso permite que você execute aplicativos por trás de dispositivos e serviços upstream, como firewalls de aplicativo Web e provedores SaaS de rede.

Aplicativos frequentemente precisam acessar recursos corporativos, como bancos de dados internos e serviços da Web.  Uma abordagem comum é disponibilizar esses pontos de extremidade apenas ao tráfego de rede interno que flui dentro de uma rede virtual do Azure.  Quando uma Ambiente do Serviço de Aplicativo é unida à mesma rede virtual que os serviços internos, os aplicativos em execução no ambiente podem acessá-los, incluindo pontos de extremidade acessíveis via conexões [site a site][SiteToSite] e [do Azure ExpressRoute][ExpressRoute] .

Para obter mais detalhes sobre como os ambientes do serviço de aplicativo funcionam com redes virtuais e redes locais, consulte os seguintes artigos sobre [arquitetura de rede][NetworkArchitectureOverview], controle de [tráfego de entrada][ControllingInboundTraffic]e [conexão segura com back-ends][SecurelyConnectingToBackends]. 

## <a name="getting-started"></a>Introdução

Para começar a usar os ambientes do serviço de aplicativo, consulte [como criar um ambiente do serviço de aplicativo][HowToCreateAnAppServiceEnvironment]

Para obter uma visão geral da arquitetura de rede Ambiente do Serviço de Aplicativo, consulte o artigo [visão geral da arquitetura de rede][NetworkArchitectureOverview] .

Para obter detalhes sobre como usar um Ambiente do Serviço de Aplicativo com o ExpressRoute, consulte o seguinte artigo sobre os [ambientes de rota expressa e serviço de aplicativo][NetworkConfigDetailsForExpressRoute].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: https://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: ../overview-hosting-plans.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[LogicApps]: https://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  app-service-app-service-environment-geo-distributed-scale.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  app-service-web-configure-an-app-service-environment.md
[ControllingInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SecurelyConnectingToBackends]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NetworkArchitectureOverview]:  app-service-app-service-environment-network-architecture-overview.md
[NetworkConfigDetailsForExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->