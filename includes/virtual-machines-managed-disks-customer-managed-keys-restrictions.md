---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c303527c7411ead585e70fc2e31db4dd2d35e996
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259805"
---
- Somente [chaves de software e HSM RSA](../articles/key-vault/keys/about-keys.md) de tamanhos de 2.048 bits, 3.072 bits e 4.096 bits têm suporte, sem outras chaves ou tamanhos.
    - As chaves [HSM](../articles/key-vault/keys/hsm-protected-keys.md) exigem a camada **Premium** de cofres de chaves do Azure.
- Os discos criados a partir de imagens personalizadas criptografadas com criptografia do lado do servidor e chaves gerenciadas pelo cliente devem ser criptografados usando as mesmas chaves gerenciadas pelo cliente e estar na mesma assinatura.
- Os instantâneos criados a partir de discos criptografados com criptografia do lado do servidor e chaves gerenciadas pelo cliente devem ser criptografados com as mesmas chaves gerenciadas pelo cliente.
- Todos os recursos relacionados às chaves gerenciadas pelo cliente (Azure Key Vaults, conjuntos de criptografia de disco, VMs, discos e instantâneos) devem estar na mesma assinatura e região.
- Discos, instantâneos e imagens criptografadas com chaves gerenciadas pelo cliente não podem migrar para outra assinatura.
- Discos gerenciados criptografados com chaves gerenciadas pelo cliente também não podem ser criptografados com Azure o Disk Encryption.
- Só é possível criar até 50 conjuntos de criptografia de disco por região por assinatura.
- Para obter informações sobre o uso de chaves gerenciadas pelo cliente com galerias de imagens compartilhadas, confira [Preview: Use chaves gerenciadas pelo cliente para criptografar imagens](../articles/virtual-machines/image-version-encryption.md).
