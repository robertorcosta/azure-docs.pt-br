---
title: Envie notificações do Azure Service Health via webhooks
description: Envie notificações personalizadas sobre eventos de saúde de serviços para o seu sistema de gerenciamento de problemas existente.
ms.topic: conceptual
ms.service: service-health
ms.date: 3/27/2018
ms.openlocfilehash: 2609a267bd151354f83482ab16c4b9345aa88cc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062850"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>Use um webhook para configurar notificações de saúde para sistemas de gerenciamento de problemas

Este artigo mostra como configurar alertas do Azure Service Health para enviar dados através de webhooks para o sistema de notificação existente.

Você pode configurar alertas de Saúde do Serviço para notificá-lo por mensagem de texto ou e-mail quando um incidente de serviço do Azure o afeta.

Mas você já pode ter um sistema de notificação externa existente no local que você prefere usar. Este artigo identifica as partes mais importantes da carga de webhook. E descreve como criar alertas personalizados para notificá-lo quando ocorrerem problemas relevantes de serviço.

Se você quiser usar uma integração pré-configurada, consulte:
* [Configurar alertas com o ServiceNow](service-health-alert-webhook-servicenow.md)
* [Configurar alertas com o PagerDuty](service-health-alert-webhook-pagerduty.md)
* [Configurar alertas com o OpsGenie](service-health-alert-webhook-opsgenie.md)

**Assista a um vídeo introdutório:**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>Configure uma notificação personalizada usando a carga de webhook do Service Health
Para configurar sua própria integração personalizada de webhook, você precisa analisar a carga útil JSON enviada via notificação do Serviço de Saúde.

Veja [um exemplo](../azure-monitor/platform/activity-log-alerts-webhook.md) `ServiceHealth` de carga útil do webhook.

Você pode confirmar que é um alerta `context.eventSource == "ServiceHealth"`de saúde do serviço olhando para . As seguintes propriedades são as mais relevantes:
- **data.context.activityLog.status**
- **data.context.activityLog.level**
- **data.context.activityLog.subscriptionId**
- **data.context.activityLog.properties.title**
- **data.context.activityLog.properties.impactStartTime**
- **data.context.activityLog.properties.communication**
- **data.context.activityLog.properties.impactedServices**
- **data.context.activityLog.properties.trackingId**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>Crie um link para o painel de saúde do serviço para um incidente
Você pode criar um link direto para o painel do Service Health em um desktop ou dispositivo móvel gerando uma URL especializada. Use o *trackingId* e os três primeiros e últimos três dígitos da sua *subscriptionId* neste formato:

<i></i>https://app.azure.com/h/*&lt;rastreando&gt;Id*/*&lt;primeiros três e últimos três dígitos de SubscriptionId&gt; *

Por exemplo, se o seu *Id de assinatura* for bba14129-e895-429b-8809-278e836ecdb3 e seu *trackingId* for 0DET-URB, sua URL de Saúde de Serviço é:

https://app.azure.com/h/0DET-URB/bbadb3<i></i>

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>Use o nível para detectar a gravidade do problema
Da menor para a maior gravidade, a propriedade **de nível** na carga útil pode ser *informacional,* *aviso,* *erro*ou *crítica.*

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>Analise os serviços impactados para determinar o escopo do incidente
Os alertas de saúde do serviço podem informá-lo sobre problemas em várias regiões e serviços. Para obter detalhes completos, você `impactedServices`precisa analisar o valor de .

O conteúdo que está dentro é uma seqüência [JSON](https://json.org/) escapou que, quando não escapou, contém outro objeto JSON que pode ser analisado regularmente. Por exemplo: 

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

Fica:

```json
[
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia East"
         },
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"Alerts & Metrics"
   },
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"App Service"
   }
]
```

Este exemplo mostra problemas para:
- "Alertas & Métricas" na Austrália Leste e Austrália Sudeste.
- "Serviço de aplicativo" na Austrália Sudeste.

## <a name="test-your-webhook-integration-via-an-http-post-request"></a>Teste sua integração de webhook através de uma solicitação HTTP POST

Siga estas etapas:

1. Crie a carga de saúde do serviço que você deseja enviar. Veja um exemplo de carga útil de webhook webhook em [Webhooks para alertas de registro de atividades do Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Crie uma solicitação HTTP POST, da seguinte maneira:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   Você deve receber uma resposta "2XX - Bem Sucedida".

1. Acesse o [PagerDuty](https://www.pagerduty.com/) para confirmar se a integração foi configurada com êxito.

## <a name="next-steps"></a>Próximas etapas
- Revise o [esquema de webhook](../azure-monitor/platform/activity-log-alerts-webhook.md)de alerta de log de atividade . 
- Conheça as [notificações de saúde dos serviços](../azure-monitor/platform/service-notifications.md).
- Saiba mais sobre [grupos de ação](../azure-monitor/platform/action-groups.md).