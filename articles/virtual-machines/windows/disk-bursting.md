---
title: Intermitência de disco gerenciado
description: Saiba mais sobre intermitência de disco para discos do Azure e intermitência de disco para máquinas virtuais do Azure
author: albecker1
ms.author: albecker
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 25aa9fc166e831acd2ed0389bbbe4d2dc7e04b19
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82594349"
---
# <a name="disk-bursting"></a>Intermitência de disco
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Intermitência no nível da máquina virtual
O suporte à intermitência no nível da VM está habilitado em todas as regiões na nuvem pública nos seguintes tamanhos com suporte: 
- [Lsv2-series](../lsv2-series.md)

A intermitência é habilitada por padrão para máquinas virtuais que dão suporte a ela.

## <a name="disk-level-bursting"></a>Intermitência no nível de disco
A intermitência também está disponível em nosso [SSDs Premium](disks-types.md#premium-ssd) para tamanhos de disco P20 e menor em todas as regiões. A intermitência de disco é habilitada por padrão em novas implantações dos tamanhos de disco que dão suporte a ela. Os tamanhos de disco existentes, se derem suporte à intermitência de disco, podem habilitar a intermitência por meio de qualquer um dos seguintes métodos: 
- **Reinicializar a VM** 
- **Desanexar e anexar novamente o disco**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
