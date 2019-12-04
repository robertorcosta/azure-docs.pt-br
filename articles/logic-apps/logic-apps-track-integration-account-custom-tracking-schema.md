---
title: Esquemas de acompanhamento personalizados para mensagens B2B
description: Criar esquemas de acompanhamento personalizados que monitorem mensagens B2B nas contas de integração para os Aplicativos Lógicos do Azure com o Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 7d7c5ef9e9a86c8b061a56fe41c0c8bbfc5ddbb3
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792788"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-apps"></a>Criar esquemas de acompanhamento personalizados que monitorem fluxos de trabalho de ponta a ponta nos Aplicativos Lógicos do Azure

Há um acompanhamento interno que você pode habilitar para diferentes partes do fluxo de trabalho entre empresas, como acompanhamento de mensagens AS2 ou X12. Quando você cria fluxos de trabalho que incluem um aplicativo lógico, o BizTalk Server, o SQL Server ou qualquer outra camada, é possível habilitar o acompanhamento personalizado que registra eventos desde o início até o fim do fluxo de trabalho. 

Este artigo fornece código personalizado que você pode usar nas camadas fora do aplicativo lógico. 

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
         "repeatItemIndex": "",
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
         "record": {                
         }
      }
   ]
}
```

| Propriedade | obrigatórios | Type | Descrição |
| --- | --- | --- | --- |
| sourceType | SIM |   | O tipo da fonte de execução. Os valores permitidos são **Microsoft.Logic/workflows** e **personalizado**. |
| source | SIM |   | Se o tipo de fonte for **Microsoft.Logic/workflows**, as informações de origem precisarão seguir este esquema. Se o tipo de origem for **personalizado**, o esquema será um JToken. |
| systemId | SIM | string | ID do sistema de aplicativo lógico. |
| runId | SIM | string | ID de execução do aplicativo lógico. |
| operationName | SIM | string | O nome da operação (por exemplo, ação ou gatilho). |
| repeatItemScopeName | SIM | string | Repita o nome do item se a ação estiver dentro de um loop `foreach`/`until`. |
| repeatItemIndex | SIM | Número inteiro | Se a ação está ou não dentro de um loop `foreach`/`until`. Indica o índice de itens repetidos. |
| trackingId | Não | string | A ID de acompanhamento para correlacionar as mensagens. |
| correlationId | Não | string | A ID de correlação para correlacionar as mensagens. |
| clientRequestId | Não | string | O cliente pode populá-la para correlacionar mensagens. |
| eventLevel | SIM |   | Nível do evento. |
| eventTime | SIM |   | A hora do evento no formato UTC AAAA-MM-DDTHH:MM:SS.00000Z. |
| recordType | SIM |   | Tipo do registro de acompanhamento. O valor permitido é **personalizado**. |
| record | SIM |   | Tipo de registro personalizado. O formato permitido é JToken. |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Esquemas de acompanhamento do protocolo B2B

Para obter informações sobre esquemas de acompanhamento do protocolo B2B, veja:

* [Esquemas de acompanhamento de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Esquemas de acompanhamento de X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre o [monitoramento de mensagens B2B](logic-apps-monitor-b2b-message.md)
* Saiba mais sobre como [controlar mensagens B2B em logs de Azure monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)
