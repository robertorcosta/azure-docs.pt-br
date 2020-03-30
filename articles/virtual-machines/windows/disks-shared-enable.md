---
title: Habilite discos compartilhados para discos gerenciados do Azure
description: Configure um disco gerenciado do Azure com discos compartilhados (visualização) para que você possa compartilhá-lo em várias VMs
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e7ada64a50d6ace6ea4d34db87e0501d0311071d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471663"
---
# <a name="enable-shared-disk"></a>Habilitar disco compartilhado

Este artigo abrange como ativar o recurso de discos compartilhados (visualização) para discos gerenciados do Azure. A visualização de discos compartilhados do Azure é um novo recurso para discos gerenciados do Azure que permite anexar um disco gerenciado a várias máquinas virtuais (VMs) simultaneamente. Anexar um disco gerenciado a várias VMs permite que você implante aplicativos novos ou migrados em cluster existentes para o Azure. 

Se você está procurando informações conceituais sobre discos gerenciados que tenham discos compartilhados ativados, consulte [discos compartilhados do Azure](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]