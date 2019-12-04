---
title: Esquemas de controle AS2 para mensagens B2B
description: Criar esquemas de acompanhamento AS2 que monitorem mensagens B2B nas contas de integração para os Aplicativos Lógicos do Azure com o Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 515d7cfc985ee9929f70de2c862170ff79ae4d60
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792816"
---
# <a name="create-schemas-for-tracking-as2-messages-and-mdns-in-integration-accounts-for-azure-logic-apps"></a>Criar esquemas para acompanhamento de mensagens AS2 e MDNs em contas de integração para os Aplicativos Lógicos do Azure

Você pode usar esses esquemas de acompanhamento AS2 em sua conta de integração para ajudá-lo a monitorar êxito, erros e propriedades de mensagem para transações B2B (entre empresas):

* Esquema de acompanhamento de mensagens AS2
* Esquema de acompanhamento MDN AS2

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
      "incomingHeaders": {
       },
      "outgoingHeaders": {
       },
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| Propriedade | Type | Descrição |
| --- | --- | --- |
| senderPartnerName | string | O nome do parceiro do remetente da mensagem AS2. (Opcional) |
| receiverPartnerName | string | O nome do parceiro do destinatário da mensagem AS2. (Opcional) |
| as2To | string | Nome do destinatário da mensagem AS2, dos cabeçalhos da mensagem AS2. (Obrigatório) |
| as2From | string | Nome do remetente da mensagem AS2, dos cabeçalhos da mensagem AS2. (Obrigatório) |
| agreementName | string | Nome do contrato AS2 para o qual as mensagens são resolvidas. (Opcional) |
| direction | string | Indica a direção do fluxo de mensagens, receber ou enviar. (Obrigatório) |
| messageId | string | ID da mensagem AS2, dos cabeçalhos da mensagem AS2 (Opcional) |
| dispositionType |string | Valor do tipo de disposição MDN (notificação de disposição de mensagem). (Opcional) |
| fileName | string | Nome do arquivo, do cabeçalho da mensagem AS2. (Opcional) |
| isMessageFailed |Booliano | Se a mensagem AS2 falha ou não. (Obrigatório) |
| isMessageSigned | Booliano | Se a mensagem AS2 foi ou não assinada. (Obrigatório) |
| isMessageEncrypted | Booliano | Se a mensagem AS2 foi ou não criptografada. (Obrigatório) |
| isMessageCompressed |Booliano | Se a mensagem AS2 foi ou não comprimida. (Obrigatório) |
| correlationMessageId | string | ID da mensagem AS2 para correlacionar mensagens com MDNs. (Opcional) |
| incomingHeaders |Dicionário de JToken | Detalhes do cabeçalho da mensagem de entrada AS2. (Opcional) |
| outgoingHeaders |Dicionário de JToken | Detalhes do cabeçalho da mensagem de saída AS2. (Opcional) |
| isNrrEnabled | Booliano | Use o valor padrão se o valor não for conhecido. (Obrigatório) |
| isMdnExpected | Booliano | Use o valor padrão se o valor não for conhecido. (Obrigatório) |
| mdnType | Enum | Os valores permitidos são **NotConfigured**, **Sync** e **Async**. (Obrigatório) |
||||

## <a name="as2-mdn-tracking-schema"></a>Esquema de acompanhamento MDN AS2

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": "g"
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

| Propriedade | Type | Descrição |
| --- | --- | --- |
| senderPartnerName | string | O nome do parceiro do remetente da mensagem AS2. (Opcional) |
| receiverPartnerName | string | O nome do parceiro do destinatário da mensagem AS2. (Opcional) |
| as2To | string | O nome do parceiro que recebe a mensagem AS2. (Obrigatório) |
| as2From | string | O nome do parceiro que envia a mensagem AS2. (Obrigatório) |
| agreementName | string | Nome do contrato AS2 para o qual as mensagens são resolvidas. (Opcional) |
| direction |string | Indica a direção do fluxo de mensagens, receber ou enviar. (Obrigatório) |
| messageId | string | ID da mensagem AS2. (Opcional) |
| originalMessageId |string | ID da mensagem AS2 original. (Opcional) |
| dispositionType | string | Valor do tipo de disposição MDN. (Opcional) |
| isMessageFailed |Booliano | Se a mensagem AS2 falha ou não. (Obrigatório) |
| isMessageSigned |Booliano | Se a mensagem AS2 foi ou não assinada. (Obrigatório) |
| isNrrEnabled | Booliano | Use o valor padrão se o valor não for conhecido. (Obrigatório) |
| statusCode | Enum | Os valores aceitos são **Accepted**, **Rejected**, **AcceptedWithErrors**. (Obrigatório) |
| micVerificationStatus | Enum | Os valores permitidos são **NotApplicable**, **Succeeded** ou **Failed**. (Obrigatório) |
| correlationMessageId | string | ID de correlação. A ID da mensagem original (a ID da mensagem para a qual o MDN está configurado). (Opcional) |
| incomingHeaders | Dicionário de JToken | Indica detalhes do cabeçalho da mensagem de entrada. (Opcional) |
| outgoingHeaders |Dicionário de JToken | Indica detalhes do cabeçalho da mensagem de saída. (Opcional) |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Esquemas de acompanhamento do protocolo B2B

Para obter informações sobre esquemas de acompanhamento do protocolo B2B, veja:

* [Esquemas de acompanhamento de X12](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Esquemas de acompanhamento B2B personalizados](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre o [monitoramento de mensagens B2B](logic-apps-monitor-b2b-message.md)
* Saiba mais sobre como [controlar mensagens B2B em logs de Azure monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)