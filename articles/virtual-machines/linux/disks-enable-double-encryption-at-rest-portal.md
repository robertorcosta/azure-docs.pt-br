---
title: Habilitar a criptografia dupla em discos gerenciados por portal do Azure em repouso
description: Habilite a criptografia dupla em repouso para os dados do disco gerenciado usando o portal do Azure.
author: roygara
ms.date: 07/23/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 7fefaa3b13b73db119da696738af66e84f8bdd63
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136043"
---
# <a name="azure-portal---enable-double-encryption-at-rest-on-your-managed-disks"></a>Portal do Azure-habilitar a criptografia dupla em repouso em seus discos gerenciados

O Armazenamento em Disco do Azure dá suporte à criptografia dupla em repouso para discos gerenciados. Para obter informações conceituais sobre a criptografia dupla em repouso, bem como outros tipos de criptografia de disco gerenciado, consulte a seção [criptografia dupla em repouso](disk-encryption.md#double-encryption-at-rest) do nosso artigo sobre criptografia de disco.

[!INCLUDE [virtual-machines-double-encryption-at-rest-portal](../../../includes/virtual-machines-disks-double-encryption-at-rest-portal.md)]

## <a name="next-steps"></a>Próximas etapas

- [Exemplos de modelo do Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [Habilitar chaves gerenciadas pelo cliente com criptografia do lado do servidor-exemplos](disks-enable-customer-managed-keys-cli.md#examples)