---
title: Conectar-se a uma VM do Windows usando a bastiões do Azure
description: Neste artigo, saiba como se conectar a uma máquina virtual do Azure que executa o Windows usando a bastiões do Azure.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/12/2020
ms.author: cherylmc
ms.openlocfilehash: 9c9bac20beb415f8bc29ca63d530e5cd8492d2d3
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91997015"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Conectar-se a uma máquina virtual do Windows usando a bastiões do Azure

Usando a bastiões do Azure, você pode se conectar de forma segura e direta às suas máquinas virtuais por SSL diretamente no portal do Azure. Quando você usa a bastiões do Azure, suas VMs não exigem um cliente, agente ou software adicional. Este artigo mostra como se conectar às suas VMs do Windows. Para obter informações sobre como se conectar a uma VM do Linux, consulte [conectar-se a uma VM do Linux](bastion-connect-vm-ssh.md).

A bastiões do Azure fornece conectividade segura para todas as VMs na rede virtual em que ela é provisionada. O uso do Azure Bastion protege suas máquinas virtuais contra a exposição das portas RDP/SSH ao mundo externo, fornecendo acesso seguro usando o RDP/o SSH. Para obter mais informações, consulte o [que é a bastiões do Azure?](bastion-overview.md).

## <a name="before-you-begin"></a>Antes de começar

Antes de começar, verifique se você atende aos seguintes critérios:

* Uma VNet com o host de bastiões já está instalada.

   Verifique se você configurou um host de bastiões do Azure para a rede virtual na qual a VM está localizada. Depois que o serviço de bastiões for provisionado e implantado em sua rede virtual, você poderá usá-lo para se conectar a qualquer VM na rede virtual. Para configurar um host de bastiões do Azure, consulte [criar um host de bastiões](tutorial-create-host-portal.md#createhost).
* Uma máquina virtual do Windows na rede virtual.
* As seguintes funções obrigatórias:
  * Função de leitor na máquina virtual.
  * Função de leitor na NIC com IP privado da máquina virtual.
  * Função de leitor no recurso de bastiões do Azure.
* Portas: para se conectar à VM do Windows, você deve ter as seguintes portas abertas na sua VM do Windows:
  * Portas de entrada: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Conectar

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]
 
## <a name="next-steps"></a>Próximas etapas

Leia as [perguntas frequentes do Bastion](bastion-faq.md).