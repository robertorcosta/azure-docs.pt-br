---
title: Depreciação do TLS 1.0 e 1.1 no IoT Hub and Device Provisioning Service (DPS) | Microsoft Docs
description: Diretrizes relativas à depreciação do TLS 1.0 e 1.1 e suporte a cifras suportadas em IoT Hub e DPS.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d61ca8fe7c6f5e7cc400714d7c31a0a7e50b8a88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402798"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>Depreciação do TLS 1.0 e 1.1 no IoT Hub and Device Provisioning Service

Para fornecer a melhor criptografia da categoria, o IoT Hub e o Device Provisioning Service (DPS) estão se movendo para o TLS (Transport Layer Security, segurança de camada de transporte) 1.2 como o mecanismo de criptografia de escolha para dispositivos e serviços IoT. 

## <a name="supported-ciphers"></a>Cifras suportadas

O cronograma para disponibilidade de várias cifras usadas no aperto de mão TLS é o seguinte:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (atualmente suportado)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (será suportado no segundo semestre de 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (será suportado no segundo semestre de 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (será suportado no segundo semestre de 2020)


## <a name="customer-feedback"></a>Comentários do cliente

Embora a aplicação do TLS 1.2 seja uma escolha de criptografia do setor e seja habilitada como planejado, ainda gostaríamos de ouvir dos clientes sobre suas implantações específicas e dificuldades em adotar o TLS 1.2. Para isso, você pode enviar [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)seus comentários para .
