---
title: Conectar-se a uma VM do Linux usando a bastiões do Azure | Microsoft Docs
description: Neste artigo, saiba como se conectar à máquina virtual do Linux usando a bastiões do Azure.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 7fe1c2f74ca2a7b0fa4aefad934c45edd6f85a73
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990434"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Conectar-se usando SSH em uma máquina virtual Linux usando a bastiões do Azure

Este artigo mostra como fazer SSH de forma segura e direta para suas VMs do Linux em uma rede virtual do Azure. É possível se conectar a uma VM diretamente do portal do Azure. Usando o Azure Bastion, as VMs não precisam de um cliente, de um agente nem de software adicional. Para obter mais informações sobre a bastiões do Azure, consulte a [visão geral](bastion-overview.md).

Você pode usar a bastiões do Azure para se conectar a uma máquina virtual Linux usando SSH. Você pode usar o nome de usuário/senha e as chaves SSH para autenticação. Você pode se conectar à sua VM com chaves SSH usando:

* Uma chave privada que você insere manualmente
* Um arquivo que contém as informações de chave privada

A chave privada SSH deve estar em um formato que comece com `"-----BEGIN RSA PRIVATE KEY-----"` e termine com `"-----END RSA PRIVATE KEY-----"`.

## <a name="before-you-begin"></a>Antes de começar

Verifique se você configurou um host de bastiões do Azure para a rede virtual na qual a VM reside. Para obter mais informações, consulte [criar um host de bastiões do Azure](bastion-create-host-portal.md). Depois que o serviço de bastiões for provisionado e implantado em sua rede virtual, você poderá usá-lo para se conectar a qualquer VM nessa rede virtual. 

Quando você usa a bastiões para se conectar, ele pressupõe que você está usando o RDP para se conectar a uma VM do Windows e o SSH para se conectar às suas VMs do Linux. Para obter informações sobre como se conectar a uma VM do Windows, consulte [conectar-se a uma VM-Windows](bastion-connect-vm-rdp.md).

### <a name="required-roles"></a>Funções necessárias

Para fazer uma conexão, são necessárias as seguintes funções:

* Função de leitor na máquina virtual
* Função de leitor na placa de interface de rede com endereço IP privado da máquina virtual
* Função de leitor no recurso do Azure Bastion

### <a name="ports"></a>Portas

Para se conectar à VM do Linux via SSH, você deve ter as seguintes portas abertas em sua VM:

* Porta de entrada: SSH (22)


## <a name="username"></a>Conectar: usando nome de usuário e senha

1.   Abra o [Portal do Azure](https://portal.azure.com). Navegue até a máquina virtual à qual você deseja se conectar e clique em **conectar**. A VM deve ser uma máquina virtual do Linux ao usar uma conexão SSH.
1. Depois de clicar em conectar, uma barra lateral é exibida com três guias – RDP, SSH e bastiões. Se a bastiões tiver sido provisionada para a rede virtual, a guia de bastiões estará ativa por padrão. Se você não provisionar a bastiões para a rede virtual, consulte [Configurar a bastiões](bastion-create-host-portal.md).

   ![Conexão de VM](./media/bastion-connect-vm-ssh/bastion.png)
1. Insira o nome de usuário e a senha para SSH em sua máquina virtual.
1. Clique no botão **conectar** depois de inserir a chave.

## <a name="privatekey"></a>Conectar: Insira manualmente uma chave privada

1. Abra o [Portal do Azure](https://portal.azure.com). Navegue até a máquina virtual à qual você deseja se conectar e clique em **conectar**. A VM deve ser uma máquina virtual do Linux ao usar uma conexão SSH.
1. Depois de clicar em conectar, uma barra lateral é exibida com três guias – RDP, SSH e bastiões. Se a bastiões tiver sido provisionada para a rede virtual, a guia de bastiões estará ativa por padrão. Se você não provisionar a bastiões para a rede virtual, consulte [Configurar a bastiões](bastion-create-host-portal.md).

   ![Conexão de VM](./media/bastion-connect-vm-ssh/bastion.png)
1. Insira o nome de usuário e selecione **chave privada SSH**.
1. Insira sua chave privada na **chave privada SSH** da área de texto (ou cole-a diretamente).
1. Clique no botão **conectar** depois de inserir a chave.

## <a name="ssh"></a>Conectar: usando um arquivo de chave privada

1. Abra o [Portal do Azure](https://portal.azure.com). Navegue até a máquina virtual à qual você deseja se conectar e clique em **conectar**. A VM deve ser uma máquina virtual do Linux ao usar uma conexão SSH.

   ![Conexão de VM](./media/bastion-connect-vm-ssh/connect.png)
1. Depois de clicar em conectar, uma barra lateral é exibida com três guias – RDP, SSH e bastiões. Se a bastiões tiver sido provisionada para a rede virtual, a guia de bastiões estará ativa por padrão. Se você não provisionar a bastiões para a rede virtual, consulte [Configurar a bastiões](bastion-create-host-portal.md).

   ![Conexão de VM](./media/bastion-connect-vm-ssh/bastion.png)
1. Insira o nome de usuário e selecione **chave privada SSH do arquivo local**.
1. Clique no botão **procurar** (o ícone de pasta no arquivo local).
1. Procure o arquivo e clique em **abrir**.
1. Clique em **conectar** para conectar-se à VM. Depois de clicar em conectar, o SSH para essa máquina virtual será aberto diretamente no portal do Azure. Essa conexão é sobre o HTML5 usando a porta 443 no serviço de bastiões por meio do IP privado de sua máquina virtual.

## <a name="next-steps"></a>Próximos passos

Leia as [perguntas frequentes de bastiões](bastion-faq.md)
