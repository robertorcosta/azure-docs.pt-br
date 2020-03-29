---
title: Solicitações de aumento de cota de vCPU do Azure Resource Manager
description: Solicitações de aumento de cota de vCPU do Azure Resource Manager
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: cdbf7364a275eb246615f398044456645a96d1a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843660"
---
# <a name="quota-increase-requests"></a>Solicitações de um aumento de cota

As cotas de vCPU do Gerenciador de Recursos para máquinas virtuais e conjuntos de escala de máquinas virtuais são aplicadas em dois níveis para cada assinatura, em cada região.

O primeiro nível é o limite de vCPUs regionais totais em todas as séries de VM. O segundo nível é o limite de vCPUs da série VM, como os vCPUs da série D. Sempre que uma nova máquina virtual for implantada, a soma do uso de vCPUs novos e existentes para essa série VM não deve exceder a cota de vCPU aprovada para aquela série VM em particular. Além disso, a contagem total de vCPU novo e existente implantado em todas as séries de VM não deve exceder a cota total de vCPUs regionais aprovada para a assinatura. Se qualquer uma dessas cotas for excedida, a implantação da VM não será permitida.
Você pode solicitar um aumento do limite de cotas vCPUs para a série VM do portal Azure. Um aumento na cota da série VM aumenta automaticamente o limite total de vCPUs regionais pelo mesmo valor.

Quando uma nova assinatura é criada, os vCPUs regionais totais padrão podem não ser iguais à soma das cotas vCPU padrão para todas as séries de VM individuais. Este fato pode resultar em uma assinatura com cota suficiente para cada série de VM individual que você deseja implantar. Poderia faltar cota suficiente para vCPUs regionais totais para todas as implantações. Neste caso, você precisará apresentar uma solicitação para aumentar explicitamente o limite de vCPUs regionais totais. O limite total de VCPUs regionais não pode exceder a soma da cota aprovada em todas as séries de VM para a região.

Para obter mais informações sobre cotas, consulte [cotas de vCPU de máquinavirtual](../../virtual-machines/windows/quotas.md) e limites de assinatura e serviço do [Azure, cotas e restrições](../../azure-resource-manager/management/azure-subscription-service-limits.md).

