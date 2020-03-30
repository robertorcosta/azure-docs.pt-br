---
title: Falha no VMware VMs/servidores físicos do Azure com recuperação do site do Azure
description: Saiba como fazer o failback para o site local após o failover para o Azure durante a recuperação de desastre de VMs e servidores físicos VMware para o Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: cd4cc90fb102d517a47ba458619e22b8921dd498
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495335"
---
# <a name="fail-back-vmware-vms-to-on-premises-site"></a>Falha nas VMVs do VMware para o site local

Este artigo descreve como refazer as VMs do Azure para um site no local, após [o failover](site-recovery-failover.md) de VMs no local para o Azure com [o Azure Site Recovery](site-recovery-overview.md). Após o failback no local, você habilita a replicação para que as VMs no local comecem a se replicar no Azure.

## <a name="before-you-start"></a>Antes de começar

1. Saiba mais sobre [o failback da VMware](failover-failback-overview.md#vmwarephysical-reprotectionfailback). 
2. Certifique-se de que revisou e completou as etapas [para se preparar para o failback](vmware-azure-prepare-failback.md)e que todos os componentes necessários estão implantados. Os componentes incluem um servidor de processo no Azure, um servidor de destino principal no local e uma conexão VPN site-to-site (ou peering privado ExpressRoute) para failback.
3. Certifique-se de que você completou os [requisitos](vmware-azure-reprotect.md#before-you-begin) para reproteção e failback, e que você [habilitou a reproteção](vmware-azure-reprotect.md#enable-reprotection) das VMs Azure, de modo que eles estão replicando do Azure para o site no local. VMs devem estar em um estado replicado é para falhar.




## <a name="run-a-failover-to-fail-back"></a>Executar um failover para falhar de volta

1. Certifique-se de que as VMs do Azure estão reprotegidas e replicadas no local. 
    - Uma VM precisa de pelo menos um ponto de recuperação para falhar.
    - Se você falhar um plano de recuperação, então todas as máquinas do plano devem ter pelo menos um ponto de recuperação.
2. No cofre > **itens replicados,** selecione a VM. Clique com o botão direito do mouse no **Failover não planejado**> VM .
3. Em **Confirmar Failover**, verifique a direção do failover (do Azure).
4. Selecione o ponto de recuperação que você deseja usar para o failover.
    - Recomendamos que você use o **último** ponto de recuperação. O ponto consistente do aplicativo está por trás do ponto mais recente no tempo, e causa alguma perda de dados.
    - **O mais recente** é um ponto de recuperação consistente com falhas.
    - Com **o Latest,** uma VM falha no seu último ponto disponível no tempo. Se você tiver um grupo de replicação para consistência de váriv-VM dentro de um plano de recuperação, cada VM do grupo falhará em seu ponto mais recente independente no tempo.
    - Se você usar um ponto de recuperação consistente com o aplicativo, cada VM falhará de volta ao seu ponto mais recente disponível. Se um plano de recuperação tiver um grupo de replicação, cada grupo se recuperará ao seu ponto de recuperação disponível comum.
5. O failover começa. A Recuperação do Site encerra as VMs do Azure.
6. Após o failover ser concluído, verifique se tudo está funcionando como esperado. Verifique se as VMs do Azure estão fechadas. 
7. Com tudo verificado, clique com o botão direito do mouse no VM > **Commit**, para concluir o processo de failover. Commit remove a VM azure com falha. 

> [!NOTE]
> Para VMs do Windows, o Site Recovery desativa as ferramentas VMware durante o failover. Durante o failback do Windows VM, as ferramentas VMware são habilitadas novamente. 




## <a name="reprotect-from-on-premises-to-azure"></a>Proteger novamente do local para o Azure

Depois de cometer o failback, as VMs do Azure são excluídas. A VM está de volta ao local, mas não está protegida. Para começar a replicar VMs para o Azure novamente, da seguinte forma:

1. No cofre > **itens replicados,** selecione VMs com falha e selecione **Re-Protect**.
2. Especifique o servidor de processo usado para enviar dados de volta ao Azure.
3. Selecione **OK** para iniciar o trabalho de proteger novamente.

> [!NOTE]
> Depois que uma VM no local é iniciada, leva até 15 minutos para o agente se registrar de volta ao servidor de configuração. Durante esse tempo, proteger novamente falhará e retornará uma mensagem de erro informando que o agente não está instalado. Se isso ocorrer, espere alguns minutos e reproteja.

## <a name="next-steps"></a>Próximas etapas

Após o término do trabalho de reproteção, a VM no local será replicada para o Azure. Conforme necessário, você pode [executar outro failover](site-recovery-failover.md) para Azure.

