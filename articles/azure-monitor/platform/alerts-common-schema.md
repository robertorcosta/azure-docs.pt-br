---
title: Esquema de alerta comum para alertas de monitor do Azure
description: Entendendo o esquema de alerta comum, por que você deve usá-lo e como habilitá-lo
ms.topic: conceptual
ms.subservice: alerts
ms.date: 03/14/2019
ms.openlocfilehash: 1445e8cf38b2694146fc8749ba5e77f2297de969
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249042"
---
# <a name="common-alert-schema"></a>Esquema comum de alertas

Este artigo descreve qual é o esquema de alerta comum, os benefícios de usá-lo e como habilitá-lo.

## <a name="what-is-the-common-alert-schema"></a>Qual é o esquema de alerta comum?

O esquema de alerta comum padroniza a experiência de consumo para notificações de alerta no Azure hoje. Historicamente, os três tipos de alerta no Azure hoje (métrica, log e registro de atividades) tiveram seus próprios modelos de e-mail, schemas webhook, etc. Com o esquema de alerta comum, agora você pode receber notificações de alerta com um esquema consistente.

Qualquer instância de alerta descreve **o recurso afetado** e a causa do **alerta**, e essas instâncias são descritas no esquema comum nas seguintes seções:
* **Essenciais**: Um conjunto de **campos padronizados,** comuns em todos os tipos de alerta, que descrevem **em que recurso** o alerta está ligado junto com metadados de alerta comuns adicionais (por exemplo, gravidade ou descrição). 
* **Contexto de alerta**: Um conjunto de campos que descrevem a **causa do alerta,** com campos que variam **de acordo com o tipo de alerta**. Por exemplo, um alerta métrico teria campos como o nome métrico e o valor métrico no contexto de alerta, enquanto um alerta de registro de atividade teria informações sobre o evento que gerou o alerta. 

Os cenários típicos de integração que ouvimos dos clientes envolvem o roteamento da instância de alerta para a equipe em questão com base em algum pivô (por exemplo, grupo de recursos), após o qual a equipe responsável começa a trabalhar nele. Com o esquema de alerta comum, você pode ter uma lógica de roteamento padronizada entre os tipos de alerta, aproveitando os campos essenciais, deixando os campos de contexto como é para as equipes interessadas investigarem mais.

Isso significa que você pode potencialmente ter menos integrações, tornando o processo de gerenciamento e manutenção deles uma tarefa _muito_ mais simples. Além disso, futuros enriquecimentos de carga de alerta (por exemplo, personalização, enriquecimento diagnóstico, etc.) só surgirão no esquema comum.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>Que aprimoramentos o esquema de alerta comum traz?

O esquema de alerta comum se manifestará principalmente em suas notificações de alerta. Os aprimoramentos que você verá estão listados abaixo:

| Ação | Aprimoramentos|
|:---|:---|
| sms | Um modelo de SMS consistente para todos os tipos de alerta. |
| Email | Um modelo de e-mail consistente e detalhado, permitindo diagnosticar facilmente problemas rapidamente. Os links profundos incorporados à instância de alerta no portal e ao recurso afetado garantem que você possa entrar rapidamente no processo de remediação. |
| Webhook/Logic App/Azure Function/Automation Runbook | Uma estrutura JSON consistente para todos os tipos de alerta, que permite construir integrações facilmente entre os diferentes tipos de alerta. |

O novo esquema também permitirá uma experiência de consumo de alerta mais rica em todo o portal Azure e no aplicativo móvel Azure no futuro imediato. 

[Saiba mais sobre as definições de esquema para Webhooks/Logic Apps/Azure Functions/Automation Runbooks.](https://aka.ms/commonAlertSchemaDefinitions)

> [!NOTE]
> As seguintes ações não suportam o esquema de alerta comum: Conector ITSM.

## <a name="how-do-i-enable-the-common-alert-schema"></a>Como habilitar o esquema de alerta comum?

Você pode optar ou optar pelo esquema de alerta comum através de Grupos de Ação, tanto no portal quanto através da API REST. O alternador para mudar para o novo esquema existe em um nível de ação. Por exemplo, você tem que optar separadamente por uma ação de e-mail e uma ação de webhook.

> [!NOTE]
> 1. Os seguintes tipos de alerta suportam o esquema comum por padrão (sem opt em obrigatório):
>     * Alertas de detecção inteligente
> 1. Os seguintes tipos de alerta atualmente não suportam o esquema comum:
>     * Alertas gerados pelo [Monitor Do Azure para VMs](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)
>     * Alertas gerados pela [Gestão de Custos do Azure](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario)

### <a name="through-the-azure-portal"></a>Através do portal Azure

![Esquema de alerta comum opta em](media/alerts-common-schema/portal-opt-in.png)

1. Abra qualquer ação existente ou nova em um grupo de ação. 
1. Selecione 'Sim' para o alternador para ativar o esquema de alerta comum como mostrado.

### <a name="through-the-action-groups-rest-api"></a>Através da API REST grupos de ação

Você também pode usar a [API action groups](https://docs.microsoft.com/rest/api/monitor/actiongroups) para optar pelo esquema de alerta comum. Ao fazer a chamada [de criação ou atualização](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) da API REST, você pode definir o sinalizador "useCommonAlertSchema" como 'true' (para optar) ou 'falso' (para desativar) para qualquer uma das seguintes ações - e-mail/webhook/logic app/Azure Function/automation runbook.

Por exemplo, o seguinte órgão de solicitação feito para [criar ou atualizar](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) a API REST fará o seguinte:

* Habilite o esquema de alerta comum para a ação de e-mail "E-mail do Desconhecido"
* Desative o esquema de alerta comum para a ação de e-mail "E-mail de Jane Smith"
* Habilite o esquema de alerta comum para a ação webhook "Sample webhook"

```json
{
  "properties": {
    "groupShortName": "sample",
    "enabled": true,
    "emailReceivers": [
      {
        "name": "John Doe's email",
        "emailAddress": "johndoe@email.com",
        "useCommonAlertSchema": true
      },
      {
        "name": "Jane Smith's email",
        "emailAddress": "janesmith@email.com",
        "useCommonAlertSchema": false
      }
    ],
    "smsReceivers": [
      {
        "name": "John Doe's mobile",
        "countryCode": "1",
        "phoneNumber": "1234567890"
      },
      {
        "name": "Jane Smith's mobile",
        "countryCode": "1",
        "phoneNumber": "0987654321"
      }
    ],
    "webhookReceivers": [
      {
        "name": "Sample webhook",
        "serviceUri": "http://www.example.com/webhook",
        "useCommonAlertSchema": true
      }
    ]
  },
  "location": "Global",
  "tags": {}
}
```





## <a name="next-steps"></a>Próximas etapas

- [Definições comuns de esquema de alerta para Webhooks/Logic Apps/Azure Functions/Automation Runbooks.](https://aka.ms/commonAlertSchemaDefinitions)
- [Aprenda a criar um aplicativo lógico que aproveita o esquema de alerta comum para lidar com todos os seus alertas.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-integrations) 



