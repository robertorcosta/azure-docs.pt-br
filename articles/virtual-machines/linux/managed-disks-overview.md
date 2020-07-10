---
title: Visão geral do Armazenamento em Disco
description: Visão geral do Azure Managed Disks, que cuida das contas de armazenamento para você ao usar VMs.
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d3932047422571399ad0562b2f9f2d19c3e6799b
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86145290"
---
# <a name="introduction-to-azure-managed-disks"></a>Introdução aos discos gerenciados do Azure

Os Azure Managed Disks são volumes de armazenamento em nível de bloco que são gerenciados pelo Azure e usados com Máquinas Virtuais do Azure. Os discos gerenciados são como um disco físico em um servidor local, mas virtualizados. Com os discos gerenciados, basta especificar o tamanho e o tipo de disco e provisioná-lo. Depois que você provisionar o disco, o Azure cuidará do resto.

Os tipos disponíveis de discos são Discos Ultra, SSD (unidades de estado sólido) Premium, SSDs Standard e HD (unidades de disco rígido) Standard. Para obter informações sobre cada tipo de disco individual, confira [Selecionar um tipo de disco para VMs IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Selecionar um tipo de disco para VMs de IaaS](disks-types.md)