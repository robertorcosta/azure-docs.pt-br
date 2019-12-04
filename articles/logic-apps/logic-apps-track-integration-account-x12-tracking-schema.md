---
title: Esquemas de acompanhamento de X12 para mensagens B2B
description: Criar esquemas de acompanhamento X12 que monitorem mensagens B2B nas contas de integração para os Aplicativos Lógicos do Azure com o Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 6f2356600f5b6a637da731c650b26d968092e2f6
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791717"
---
# <a name="create-schemas-for-tracking-x12-messages-in-integration-accounts-for-azure-logic-apps"></a>Criar esquemas para acompanhamento de mensagens X12 em contas de integração para os Aplicativos Lógicos do Azure

Você pode usar esses esquemas de acompanhamento X12 em sua conta de integração para ajudá-lo a monitorar êxito, erros e propriedades de mensagem para transações B2B (entre empresas):

* Esquema de acompanhamento do conjunto de transações X12
* Esquema de acompanhamento de confirmação do conjunto de transações X12
* Esquema de acompanhamento de intercâmbio X12
* Esquema de acompanhamento de confirmação do intercâmbio X12
* Esquema de acompanhamento de grupo funcional X12
* Esquema de acompanhamento de confirmação do grupo funcional X12

## <a name="x12-transaction-set-tracking-schema"></a>Esquema de acompanhamento do conjunto de transações X12

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "transactionSetControlNumber": "",
      "CorrelationMessageId": "",
      "messageType": "",
      "isMessageFailed": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "needAk2LoopForValidMessages":  "",
      "segmentsCount": ""
   }
}
```

| Propriedade | Type | Descrição |
| --- | --- | --- |
| senderPartnerName | string | O nome do parceiro do remetente da mensagem X12. (Opcional) |
| receiverPartnerName | string | O nome do parceiro do destinatário da mensagem X12. (Opcional) |
| senderQualifier | string | Qualificador de parceiro de envio. (Obrigatório) |
| senderIdentifier | string | Identificador de parceiro de envio. (Obrigatório) |
| receiverQualifier | string | Qualificador de parceiro de recebimento. (Obrigatório) |
| receiverIdentifier | string | Identificador de parceiro de recebimento. (Obrigatório) |
| agreementName | string | Nome do contrato X12 para o qual as mensagens são resolvidas. (Opcional) |
| direction | Enum | Indica a direção do fluxo de mensagens, receber ou enviar. (Obrigatório) |
| interchangeControlNumber | string | Número de controle de intercâmbio. (Opcional) |
| functionalGroupControlNumber | string | Número de controle funcional. (Opcional) |
| transactionSetControlNumber | string | Número de controle de conjunto de transações. (Opcional) |
| CorrelationMessageId | string | ID de mensagem de correlação. Uma combinação de {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber}. (Opcional) |
| messageType | string | Tipo de documento ou conjunto de transações. (Opcional) |
| isMessageFailed | Booliano | Se a mensagem X12 falha ou não. (Obrigatório) |
| isTechnicalAcknowledgmentExpected | Booliano | Se a confirmação técnica está configurada ou não no contrato X12. (Obrigatório) |
| isFunctionalAcknowledgmentExpected | Booliano | Se a confirmação funcional está configurada ou não no contrato X12. (Obrigatório) |
| needAk2LoopForValidMessages | Booliano | Se o loop AK2 é ou não necessário para uma mensagem válida. (Obrigatório) |
| segmentsCount | Número inteiro | O número de segmentos no conjunto de transações do X12. (Opcional) |
||||

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>Esquema de acompanhamento de confirmação do conjunto de transações X12

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "respondingtransactionSetControlNumber": "",
      "respondingTransactionSetId": "",
      "statusCode": "",
      "processingStatus": "",
      "CorrelationMessageId": "",
      "isMessageFailed": "",
      "ak2Segment": "",
      "ak3Segment": "",
      "ak5Segment": ""
   }
}
```

