---
title: Habilitar discos compartilhados para o Azure Managed disks
description: Configurar um disco gerenciado do Azure com discos compartilhados (versão prévia) para que você possa compartilhá-lo em várias VMs
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 4562567aea667dd7efadbcfb033e782ec9418e3f
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472053"
---
# <a name="enable-shared-disk"></a>Habilitar disco compartilhado

Este artigo aborda como habilitar o recurso de discos compartilhados (versão prévia) para o Azure Managed disks. Os discos compartilhados do Azure (versão prévia) são um novo recurso para discos gerenciados do Azure que permite anexar um disco gerenciado a várias VMs (máquinas virtuais) simultaneamente. A anexação de um disco gerenciado a várias VMs permite que você implante novos aplicativos em cluster existentes ou migre-os para o Azure. 

Se você estiver procurando informações conceituais sobre discos gerenciados que têm discos compartilhados habilitados, consulte [discos compartilhados do Azure](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]