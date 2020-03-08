---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: da43e639779dd4946cb014be777e3a6db0366e24
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671484"
---
* Os sistemas multilocatário que dão suporte a toda a gama de planos de preço, exceto Isolado
* O Ambiente do Serviço de Aplicativo (ASE), que é implantado em sua VNet e dá suporte a aplicativos de plano de preços isolados

Este documento passa pelo recurso de integração VNet, que é para uso em aplicativos multilocatário. Se seu aplicativo estiver em [ambiente do serviço de aplicativo][ASEintro], ele já estará em uma VNet e não exigirá o uso do recurso de integração VNet para alcançar recursos na mesma VNet. Para obter detalhes sobre todos os recursos de rede, leia [recursos de rede do serviço de aplicativo][Networkingfeatures]

A integração VNet dá ao seu aplicativo acesso aos recursos em sua rede virtual, mas não concede acesso privado de entrada ao seu aplicativo por meio da VNet. Acesso ao site privado refere-se a tornar o aplicativo acessível somente de uma rede privada, como de dentro de uma rede virtual do Azure. A integração VNet é apenas para fazer chamadas de saída de seu aplicativo para sua VNet. O recurso de integração VNet se comporta de maneira diferente quando usado com VNets na mesma região e com VNets em outras regiões. O recurso integração VNet tem duas variações.

* Integração de VNet regional – ao se conectar ao Gerenciador de recursos VNets na mesma região, você deve ter uma sub-rede dedicada na VNet com a qual está se integrando. 
* Integração VNet necessária do gateway-ao conectar-se ao VNets em outras regiões ou a uma VNet clássica na mesma região, você precisa de um gateway de rede virtual provisionado na VNet de destino.

Os recursos de integração VNet:

* exigir um plano de preços Standard, Premium, PremiumV2 ou elástico Premium 
* suporte a TCP e UDP
* trabalhar com aplicativos do serviço de aplicativo e aplicativos de funções

Há algumas coisas a que a Integração VNet não dá suporte, incluindo:

* montagem de unidade
* integração ao AD 
* NetBios

A integração VNet necessária do gateway só fornece acesso aos recursos na VNet de destino ou em redes conectadas à VNet de destino com emparelhamento ou VPNs. A integração VNet necessária do gateway não permite o acesso aos recursos disponíveis em conexões do ExpressRoute ou funciona com pontos de extremidade de serviço. 

Independentemente da versão usada, a integração VNet dá ao seu aplicativo acesso aos recursos em sua rede virtual, mas não concede acesso privado de entrada ao seu aplicativo a partir da rede virtual. Acesso ao site privado significa tornar seu aplicativo acessível somente de uma rede privada, como de dentro de uma rede virtual do Azure. A integração VNet é apenas para fazer chamadas de saída de seu aplicativo para sua VNet. 

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features