| Propriedade | Type | Descrição |
| --- | --- | --- |
| senderPartnerName | string | O nome do parceiro do remetente da mensagem X12. (Opcional) |
| receiverPartnerName | string | O nome do parceiro do destinatário da mensagem X12. (Opcional) |
| senderQualifier | string | Qualificador de parceiro de envio. (Obrigatório) |
| senderIdentifier | string | Identificador de parceiro de envio. (Obrigatório) |
| receiverQualifier | string | Qualificador de parceiro de recebimento. (Obrigatório) |
| receiverIdentifier | string | Identificador de parceiro de recebimento. (Obrigatório) |
| agreementName | string | Nome do contrato X12 para o qual as mensagens são resolvidas. (Opcional) |
| direction | Enum | Indica a direção do fluxo de mensagens, receber ou enviar. (Obrigatório) |
| interchangeControlNumber | string | O número de controle de intercâmbio da confirmação funcional. O valor popula apenas o lado de envio em que a confirmação funcional é recebida para as mensagens enviadas ao parceiro. (Opcional) |
| functionalGroupControlNumber | string | O número de controle do grupo funcional da confirmação funcional. O valor popula apenas o lado de envio em que a confirmação funcional é recebida para as mensagens enviadas ao parceiro. (Opcional) |
| isaSegment | string | O segmento ISA da mensagem. O valor popula apenas o lado de envio em que a confirmação funcional é recebida para as mensagens enviadas ao parceiro. (Opcional) |
| gsSegment | string | O segmento GS da mensagem. O valor popula apenas o lado de envio em que a confirmação funcional é recebida para as mensagens enviadas ao parceiro. (Opcional) |
| respondingfunctionalGroupControlNumber | string | O número de controle de intercâmbio de resposta. (Opcional) |
| respondingFunctionalGroupId | string | A ID de grupo funcional de resposta, que mapeia para AK101 na confirmação. (Opcional) |
| respondingtransactionSetControlNumber | string | Número de controle de conjunto de transações de resposta. (Opcional) |
| respondingTransactionSetId | string | A ID de conjunto de transações de resposta, que mapeia para AK201 na confirmação. (Opcional) |
| statusCode | Booliano | O código de status de confirmação do conjunto de transações. (Obrigatório) |
| segmentsCount | Enum | O código de status de confirmação. Os valores aceitos são **Accepted**, **Rejected**, **AcceptedWithErrors**. (Obrigatório) |
| processingStatus | Enum | O status de processamento da confirmação. Os valores permitidos são **Received**, **Generated**, **Sent**. (Obrigatório) |
| CorrelationMessageId | string | ID de mensagem de correlação. Uma combinação de {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber}. (Opcional) |
| isMessageFailed | Booliano | Se a mensagem X12 falha ou não. (Obrigatório) |
| ak2Segment | string | A confirmação de um conjunto de transações no grupo funcional recebido. (Opcional) |
| ak3Segment | string | Relata erros em um segmento de dados. (Opcional) |
| ak5Segment | string | Relata se o conjunto de transações identificado no segmento AK2 foi aceito ou rejeitado e a razão. (Opcional) |
||||

## <a name="x12-interchange-tracking-schema"></a>Esquema de acompanhamento de intercâmbio X12

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "isa09": "",
      "isa10": "",
      "isa11": "",
      "isa12": "",
      "isa14": "",
      "isa15": "",
      "isa16": ""
   }
}
```

| Propriedade | Type | Descrição |
| --- | --- | --- |
| senderPartnerName | string | O nome do parceiro do remetente da mensagem X12. (Opcional) |
| receiverPartnerName | string | O nome do parceiro do destinatário da mensagem X12. (Opcional) |
| senderQualifier | string | Qualificador de parceiro de envio. (Obrigatório) |
| senderIdentifier | string | Identificador de parceiro de envio. (Obrigatório) |
| receiverQualifier | string | Qualificador de parceiro de recebimento. (Obrigatório) |
| receiverIdentifier | string | Identificador de parceiro de recebimento. (Obrigatório) |
| agreementName | string | Nome do contrato X12 para o qual as mensagens são resolvidas. (Opcional) |
| direction | Enum | Indica a direção do fluxo de mensagens, receber ou enviar. (Obrigatório) |
| interchangeControlNumber | string | Número de controle de intercâmbio. (Opcional) |
| isaSegment | string | Segmento ISA de mensagem. (Opcional) |
| isTechnicalAcknowledgmentExpected | Booliano | Se a confirmação técnica está configurada ou não no contrato X12. (Obrigatório) |
| isMessageFailed | Booliano | Se a mensagem X12 falha ou não. (Obrigatório) |
| isa09 | string | A data de intercâmbio do documento X12. (Opcional) |
| isa10 | string | A hora de intercâmbio do documento X12. (Opcional) |
| isa11 | string | O identificador de Padrões de Controle de intercâmbio X12. (Opcional) |
| isa12 | string | O número de versão de controle de intercâmbio X12. (Opcional) |
| isa14 | string | A confirmação do X12 é solicitada. (Opcional) |
| isa15 | string | O indicador de teste ou produção. (Opcional) |
| isa16 | string | Separador de elementos. (Opcional) |
||||

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>Esquema de acompanhamento de confirmação do intercâmbio X12

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "respondingInterchangeControlNumber": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ta102": "",
      "ta103": "",
      "ta105": ""
   }
}
```

