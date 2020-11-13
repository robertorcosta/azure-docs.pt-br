---
ms.openlocfilehash: 82571d1a0e651f638dec29184f0ecdc88562b3ad
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578802"
---


| Recurso |[Plano de Consumo](../articles/azure-functions/functions-scale.md#consumption-plan)|[Plano Premium](../articles/azure-functions/functions-scale.md#premium-plan)|[Plano dedicado](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Restrições de IP de entrada e acesso ao site privado](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅Sim|✅Sim|✅Sim|✅Sim|✅Sim|
|[Integração de rede virtual](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌Não|✅Sim (Regional)|✅Sim (regional e gateway)|✅Sim| ✅Sim|
|[Gatilhos de rede virtual (não HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌Não| ✅Sim |✅Sim|✅Sim|✅Sim|
|[Conexões híbridas](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (somente Windows)|❌Não|✅Sim|✅Sim|✅Sim|✅Sim|
|[Restrições de IP de saída](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌Não| ✅Sim|✅Sim|✅Sim|✅Sim|