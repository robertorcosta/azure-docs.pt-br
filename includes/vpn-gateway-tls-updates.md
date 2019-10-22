---
title: arquivo de inclusão
description: arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e4d20cd39d2a843ee1ab57a412ac668b3495fdb1
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "67171746"
---
>[!NOTE]
>A partir de 1º de julho de 2018, o suporte está sendo removido para TLS 1,0 e 1,1 do gateway de VPN do Azure. O gateway de VPN dará suporte apenas a TLS 1,2. Para manter o suporte, consulte as [atualizações para habilitar o suporte para TLS 1.2](#tls1).

Além disso, os seguintes algoritmos herdados também serão preteridos para o TLS em 1º de julho de 2018:

* RC4 (Rivest Cipher 4)
* DES (algoritmo de criptografia de dados)
* 3DES (algoritmo de criptografia de dados triplo)
* MD5 (Message Digest 5)

### <a name="tls1"></a>Como fazer habilitar o suporte para TLS 1,2 no Windows 7 e Windows 8.1?

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]
