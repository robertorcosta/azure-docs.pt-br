---
title: Habilitar discos compartilhados para o Azure Managed disks
description: Configurar um disco gerenciado do Azure com discos compartilhados (versão prévia) para que você possa compartilhá-lo em várias VMs
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 60070dc4c49f83866e2d789a6bc1f9fd6b253bae
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202275"
---
# <a name="enable-shared-disk"></a>Habilitar disco compartilhado

Este artigo aborda como habilitar o recurso de discos compartilhados (versão prévia) para o Azure Managed disks. Os discos compartilhados do Azure (versão prévia) são um novo recurso para discos gerenciados do Azure que permite anexar um disco gerenciado a várias VMs (máquinas virtuais) simultaneamente. A anexação de um disco gerenciado a várias VMs permite que você implante novos aplicativos em cluster existentes ou migre-os para o Azure. 

Se você estiver procurando informações conceituais sobre discos gerenciados que têm discos compartilhados habilitados, consulte [discos compartilhados do Azure](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]