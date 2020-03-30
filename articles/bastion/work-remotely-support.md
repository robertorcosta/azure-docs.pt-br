---
title: 'Trabalhando remotamente usando Bastião: Azure Bastion'
description: Esta página descreve como você pode aproveitar o Azure Bastion para permitir o funcionamento remoto devido à pandemia COVID-19.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mialdrid
ms.openlocfilehash: c0bba2560cccc208e2384421218ecebdfef65236
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333060"
---
# <a name="working-remotely-using-azure-bastion"></a>Trabalhando remotamente usando o Azure Bastion

O Azure Bastion desempenha um papel fundamental no suporte a cenários de trabalho remoto, permitindo que os usuários com conectividade com a internet acessem máquinas virtuais do Azure. Em particular, ele permite que os administradores de TI gerenciem seus aplicativos em execução no Azure a qualquer hora e de qualquer lugar do mundo.

>[!NOTE]
>Este artigo descreve como você pode aproveitar a rede Azure Bastion, Azure, Microsoft e o ecossistema de parceiros Do Zure para trabalhar remotamente e mitigar problemas de rede que você está enfrentando por causa da crise do COVID-19.
>

## <a name="securely-access-virtual-machines"></a>Acesse com segurança máquinas virtuais

Especificamente, o Azure Bastion fornece conectividade RDP/SSH segura e perfeita para máquinas virtuais dentro da rede virtual Azure, diretamente no portal Azure, sem o uso de um endereço IP público. Para obter mais informações sobre a arquitetura do Bastião do Azure e as principais características, confira [o que é o Azure Bastion](bastion-overview.md).

O Azure Bastion é implantado por rede virtual, o que significa que as empresas podem configurar e gerenciar um Azure Bastion para suportar rapidamente o acesso remoto do usuário a máquinas virtuais dentro de uma rede virtual Azure. Para obter orientações sobre como criar e gerenciar o Azure Bastion, consulte [Criar um host bastião](bastion-create-host-portal.md).

## <a name="next-steps"></a>Próximas etapas

* Configure o Azure Bastion usando o [portal Azure,](bastion-create-host-portal.md) [PowerShell](bastion-create-host-powershell.md)ou Azure CLI.

* Leia o [Bastion FAQ](bastion-faq.md) para obter informações adicionais.
