---
title: Entenda o Azure Security Center para gêmeos do módulo de segurança ioT| Microsoft Docs
description: Conheça o conceito de gêmeos do módulo de segurança e como eles são usados no Azure Security Center para IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: ab3b6e740e644a1ed1495eb776045888be448047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68596504"
---
# <a name="security-module"></a>Módulo de segurança


Este artigo explica como o Azure Security Center for IoT usa gêmeos e módulos de dispositivos. 

## <a name="device-twins"></a>Dispositivos gêmeos

Para soluções de IoT criadas no Azure, dispositivos gêmeos desempenham um papel fundamental tanto no gerenciamento do dispositivo quanto na automação do processo.  

A Central de Segurança do Azure para IoT oferece integração completa com sua plataforma de gerenciamento de dispositivo IoT existente, permitindo que você gerencie seu status de segurança de dispositivo e faça uso de funcionalidades de controle de dispositivo existente. A integração é alcançada fazendo uso do mecanismo gêmeo IoT Hub.  

Saiba mais sobre o conceito de [gêmeos de dispositivos](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) no Azure IoT Hub. 

## <a name="security-module-twins"></a>Gêmeos do módulo de segurança

O Azure Security Center for IoT mantém um módulo de segurança duplo para cada dispositivo no serviço.
O módulo de segurança twin mantém todas as informações relevantes para a segurança do dispositivo para cada dispositivo específico em sua solução.
As propriedades de segurança do dispositivo são mantidas em um módulo de segurança dedicado para uma comunicação mais segura e para permitir atualizações e manutenção que exijam menos recursos.  

Consulte [Criar o módulo de segurança twin](quickstart-create-security-twin.md) e configurar agentes de [segurança](how-to-agent-configuration.md) para aprender como criar, personalizar e configurar o gêmeo. Consulte [Understanding module twins](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) para saber mais sobre o conceito de módulo stwins no IoT Hub. 
 

## <a name="see-also"></a>Confira também
- [Azure Security Center para visão geral de IoT](overview.md)
- [Implantar agentes de segurança](how-to-deploy-agent.md)
- [Métodos de autenticação de agentes de segurança](concept-security-agent-authentication-methods.md)