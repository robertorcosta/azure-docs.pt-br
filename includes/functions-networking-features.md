---
ms.openlocfilehash: 8d5c3fff2646536ffe5c593e13ccdab23397d44b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84906695"
---


| Recurso |[Plano de Consumo](../articles/azure-functions/functions-scale.md#consumption-plan)|[Plano Premium](../articles/azure-functions/functions-scale.md#premium-plan)|[Plano dedicado](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Restrições de IP de entrada e acesso ao site privado](../articles/azure-functions/functions-networking-options.md#inbound-ip-restrictions)|✅Sim|✅Sim|✅Sim|✅Sim|✅Sim|
|[Integração de rede virtual](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌Não|✅Sim (Regional)|✅Sim (regional e gateway)|✅Sim| ✅Sim|
|[Gatilhos de rede virtual (não HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌Não| ✅Sim |✅Sim|✅Sim|✅Sim|
|[Conexões híbridas](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (somente Windows)|❌Não|✅Sim|✅Sim|✅Sim|✅Sim|
|[Restrições de IP de saída](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌Não| ✅Sim|✅Sim|✅Sim|✅Sim|