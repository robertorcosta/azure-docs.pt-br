---
title: Habilitar discos compartilhados para o Azure Managed disks
description: Configurar um disco gerenciado do Azure com discos compartilhados para que você possa compartilhá-lo em várias VMs
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/30/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: cc365f6eef4da61ab7de5113b8f4b2b185360740
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460041"
---
# <a name="enable-shared-disk"></a>Habilitar disco compartilhado

Este artigo aborda como habilitar o recurso de discos compartilhados para o Azure Managed disks. Os discos compartilhados do Azure são um novo recurso para discos gerenciados do Azure que permite anexar um disco gerenciado a várias VMs (máquinas virtuais) simultaneamente. Anexar um disco gerenciado a várias VMs permite implantar novos aplicativos clusterizados ou migrar os existentes para o Azure. 

Se você estiver procurando informações conceituais sobre discos gerenciados que têm discos compartilhados habilitados, consulte [discos compartilhados do Azure](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]