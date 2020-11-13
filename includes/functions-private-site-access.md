---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 2d66e7f497f85141de172c59b67676e1bb93955e
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578800"
---
O [Ponto de Extremidade Privado do Azure](../articles/private-link/private-endpoint-overview.md) é um adaptador de rede que conecta você de maneira privada e segura a um serviço que conta com o Link Privado do Azure.  O Ponto de Extremidade Privado usa um endereço IP privado da rede virtual, colocando efetivamente o serviço na sua rede virtual.

Você pode usar o ponto de extremidade privado para suas funções hospedadas nos planos de [serviço de aplicativo](../articles/azure-functions/functions-scale.md#app-service-plan) e [Premium](../articles/azure-functions/functions-premium-plan.md) .

Ao criar uma conexão de ponto de extremidade privada de entrada para funções, você também precisará de um registro DNS para resolver o endereço privado.  Por padrão, um registro DNS privado será criado para você ao criar um ponto de extremidade privado usando o portal do Azure.

Para saber mais, consulte [usando pontos de extremidade privados para aplicativos Web](../articles/app-service/networking/private-endpoint.md).