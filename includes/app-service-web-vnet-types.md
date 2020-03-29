---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: da43e639779dd4946cb014be777e3a6db0366e24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671484"
---
* Os sistemas multilocatário que dão suporte a toda a gama de planos de preço, exceto Isolado
* O App Service Environment (ASE), que implanta em seu VNet e suporta aplicativos de plano de preços isolados

Este documento passa pelo recurso VNet Integration, que é para uso nos aplicativos multi-inquilinos. Se o aplicativo está no [Ambiente do Serviço de Aplicativo][ASEintro], ele já está em uma rede virtual e não requer o uso do recurso de Integração VNet para acessar recursos na mesma VNet. Para obter detalhes sobre todos os recursos de rede, leia os [recursos de rede do App Service][Networkingfeatures]

A VNet Integration dá ao seu aplicativo acesso aos recursos em sua rede virtual, mas não concede acesso privado de entrada ao seu aplicativo a partir do VNet. O acesso privado ao site refere-se a tornar o aplicativo acessível apenas a partir de uma rede privada, como dentro de uma rede virtual Do Zure. A Integração VNet é apenas para fazer chamadas de saída do seu aplicativo em seu VNet. O recurso De Integração VNet se comporta de forma diferente quando usado com VNets na mesma região e com VNets em outras regiões. O recurso De Integração VNet tem duas variações.

* Integração Regional VNet - Ao se conectar aos VNets do Resource Manager na mesma região, você deve ter uma sub-rede dedicada no VNet com o que você está integrando. 
* Gateway exigiu integração VNet - Ao se conectar a VNets em outras regiões ou a um VNet clássico na mesma região, você precisa de um gateway de Rede Virtual provisionado no VNet de destino.

Os recursos da Integração VNet:

* requerem um plano de preços Standard, Premium, PremiumV2 ou Elastic Premium 
* suporte TCP e UDP
* trabalhar com aplicativos de serviço de aplicativo e aplicativos de função

Há algumas coisas a que a Integração VNet não dá suporte, incluindo:

* montagem de unidade
* integração ao AD 
* NetBios

O Gateway required VNet Integration só fornece acesso a recursos no VNet de destino ou em redes conectadas ao VNet de destino com peering ou VPNs. O Gateway required VNet Integration não permite o acesso aos recursos disponíveis nas conexões expressRoute ou funciona com pontos finais de serviço. 

Independentemente da versão usada, o VNet Integration dá ao seu aplicativo acesso a recursos em sua rede virtual, mas não concede acesso privado de entrada ao seu aplicativo a partir da rede virtual. Acesso ao site privado significa tornar seu aplicativo acessível somente de uma rede privada, como de dentro de uma rede virtual do Azure. A Integração VNet é apenas para fazer chamadas de saída do seu aplicativo em seu VNet. 

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features