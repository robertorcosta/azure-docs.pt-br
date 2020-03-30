---
title: Exclua os discos VMware VMDa da recuperação de desastres para o Azure com a recuperação do site do Azure
description: Como excluir discos VMware VMda replicação para o Azure com o Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.date: 12/10/2019
ms.author: mayg
ms.topic: conceptual
ms.openlocfilehash: cd54da5ee01206e576157435135065189bfb8035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495359"
---
# <a name="exclude-disks-from-vmware-vm-replication-to-azure"></a>Exclua discos da replicação VMware VMpara o Azure

Este artigo descreve como excluir discos ao replicar VMware VMs para o Azure para recuperação de desastres. Você pode querer excluir discos da replicação por uma série de razões:

- Certifique-se de que os dados não importantes no disco excluído não sejam replicados.
- Otimize a largura de banda de replicação consumida ou os recursos do lado do destino, excluindo discos que você não precisa replicar.
- Economize recursos de armazenamento e rede não replicando dados que você não precisa.

Antes de excluir discos da replicação:

- [Saiba mais](exclude-disks-replication.md) sobre a exclusão de discos.
- Revise [cenários de exclusão típicos](exclude-disks-replication.md#typical-scenarios) e [exemplos](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) que mostram como a exclusão de um disco afeta a replicação, o failover e o failback.

## <a name="before-you-start"></a>Antes de começar

 Observe o seguinte antes de começar:

- **Replicação**: Por padrão, todos os discos de uma máquina são replicados.
- **Tipo de disco:** Somente discos básicos podem ser excluídos da replicação. Você não pode excluir o sistema operacional ou discos dinâmicos.
- **Serviço de mobilidade**: Para excluir um disco da replicação, você deve instalar manualmente o serviço mobility na máquina antes de ativar a replicação. Você não pode usar a instalação push, uma vez que este método instala o serviço de mobilidade em uma VM somente após a replicação estar ativada.  
- **Adicionar/remover/excluir discos**: Depois de ativar a replicação, não é possível adicionar/remover/excluir discos para replicação. Se você quiser adicionar/remover ou excluir discos, você precisa desativar a proteção da máquina e, em seguida, ativá-la novamente.
- **Failover**: Após failover, se falhado sobre aplicativos precisa de discos excluídos para funcionar, você precisa criar esses discos manualmente. Alternativamente, você pode integrar a automação do Azure em um plano de recuperação, para criar o disco durante o failover da máquina.
- **Failback-Windows**: Quando você falha ao seu site no local após failover, os discos do Windows que você cria manualmente no Azure não são falhados de volta. Por exemplo, se você falhar em três discos e criar dois discos diretamente nas VMs do Azure, apenas os três discos que foram falhados serão retumadas.
- **Failback-Linux**: Para failback de máquinas Linux, os discos que você cria manualmente no Azure são falhados de volta. Por exemplo, se você falhar em três discos e criar dois discos diretamente nas VMs do Azure, todos os cinco serão falhados de volta. Você não pode excluir discos que foram criados manualmente no failback ou na reproteção de VMs.



## <a name="exclude-disks-from-replication"></a>Excluir discos da replicação

1. Quando você [habilitar a replicação](site-recovery-hyper-v-site-to-azure.md) para uma VMware VMM, depois de selecionar as VMs que deseja replicar, na página Ativar**propriedades** >  **de replicação** > **Configure propriedades,** revise a coluna **Discos para Replicar.** Por padrão, todos os discos são selecionados para replicação.
2. Se você não quiser replicar um disco específico, em **Discos para replicar** limpar a seleção para quaisquer discos que você deseja excluir. 

    ![Excluir discos da replicação](./media/vmware-azure-exclude-disk/enable-replication-exclude-disk1.png)



## <a name="next-steps"></a>Próximas etapas
Depois que a implantação estiver configurada e em funcionamento, [saiba mais](failover-failback-overview.md) sobre o os diferentes tipos de failover.
