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
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008615"
---
Por enquanto, os discos ultra têm limitações adicionais, eles são os seguintes:

As únicas opções de redundância de infra-estrutura atualmente disponíveis para discos ultra são zonas de disponibilidade. As VMs que usam outras opções de redundância não podem anexar um disco ultra.

A tabela a seguir descreve as regiões em que os discos ultra estão disponíveis, bem como suas opções de disponibilidade correspondentes:

> [!NOTE]
> Algumas zonas de disponibilidade nessas regiões não oferecem ultra discos.

|Regiões  |Sem redundância de infra-estrutura  |Zonas de disponibilidade  |
|---------|---------|---------|
|Oeste dos EUA     |Sim         |Não         |
|Oeste dos EUA 2    |Não         |Sim         |
|Leste dos EUA     |Não         |Sim         |
|Leste dos EUA 2     |Não         |Sim         |
|Sudeste asiático     |Não         |Sim         |
|Norte da Europa     |Não         |Sim         |
|Europa Ocidental     |Não         |Sim         |
|Sul do Reino Unido     |Não         |Sim         |

- Só são suportados nas seguintes séries de VM:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Nem todo tamanho de VM está disponível em todas as regiões suportadas com ultra discos
- Estão disponíveis apenas como discos de dados e suportam apenas o tamanho do setor físico 4k. Devido ao tamanho do setor nativo 4K do Ultra Disk, existem alguns aplicativos que não serão compatíveis com ultra discos. Um exemplo seria o Oracle Database, que requer a liberação 12.2 ou posterior para suportar discos ultra.  
- Só podem ser criados como discos vazios  
- Atualmente, não suporta instantâneos de disco, imagens de VM, conjuntos de disponibilidade, hosts dedicados do Azure ou criptografia de disco Azure
- No momento, não suporta integração com o Azure Backup ou a Azure Site Recovery
- O limite máximo atual para IOPS em VMs GA é de 80.000.

Os discos ultra do Azure oferecem até 16 TiB por região por assinatura por padrão, mas os discos ultra suportam maior capacidade por solicitação. Para solicitar um aumento de capacidade, entre em contato com o Suporte Azure.