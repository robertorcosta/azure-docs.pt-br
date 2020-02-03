---
title: Preterindo o TLS 1,0 e 1,1 no Hub IoT e no DPS (serviço de provisionamento de dispositivos) | Microsoft Docs
description: Diretrizes sobre a substituição de TLS 1,0 e 1,1 e codificações com suporte no Hub IoT e no DPS.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: 95733f579740f2928cda917eec0023bf00d53076
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722779"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>Substituição de TLS 1,0 e 1,1 no Hub IoT e no serviço de provisionamento de dispositivos

Para fornecer a melhor criptografia, o Hub IoT e o DPS (serviço de provisionamento de dispositivos) estão mudando para o protocolo TLS 1,2 como o mecanismo de criptografia de escolha para dispositivos e serviços de IoT. Assim, o suporte herdado para TLS 1,0 e TLS 1,1, bem como várias codificações herdadas não recomendadas, será preterido em **1º de julho de 2020**.


## <a name="impact"></a>Impacto
Com base nas circunstâncias e configurações específicas dos clientes, a substituição de TLS 1,0 e 1,1 e de codificações herdadas não recomendadas pode ser uma alteração impactante para seus dispositivos e serviços de IoT se comunicando com o Hub IoT ou com o DPS. Em alguns casos, os dispositivos e serviços incompatíveis com essas alterações não poderão se conectar ao Hub IoT ou ao DPS após a data de fechamento mencionada.


## <a name="supported-ciphers"></a>Codificações com suporte

Somente as seguintes codificações são permitidas durante o handshake de TLS:

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="customer-feedback"></a>Comentários do cliente

Embora a imposição de TLS 1,2 seja uma opção de criptografia de nível mais abrangente do setor e seja habilitada como planejada, ainda gostaríamos de ouvir os clientes em relação a suas implantações específicas e dificuldades ao adotar o TLS 1,2. Para essa finalidade, você pode enviar seus comentários para [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).