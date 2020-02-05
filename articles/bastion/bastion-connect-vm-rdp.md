---
title: Conectar-se a uma VM do Windows usando a bastiões do Azure | Microsoft Docs
description: Neste artigo, saiba como se conectar a uma máquina virtual do Azure que executa o Windows usando a bastiões do Azure.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 7dad6a517341f83f693e1e7e1f7d27e899e00f7e
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990480"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Conectar-se a uma máquina virtual do Windows usando a bastiões do Azure

Este artigo mostra como usar o RDP de forma segura e direta para suas VMs do Windows em uma rede virtual do Azure usando a bastiões do Azure. É possível se conectar a uma VM diretamente do portal do Azure. Usando o Azure Bastion, as VMs não precisam de um cliente, de um agente nem de software adicional. Para obter mais informações sobre a bastiões do Azure, consulte a [visão geral](bastion-overview.md).

## <a name="before-you-begin"></a>Antes de começar

Verifique se você configurou um host de bastiões do Azure para a rede virtual na qual a VM reside. Para obter mais informações, consulte [criar um host de bastiões do Azure](bastion-create-host-portal.md). Depois que o serviço de bastiões for provisionado e implantado em sua rede virtual, você poderá usá-lo para se conectar a qualquer VM nessa rede virtual.

A bastiões pressupõe que você esteja usando o RDP para se conectar a uma VM do Windows e o SSH para se conectar às suas VMs do Linux. Para obter informações sobre a conexão a uma VM do Linux, consulte [conectar-se a uma VM-Linux](bastion-connect-vm-ssh.md).

### <a name="required-roles"></a>Funções necessárias
Para fazer uma conexão, são necessárias as seguintes funções:

* Função de leitor na máquina virtual
* Função de leitor na placa de interface de rede com endereço IP privado da máquina virtual
* Função de leitor no recurso do Azure Bastion

### <a name="ports"></a>Portas

Para se conectar à VM do Windows via RDP, você deve ter as seguintes portas abertas na sua VM do Windows:

* Portas de entrada: RDP (3389)

## <a name="rdp"></a>Conectar usando RDP

1. Abra o [Portal do Azure](https://portal.azure.com). Navegue até a máquina virtual à qual você deseja se conectar e clique em **conectar**. A VM deve ser uma máquina virtual do Windows ao usar uma conexão RDP.

   ![Conexão de VM](./media/bastion-connect-vm-rdp/connect.png)
1. Depois de clicar em conectar, uma barra lateral é exibida com três guias – RDP, SSH e bastiões. Se a bastiões tiver sido provisionada para a rede virtual, a guia de bastiões estará ativa por padrão. Se você não provisionar a bastiões para a rede virtual, poderá clicar no link para configurar a bastiões. Para obter instruções de configuração, consulte [Configurar a bastiões](bastion-create-host-portal.md).

   ![Conexão de VM](./media/bastion-connect-vm-rdp/bastion.png)
1. Na guia bastião, o nome de usuário e a senha para sua máquina virtual e clique em **conectar**. A conexão RDP com essa máquina virtual via bastiões será aberta diretamente no portal do Azure (sobre o HTML5) usando a porta 443 e o serviço de bastiões.

   ![Conexão de VM](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Próximos passos

Leia as [perguntas frequentes de bastiões](bastion-faq.md)
