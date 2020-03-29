---
title: Esquemas de rastreamento personalizados para mensagens B2B
description: Crie esquemas de rastreamento personalizados para monitorar mensagens B2B em aplicativos de lógica do Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: c82f9cbfaf2e23ddaa5e4b05f4aac4795d3e16a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76903056"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-a"></a>Crie esquemas de rastreamento personalizados que monitoram fluxos de trabalho de ponta a ponta no Azure Logic A

O Azure Logic Apps tem um rastreamento integrado que você pode habilitar para partes do seu fluxo de trabalho. No entanto, você pode configurar o rastreamento personalizado que registra eventos do início ao fim dos fluxos de trabalho, por exemplo, fluxos de trabalho que incluem um aplicativo lógico, O Servidor BizTalk, o SQL Server ou qualquer outra camada. Este artigo fornece código personalizado que você pode usar nas camadas fora do aplicativo lógico.

## <a name="custom-tracking-schema"></a>Esquema de controle personalizado

```json
{
   "sourceType": "",
   "source": {
      "workflow": {
         "systemId": ""
      },
      "runInstance": {
         "runId": ""
      },
      "operation": {
         "operationName": "",
         "repeatItemScopeName": "",
         "repeatItemIndex": ,
         "trackingId": "",
         "correlationId": "",
         "clientRequestId": ""
      }
   },
   "events": [
      {
         "eventLevel": "",
         "eventTime": "",
         "recordType": "",
         "record": {}
      }
   ]
}
```

| Propriedade | Obrigatório | Type | Descrição |
|----------|----------|------|-------------|
| sourceType | Sim | String | Tipo da fonte de execução `Microsoft.Logic/workflows`com estes valores permitidos: ,`custom` |
| source | Sim | String ou JToken | Se o tipo `Microsoft.Logic/workflows`de origem for, a informação de origem precisa seguir este esquema. Se o tipo `custom`de origem for, o esquema é um JToken. |
| systemId | Sim | String | ID do sistema de aplicativos lógicos |
| runId | Sim | String | ID de execução de aplicativo lógico |
| operationName | Sim | String | Nome da operação, por exemplo, ação ou gatilho |
| repeatItemScopeName | Sim | String | Repita o nome do item `foreach` `until` se a ação estiver dentro de um ou loop |
| repeatItemIndex | Sim | Integer | Indica que a ação `foreach` `until` está dentro de um loop ou é o número de índice de item repetido. |
| trackingId | Não | String | Rastreamento de ID para correlacionar as mensagens |
| correlationId | Não | String | ID de correlação para correlacionar as mensagens |
| clientRequestId | Não | String | O cliente pode preencher esta propriedade para correlacionar mensagens |
| eventLevel | Sim | String | Nível do evento |
| eventTime | Sim | Datetime | Hora do evento no formato UTC: *YYYY-MM-DDTHH:MM:SS.00000Z* |
| recordType | Sim | String | Tipo do histórico apenas com este valor permitido:`custom` |
| registro | Sim | JToken | Tipo de gravação personalizado apenas com formato JToken |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Esquemas de acompanhamento do protocolo B2B

Para obter informações sobre esquemas de acompanhamento do protocolo B2B, veja:

* [Esquemas de rastreamento AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de rastreamento X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [o monitoramento de mensagens B2B com registros do Monitor Do Azure](../logic-apps/monitor-b2b-messages-log-analytics.md)