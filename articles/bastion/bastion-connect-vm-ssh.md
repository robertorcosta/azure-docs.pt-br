---
title: Conecte-se a um VM Linux usando o Azure Bastion
description: Neste artigo, aprenda a se conectar ao Linux Virtual Machine usando o Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 381f45a51002188c72174cff83c26b829912a0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596820"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Conecte-se usando SSH a uma máquina virtual Linux usando o Azure Bastion

Este artigo mostra como ssh de forma segura e perfeita para seus VMs Linux em uma rede virtual Azure. Você pode se conectar a uma VM diretamente do portal Azure. Ao usar o Azure Bastion, as VMs não exigem um cliente, agente ou software adicional. Para obter mais informações sobre o Azure Bastion, consulte a [visão geral](bastion-overview.md).

Você pode usar o Azure Bastion para se conectar a uma máquina virtual Linux usando SSH. Você pode usar tanto o nome de usuário/senha quanto as chaves SSH para autenticação. Você pode se conectar à sua VM com chaves SSH usando qualquer um:

* Uma chave privada que você digita manualmente
* Um arquivo que contém as informações de chave privadas

A chave privada SSH deve estar `"-----BEGIN RSA PRIVATE KEY-----"` em um `"-----END RSA PRIVATE KEY-----"`formato que começa com e termina com .

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que você configurou um host Azure Bastion para a rede virtual em que a VM reside. Para obter mais informações, consulte [Criar um host Azure Bastion](bastion-create-host-portal.md). Uma vez que o serviço Bastion seja provisionado e implantado em sua rede virtual, você pode usá-lo para se conectar a qualquer VM nesta rede virtual. 

Quando você usa bastion para se conectar, ele assume que você está usando RDP para se conectar a uma VM do Windows e SSH para se conectar às suas VMs Linux. Para obter informações sobre como se conectar a uma VM windows, consulte [Conectar-se a uma VM - Windows](bastion-connect-vm-rdp.md).

### <a name="required-roles"></a>Funções necessárias

Para fazer uma conexão, são necessárias as seguintes funções:

* Função de leitor na máquina virtual
* Função de leitor na placa de interface de rede com endereço IP privado da máquina virtual
* Função de leitor no recurso do Azure Bastion

### <a name="ports"></a>Portas

Para se conectar ao Linux VM via SSH, você deve ter as seguintes portas abertas em sua VM:

* Porta de entrada: SSH (22)

## <a name="connect-using-username-and-password"></a><a name="username"></a>Conectar: Usando nome de usuário e senha

1. Abra o [portal Azure.](https://portal.azure.com) Navegue até a máquina virtual à que deseja se conectar e clique em **Conectar** e selecione **Bastião** a partir da estada.

   ![Conectar](./media/bastion-connect-vm-ssh/connect.png)
1. Depois de clicar em Bastion, aparece uma barra lateral que tem três guias – RDP, SSH e Bastion. Se Bastion foi provisionado para a rede virtual, a guia Bastion está ativa por padrão. Se você não provisionou Bastion para a rede virtual, consulte [Configurar Bastião](bastion-create-host-portal.md).

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Digite o nome de usuário e a senha do SSH na sua máquina virtual.
1. Clique no botão **Conectar** depois de digitar a tecla.

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>Conectar: Digite manualmente uma chave privada

1. Abra o [portal Azure.](https://portal.azure.com) Navegue até a máquina virtual à que deseja se conectar e clique em **Conectar** e selecione **Bastião** a partir da estada.

   ![Conectar](./media/bastion-connect-vm-ssh/connect.png)
1. Depois de clicar em Bastion, aparece uma barra lateral que tem três guias – RDP, SSH e Bastion. Se Bastion foi provisionado para a rede virtual, a guia Bastion está ativa por padrão. Se você não provisionou Bastion para a rede virtual, consulte [Configurar Bastião](bastion-create-host-portal.md).

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Digite o nome de usuário e selecione **SSH Private Key**.
1. Digite sua chave privada na área de texto **SSH Private Key** (ou cole-a diretamente).
1. Clique no botão **Conectar** depois de digitar a tecla.

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>Conectar: Usando um arquivo de chave privada

1. Abra o [portal Azure.](https://portal.azure.com) Navegue até a máquina virtual à que deseja se conectar e clique em **Conectar** e selecione **Bastião** a partir da estada.

   ![Conectar](./media/bastion-connect-vm-ssh/connect.png)
1. Depois de clicar em Bastion, aparece uma barra lateral que tem três guias – RDP, SSH e Bastion. Se Bastion foi provisionado para a rede virtual, a guia Bastion está ativa por padrão. Se você não provisionou Bastion para a rede virtual, consulte [Configurar Bastião](bastion-create-host-portal.md).

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Digite o nome de usuário e selecione **seleção ssh private key a partir de arquivo local**.
1. Clique no botão **Procurar** (o ícone da pasta no arquivo local).
1. Procure o arquivo e clique em **Abrir**.
1. Clique **em Conectar** para conectar-se à VM. Uma vez que você clique em Conectar, SSH para esta máquina virtual será aberta diretamente no portal Azure. Esta conexão é sobre HTML5 usando a porta 443 no serviço Bastion sobre o IP privado da sua máquina virtual.

## <a name="next-steps"></a>Próximas etapas

Leia o [Bastion FAQ](bastion-faq.md)
