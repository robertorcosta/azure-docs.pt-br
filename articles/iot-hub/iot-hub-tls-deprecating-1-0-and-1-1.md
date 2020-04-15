---
title: Depreciando o TLS 1.0 e o 1.1 no IoT Hub | Microsoft Docs
description: Diretrizes relativas à depreciação do TLS 1.0 e 1.1 e às cifras suportadas no IoT Hub.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: a887dd4df44ba58b0e6646ffb1c10eb21edf3e69
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381305"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>Depreciação do TLS 1.0 e 1.1 no IoT Hub

Para fornecer a melhor criptografia da categoria, o IoT Hub está se movendo para o TLS (Transport Layer Security, segurança de camada de transporte) 1.2 como o mecanismo de criptografia de escolha para dispositivos e serviços IoT. 

## <a name="timeline"></a>Linha do tempo

O IoT Hub continuará a suportar o TLS 1.0/1.1 até novo aviso. No entanto, recomendamos que todos os clientes migrem para o TLS 1.2 o mais rápido possível.

## <a name="supported-ciphers"></a>Cifras suportadas

O cronograma para disponibilidade de várias cifras usadas no aperto de mão TLS é o seguinte:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (atualmente suportado)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (será suportado no segundo semestre de 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (será suportado no segundo semestre de 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (será suportado no segundo semestre de 2020)

## <a name="customer-feedback"></a>Comentários do cliente

Embora a aplicação do TLS 1.2 seja uma escolha de criptografia do setor e seja habilitada como planejado, ainda gostaríamos de ouvir dos clientes sobre suas implantações específicas e dificuldades em adotar o TLS 1.2. Para isso, você pode enviar [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)seus comentários para .
