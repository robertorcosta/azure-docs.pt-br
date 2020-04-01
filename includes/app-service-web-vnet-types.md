---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 41b7c7163ff56c848b1b66e606c06ba45d36e610
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419519"
---
* Os sistemas multilocatários que suportam toda a gama de planos de preços, exceto isolados.
* O App Service Environment, que se implanta em sua rede virtual e suporta aplicativos de plano de preços isolados.

O recurso VNet Integration é usado em aplicativos multilocatários. Se o seu aplicativo estiver no [App Service Environment,][ASEintro]então ele já está em uma rede virtual e não requer o uso do recurso De Integração VNet para alcançar recursos na mesma rede virtual. Para obter mais informações sobre todos os recursos de rede, consulte os recursos de [rede do App Service][Networkingfeatures].

A VNet Integration dá ao seu aplicativo acesso aos recursos da sua rede virtual, mas não concede acesso privado de entrada ao seu aplicativo a partir da rede virtual. O acesso privado ao site refere-se a tornar um aplicativo acessível apenas a partir de uma rede privada, como dentro de uma rede virtual Do Zure. O VNet Integration é usado apenas para fazer chamadas de saída do seu aplicativo para sua rede virtual. O recurso De Integração VNet se comporta de forma diferente quando é usado com redes virtuais na mesma região e com redes virtuais em outras regiões. O recurso De Integração VNet tem duas variações:

* **Integração VNet Regional**: Quando você se conecta às redes virtuais do Azure Resource Manager na mesma região, você deve ter uma sub-rede dedicada na rede virtual com a que está se integrando.
* **Integração VNet necessária por gateway**: Quando você se conecta a redes virtuais em outras regiões ou a uma rede virtual clássica na mesma região, você precisa de um gateway de Rede Virtual Azure provisionado na rede virtual de destino.

Os recursos da Integração VNet:

* Exija um plano de preços Standard, Premium, PremiumV2 ou Elastic Premium.
* Suporte TCP e UDP.
* Trabalhe com aplicativos e aplicativos de função do Azure App Service.

Há algumas coisas que a VNet Integration não suporta, como:

* A montagem de uma unidade.
* Integração do Active Directory.
* Netbios.

A Integração VNet necessária ao Gateway fornece acesso a recursos apenas na rede virtual de destino ou em redes conectadas à rede virtual de destino com peering ou VPNs. A Integração VNet exigida pelo Gateway não permite o acesso aos recursos disponíveis nas conexões do Azure ExpressRoute ou funciona com pontos finais de serviço.

Independentemente da versão usada, o VNet Integration dá ao seu aplicativo acesso a recursos em sua rede virtual, mas não concede acesso privado de entrada ao seu aplicativo a partir da rede virtual. O acesso privado ao site refere-se a tornar seu aplicativo acessível apenas a partir de uma rede privada, como dentro de uma rede virtual Do Zure. A Integração VNet é apenas para fazer chamadas de saída do seu aplicativo em sua rede virtual.

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
