---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: c31a5aaa9866a4ce97cd3cd59a8e363834f70587
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312825"
---
* Os sistemas multilocatários que suportam toda a gama de planos de preços, exceto isolados.
* O App Service Environment, que é implantado em seu VNet e suporta aplicativos de plano de preços isolados.

O recurso VNet Integration é usado em aplicativos multilocatários. Se o aplicativo está no [Ambiente do Serviço de Aplicativo][ASEintro], ele já está em uma rede virtual e não requer o uso do recurso de Integração VNet para acessar recursos na mesma VNet. Para obter mais informações sobre todos os recursos de rede, consulte os recursos de [rede do App Service][Networkingfeatures].

O VNet Integration dá ao seu aplicativo acesso aos recursos em seu VNet, mas não concede acesso privado de entrada ao seu aplicativo a partir do VNet. O acesso privado ao site refere-se a tornar um aplicativo acessível apenas a partir de uma rede privada, como dentro de uma rede virtual Do Zure. O VNet Integration é usado apenas para fazer chamadas de saída do seu aplicativo em seu VNet. O recurso De Integração VNet se comporta de forma diferente quando é usado com VNet na mesma região e com VNet em outras regiões. O recurso De Integração VNet tem duas variações:

* **Integração VNet Regional**: Quando você se conecta às redes virtuais do Azure Resource Manager na mesma região, você deve ter uma sub-rede dedicada na VNet com a que está se integrando.
* **Integração VNet necessária por gateway**: Quando você se conecta ao VNet em outras regiões ou a uma rede virtual clássica na mesma região, você precisa de um gateway de Rede Virtual Azure provisionado no VNet de destino.

Os recursos da Integração VNet:

* Exija um plano de preços Standard, Premium, PremiumV2 ou Elastic Premium.
* Suporte TCP e UDP.
* Trabalhe com aplicativos e aplicativos de função do Azure App Service.

Há algumas coisas que a VNet Integration não suporta, como:

* A montagem de uma unidade.
* Integração do Active Directory.
* Netbios.

A VNet Integration, necessária ao Gateway, fornece acesso a recursos apenas no VNet de destino ou em redes conectadas ao VNet de destino com peering ou VPNs. A Integração VNet exigida pelo Gateway não permite o acesso aos recursos disponíveis nas conexões do Azure ExpressRoute ou funciona com pontos finais de serviço.

Independentemente da versão usada, o VNet Integration dá ao seu aplicativo acesso a recursos em seu VNet, mas não concede acesso privado de entrada ao seu aplicativo a partir do VNet. O acesso privado ao site refere-se a tornar seu aplicativo acessível apenas a partir de uma rede privada, como de dentro de um Azure VNet. A Integração VNet é apenas para fazer chamadas de saída do seu aplicativo em seu VNet.

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
