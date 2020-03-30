---
title: Mover recursos do Azure Networking para um novo grupo de assinatura ou recursos
description: Use o Azure Resource Manager para mover redes virtuais e outros recursos de rede para um novo grupo de recursos ou assinatura.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 0cd6887d3489f2ffede0f5e3d63533a33a6ccc04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485228"
---
# <a name="move-guidance-for-networking-resources"></a>Mover orientação para recursos de rede

Este artigo descreve como mover redes virtuais e outros recursos de rede para cenários específicos.

## <a name="dependent-resources"></a>Recursos dependentes

Ao mover uma rede virtual, você também deve mover os recursos dependentes. Para Gateways de VPN, você deve mover endereços IP, gateways de rede virtual e todos os recursos de conexão associados. Os gateways de rede local podem estar em um grupo de recursos diferentes.

Para mover uma máquina virtual com um cartão de interface de rede para uma nova assinatura, você deve mover todos os recursos dependentes. Mova a rede virtual para a placa de interface de rede, todos os outros cartões de interface de rede para a rede virtual e os gateways VPN.

Para obter mais informações, consulte [Cenário para mover através de assinaturas](../move-resource-group-and-subscription.md#scenario-for-move-across-subscriptions).

## <a name="peered-virtual-network"></a>Rede virtual peered

Para mover uma rede virtual emparelhada, primeiro é necessário desabilitar o emparelhamento de rede virtual. Quando desabilitado, você pode mover a rede virtual. Após a movimentação, reabilite o emparelhamento de rede virtual.

## <a name="subnet-links"></a>Links de sub-rede

Você não pode mover uma rede virtual para uma assinatura diferente caso a rede virtual contenha uma sub-rede com links de navegação de recurso. Por exemplo, se um recurso de Cache do Azure para Redis estiver implantado em uma sub-rede, essa sub-rede terá um link de navegação do recurso.

## <a name="next-steps"></a>Próximas etapas

Para ver comandos para mover recursos, confira [Move resources to new resource group or subscription](../move-resource-group-and-subscription.md) (Mover recursos para o novo grupo de recursos ou assinatura).