| Propriedade | Type | Descrição |
| --- | --- | --- |
| senderPartnerName | string | O nome do parceiro do remetente da mensagem X12. (Opcional) |
| receiverPartnerName | string | O nome do parceiro do destinatário da mensagem X12. (Opcional) |
| senderQualifier | string | Qualificador de parceiro de envio. (Obrigatório) |
| senderIdentifier | string | Identificador de parceiro de envio. (Obrigatório) |
| receiverQualifier | string | Qualificador de parceiro de recebimento. (Obrigatório) |
| receiverIdentifier | string | Identificador de parceiro de recebimento. (Obrigatório) |
| agreementName | string | Nome do contrato X12 para o qual as mensagens são resolvidas. (Opcional) |
| direction | Enum | Indica a direção do fluxo de mensagens, receber ou enviar. (Obrigatório) |
| interchangeControlNumber | string | O número de controle de intercâmbio da confirmação técnica recebida dos parceiros. (Opcional) |
| isaSegment | string | O segmento ISA da confirmação técnica recebida dos parceiros. (Opcional) |
| respondingInterchangeControlNumber |string | O número de controle de intercâmbio da confirmação técnica recebida dos parceiros. (Opcional) |
| isMessageFailed | Booliano | Se a mensagem X12 falha ou não. (Obrigatório) |
| statusCode | Enum | O código de status de confirmação do intercâmbio. Os valores aceitos são **Accepted**, **Rejected**, **AcceptedWithErrors**. (Obrigatório) |
| processingStatus | Enum | Status de confirmação. Os valores permitidos são **Received**, **Generated**, **Sent**. (Obrigatório) |
| ta102 | string | Data do intercâmbio. (Opcional) |
| ta103 | string | Hora do intercâmbio. (Opcional) |
| ta105 | string | Código de observação de intercâmbio. (Opcional) |
||||

