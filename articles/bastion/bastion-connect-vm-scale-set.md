---
title: Conecte-se a um conjunto de escala de máquina virtual do Windows usando o Azure Bastion | Microsoft Docs
description: Neste artigo, saiba como se conectar a um conjunto de escala de máquina virtual do Azure usando o Azure Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 290a20fcd827841c24983f3bdd54b6db8e154462
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619335"
---
# <a name="connect-to-a-virtual-machine-scale-set-using-azure-bastion"></a>Conecte-se a um conjunto de escala de máquina virtual usando o Azure Bastion

Este artigo mostra como RDP de forma segura e perfeita para a instância de conjunto de escala de máquina virtual do Windows em uma rede virtual Azure usando o Azure Bastion. Você pode se conectar a uma instância de conjunto de escala de máquina virtual diretamente do portal Azure. Ao usar o Azure Bastion, as VMs não exigem um cliente, agente ou software adicional. Para obter mais informações sobre o Azure Bastion, consulte a [visão geral](bastion-overview.md).

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que você configurou um host Azure Bastion para a rede virtual na qual reside o conjunto de escala de máquina virtual. Para obter mais informações, consulte [Criar um host Azure Bastion](bastion-create-host-portal.md). Uma vez que o serviço Bastion seja provisionado e implantado em sua rede virtual, você pode usá-lo para se conectar a uma instância de conjunto de escala de máquina virtual nesta rede virtual. Bastion assume que você está usando RDP para se conectar a um conjunto de escala de máquina virtual do Windows e SSH para se conectar ao conjunto de escala da máquina virtual Linux. Para obter informações sobre conexão com um VM Linux, consulte [Conecte-se a uma VM - Linux](bastion-connect-vm-ssh.md).

## <a name="connect-using-rdp"></a><a name="rdp"></a>Conecte-se usando RDP

1. Abra o [portal Azure.](https://portal.azure.com) Navegue até o conjunto de escala seleto da máquina virtual ao que deseja se conectar.

   ![navigate](./media/bastion-connect-vm-scale-set/1.png)
2. Navegue até a instância de conjunto de escala de máquina virtual à que deseja se conectar e selecione **Conectar**. Ao usar uma conexão RDP, o conjunto de escala da máquina virtual deve ser um conjunto de escala de máquina virtual do Windows.

   ![conjunto de escala de máquina virtual](./media/bastion-connect-vm-scale-set/2.png)
3. Depois de selecionar **Conectar**, aparece uma barra lateral que tem três guias – RDP, SSH e Bastion. Selecione a guia **Bastion** na barra lateral. Se você não provisionou Bastion para a rede virtual, você pode selecionar o link para configurar Bastion. Para obter instruções de configuração, consulte [Configure Bastion](bastion-create-host-portal.md).

   ![Guia bastião](./media/bastion-connect-vm-scale-set/3.png)
4. Na guia Bastion, digite o nome de usuário e a senha para o conjunto de escalas da máquina virtual e selecione **Conectar**.

   ![conectar](./media/bastion-connect-vm-scale-set/4.png)
5. A conexão RDP a esta máquina virtual via Bastion será aberta diretamente no portal Azure (sobre HTML5) usando a porta 443 e o serviço Bastion.

## <a name="next-steps"></a>Próximas etapas

Leia o [Bastião FAQ](bastion-faq.md).