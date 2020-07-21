---
title: Intermitência de disco gerenciado
description: Saiba mais sobre intermitência de disco para discos do Azure e intermitência de disco para máquinas virtuais do Azure
author: albecker1
ms.author: albecker
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 4408b7d442ce3f4fbb1ea61031295375ae036493
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86494540"
---
# <a name="managed-disk-bursting"></a>Intermitência de disco gerenciado
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Intermitência no nível da máquina virtual
O suporte à intermitência no nível da VM está habilitado em todas as regiões na nuvem pública nos seguintes tamanhos com suporte: 
- [Lsv2-series](../lsv2-series.md)

A intermitência é habilitada por padrão para máquinas virtuais que dão suporte a ela.

## <a name="disk-level-bursting"></a>Intermitência no nível de disco
A intermitência também está disponível em nossos [SSDs permium](disks-types.md#premium-ssd) para tamanhos de disco P20 e menores em todas as regiões na nuvem pública do Azure, na nuvem do Azure Governamental e na nuvem do Azure China. A intermitência de disco é habilitada por padrão em todas as implantações novas e existentes dos tamanhos de disco que dão suporte a ela. 

[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
