---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/09/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 1e53dda2c6cb293a9204f344d152daa1937aa38b
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008314"
---
Durante a visualização, a ativação de discos compartilhados só está disponível para um subconjunto de tipos de disco. Atualmente, apenas discos ultra e SSDs premium podem habilitar discos compartilhados. Cada disco gerenciado que tenha discos compartilhados ativados está sujeito às seguintes limitações, organizadas por tipo de disco:

### <a name="ultra-disks"></a>Discos Ultra

Os discos ultra têm sua própria lista separada de limitações, não relacionadas a discos compartilhados. Para obter limitações de ultra disco, consulte [Usando discos ultra azure](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md).

Ao compartilhar discos ultra, eles têm as seguintes limitações adicionais:

- Atualmente, só suportado no Oeste dos EUA.
- Atualmente limitado ao suporte ao Azure Resource Manager ou SDK.
- Apenas discos básicos podem ser usados com algumas versões do Cluster Failover do Windows Server, para obter detalhes ver [requisitos de hardware de clusterdo Failover e opções de armazenamento](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).

### <a name="premium-ssds"></a>Premium SSDs

- Atualmente, só é apoiado na região centro-oeste dos EUA.
- Todas as máquinas virtuais que compartilham um disco devem ser implantadas nos [mesmos grupos de colocação de proximidade](../articles/virtual-machines/windows/proximity-placement-groups.md).
- Só pode ser ativado em discos de dados, não em discos do sistema operacional.
- Apenas discos básicos podem ser usados com algumas versões do Cluster Failover do Windows Server, para obter detalhes ver [requisitos de hardware de clusterdo Failover e opções de armazenamento](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- O cache host ReadOnly não está disponível `maxShares>1`para SSDs premium com .
- Conjuntos de disponibilidade e conjuntos `FaultDomainCount` de escala de máquinas virtuais só podem ser usados com conjunto para 1.
- O suporte ao Azure Backup e ao Azure Site Recovery ainda não está disponível.

Se você estiver interessado em tentar discos compartilhados, [inscreva-se para a nossa pré-visualização](https://aka.ms/AzureSharedDiskPreviewSignUp).
