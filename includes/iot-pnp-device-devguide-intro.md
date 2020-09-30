---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 09/24/2020
ms.openlocfilehash: 706e936590f5f47ee2989bf77b3caad67a8f28fc
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91579520"
---
O Plug and Play IoT permite criar dispositivos inteligentes que anunciam seus recursos para os aplicativos IoT do Azure. Os dispositivos IoT Plug and Play não exigem configuração manual quando um cliente os conecta aos aplicativos habilitados para IoT Plug and Play.

Um dispositivo inteligente pode ser implementado diretamente, usar [módulos](../articles/iot-hub/iot-hub-devguide-module-twins.md)ou usar [módulos IOT Edge](../articles/iot-edge/about-iot-edge.md).

Este guia descreve as etapas básicas necessárias para criar um dispositivo, módulo ou IoT Edge módulo que segue as [convenções de plug and Play de IOT](../articles/iot-pnp/concepts-convention.md).

Para criar um dispositivo Plug and Play de IoT, módulo ou módulo de IoT Edge, siga estas etapas:

1. Verifique se o dispositivo está usando o protocolo MQTT ou MQTT sobre Websockets para se conectar ao Hub IoT do Azure.
1. Crie um modelo de [DTDL (digital gêmeos Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) para descrever seu dispositivo. Para saber mais, confira [entender os componentes em modelos de plug and Play IOT](../articles/iot-pnp/concepts-components.md).
1. Atualize seu dispositivo ou módulo para anunciar o `model-id` como parte da conexão do dispositivo.
1. Implementar telemetria, propriedades e comandos usando as [convenções de plug and Play IOT](../articles/iot-pnp/concepts-convention.md)

Depois que a implementação do dispositivo ou do módulo estiver pronta, use o [Azure IOT Explorer](../articles/iot-pnp/howto-use-iot-explorer.md) para validar que o dispositivo segue as convenções de plug and Play de IOT.
