---
title: Habilitar criptografia de ponta a ponta usando criptografia em discos gerenciados por portal do Azure de host
description: Use a criptografia no host para habilitar a criptografia de ponta a ponta em seus Azure Managed disks-portal do Azure.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/23/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: e969fe34e7e7723218586f24807ad70944ba5716
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87171349"
---
# <a name="enable-end-to-end-encryption-using-encryption-at-host---azure-portal"></a>Habilitar criptografia de ponta a ponta usando criptografia no host-portal do Azure

Quando você habilita a criptografia no host, os dados armazenados no host da VM são criptografados em repouso e os fluxos são criptografados para o serviço de armazenamento. Para obter informações conceituais sobre criptografia no host, bem como outros tipos de criptografia de disco gerenciado, consulte [criptografia em criptografia de host de ponta a ponta para os dados da VM](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

[!INCLUDE [virtual-machines-disks-encryption-at-host-portal](../../../includes/virtual-machines-disks-encryption-at-host-portal.md)]

## <a name="next-steps"></a>Próximas etapas

[Exemplos de modelo do Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)