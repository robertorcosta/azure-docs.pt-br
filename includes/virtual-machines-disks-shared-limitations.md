---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/03/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ce964ac197fbff64bbb7cc36e8c2bf762f93663f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84337336"
---
Durante a visualização, a habilitação de discos compartilhados só está disponível para um subconjunto de tipos de disco. No momento, apenas ultra discos e o SSDs Premium podem habilitar discos compartilhados. Cada disco gerenciado que tem discos compartilhados habilitados está sujeito às seguintes limitações, organizadas por tipo de disco:

### <a name="ultra-disks"></a>Discos Ultra

Ultra discos têm sua própria lista separada de limitações, não relacionadas a discos compartilhados. Para limitações de ultra Disk, consulte [usando os ultra discos do Azure](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md).

Ao compartilhar ultra disks, eles têm as seguintes limitações adicionais:

- Atualmente limitado a suporte a Azure Resource Manager ou SDK.
- Somente discos básicos podem ser usados com algumas versões do cluster de failover do Windows Server, para obter detalhes, consulte [requisitos de hardware de clustering de failover e opções de armazenamento](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).

### <a name="premium-ssds"></a>Premium SSDs

- Atualmente, só tem suporte na região EUA Central ocidental.
- Todas as máquinas virtuais que compartilham um disco devem ser implantadas nos mesmos [grupos de posicionamento de proximidade](../articles/virtual-machines/windows/proximity-placement-groups.md).
- Só pode ser habilitado em discos de dados, não em discos do sistema operacional.
- Somente discos básicos podem ser usados com algumas versões do cluster de failover do Windows Server, para obter detalhes, consulte [requisitos de hardware de clustering de failover e opções de armazenamento](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- O cache de host ReadOnly não está disponível para o SSDs Premium com `maxShares>1` .
- Conjuntos de disponibilidade e conjuntos de dimensionamento de máquinas virtuais só podem ser usados com `FaultDomainCount` definido como 1.
- O backup do Azure e o suporte a Azure Site Recovery ainda não estão disponíveis.

Se você estiver interessado em tentar discos compartilhados, [Inscreva-se para nossa versão prévia](https://aka.ms/AzureSharedDiskPreviewSignUp).
