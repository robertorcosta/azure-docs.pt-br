---
title: Intermitência de disco gerenciado
description: Saiba mais sobre intermitência de disco para discos do Azure e intermitência de disco para máquinas virtuais do Azure
author: albecker1
ms.author: albecker
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: baabad550f5e6b0ae39936fc182e6d9789d189d8
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650931"
---
# <a name="disk-bursting"></a>Intermitência de disco
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Intermitência no nível da máquina virtual
O suporte à intermitência no nível da VM está habilitado em todas as regiões na nuvem pública nos seguintes tamanhos com suporte: 
- [Lsv2-series](../lsv2-series.md)

A intermitência é habilitada por padrão para máquinas virtuais que dão suporte a ela.

## <a name="disk-level-bursting"></a>Intermitência no nível de disco
A intermitência também está disponível em nossos [SSDs permium](disks-types.md#premium-ssd) para tamanhos de disco P20 e menores em todas as regiões na nuvem pública do Azure, na nuvem do Azure Governamental e na nuvem do Azure China. A intermitência de disco é habilitada por padrão em novas implantações dos tamanhos de disco que dão suporte a ela. Os tamanhos de disco existentes, se derem suporte à intermitência de disco, podem habilitar a intermitência por meio de qualquer um dos seguintes métodos: 
- **Reinicializar a VM** 
- **Desanexar e anexar novamente o disco**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
