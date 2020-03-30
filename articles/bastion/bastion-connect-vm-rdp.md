---
title: Conecte-se a um VM do Windows usando o Azure Bastion
description: Neste artigo, aprenda a se conectar a uma Máquina Virtual do Azure executando o Windows usando o Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 6ec60dc313c8a4374637adf38ea0e5a7d4ed964b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597330"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Conecte-se a uma máquina virtual do Windows usando o Azure Bastion

Usando o Azure Bastion, você pode se conectar com segurança e perfeição às suas máquinas virtuais através do SSL diretamente no portal Azure. Quando você usa o Azure Bastion, suas VMs não exigem um cliente, agente ou software adicional. Este artigo mostra como se conectar às Suas VMs do Windows. Para obter informações sobre como se conectar a um VM Linux, consulte [Conectar-se a uma VM usando o Azure Bastion - Linux](bastion-connect-vm-ssh.md).

O Azure Bastion fornece conectividade segura a todas as VMs na rede virtual em que é provisionado. O uso do Azure Bastion protege suas máquinas virtuais contra a exposição das portas RDP/SSH ao mundo externo, fornecendo acesso seguro usando o RDP/o SSH. Para obter mais informações, consulte a [visão geral](bastion-overview.md).

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que você configurou um host Azure Bastion para a rede virtual em que a VM está localizada. Uma vez que o serviço Bastion é provisionado e implantado em sua rede virtual, você pode usá-lo para se conectar a qualquer VM na rede virtual. Para configurar um host Azure Bastion, consulte [Criar um host Azure Bastion](bastion-create-host-portal.md).

### <a name="required-roles"></a>Funções necessárias

Para fazer uma conexão, são necessárias as seguintes funções:

* Função de leitor na máquina virtual
* Função de leitor na placa de interface de rede com endereço IP privado da máquina virtual
* Função de leitor no recurso do Azure Bastion

### <a name="ports"></a>Portas

Para se conectar ao Windows VM, você deve ter as seguintes portas abertas em sua VM do Windows:

* Portas de entrada: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Conectar

1. Abra o [portal Azure.](https://portal.azure.com) Navegue até a máquina virtual à que deseja se conectar e clique em **Conectar** e selecione **Bastião** a partir da estada.

   ![Conexão VM](./media/bastion-connect-vm-rdp/connect.png)
1. Depois de clicar em Bastion, aparece uma barra lateral que tem três guias – RDP, SSH e Bastion. Se Bastion foi provisionado para a rede virtual, a guia Bastion está ativa por padrão. Se você não provisionou Bastion para a rede virtual, você pode clicar no link para configurar Bastion. Para obter instruções de configuração, consulte [Configure Bastion](bastion-create-host-portal.md).

   ![guia bastião](./media/bastion-connect-vm-rdp/bastion.png)
1. Na guia Bastion, insira o nome de usuário e a senha da sua máquina virtual e clique em **Conectar**. A conexão RDP a esta máquina virtual via Bastion será aberta diretamente no portal Azure (sobre HTML5) usando a porta 443 e o serviço Bastion.

   ![Conexão RDP](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Próximas etapas

Leia o [Bastion FAQ](bastion-faq.md)
