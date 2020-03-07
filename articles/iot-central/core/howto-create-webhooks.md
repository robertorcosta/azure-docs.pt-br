---
title: Criar webhooks em regras no Microsoft Azure IoT Central | Microsoft Docs
description: Crie webhooks no Microsoft Azure IoT Central para notificar automaticamente outros aplicativos ao acionar regras.
author: viv-liu
ms.author: viviali
ms.date: 12/02/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: db4e48a7bff9127810b051a9ab63bbe9d78cf6da
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394170"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Criar ações de webhook em regras no Microsoft Azure IoT Central

*Este tópico aplica-se a construtores e administradores.*

Webhooks permitem que você conecte o aplicativo IoT Central a outros aplicativos e serviços de monitoramento remoto e notificações. Webhooks notificam automaticamente os outros aplicativos e serviços conectados sempre que uma regra é acionada no aplicativo IoT Central. Seu aplicativo IoT Central envia uma solicitação POST para o ponto de extremidade HTTP do outro aplicativo sempre que uma regra é disparada. A carga contém detalhes do dispositivo e detalhes do gatilho da regra.

## <a name="set-up-the-webhook"></a>Configurar o webhook

Neste exemplo, você se conecta ao RequestBin para ser notificado quando as regras forem acionadas usando WebHooks.

1. Abra [RequestBin](https://requestbin.net/).

1. Crie um novo RequestBin e copie a **URL do Compartimento**.

1. Crie uma [regra de telemetria](tutorial-create-telemetry-rules.md). Salve a regra e adicione uma nova ação.

    ![Tela de criação do webhook](media/howto-create-webhooks/webhookcreate.png)

1. Escolha a ação do webhook, forneça um nome de exibição e cole a URL do Compartimento como URL de Retorno de Chamada.

1. Salve a regra.

Agora, quando a regra for disparada, você verá que uma nova solicitação aparecerá em RequestBin.

## <a name="payload"></a>Carga

Quando uma regra é disparada, uma solicitação HTTP POST é feita à URL de retorno de chamada que contém uma carga JSON com os detalhes de telemetria, dispositivo, regra e aplicativo. O conteúdo pode ser semelhante ao seguinte:

```json
{
    "id": "<id>",
    "displayName": "Webhook 1",
    "timestamp": "2019-10-24T18:27:13.538Z",
    "rule": {
        "id": "<id>",
        "displayName": "High temp alert",
        "enabled": true
    },
    "device": {
        "id": "mx1",
        "displayName": "MXChip IoT DevKit - mx1",
        "instanceOf": "<device-template-id>",
        "simulated": true,
        "provisioned": true,
        "approved": true
    },
    "data": [{
        "@id": "<id>",
        "@type": ["Telemetry"],
        "name": "temperature",
        "displayName": "Temperature",
        "value": 66.27310467496761,
        "interfaceInstanceName": "sensors"
    }],
    "application": {
        "id": "<id>",
        "displayName": "x - Store Analytics Checkout---PnP",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="known-limitations"></a>Limitações conhecidas

Atualmente não há nenhuma maneira programática de assinar/cancelar a assinatura desses webhooks por meio de uma API.

Se você tiver ideias sobre como melhorar esse recurso, poste suas sugestões em nosso [Fórum de voz do usuário](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Agora que você aprendeu como configurar e usar WebHooks, a próxima etapa sugerida é explorar a [configuração Azure monitor grupos de ação](howto-use-action-groups.md).
