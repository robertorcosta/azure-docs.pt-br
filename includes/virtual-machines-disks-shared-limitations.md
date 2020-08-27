---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/26/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: edb5836ace6f7a1a0cff703b9921f6e6999de578
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88944506"
---
A habilitação de discos compartilhados só está disponível para um subconjunto de tipos de disco. No momento, apenas ultra discos e o SSDs Premium podem habilitar discos compartilhados. Cada disco gerenciado que tem discos compartilhados habilitados está sujeito às seguintes limitações, organizadas por tipo de disco:

### <a name="ultra-disks"></a>Discos Ultra

Ultra discos têm sua própria lista separada de limitações, não relacionadas a discos compartilhados. Para limitações de ultra Disk, consulte [usando os ultra discos do Azure](../articles/virtual-machines/disks-enable-ultra-ssd.md).

Ao compartilhar ultra disks, eles têm as seguintes limitações adicionais:

- Atualmente limitado a suporte a Azure Resource Manager ou SDK. 
- Somente discos básicos podem ser usados com algumas versões do cluster de failover do Windows Server, para obter detalhes, consulte [requisitos de hardware de clustering de failover e opções de armazenamento](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).

Ultra discos compartilhados estão disponíveis em todas as regiões que dão suporte a ultra discos por padrão e não exigem que você se inscreva no Access para usá-los.

### <a name="premium-ssds"></a>SSDs Premium

- Atualmente, só tem suporte em [um subconjunto de regiões](#regional-availability).
- Atualmente limitado a suporte a Azure Resource Manager ou SDK. 
- Só pode ser habilitado em discos de dados, não em discos do sistema operacional.
- O cache de host **ReadOnly** não está disponível para o SSDs Premium com `maxShares>1` .
- A intermitência de disco não está disponível para o SSDs Premium com `maxShares>1` .
- Ao usar conjuntos de disponibilidade e conjuntos de dimensionamento de máquinas virtuais com discos compartilhados do Azure, o [alinhamento do domínio de falha de armazenamento](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set) com o domínio de falha de máquina virtual não é imposto para o disco de dados compartilhado.
- Ao usar [PPG (grupos de posicionamento de proximidade)](../articles/virtual-machines/windows/proximity-placement-groups.md), todas as máquinas virtuais que compartilham um disco devem fazer parte do mesmo PPG.
- Somente discos básicos podem ser usados com algumas versões do cluster de failover do Windows Server, para obter detalhes, consulte [requisitos de hardware de clustering de failover e opções de armazenamento](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- O backup do Azure e o suporte a Azure Site Recovery ainda não estão disponíveis.

#### <a name="regional-availability"></a>Disponibilidade regional

O SSDs Premium compartilhado só tem suporte nas seguintes regiões:

- Leste dos EUA
- Leste dos EUA 2
- Oeste dos EUA
- Oeste dos EUA 2
- Centro-Oeste dos EUA
- Centro-Sul dos Estados Unidos
- Centro-Norte dos EUA
- Centro dos EUA
- Europa Ocidental
- Norte da Europa
- Coreia Central
- Canadá Central
- Leste do Canadá
- Leste do Japão
- Oeste do Japão
- Gov. dos EUA – Virgínia
- Governo dos EUA do Arizona

Se você estiver interessado em experimentar o SSDs Premium compartilhado, [Inscreva-](https://aka.ms/AzureSharedDiskGASignUp)se no Access.