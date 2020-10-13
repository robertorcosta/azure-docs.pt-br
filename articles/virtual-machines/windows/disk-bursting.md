---
title: Intermitência de disco gerenciado
description: Saiba mais sobre intermitência de disco para discos do Azure e intermitência de disco para máquinas virtuais do Azure
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 547834542b605b226ebffd68e05296ee847dc6de
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974321"
---
# <a name="disk-bursting"></a>Intermitência de disco
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Intermitência no nível da máquina virtual
O suporte à intermitência no nível da VM está habilitado em todas as regiões na nuvem pública nos seguintes tamanhos com suporte: 
- [Lsv2-series](../lsv2-series.md)

A intermitência de nível de VM também está disponível na EUA Central ocidental para os seguintes tamanhos com suporte:
- [Dsv3-series](../dv3-dsv3-series.md)
- [Série Esv3](../ev3-esv3-series.md)

A intermitência é habilitada por padrão para máquinas virtuais que dão suporte a ela.

## <a name="disk-level-bursting"></a>Intermitência no nível de disco
A intermitência também está disponível em nosso [SSDs Premium](../disks-types.md#premium-ssd) para tamanhos de disco P20 e menor em todas as regiões. A intermitência de disco é habilitada por padrão em novas implantações dos tamanhos de disco que dão suporte a ela. Os tamanhos de disco existentes, se derem suporte à intermitência de disco, podem habilitar a intermitência por meio de qualquer um dos seguintes métodos: 
- **Reinicializar a VM** 
- **Desanexar e anexar novamente o disco**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]