---
title: Preterindo o TLS 1,0 e 1,1 no Hub IoT e no DPS (serviço de provisionamento de dispositivos) | Microsoft Docs
description: Diretrizes sobre a substituição de TLS 1,0 e 1,1 e codificações com suporte no Hub IoT e no DPS.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d61ca8fe7c6f5e7cc400714d7c31a0a7e50b8a88
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402798"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>Substituição de TLS 1,0 e 1,1 no Hub IoT e no serviço de provisionamento de dispositivos

Para fornecer a melhor criptografia, o Hub IoT e o DPS (serviço de provisionamento de dispositivos) estão mudando para o protocolo TLS 1,2 como o mecanismo de criptografia de escolha para dispositivos e serviços de IoT. 

## <a name="supported-ciphers"></a>Codificações com suporte

A linha do tempo para a disponibilidade de várias codificações usadas no handshake de TLS é a seguinte:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (atualmente com suporte)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (haverá suporte no segundo semestre de 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (haverá suporte no segundo semestre de 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (haverá suporte no segundo semestre de 2020)


## <a name="customer-feedback"></a>Comentários do cliente

Embora a imposição de TLS 1,2 seja uma opção de criptografia de nível mais abrangente do setor e seja habilitada como planejada, ainda gostaríamos de ouvir os clientes em relação a suas implantações específicas e dificuldades ao adotar o TLS 1,2. Para essa finalidade, você pode enviar seus comentários para [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).
