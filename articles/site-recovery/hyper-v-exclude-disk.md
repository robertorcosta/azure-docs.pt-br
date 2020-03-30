---
title: Exclua os discos Hyper-V VM da recuperação de desastres para o Azure com a recuperação do site do Azure
description: Como excluir discos Hyper-V V M da replicação para o Azure com o Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.topic: conceptual
ms.author: mayg
ms.date: 11/12/2019
ms.openlocfilehash: 50fb6da2905b2ae27547f25cce3d7a76ca7976b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498125"
---
# <a name="exclude-disks-from-replication"></a>Excluir discos da replicação

Este artigo descreve como excluir discos ao replicar Hiper-VVs ao Azure. Você pode querer excluir discos da replicação por uma série de razões:

- Certifique-se de que os dados não importantes no disco excluído não sejam replicados.
- Otimize a largura de banda de replicação consumida ou os recursos do lado do destino, excluindo discos que você não precisa replicar.
- Economize recursos de armazenamento e rede não replicando dados que você não precisa.

Antes de excluir discos da replicação:

- [Saiba mais](exclude-disks-replication.md) sobre a exclusão de discos.
- Revise [cenários de exclusão típicos](exclude-disks-replication.md#typical-scenarios) e [exemplos](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) que mostram como a exclusão de um disco afeta a replicação, o failover e o failback.

## <a name="before-you-start"></a>Antes de começar

Observe o seguinte antes de começar:

- **Replicação**: Por padrão, todos os discos de uma máquina são replicados.
- **Tipo de disco:**
    - Você pode excluir discos básicos da replicação.
    - Você não pode excluir os discos do sistema operacional.
    - É recomendável que você não exclua discos dinâmicos. A Recuperação do Site não pode identificar qual VHD é básico ou dinâmico na VM convidada.  Se você não excluir todos os discos de volume dinâmico dependentes, o disco dinâmico protegido se tornará um disco com falha em uma falha em VM e os dados desse disco não estão acessíveis.
- **Adicionar/remover/excluir discos**: Depois de ativar a replicação, não é possível adicionar/remover/excluir discos para replicação. Se você quiser adicionar/remover ou excluir um disco, você precisa desativar a proteção para a VM e, em seguida, ativá-lo novamente.
- **Failover**: Após failover, se falhado sobre aplicativos precisa excluir discos para funcionar, você precisa criar esses discos manualmente. Alternativamente, você pode integrar a automação do Azure em um plano de recuperação, para criar o disco durante o failover da máquina.
- **Failback**: Quando você falha ao seu site no local após failover, os discos que você criou manualmente no Azure não são retestados. Por exemplo, se você falhar em três discos e criar dois discos diretamente em uma VM do Azure, apenas três discos que foram falhados serão então retumam a reprodução. Você não pode incluir discos que foram criados manualmente em failback ou em replicação reversa de VMs.

## <a name="exclude-disks"></a>Excluir discos

1. Para excluir discos quando você [habilitar a replicação](site-recovery-hyper-v-site-to-azure.md) para um VV Hyper-V, depois de selecionar as VMs que deseja replicar, na página Propriedades de **replicação** > **Properties** > **Ativar propriedades configurar propriedades,** revise a coluna Discos para **Replicar.** Por padrão, todos os discos são selecionados para replicação.
2. Se você não quiser replicar um disco específico, em **Discos para replicar** limpar a seleção para quaisquer discos que você deseja excluir. 

    ![Excluir discos da replicação](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)


## <a name="next-steps"></a>Próximas etapas
Depois que a implantação estiver configurada e em funcionamento, [saiba mais](failover-failback-overview.md) sobre o os diferentes tipos de failover.
