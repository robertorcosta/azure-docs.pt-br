---
title: Esquemas de rastreamento AS2 para mensagens B2B
description: Crie esquemas de rastreamento para monitorar mensagens AS2 em aplicativos azure logic
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: bccf69362279afd9e8148b20b61ff3ea9b472a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906963"
---
# <a name="create-schemas-for-tracking-as2-messages-in-azure-logic-apps"></a>Crie esquemas para rastrear mensagens AS2 em aplicativos azure logic

Você pode usar esses esquemas de acompanhamento AS2 em sua conta de integração para ajudá-lo a monitorar êxito, erros e propriedades de mensagem para transações B2B (entre empresas):

* Esquema de acompanhamento de mensagens AS2
* MDN (Menção de descarte de mensagens DE 2) do AS2

## <a name="as2-message-tracking-schema"></a>Esquema de acompanhamento de mensagens AS2

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "dispositionType": "",
      "fileName": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isMessageEncrypted": "",
      "isMessageCompressed": "",
      "correlationMessageId": "",
      "incomingHeaders": {},
      "outgoingHeaders": {},
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| Propriedade | Obrigatório | Type | Descrição |
|----------|----------|------|-------------|
| senderPartnerName | Não | String | Nome do parceiro do remetente de mensagem AS2 |
| receiverPartnerName | Não | String | Nome do receptor de mensagem AS2 |
| as2To | Sim | String | Nome do receptor de mensagem AS2 dos cabeçalhos da mensagem AS2 |
| as2From | Sim | String | Nome do remetente de mensagem AS2 dos cabeçalhos da mensagem AS2 |
| agreementName | Não | String | Nome do contrato AS2 para o qual as mensagens são resolvidas |
| direction | Sim | String | Direção do fluxo de `receive` mensagem, que é ou ou`send` |
| messageId | Não | String | ID de mensagem AS2 dos cabeçalhos da mensagem AS2 |
| dispositionType | Não | String | Valor do tipo de descarte de disto de descarte de mensagens (MDN) |
| fileName | Não | String | Nome do arquivo do cabeçalho da mensagem AS2 |
| isMessageFailed | Sim | Boolean | Se a mensagem AS2 falhou |
| isMessageSigned | Sim | Boolean | Se a mensagem AS2 foi assinada |
| isMessageEncrypted | Sim | Boolean | Se a mensagem AS2 foi criptografada |
| isMessageCompressed | Sim | Boolean | Se a mensagem AS2 foi compactada |
| correlationMessageId | Não | String | ID de mensagem AS2, para correlacionar mensagens com MDNs |
| incomingHeaders | Não | Dicionário de JToken | Detalhes do cabeçalho de mensagem AS2 de entrada |
| outgoingHeaders | Não | Dicionário de JToken | Detalhes do cabeçalho de mensagens AS2 de saída |
| isNrrEnabled | Sim | Boolean | Se usar o valor padrão se o valor não for conhecido |
| isMdnExpected | Sim | Boolean | Se usar o valor padrão se o valor não for conhecido |
| mdnType | Sim | Enum | Valores `NotConfigured`permitidos: , `Sync`e`Async` |
|||||

## <a name="as2-mdn-tracking-schema"></a>Esquema de acompanhamento MDN AS2

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "originalMessageId": "",
      "dispositionType": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isNrrEnabled": "",
      "statusCode": "",
      "micVerificationStatus": "",
      "correlationMessageId": "",
      "incomingHeaders": {
      },
      "outgoingHeaders": {
      }
   }
}
```

| Propriedade | Obrigatório | Type | Descrição |
|----------|----------|------|-------------|
| senderPartnerName | Não | String | Nome do parceiro do remetente de mensagem AS2 |
| receiverPartnerName | Não | String | Nome do receptor de mensagem AS2 |
| as2To | Sim | String | Nome do parceiro que recebe a mensagem AS2 |
| as2From | Sim | String | Nome do parceiro que envia a mensagem AS2 |
| agreementName | Não | String | Nome do contrato AS2 para o qual as mensagens são resolvidas |
| direction | Sim | String | Direção do fluxo de `receive` mensagem, que é ou ou`send` |
| messageId | Não | String | ID de mensagem AS2 |
| originalMessageId | Não | String | ID de mensagem original AS2 |
| dispositionType | Não | String | Valor do tipo de disposição MDN |
| isMessageFailed | Sim | Boolean | Se a mensagem AS2 falhou |
| isMessageSigned | Sim | Boolean | Se a mensagem AS2 foi assinada |
| isNrrEnabled | Sim | Boolean | Se usar o valor padrão se o valor não for conhecido |
| statusCode | Sim | Enum | Valores `Accepted`permitidos: , `Rejected`e`AcceptedWithErrors` |
| micVerificationStatus | Sim | Enum | Valores`NotApplicable`permitidos: , `Succeeded`e`Failed` |
| correlationMessageId | Não | String | ID de correlação, que é o ID da mensagem original que tem o MDN configurado |
| incomingHeaders | Não | Dicionário de JToken | Detalhes do cabeçalho da mensagem recebida |
| outgoingHeaders | Não | Dicionário de JToken | Detalhes do cabeçalho de mensagens de saída |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Esquemas de acompanhamento do protocolo B2B

Para obter informações sobre esquemas de acompanhamento do protocolo B2B, veja:

* [Esquemas de rastreamento X12](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Esquemas de acompanhamento B2B personalizados](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Próximas etapas

* [Monitorar mensagens B2B com os logs do Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)