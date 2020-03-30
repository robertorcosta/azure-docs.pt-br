---
title: Visão geral das máquinas virtuais
titleSuffix: Azure VMware Solution by CloudSimple
description: Conheça as máquinas virtuais CloudSimple e seus benefícios.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 436a5ab6fb286ce206f981487b6a29d774ecd1a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024901"
---
# <a name="cloudsimple-virtual-machines-overview"></a>Visão geral das máquinas virtuais cloudsimple

O CloudSimple permite gerenciar máquinas virtuais VMware (VMs) a partir do portal Azure.  Um cluster ou um pool de recursos do seu cluster vSphere é gerenciado através do Azure, mapeando-o para sua assinatura.

Para criar uma VM CloudSimple do Azure, um modelo de VM deve existir no seu vCenter private cloud.  O modelo é usado para personalizar o sistema operacional e aplicativos.  O modelo VM pode ser endurecido para atender às políticas de segurança corporativa.  Você pode usar o modelo para criar VMs e, em seguida, consumi-los a partir do portal Azure usando um modelo de autoatendimento.

## <a name="benefits"></a>Benefícios

As máquinas virtuais CloudSimple do portal Azure fornecem um mecanismo de autoatendimento para os usuários criarem e gerenciarem máquinas virtuais VMware.

* Crie um VM CloudSimple no seu vCenter de nuvem privada
* Gerenciar propriedades VM
  * Adicionar/remover discos
  * Adicionar/remover NICs
* Operações de energia do seu VM CloudSimple
  * Ligar e desligar a energia
  * Reiniciar VM
* Excluir VM

## <a name="next-steps"></a>Próximas etapas

* Saiba como [consumir VMs VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba como [mapear sua assinatura do Azure](azure-subscription-mapping.md)
