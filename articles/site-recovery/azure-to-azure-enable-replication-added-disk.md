---
title: Habilite a replicação de um disco Azure VM adicionado na recuperação do site do Azure
description: Este artigo descreve como habilitar a replicação de um disco adicionado a uma VM do Azure habilitada para recuperação de desastres com o Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2019
ms.openlocfilehash: 6cbbe63d7968816de78256f5a8408517bb8da278
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973809"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Habilitar a replicação de um disco adicionado a uma VM do Azure


Este artigo descreve como habilitar a replicação para discos de dados adicionados a uma VM do Azure que já está habilitada para recuperação de desastres em outra região do Azure, usando [o Azure Site Recovery](site-recovery-overview.md).

A ativação da replicação de um disco adicionado a uma VM é suportada para VMs Azure com discos gerenciados.

Quando você adiciona um novo disco a uma VM do Azure que está se replicando em outra região do Azure, ocorre o seguinte:

-   A saúde de replicação da VM mostra um aviso, e uma nota no portal informa que um ou mais discos estão disponíveis para proteção.
-   Se você habilitar a proteção para os discos adicionados, o aviso desaparecerá após a replicação inicial do disco.
-   Se você optar por não habilitar a replicação para o disco, você pode selecionar para descartar o aviso.

![Novo disco adicionado](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>Antes de começar

Este artigo pressupõe que você já configurou a recuperação de desastres para a VM à qual você está adicionando o disco. Se você não tiver, siga o [tutorial de recuperação de desastres do Azure para Azure](azure-to-azure-tutorial-enable-replication.md).

## <a name="enable-replication-for-an-added-disk"></a>Habilitar a replicação para um disco adicionado

Para habilitar a replicação de um disco adicionado, faça o seguinte:

1. No cofre > **Itens Replicados,** clique na VM à qual você adicionou o disco.
2. Clique **em Discos**e selecione o disco de dados para o qual deseja ativar a replicação (esses discos têm um status Não **protegido).**
3.  Em **Detalhes em disco,** clique **em Ativar a replicação**.

    ![Habilitar a replicação para adicionar disco](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

Depois que o trabalho de replicação habilitado é executado e a replicação inicial termina, o aviso de saúde de replicação para o problema do disco é removido.



## <a name="next-steps"></a>Próximas etapas

[Saiba mais](site-recovery-test-failover-to-azure.md) sobre a execução de failovers de teste.
