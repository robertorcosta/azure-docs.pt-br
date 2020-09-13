---
title: Gerenciar recursos que são criados durante o processo de movimentação da VM no Azure Resource mover
description: Saiba como gerenciar recursos que são criados durante o processo de movimentação da VM no Azure Resource mover
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 4ff1905eb164d004af69ce5b0df3278bf3a46884
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89670259"
---
# <a name="manage-resources-created-for-the-vm-move"></a>Gerenciar recursos criados para a movimentação da VM

Este artigo descreve como gerenciar recursos que são criados explicitamente pelo [Azure Resource mover](overview.md) para facilitar o processo de movimentação da VM. 

Depois de mover as VMs entre regiões, há vários recursos criados pelo movimentador de recursos que devem ser limpos manualmente.

## <a name="delete-resources-created-for-vm-move"></a>Excluir recursos criados para movimentação de VM

Exclua manualmente a coleção de movimentação e Site Recovery recursos criados para a movimentação da VM.

1. Na região de origem da qual você moveu VMs, examine os recursos no grupo de recursos ```RegionMoveRG-<sourceregion>-<target-region>``` .
2. Verifique se a VM e todos os outros recursos de origem na coleção de movimentação foram movidos/excluídos. Isso garante que não haja recursos pendentes usando-os.
2. Exclua esses recursos.

    - O nome da coleção de movimentação é ```movecollection-<sourceregion>-<target-region>``` .
    - O nome da conta de armazenamento em cache é ```resmovecache<guid>```
    - O nome do cofre é ```ResourceMove-<sourceregion>-<target-region>-GUID``` .

## <a name="next-steps"></a>Próximas etapas

Tente [mover uma VM](tutorial-move-region-virtual-machines.md) para outra região com o Resource mover.