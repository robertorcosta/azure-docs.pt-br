---
title: Preterindo TLS 1,0 e 1,1 no Hub IoT | Microsoft Docs
description: Diretrizes sobre a substituição de TLS 1,0 e 1,1 e codificações com suporte no Hub IoT.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: a887dd4df44ba58b0e6646ffb1c10eb21edf3e69
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81381305"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>Substituição de TLS 1,0 e 1,1 no Hub IoT

Para fornecer a melhor criptografia de classe, o Hub IoT está migrando para o protocolo TLS 1,2 como o mecanismo de criptografia de escolha para dispositivos e serviços de IoT. 

## <a name="timeline"></a>Linha do tempo

O Hub IoT continuará a dar suporte a TLS 1.0/1.1 até um aviso adicional. No entanto, recomendamos que todos os clientes migrem para o TLS 1,2 assim que possível.

## <a name="supported-ciphers"></a>Codificações com suporte

A linha do tempo para a disponibilidade de várias codificações usadas no handshake de TLS é a seguinte:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (atualmente com suporte)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (haverá suporte no segundo semestre de 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (haverá suporte no segundo semestre de 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (haverá suporte no segundo semestre de 2020)

## <a name="customer-feedback"></a>Comentários de clientes

Embora a imposição de TLS 1,2 seja uma opção de criptografia de nível mais abrangente do setor e seja habilitada como planejada, ainda gostaríamos de ouvir os clientes em relação a suas implantações específicas e dificuldades ao adotar o TLS 1,2. Para essa finalidade, você pode enviar seus comentários para [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).
