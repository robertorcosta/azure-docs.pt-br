---
title: Esquemas para os alertas do Centro de Segurança Azure
description: Este artigo descreve os diferentes esquemas usados pelo Azure Security Center para alertas de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2020
ms.author: memildin
ms.openlocfilehash: 19ca17f66f6818ed4c3ef532e2030cc03f0e73ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062959"
---
# <a name="security-alerts-schemas"></a>Alertas de segurança esquemas

Os usuários do nível padrão do Azure Security Center recebem alertas de segurança quando o Security Center detecta ameaças aos seus recursos.

Você pode visualizar esses alertas de segurança nas páginas de **proteção contra ameaças** do Azure Security Center ou através de ferramentas externas como:

- [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/) - SIEM nativo da Nuvem da Microsoft. O Conector Sentinel recebe alertas do Azure Security Center e os envia para o [espaço de trabalho do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) para o Azure Sentinel.
- SIEMs de terceiros - Use as ferramentas contínuas de [exportação](continuous-export.md) do Security Center para enviar dados para [o Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/). Em seguida, integre seus dados do Event Hub com um SIEM de terceiros.
- [A API REST](https://docs.microsoft.com/rest/api/securitycenter/) - Se você estiver usando a API REST para acessar alertas, consulte a [documentação da API alertos on-line](https://docs.microsoft.com/rest/api/securitycenter/alerts).

Se você estiver usando quaisquer métodos programáticos para consumir os alertas, você precisará do esquema correto para encontrar os campos que são relevantes para você. Além disso, se você estiver exportando para um Hub de Eventos ou tentando ativar a Automação do Fluxo de Trabalho com conectores HTTP genéricos, use os esquemas para analisar corretamente os objetos JSON.

>[!IMPORTANT]
> O esquema é ligeiramente diferente para cada um desses cenários, por isso certifique-se de selecionar a guia relevante abaixo.


## <a name="the-schemas"></a>Os esquemas 


### <a name="workflow-automation-and-continuous-export-to-event-hub"></a>[Automação do fluxo de trabalho e exportação contínua para o Event Hub](#tab/schema-continuousexport)

### <a name="sample-json-for-alerts-sent-to-logic-apps-event-hub-and-third-party-siems"></a>Amostra de JSON para alertas enviados para aplicativos lógicos, hub de eventos e SIEMs de terceiros

Abaixo você encontrará o esquema dos eventos de alerta passados para:

- Instâncias do Azure Logic App que foram configuradas na automação do fluxo de trabalho do Security Center
- Azure Event Hub usando o recurso de exportação contínua do Security Center

Para obter mais informações sobre o recurso de automação do fluxo de trabalho, consulte [Automate respostas a alertas e recomendações](workflow-automation.md).
Para obter mais informações sobre exportação contínua, consulte [alertas e recomendações de exportação](continuous-export.md).

[!INCLUDE [Workflow schema](../../includes/security-center-alerts-schema-workflow-automation.md)]




### <a name="azure-sentinel-and-log-analytics-workspaces"></a>[Espaços de trabalho do Azure Sentinel e Log Analytics](#tab/schema-sentinel)

O Conector Sentinel recebe alertas do Azure Security Center e os envia para o Log Analytics Workspace para o Azure Sentinel. 

Para criar um caso sentinela ou incidente usando alertas do Security Center, você precisará do esquema para os alertas mostrados abaixo. 

Para obter mais informações sobre o Azure Sentinel, consulte [a documentação](https://docs.microsoft.com/azure/sentinel/).

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]




### <a name="azure-activity-log"></a>[Log de Atividades do Azure](#tab/schema-activitylog)

As auditorias do Azure Security Center geraram alertas de segurança como eventos no Azure Activity Log.

Você pode visualizar os eventos de alertas de segurança no Registro de Atividades pesquisando o evento Ativar alerta como mostrado:

[![Pesquisando no registro de atividades para o evento Ativar alerta](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>Amostra de JSON para alertas enviados ao Azure Activity Log

```json
{
    "channels": "Operation",
    "correlationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "description": "PREVIEW - Role binding to the cluster-admin role detected. Kubernetes audit log analysis detected a new binding to the cluster-admin role which gives administrator privileges.\r\nUnnecessary administrator privileges might cause privilege escalation in the cluster.",
    "eventDataId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "eventName": {
        "value": "PREVIEW - Role binding to the cluster-admin role detected",
        "localizedValue": "PREVIEW - Role binding to the cluster-admin role detected"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2019-12-25T18:52:36.801035Z",
    "id": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/events/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/ticks/637128967568010350",
    "level": "Informational",
    "operationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Activate Alert"
    },
    "resourceGroupName": "RESOURCE_GROUP_NAME",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-12-25T19:14:03.5507487Z",
    "subscriptionId": "SUBSCRIPTION_ID",
    "properties": {
        "clusterRoleBindingName": "cluster-admin-binding",
        "subjectName": "for-binding-test",
        "subjectKind": "ServiceAccount",
        "username": "masterclient",
        "actionTaken": "Detected",
        "resourceType": "Kubernetes Service",
        "severity": "Low",
        "intent": "[\"Persistence\"]",
        "compromisedEntity": "ASC-IGNITE-DEMO",
        "remediationSteps": "[\"Review the user in the alert details. If cluster-admin is unnecessary for this user, consider granting lower privileges to the user.\"]",
        "attackedResourceType": "Kubernetes Service"
    },
    "relatedEvents": []
}
```

### <a name="the-data-model-of-the-schema"></a>O modelo de dados do esquema

|Campo|Descrição|
|----|----|
|**Canais**|Constante, "Operação"|
|**Correlationid**|O Azure Security Center alerta ID|
|**Descrição**|Descrição do alerta|
|**eventDataId**|Ver correlaçãoId|
|**Eventname**|Os subcampos valor e valor localizadodocontêm o nome do display de alerta|
|**Categoria**|O valor e os subcampos de valor localizados são constantes - "Segurança"|
|**eventTimestamp**|Carimbo de data e hora UTC para quando o alerta foi gerado|
|**id**|O ID de alerta totalmente qualificado|
|**Nível**|Constante, "Informativo"|
|**Operationid**|Ver correlaçãoId|
|**Operationname**|O campo de valor é constante - "Microsoft.Security/locations/alerts/activate/action", e o valor localizado será "Ativar alerta" (pode ser localizado potencialmente par da localidade do usuário)|
|**resourceGroupName**|Incluirá o nome do grupo de recursos|
|**resourceProviderName**|O valor e os subcampos de valor localizados são constantes - "Microsoft.Security"|
|**Resourcetype**|O valor e os subcampos localizadosValue são constantes - "Microsoft.Security/locations/alerts"|
|**Resourceid**|O ID de recurso do Azure totalmente qualificado|
|**status**|O valor e os subcampos de valor localizados são constantes - "Ativo"|
|**Substatus**|O valor e os subcampos localizadosValor estão vazios|
|**submissionTimestamp**|O carimbo de data e hora utc de envio de eventos ao Registro de Atividades|
|**Subscriptionid**|O ID de assinatura do recurso comprometido|
|**Propriedades**|Um saco JSON de propriedades adicionais relativas ao alerta. Estes podem mudar de um alerta para o outro, no entanto, os seguintes campos aparecerão em todos os alertas:<br>- gravidade: A gravidade do ataque<br>- comprometidaEntity: O nome do recurso comprometido<br>- medidas de remediação: Conjunto de medidas de correção a serem tomadas<br>- intenção: a intenção da cadeia de morte do alerta. Possíveis intenções estão documentadas na [tabela Intenções](alerts-reference.md#intentions)|
|**relatedEvents**|Matriz constante - vazia|
|||





### <a name="ms-graph-api"></a>[API do gráfico de MS](#tab/schema-graphapi)

O Microsoft Graph é a porta de entrada para dados e inteligência no Microsoft 365. Ele fornece um modelo de programação unificado que você pode usar para acessar a enorme quantidade de dados no Office 365, Windows 10 e Enterprise Mobility + Security. Use a riqueza de dados no Microsoft Graph para criar aplicativos para organizações e consumidores que interagem com milhões de usuários.

O esquema e uma representação JSON para alertas de segurança enviados ao MS Graph estão disponíveis [na documentação do Microsoft Graph](https://docs.microsoft.com/graph/api/resources/alert?view=graph-rest-1.0).

---


## <a name="next-steps"></a>Próximas etapas

Este artigo descreveu os esquemas que as ferramentas de proteção contra ameaças do Azure Security Center usam ao enviar informações de alerta de segurança.

Para obter mais informações sobre as formas de acessar alertas de segurança de fora do Security Center, consulte as seguintes páginas:

- [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/) - SIEM nativo da nuvem da Microsoft
- [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/) - O serviço de ingestão de dados totalmente gerenciado e em tempo real da Microsoft
- Recurso de [exportação contínua](continuous-export.md) do Security Center
- [Log Analytics espaços de trabalho](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) - O Azure Monitor armazena dados de registro em um espaço de trabalho do Log Analytics, um contêiner que inclui informações de dados e configuração
