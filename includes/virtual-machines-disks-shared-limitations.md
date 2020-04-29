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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008314"
---
Durante a visualização, a habilitação de discos compartilhados só está disponível para um subconjunto de tipos de disco. No momento, apenas ultra discos e o SSDs Premium podem habilitar discos compartilhados. Cada disco gerenciado que tem discos compartilhados habilitados está sujeito às seguintes limitações, organizadas por tipo de disco:

### <a name="ultra-disks"></a>Discos Ultra

Ultra discos têm sua própria lista separada de limitações, não relacionadas a discos compartilhados. Para limitações de ultra Disk, consulte [usando os ultra discos do Azure](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md).

Ao compartilhar ultra disks, eles têm as seguintes limitações adicionais:

- Atualmente, só tem suporte no oeste dos EUA.
- Atualmente limitado a suporte a Azure Resource Manager ou SDK.
- Somente discos básicos podem ser usados com algumas versões do cluster de failover do Windows Server, para obter detalhes, consulte [requisitos de hardware de clustering de failover e opções de armazenamento](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).

### <a name="premium-ssds"></a>Premium SSDs

- Atualmente, só tem suporte na região EUA Central ocidental.
- Todas as máquinas virtuais que compartilham um disco devem ser implantadas nos mesmos [grupos de posicionamento de proximidade](../articles/virtual-machines/windows/proximity-placement-groups.md).
- Só pode ser habilitado em discos de dados, não em discos do sistema operacional.
- Somente discos básicos podem ser usados com algumas versões do cluster de failover do Windows Server, para obter detalhes, consulte [requisitos de hardware de clustering de failover e opções de armazenamento](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- O cache de host ReadOnly não está disponível para o `maxShares>1`SSDs Premium com.
- Conjuntos de disponibilidade e conjuntos de dimensionamento de máquinas virtuais só `FaultDomainCount` podem ser usados com definido como 1.
- O backup do Azure e o suporte a Azure Site Recovery ainda não estão disponíveis.

Se você estiver interessado em tentar discos compartilhados, [Inscreva-se para nossa versão prévia](https://aka.ms/AzureSharedDiskPreviewSignUp).
