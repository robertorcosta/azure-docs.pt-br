---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/24/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8833639b6efacc664596ecb2aa6f9da41ad23b81
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814121"
---
- Somente [chaves de software e HSM RSA](../articles/key-vault/keys/about-keys.md) de tamanhos de 2.048 bits, 3.072 bits e 4.096 bits têm suporte, sem outras chaves ou tamanhos.
    - As chaves [HSM](../articles/key-vault/keys/hsm-protected-keys.md) exigem a camada **Premium** de cofres de chaves do Azure.
- Os discos criados a partir de imagens personalizadas criptografadas com criptografia do lado do servidor e chaves gerenciadas pelo cliente devem ser criptografados usando as mesmas chaves gerenciadas pelo cliente e estar na mesma assinatura.
- Os instantâneos criados a partir de discos criptografados com criptografia do lado do servidor e chaves gerenciadas pelo cliente devem ser criptografados com as mesmas chaves gerenciadas pelo cliente.
- Todos os recursos relacionados às chaves gerenciadas pelo cliente (Azure Key Vaults, conjuntos de criptografia de disco, VMs, discos e instantâneos) devem estar na mesma assinatura e região.
- Discos, instantâneos e imagens criptografadas com chaves gerenciadas pelo cliente não podem migrar para outra assinatura.
- Os discos gerenciados atualmente ou criptografados anteriormente usando Azure Disk Encryption não podem ser criptografados usando chaves gerenciadas pelo cliente.
- Só é possível criar até 50 conjuntos de criptografia de disco por região por assinatura.
- Para obter informações sobre o uso de chaves gerenciadas pelo cliente com galerias de imagens compartilhadas, confira [Preview: Use chaves gerenciadas pelo cliente para criptografar imagens](../articles/virtual-machines/image-version-encryption.md).