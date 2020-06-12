---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 5e0cff7bde6e80a776d694820ca7b69dafa7c0d9
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648831"
---
Acesso ao site privado significa tornar seu aplicativo acessível somente de uma rede privada, como em uma rede virtual do Azure.

* O acesso ao site privado está disponível nos planos [Premium](../articles/azure-functions/functions-premium-plan.md), [Consumo](../articles/azure-functions/functions-scale.md#consumption-plan) e [Serviço de Aplicativo](../articles/azure-functions/functions-scale.md#app-service-plan) quando os pontos de extremidade de serviço são configurados.
    * Os pontos de extremidade de serviço podem ser configurados por aplicativo em **Recursos da plataforma** > **Rede** > **Configurar restrições de acesso** > **Adicionar regra**. As redes virtuais agora podem ser selecionadas como um tipo de regra.
    * Para obter mais informações, consulte [Pontos de extremidade de serviço de rede virtual](../articles/virtual-network/virtual-network-service-endpoints-overview.md).
    * Saiba que, com pontos de extremidade de serviço, sua função ainda tem acesso total de saída à Internet, mesmo com a integração de rede virtual configurada.
* O acesso ao site privado também está disponível em um Ambiente do Serviço de Aplicativo configurado com um ILB (balanceador de carga interno). Para obter mais informações, confira [Criar e usar um balanceador de carga Interno com um Ambiente do Serviço de Aplicativo](../articles/app-service/environment/create-ilb-ase.md).

Para saber como configurar o acesso ao site privado, confira [Estabelecer acesso ao site privado do Azure Functions](../articles/azure-functions/functions-create-private-site-access.md).