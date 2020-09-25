---
title: Intermitência de disco gerenciado
description: Saiba mais sobre a intermitência de disco para discos do Azure e intermitência de disco para máquinas virtuais do Azure no Linux.
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 2e5fc8bde2c79a355fb7963c9101c4b040f97fa7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91275142"
---
# <a name="managed-disk-bursting"></a>Intermitência de disco gerenciado
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Intermitência no nível da máquina virtual
O suporte à intermitência no nível da VM está habilitado em todas as regiões na nuvem pública nos seguintes tamanhos com suporte: 
- [Lsv2-series](../lsv2-series.md)

A intermitência de nível de VM também está disponível na EUA Central ocidental para os seguintes tamanhos com suporte:
- [Dsv3-series](../dv3-dsv3-series.md)
- [Série Esv3](../ev3-esv3-series.md)

A intermitência é habilitada por padrão para máquinas virtuais que dão suporte a ela.

## <a name="disk-level-bursting"></a>Intermitência no nível de disco
A intermitência também está disponível em nossos [SSDs permium](disks-types.md#premium-ssd) para tamanhos de disco P20 e menores em todas as regiões na nuvem pública do Azure, na nuvem do Azure Governamental e na nuvem do Azure China. A intermitência de disco é habilitada por padrão em todas as implantações novas e existentes dos tamanhos de disco que dão suporte a ela. 

[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
