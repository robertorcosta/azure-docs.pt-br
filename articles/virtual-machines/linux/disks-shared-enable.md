---
title: Habilitar discos compartilhados para o Azure Managed disks
description: Configurar um disco gerenciado do Azure com discos compartilhados (versão prévia) para que você possa compartilhá-lo em várias VMs
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0dc7cf6e3f72ff9fa7ef5d7cbd85c1fb93f76f9a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146782"
---
# <a name="enable-shared-disk"></a>Habilitar disco compartilhado

Este artigo aborda como habilitar o recurso de discos compartilhados (versão prévia) para o Azure Managed disks. Os discos compartilhados do Azure (versão prévia) são um novo recurso para discos gerenciados do Azure que permite anexar um disco gerenciado a várias VMs (máquinas virtuais) simultaneamente. A anexação de um disco gerenciado a várias VMs permite que você implante novos aplicativos em cluster existentes ou migre-os para o Azure. 

Se você estiver procurando informações conceituais sobre discos gerenciados que têm discos compartilhados habilitados, consulte [discos compartilhados do Azure](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]