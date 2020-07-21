---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/14/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: d2cf7dbcd97c8f740447607eaf443bc3ea4a6733
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86500575"
---
A habilitação de discos compartilhados só está disponível para um subconjunto de tipos de disco. No momento, apenas ultra discos e o SSDs Premium podem habilitar discos compartilhados. Cada disco gerenciado que tem discos compartilhados habilitados está sujeito às seguintes limitações, organizadas por tipo de disco:

### <a name="ultra-disks"></a>Discos Ultra

Ultra discos têm sua própria lista separada de limitações, não relacionadas a discos compartilhados. Para limitações de ultra Disk, consulte [usando os ultra discos do Azure](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md).

Ao compartilhar ultra disks, eles têm as seguintes limitações adicionais:

- Atualmente limitado a suporte a Azure Resource Manager ou SDK. 
- Somente discos básicos podem ser usados com algumas versões do cluster de failover do Windows Server, para obter detalhes, consulte [requisitos de hardware de clustering de failover e opções de armazenamento](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).

### <a name="premium-ssds"></a>SSDs Premium

- Atualmente, só tem suporte na região EUA Central ocidental.
- Atualmente limitado a suporte a Azure Resource Manager ou SDK. 
- Só pode ser habilitado em discos de dados, não em discos do sistema operacional.
- O cache de host **ReadOnly** não está disponível para o SSDs Premium com `maxShares>1` .
- A intermitência de disco não está disponível para o SSDs Premium com `maxShares>1` .
- Ao usar conjuntos de disponibilidade e conjuntos de dimensionamento de máquinas virtuais com discos compartilhados do Azure, o [alinhamento do domínio de falha de armazenamento](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set) com o domínio de falha de máquina virtual não é imposto para o disco de dados compartilhado.
- Ao usar [PPG (grupos de posicionamento de proximidade)](../articles/virtual-machines/windows/proximity-placement-groups.md), todas as máquinas virtuais que compartilham um disco devem fazer parte do mesmo PPG.
- Somente discos básicos podem ser usados com algumas versões do cluster de failover do Windows Server, para obter detalhes, consulte [requisitos de hardware de clustering de failover e opções de armazenamento](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- O backup do Azure e o suporte a Azure Site Recovery ainda não estão disponíveis.

Se você estiver interessado em tentar discos compartilhados, [Inscreva-](https://aka.ms/AzureSharedDiskGASignUp)se no Access.