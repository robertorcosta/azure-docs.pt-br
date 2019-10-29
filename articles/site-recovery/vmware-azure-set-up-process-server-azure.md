---
title: Configurar um servidor em processo no Azure para o failback de servidor físico e da VM do VMware com o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como configurar um servidor em processo no Azure para fazer failback de VMs do Azure para o VMware.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: ramamill
ms.openlocfilehash: 080edfc86848bb6c6579c177c72d3fbd3214a06a
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968849"
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>Configurar um servidor em processo no Azure para failback

Depois de fazer failover de servidores físicos ou VMs do VMware no Azure usando o [Site Recovery](site-recovery-overview.md), você pode fazer failover deles no site local quando ele estiver em funcionamento novamente. Para fazer failback, você precisa configurar um servidor em processo temporário no Azure, a fim de lidar com a replicação do Azure local. Você pode excluir essa VM após a conclusão do failback.

## <a name="before-you-start"></a>Antes de começar

Saiba mais sobre o processo de [nova proteção](vmware-azure-reprotect.md) e [failback](vmware-azure-failback.md).

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]


## <a name="deploy-a-process-server-in-azure"></a>Implantar um servidor em processo no Azure

1. No cofre > **Infraestrutura do Site Recovery**> **Gerenciar** > **Servidores de Configuração**, selecione o servidor de configuração.
2. Na página do servidor, clique em **+ Servidor em processo**
3. Na página **Adicionar servidor em processo**, selecione a opção para implantar o servidor em processo no Azure.
4. Especifique as configurações do Azure, incluindo a assinatura usada para failover, um grupo de recursos, a região do Azure usada para failover e a rede virtual na qual as VMs do Azure estão localizadas. Se você usou várias redes do Azure, você precisa ter um servidor em processo em cada uma.

   ![Adicionar item da galeria do servidor de processo](./media/vmware-azure-set-up-process-server-azure/add-ps-page-1.png)

4. Em **Nome do servidor**, **Nome de usuário** e **Senha**, especifique um nome para o servidor em processo e as credenciais que receberão permissões de Administrador no servidor.
5. Especifique uma conta de armazenamento a ser usada para os discos de VM do servidor, a sub-rede na qual a VM do servidor em processo estará localizada e o endereço IP do servidor que será atribuído quando a VM for iniciada.
6. Clique no botão **OK** para começar a implantar a VM do servidor em processo. O servidor de processo será implantado no SKU do Standard_A8_v2. Verifique se esse SKU de VM está disponível para sua assinatura.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Registrando o servidor de processo (em execução no Azure) para um servidor de configuração (em execução local)

Depois que a VM do servidor em processo estiver em funcionamento, você precisa registrá-la no servidor de configuração local, da seguinte maneira:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


