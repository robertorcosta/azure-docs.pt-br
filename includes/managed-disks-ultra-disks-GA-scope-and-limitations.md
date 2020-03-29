---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/18/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b819264895e35c6ef4fe9dc5263444dcac17eaa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74935868"
---
Por enquanto, os discos ultra têm limitações adicionais, eles são os seguintes:

- São suportados nas seguintes regiões, com um número variado de zonas de disponibilidade por região:
    - Leste dos EUA 2
    - Leste dos EUA
    - Oeste dos EUA 2
    - Sudeste asiático
    - Norte da Europa
    - Europa Ocidental
    - Sul do Reino Unido 
- Só poderão ser usados com zonas de disponibilidade (conjuntos de disponibilidade e implantações de VM única fora das zonas não terão a possibilidade de anexar um disco Ultra)
- Só são suportados nas seguintes séries de VM:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Nem todo tamanho de VM está disponível em todas as regiões suportadas com ultra discos
- Estão disponíveis apenas como discos de dados e suportam apenas o tamanho do setor físico 4k. Devido ao tamanho do setor nativo 4K do Ultra Disk, existem alguns aplicativos que não serão compatíveis com ultra discos. Um exemplo seria o Oracle Database, que requer a liberação 12.2 ou posterior para suportar discos ultra.  
- Só podem ser criados como discos vazios  
- Ainda não suporta instantâneos de disco, imagens vm, conjuntos de disponibilidade e criptografia de disco Do Zure
- Ainda não suporta a integração com o Azure Backup ou a Azure Site Recovery
- O limite máximo atual para IOPS em VMs GA é de 80.000.
- Se você quiser participar de uma pré-visualização limitada de uma VM que pode realizar 160.000 IOPS com discos ultra, envie um e-mail UltraDiskFeedback@microsoft para .com