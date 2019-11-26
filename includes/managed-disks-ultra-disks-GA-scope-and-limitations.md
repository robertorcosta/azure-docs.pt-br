---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/18/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: df488c2a3033015175943f7d092beaaa41e34046
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74260641"
---
Por enquanto, ultra discos têm limitações adicionais, como a seguir:

- Têm suporte nas seguintes regiões, com um número variável de zonas de disponibilidade por região:
    - Leste dos EUA 2
    - Leste dos EUA
    - Oeste dos EUA 2
    - Sudeste da Ásia
    - Norte da Europa
    - Europa Ocidental
    - Sul do Reino Unido 
- Só poderão ser usados com zonas de disponibilidade (conjuntos de disponibilidade e implantações de VM única fora das zonas não terão a possibilidade de anexar um disco Ultra)
- Há suporte apenas na seguinte série de VMs:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Nem todo tamanho de VM está disponível em todas as regiões com suporte com ultra discos.
- Só estarão disponíveis como discos de dados e serão compatíveis somente com tamanho de setor físico de 4k  
- Só podem ser criados como discos vazios  
- Ainda não há suporte para instantâneos de disco, imagens de VM, conjuntos de disponibilidade e Azure Disk Encryption
- Ainda não há suporte para integração com o backup do Azure ou Azure Site Recovery
- O limite máximo atual para IOPS em VMs GA é 80.000.
- Se você quiser participar de uma versão prévia limitada de uma VM que pode atingir 160.000 IOPS com ultra discos, envie um email para UltraDiskFeedback@microsoft. com