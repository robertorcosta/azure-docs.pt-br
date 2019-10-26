---
title: Criar webhooks em regras no Microsoft Azure IoT Central | Microsoft Docs
description: Crie webhooks no Microsoft Azure IoT Central para notificar automaticamente outros aplicativos ao acionar regras.
author: viv-liu
ms.author: viviali
ms.date: 06/16/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 5c2bef7f3eb8d6f8d6d78755d839a33556259b65
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72953662"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Criar ações de webhook em regras no Microsoft Azure IoT Central

*Este tópico aplica-se a construtores e administradores.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Webhooks permitem que você conecte o aplicativo IoT Central a outros aplicativos e serviços de monitoramento remoto e notificações. Webhooks notificam automaticamente os outros aplicativos e serviços conectados sempre que uma regra é acionada no aplicativo IoT Central. Seu aplicativo IoT Central envia uma solicitação POST para o ponto de extremidade HTTP do outro aplicativo sempre que uma regra é disparada. A carga contém detalhes do dispositivo e detalhes do gatilho da regra.

## <a name="set-up-the-webhook"></a>Configurar o webhook

Neste exemplo, você se conecta ao RequestBin para ser notificado quando as regras forem acionadas usando WebHooks.

1. Abra [RequestBin](https://requestbin.net/).

1. Crie um novo RequestBin e copie a **URL do Compartimento**.

1. Crie uma [regra de telemetria](howto-create-telemetry-rules.md) ou uma [regra de evento](howto-create-event-rules.md). Salve a regra e adicione uma nova ação.

    ![Tela de criação do webhook](media/howto-create-webhooks/webhookcreate.png)

1. Escolha a ação do webhook, forneça um nome de exibição e cole a URL do Compartimento como URL de Retorno de Chamada.

1. Salve a regra.

Agora, quando a regra for disparada, você verá que uma nova solicitação aparecerá em RequestBin.

## <a name="payload"></a>Carga útil

Quando uma regra é acionada, uma solicitação HTTP POST é feita para a URL de retorno de chamada que contém uma carga json com as medidas, dispositivo, regra e detalhes do aplicativo. Para uma regra de telemetria, o conteúdo é semelhante ao seguinte:

```json
{
    "id": "ID",
    "timestamp": "date-time",
    "device" : {
        "id":"ID",
        "name":  "Refrigerator1",
        "simulated" : true,
        "deviceId": "deviceID",
        "deviceTemplate":{
            "id": "ID",
            "version":"1.0.0"
        },
        "properties":{
            "device":{
                "firmwareversion":"1.0"
            },
            "cloud":{
                "location":"One Microsoft Way"
            }
        },
        "measurements":{
            "telemetry":{
                "temperature":20,
                "pressure":10
            }
        }

    },
    "rule": {
        "id": "ID",
        "name": "High temperature alert",
        "enabled": true,
        "deviceTemplate": {
            "id":"GUID",
            "version":"1.0.0"
        }
    },
    "application": {
        "id": "ID",
        "name": "Contoso app",
        "subdomain":"contoso-app"
    }
}
```

## <a name="known-limitations"></a>Limitações conhecidas

Atualmente não há nenhuma maneira programática de assinar/cancelar a assinatura desses webhooks por meio de uma API.

Se você tiver algumas ideias para melhorar esse recurso, poste suas sugestões em nosso [fórum Uservoice](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Próximos passos

Agora que você aprendeu como configurar e usar WebHooks, a próxima etapa sugerida é explorar a [criação de fluxos de trabalho em Microsoft Flow](howto-add-microsoft-flow.md).