## <a name="x12-functional-group-tracking-schema"></a>Esquema de acompanhamento de grupo funcional X12

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "gsSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "gs01": "",
      "gs02": "",
      "gs03": "",
      "gs04": "",
      "gs05": "",
      "gs07": "",
      "gs08": ""
   }
}
```

| Propriedade | Type | Descrição |
| --- | --- | --- |
| senderPartnerName | string | O nome do parceiro do remetente da mensagem X12. (Opcional) |
| receiverPartnerName | string | O nome do parceiro do destinatário da mensagem X12. (Opcional) |
| senderQualifier | string | Qualificador de parceiro de envio. (Obrigatório) |
| senderIdentifier | string | Identificador de parceiro de envio. (Obrigatório) |
| receiverQualifier | string | Qualificador de parceiro de recebimento. (Obrigatório) |
| receiverIdentifier | string | Identificador de parceiro de recebimento. (Obrigatório) |
| agreementName | string | Nome do contrato X12 para o qual as mensagens são resolvidas. (Opcional) |
| direction | Enum | Indica a direção do fluxo de mensagens, receber ou enviar. (Obrigatório) |
| interchangeControlNumber | string | Número de controle de intercâmbio. (Opcional) |
| functionalGroupControlNumber | string | Número de controle funcional. (Opcional) |
| gsSegment | string | Segmento GS de mensagem. (Opcional) |
| isTechnicalAcknowledgmentExpected | Booliano | Se a confirmação técnica está configurada ou não no contrato X12. (Obrigatório) |
| isFunctionalAcknowledgmentExpected | Booliano | Se a confirmação funcional está configurada ou não no contrato X12. (Obrigatório) |
| isMessageFailed | Booliano | Se a mensagem X12 falha ou não. (Obrigatório)|
| gs01 | string | O código do identificador funcional. (Opcional) |
| gs02 | string | O código do remetente do aplicativo. (Opcional) |
| gs03 | string | O código do receptor do aplicativo. (Opcional) |
| gs04 | string | Data de grupo funcional. (Opcional) |
| gs05 | string | Hora de grupo funcional. (Opcional) |
| gs07 | string | Código da agência responsável. (Opcional) |
| gs08 | string | Código de identificador de versão/lançamento/setor. (Opcional) |
||||

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>Esquema de acompanhamento de confirmação do grupo funcional X12

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ak903": "",
      "ak904": "",
      "ak9Segment": ""
   }
}
```

| Propriedade | Type | Descrição |
| --- | --- | --- |
| senderPartnerName | string | O nome do parceiro do remetente da mensagem X12. (Opcional) |
| receiverPartnerName | string | O nome do parceiro do destinatário da mensagem X12. (Opcional) |
| senderQualifier | string | Qualificador de parceiro de envio. (Obrigatório) |
| senderIdentifier | string | Identificador de parceiro de envio. (Obrigatório) |
| receiverQualifier | string | Qualificador de parceiro de recebimento. (Obrigatório) |
| receiverIdentifier | string | Identificador de parceiro de recebimento. (Obrigatório) |
| agreementName | string | Nome do contrato X12 para o qual as mensagens são resolvidas. (Opcional) |
| direction | Enum | Indica a direção do fluxo de mensagens, receber ou enviar. (Obrigatório) |
| interchangeControlNumber | string | Número de controle de intercâmbio, que popula o lado de envio quando uma confirmação técnica é recebida de parceiros. (Opcional) |
| functionalGroupControlNumber | string | Número de controle de grupo funcional da confirmação técnica, que popula o lado de envio quando uma confirmação técnica é recebida de parceiros. (Opcional) |
| isaSegment | string | O mesmo que o número de controle de intercâmbio, só é populado em casos específicos. (Opcional) |
| gsSegment | string | O mesmo que o número de controle de grupo funcional, só é populado em casos específicos. (Opcional) |
| respondingfunctionalGroupControlNumber | string | Número de controle do grupo funcional original. (Opcional) |
| respondingFunctionalGroupId | string | Mapeia para AK101 na ID do grupo funcional de confirmação. (Opcional) |
| isMessageFailed | Booliano | Se a mensagem X12 falha ou não. (Obrigatório) |
| statusCode | Enum | O código de status de confirmação. Os valores aceitos são **Accepted**, **Rejected**, **AcceptedWithErrors**. (Obrigatório) |
| processingStatus | Enum | O status de processamento da confirmação. Os valores permitidos são **Received**, **Generated**, **Sent**. (Obrigatório) |
| ak903 | string | Número de conjuntos de transação recebidos. (Opcional) |
| ak904 | string | Indica o número de conjuntos de transação aceitos no grupo funcional identificado. (Opcional) |
| ak9Segment | string | Se o grupo funcional identificado no segmento AK1 foi aceito ou rejeitado e a razão. (Opcional) |
|||| 

## <a name="b2b-protocol-tracking-schemas"></a>Esquemas de acompanhamento do protocolo B2B

Para obter informações sobre esquemas de acompanhamento do protocolo B2B, veja:

* [Esquemas de acompanhamento de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Esquemas de acompanhamento B2B personalizados](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre o [monitoramento de mensagens de B2B](logic-apps-monitor-b2b-message.md).
* Saiba mais sobre como [controlar mensagens B2B em logs de Azure monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
