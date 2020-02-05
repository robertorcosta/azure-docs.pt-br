---
title: Soluções VMware do Azure (AVS) – visão geral das máquinas virtuais
description: Saiba mais sobre máquinas virtuais de AVS e seus benefícios.
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0e6cc6d51e37cea98cc553a236295311d21f595a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024901"
---
# <a name="avs-virtual-machines-overview"></a>Visão geral das máquinas virtuais da AVS

O AVS permite que você gerencie VMs (máquinas virtuais) VMware a partir do portal do Azure. Um cluster ou um pool de recursos do seu cluster vSphere é gerenciado por meio do Azure, mapeando-o para sua assinatura.

Para criar uma VM de AVS do Azure, um modelo de VM deve existir em seu vCenter de nuvem privada da AVS. O modelo é usado para personalizar o sistema operacional e os aplicativos. A VM de modelo pode ser protegida para atender às políticas de segurança corporativa. Você pode usar o modelo para criar VMs e, em seguida, consumi-las do portal do Azure usando um modelo de autoatendimento.

## <a name="benefits"></a>Benefícios

As máquinas virtuais de AVS da portal do Azure fornecem um mecanismo de autoatendimento para que os usuários criem e gerenciem máquinas virtuais VMware.

* Criar uma VM de AVS em seu vCenter privado de nuvem privada da AVS
* Gerenciar Propriedades da VM
  * Adicionar/remover discos
  * Adicionar/remover NICs
* Operações de energia da sua VM da AVS
  * Ligar e desligar
  * Redefinir VM
* Excluir VM

## <a name="next-steps"></a>Próximos passos

* Saiba como [consumir VMs VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba como [mapear sua assinatura do Azure](azure-subscription-mapping.md)
