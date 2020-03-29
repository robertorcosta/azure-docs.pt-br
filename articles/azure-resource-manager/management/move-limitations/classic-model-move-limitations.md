---
title: Mover recursos de implantação do Azure Classic
description: Use o Azure Resource Manager para mover recursos de implantação clássicos para um novo grupo de recursos ou assinatura.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 78b9769a31fa0c96c12e18d05cb9c484aa52a1d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485280"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>Mova a orientação para os recursos do modelo de implantação clássico

As etapas para mover recursos implantados através do modelo clássico diferem com base em se você está movendo os recursos dentro de uma assinatura ou para uma nova assinatura.

## <a name="move-in-the-same-subscription"></a>Mova-se na mesma assinatura

Ao mover recursos de um grupo de recursos para outro na mesma assinatura, as seguintes restrições se aplicarão:

* Redes virtuais (clássicas) não podem ser movidas.
* Máquinas virtuais (clássicas) devem ser movidas com o serviço de nuvem.
* Um serviço de nuvem pode ser movido apenas quando a movimentação incluir todas as suas máquinas virtuais.
* Apenas um serviço de nuvem pode ser movido por vez.
* Apenas uma conta de armazenamento (clássica) pode ser movida por vez.
* Uma conta de armazenamento (clássica) não pode ser movida na mesma operação com uma máquina virtual ou um serviço de nuvem.

Para transferir recursos clássicos para um novo grupo de recursos dentro da mesma assinatura, use as [operações padrão](../move-resource-group-and-subscription.md) de movimentação através do portal, Azure PowerShell, Azure CLI ou Rest API. Use as mesmas operações como você usa para mover os recursos do Resource Manager.

## <a name="move-across-subscriptions"></a>Mova-se através de assinaturas

Ao mover recursos para uma nova assinatura, as seguintes restrições se aplicarão:

* Todos os recursos clássicos na assinatura devem ser movidos na mesma operação.
* A assinatura de destino não deve ter nenhum outro recurso clássico.
* A movimentação pode ser solicitada apenas por meio de uma API REST separada para movimentações clássicas. Os comandos de movimentação padrão do Gerenciador de Recursos não funcionam quando há uma movimentação dos recursos clássicos para uma nova assinatura.

Para mover recursos clássicos para uma nova assinatura, use operações REST específicas para recursos clássicos. Para usar REST, faça as seguintes etapas:

1. Verifique se a assinatura de origem pode participar de uma movimentação entre assinaturas. Use a operação a seguir:

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     No corpo da solicitação, inclua:

   ```json
   {
    "role": "source"
   }
   ```

     A resposta para a operação de validação é no seguinte formato:

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

1. Verifique se a assinatura de destino pode participar de uma movimentação entre assinaturas. Use a operação a seguir:

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     No corpo da solicitação, inclua:

   ```json
   {
    "role": "target"
   }
   ```

     A resposta está no mesmo formato que a validação de assinatura de origem.
1. Se ambas as assinaturas forem aprovadas na validação, mova todos os recursos clássicos de uma assinatura para outra, use a seguinte operação:

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    No corpo da solicitação, inclua:

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

A operação pode executar por vários minutos.

## <a name="next-steps"></a>Próximas etapas

Se você tiver problemas para mover recursos clássicos, entre em contato com [o Suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Para ver comandos para mover recursos, confira [Move resources to new resource group or subscription](../move-resource-group-and-subscription.md) (Mover recursos para o novo grupo de recursos ou assinatura).
