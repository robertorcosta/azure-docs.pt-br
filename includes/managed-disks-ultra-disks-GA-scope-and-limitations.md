---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a5e0e459800e7cb57672518597f3d04a74f53118
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008615"
---
Por enquanto, ultra discos têm limitações adicionais, como a seguir:

As únicas opções de redundância de infraestrutura disponíveis atualmente para ultra discos são zonas de disponibilidade. As VMs que usam qualquer outra opção de redundância não podem anexar um ultra Disk.

A tabela a seguir descreve as regiões em que os ultra discos estão disponíveis, bem como suas opções de disponibilidade correspondentes:

> [!NOTE]
> Algumas zonas de disponibilidade nessas regiões não oferecem ultra discos.

|Regiões  |Sem redundância de infraestrutura  |Zonas de disponibilidade  |
|---------|---------|---------|
|Oeste dos EUA     |Sim         |Não         |
|Oeste dos EUA 2    |Não         |Sim         |
|Leste dos EUA     |Não         |Sim         |
|Leste dos EUA 2     |Não         |Sim         |
|Sudeste da Ásia     |Não         |Sim         |
|Norte da Europa     |Não         |Sim         |
|Europa Ocidental     |Não         |Sim         |
|Sul do Reino Unido     |Não         |Sim         |

- Há suporte apenas na seguinte série de VMs:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Nem todo tamanho de VM está disponível em todas as regiões com suporte com ultra discos
- Estão disponíveis somente como discos de dados e dão suporte apenas ao tamanho de setor físico de 4K. Devido ao tamanho do setor nativo de 4K do ultra Disk, há alguns aplicativos que não serão compatíveis com ultra discos. Um exemplo seria Oracle Database, que requer a versão 12,2 ou posterior para dar suporte a ultra discos.  
- Só podem ser criados como discos vazios  
- Atualmente não dá suporte a instantâneos de disco, imagens de VM, conjuntos de disponibilidade, hosts dedicados do Azure ou Azure Disk Encryption
- Atualmente não dá suporte à integração com o backup do Azure ou Azure Site Recovery
- O limite máximo atual para IOPS em VMs GA é 80.000.

Os ultra discos do Azure oferecem até 16 TiB por região por assinatura por padrão, mas os ultra discos oferecem suporte à maior capacidade por solicitação. Para solicitar um aumento na capacidade, entre em contato com o suporte do Azure.