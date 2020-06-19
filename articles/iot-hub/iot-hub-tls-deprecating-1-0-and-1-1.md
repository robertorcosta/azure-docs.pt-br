---
title: Preterindo o TLS 1.0 e 1.1 no Hub IoT | Microsoft Docs
description: Diretrizes sobre a substituição do TLS 1.0 e 1.1 e das codificações com suporte no Hub IoT.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 5c717a02c2008436617d16f08625a1cecc204340
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849511"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>Substituição do TLS 1.0 e 1.1 no Hub IoT

Para fornecer a melhor criptografia de classe, o Hub IoT está migrando para o protocolo TLS 1.2 como o mecanismo de criptografia de escolha para dispositivos e serviços de IoT. 

## <a name="timeline"></a>Linha do tempo

O Hub IoT continuará a dar suporte ao TLS 1.0/1.1 até um aviso adicional. No entanto, recomendamos que todos os clientes migrem para o TLS 1.2 assim que possível.

## <a name="deprecating-tls-11-ciphers"></a>Preterindo codificações do TLS 1.1

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SH`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="deprecating-tls-10-ciphers"></a>Preterindo codificações do TLS 1.0

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="tls-12-ciphers"></a>Codificações do TLS 1.2

Confira [Codificações recomendadas do TLS 1.2 do Hub IoT](iot-hub-tls-support.md#recommended-ciphers).
 
## <a name="customer-feedback"></a>Comentários do cliente

Embora a imposição do TLS 1.2 seja uma opção de criptografia de nível mais abrangente do setor e seja habilitada como planejada, ainda gostaríamos de ouvir os clientes em relação a suas implantações específicas e dificuldades ao adotar o TLS 1.2. Para essa finalidade, envie seus comentários para [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).
