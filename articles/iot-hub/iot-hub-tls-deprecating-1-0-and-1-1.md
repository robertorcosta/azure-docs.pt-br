---
title: Preterindo o TLS 1,0 e 1,1 no Hub IoT e no DPS (serviço de provisionamento de dispositivos) | Microsoft Docs
description: Diretrizes sobre a substituição de TLS 1,0 e 1,1 e codificações com suporte no Hub IoT e no DPS.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: 8e2f8fd7f99c359949b02959cc442f2f3d3ebfff
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912147"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>Substituição de TLS 1,0 e 1,1 no Hub IoT e no serviço de provisionamento de dispositivos

Para fornecer a melhor criptografia, o Hub IoT e o DPS (serviço de provisionamento de dispositivos) estão mudando para o protocolo TLS 1,2 como o mecanismo de criptografia de escolha para dispositivos e serviços de IoT. Assim, o suporte herdado para TLS 1,0 e TLS 1,1, bem como várias codificações herdadas não recomendadas, será preterido em **1º de julho de 2020**.


## <a name="impact"></a>Impacto
Com base nas circunstâncias e configurações específicas dos clientes, a substituição de TLS 1,0 e 1,1 e de codificações herdadas não recomendadas pode ser uma alteração impactante para seus dispositivos e serviços de IoT se comunicando com o Hub IoT ou com o DPS. Em alguns casos, os dispositivos e serviços incompatíveis com essas alterações não poderão se conectar ao Hub IoT ou ao DPS após a data de fechamento mencionada.


## <a name="supported-ciphers"></a>Codificações com suporte

A linha do tempo para a disponibilidade de várias codificações usadas no handshake de TLS é a seguinte:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (atualmente com suporte)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (haverá suporte no segundo semestre de 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (haverá suporte no segundo semestre de 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (haverá suporte no segundo semestre de 2020)


## <a name="customer-feedback"></a>Comentários do cliente

Embora a imposição de TLS 1,2 seja uma opção de criptografia de nível mais abrangente do setor e seja habilitada como planejada, ainda gostaríamos de ouvir os clientes em relação a suas implantações específicas e dificuldades ao adotar o TLS 1,2. Para essa finalidade, você pode enviar seus comentários para [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).
