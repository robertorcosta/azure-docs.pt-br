---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/11/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 5f14a269478541eaa5852697a917afb3d771841a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196968"
---
Por enquanto, ultra discos têm limitações adicionais, como a seguir:

As únicas opções de redundância de infraestrutura disponíveis atualmente para ultra discos são zonas de disponibilidade. As VMs que usam qualquer outra opção de redundância não podem anexar um ultra Disk.

A tabela a seguir descreve as regiões em que os ultra discos estão disponíveis, bem como suas opções de disponibilidade correspondentes:

> [!NOTE]
> Se uma região na lista a seguir não tiver nenhuma zona de disponibilidade com capacidade de disco ultra, as VMs nessa região deverão ser implantadas sem nenhuma opção de redundância de infraestrutura para anexar um ultra Disk.

|Regiões  |Número de zonas de disponibilidade com suporte para ultra disks  |
|---------|---------|
|Gov. dos EUA – Virgínia     |Não         |
|Centro-Sul dos Estados Unidos     |Não         |
|Centro dos EUA     |Três zonas         |
|Oeste dos EUA     |Não         |
|Oeste dos EUA 2    |Três zonas         |
|Leste dos EUA     |Três zonas         |
|Leste dos EUA 2     |Duas zonas         |
|Sudeste da Ásia     |Três zonas         |
|Norte da Europa     |Três zonas          |
|Europa Ocidental     |Três zonas          |
|Sul do Reino Unido     |Três zonas          |
|Leste do Japão     |Duas zonas         |



- Há suporte apenas na seguinte série de VMs:
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)
    - [FSv2](../articles/virtual-machines/fsv2-series.md)
    - [LSv2](../articles/virtual-machines/lsv2-series.md)
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Nem todo tamanho de VM está disponível em todas as regiões com suporte com ultra discos
- Estão disponíveis somente como discos de dados e dão suporte apenas ao tamanho de setor físico de 4K. Devido ao tamanho do setor nativo de 4K do ultra Disk, há alguns aplicativos que não serão compatíveis com ultra discos. Um exemplo seria Oracle Database, que requer a versão 12,2 ou posterior para dar suporte a ultra discos.  
- Só podem ser criados como discos vazios  
- Atualmente não dá suporte a instantâneos de disco, imagens de VM, conjuntos de disponibilidade, hosts dedicados do Azure ou Azure Disk Encryption
- Atualmente não dá suporte à integração com o backup do Azure ou Azure Site Recovery
- O limite máximo atual para IOPS em VMs GA é 80.000.

Os ultra discos do Azure oferecem até 16 TiB por região por assinatura por padrão, mas os ultra discos oferecem suporte à maior capacidade por solicitação. Para solicitar um aumento na capacidade, entre em contato com o suporte do Azure.