---
title: Esquemas de rastreamento x12 para mensagens B2B
description: Crie esquemas de rastreamento para monitorar mensagens X12 para aplicativos azure logic
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: 5b2df194761ebc167e67498a985960a4fce35f19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905293"
---
# <a name="create-schemas-for-tracking-x12-messages-in-azure-logic-apps"></a>Crie esquemas para rastrear mensagens X12 em aplicativos azure logic

Você pode usar esses esquemas de acompanhamento X12 em sua conta de integração para ajudá-lo a monitorar êxito, erros e propriedades de mensagem para transações B2B (entre empresas):

* Esquema de acompanhamento do conjunto de transações X12
* Esquema de rastreamento de reconhecimento do conjunto de transações X12
* Esquema de acompanhamento de intercâmbio X12
* Esquema de rastreamento de reconhecimento de intercâmbio X12
* Esquema de acompanhamento de grupo funcional X12
* Esquema de rastreamento de reconhecimento de grupo funcional X12

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
      "needAk2LoopForValidMessages": "",
      "segmentsCount": ""
   }
}
```

| Propriedade | Obrigatório | Type | Descrição |
|----------|----------|------|-------------|
| senderPartnerName | Não | String | Nome do parceiro do remetente de mensagem X12 |
| receiverPartnerName | Não | String | Nome do parceiro do receptor de mensagem X12 |
| senderQualifier | Sim | String | Enviar qualificação de parceiro |
| senderIdentifier | Sim | String | Enviar identificador de parceiro |
| receiverQualifier | Sim | String | Receba qualificação de parceiros |
| receiverIdentifier | Sim | String | Receba identificador de parceiro |
| agreementName | Não | String | Nome do contrato X12 para o qual as mensagens são resolvidas |
| direction | Sim | Enum | Direção do fluxo de `receive` mensagem, que é ou ou`send` |
| interchangeControlNumber | Não | String | Número de controle de intercâmbio |
| functionalGroupControlNumber | Não | String | Número de controle funcional |
| transactionSetControlNumber | Não | String | Número de controle de conjunto de transação |
| CorrelationMessageId | Não | String | ID de mensagem de correlação, que é uma combinação de {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber} |
| messageType | Não | String | Conjunto de transações ou tipo de documento |
| isMessageFailed | Sim | Boolean | Se a mensagem X12 falhou |
| isTechnicalAcknowledgmentExpected | Sim | Boolean | Se o reconhecimento técnico está configurado no acordo X12 |
| isFunctionalAcknowledgmentExpected | Sim | Boolean | Se o reconhecimento funcional está configurado no acordo X12 |
| needAk2LoopForValidMessages | Sim | Boolean | Se o loop AK2 é necessário para uma mensagem válida |
| segmentsCount | Não | Integer | Número de segmentos no conjunto de transações X12 |
|||||

## <a name="x12-transaction-set-acknowledgment-tracking-schema"></a>Esquema de rastreamento de reconhecimento do conjunto de transações X12

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

| Propriedade | Obrigatório | Type | Descrição |
|----------|----------|------|-------------|
| senderPartnerName | Não | String | Nome do parceiro do remetente de mensagem X12 |
| receiverPartnerName | Não | String | Nome do parceiro do receptor de mensagem X12 |
| senderQualifier | Sim | String | Enviar qualificação de parceiro |
| senderIdentifier | Sim | String | Enviar identificador de parceiro |
| receiverQualifier | Sim | String | Receba qualificação de parceiros |
| receiverIdentifier | Sim | String | Receba identificador de parceiro |
| agreementName | Não | String | Nome do contrato X12 para o qual as mensagens são resolvidas |
| direction | Sim | Enum | Direção do fluxo de `receive` mensagem, que é ou ou`send` |
| interchangeControlNumber | Não | String | Número de controle de intercâmbio do reconhecimento funcional. O valor é preenchido apenas para o lado de envio onde o reconhecimento funcional é recebido para as mensagens enviadas ao parceiro. |
| functionalGroupControlNumber | Não | String | Número de controle de grupo funcional do reconhecimento funcional. O valor é preenchido apenas para o lado de envio onde o reconhecimento funcional é recebido para as mensagens enviadas ao parceiro |
| isaSegment | Não | String | O segmento ISA da mensagem. O valor é preenchido apenas para o lado de envio onde o reconhecimento funcional é recebido para as mensagens enviadas ao parceiro |
| gsSegment | Não | String | O segmento GS da mensagem. O valor é preenchido apenas para o lado de envio onde o reconhecimento funcional é recebido para as mensagens enviadas ao parceiro |
| respondingfunctionalGroupControlNumber | Não | String | O número de controle de intercâmbio respondendo |
| respondingFunctionalGroupId | Não | String | O ID do grupo funcional em resposta, que mapeia para AK101 no reconhecimento |
| respondingtransactionSetControlNumber | Não | String | O número de controle do conjunto de transações de resposta |
| respondingTransactionSetId | Não | String | O iD do conjunto de transações de resposta, que mapeia para AK201 no reconhecimento |
| statusCode | Sim | Boolean | Código de status de reconhecimento definido por transação |
| segmentsCount | Sim | Enum | Código de status de reconhecimento `Accepted`com `Rejected`estes valores permitidos: , e`AcceptedWithErrors` |
| processingStatus | Sim | Enum | Status de processamento do reconhecimento com estes valores permitidos: `Received`, `Generated`e`Sent` |
| CorrelationMessageId | Não | String | ID de mensagem de correlação, que é uma combinação de {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber} |
| isMessageFailed | Sim | Boolean | Se a mensagem X12 falhou |
| ak2Segment | Não | String | Reconhecimento para uma transação definida dentro do grupo funcional recebido |
| ak3Segment | Não | String | Reporta erros em um segmento de dados |
| ak5Segment | Não | String | Informa se o conjunto de transações identificado no segmento AK2 é aceito ou rejeitado, e por que |
|||||

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

| Propriedade | Obrigatório | Type | Descrição |
|----------|----------|------|-------------|
| senderPartnerName | Não | String | Nome do parceiro do remetente de mensagem X12 |
| receiverPartnerName | Não | String | Nome do parceiro do receptor de mensagem X12 |
| senderQualifier | Sim | String | Enviar qualificação de parceiro |
| senderIdentifier | Sim | String | Enviar identificador de parceiro |
| receiverQualifier | Sim | String | Receba qualificação de parceiros |
| receiverIdentifier | Sim | String | Receba identificador de parceiro |
| agreementName | Não | String | Nome do contrato X12 para o qual as mensagens são resolvidas |
| direction | Sim | Enum | Direção do fluxo de `receive` mensagem, que é ou ou`send` |
| interchangeControlNumber | Não | String | Número de controle de intercâmbio |
| isaSegment | Não | String | Segmento ISA de mensagem |
| isTechnicalAcknowledgmentExpected | Boolean | Se o reconhecimento técnico está configurado no acordo X12  |
| isMessageFailed | Sim | Boolean | Se a mensagem X12 falhou |
| isa09 | Não | String | Data de intercâmbio de documentos X12 |
| isa10 | Não | String | Tempo de intercâmbio de documentos X12 |
| isa11 | Não | String | Identificador de padrões de controle de intercâmbio X12 |
| isa12 | Não | String | Número da versão do controle de intercâmbio X12 |
| isa14 | Não | String | Reconhecimento X12 é solicitado |
| isa15 | Não | String | Indicador para teste ou produção |
| isa16 | Não | String | Separador de elementos |
|||||

## <a name="x12-interchange-acknowledgment-tracking-schema"></a>Esquema de rastreamento de reconhecimento de intercâmbio X12

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

| Propriedade | Obrigatório | Type | Descrição |
|----------|----------|------|-------------|
| senderPartnerName | Não | String | Nome do parceiro do remetente de mensagem X12 |
| receiverPartnerName | Não | String | Nome do parceiro do receptor de mensagem X12 |
| senderQualifier | Sim | String | Enviar qualificação de parceiro |
| senderIdentifier | Sim | String | Enviar identificador de parceiro |
| receiverQualifier | Sim | String | Receba qualificação de parceiros |
| receiverIdentifier | Sim | String | Receba identificador de parceiro |
| agreementName | Não | String | Nome do contrato X12 para o qual as mensagens são resolvidas |
| direction | Sim | Enum | Direção do fluxo de `receive` mensagem, que é ou ou`send` |
| interchangeControlNumber | Não | String | Número de controle de intercâmbio do reconhecimento técnico recebido dos parceiros |
| isaSegment | Não | String | Segmento ISA para o reconhecimento técnico que é recebido de parceiros |
| respondingInterchangeControlNumber | Não | String | Número de controle de intercâmbio para o reconhecimento técnico que é recebido dos parceiros |
| isMessageFailed | Sim | Boolean | Se a mensagem X12 falhou |
| statusCode | Sim | Enum | Código de status de reconhecimento de `Accepted` `Rejected`intercâmbio com estes valores permitidos: , e`AcceptedWithErrors` |
| processingStatus | Sim | Enum | Status de reconhecimento com estes valores permitidos: `Received`, `Generated`e`Sent` |
| ta102 | Não | String | Data de intercâmbio |
| ta103 | Não | String | Tempo de intercâmbio |
| ta105 | Não | String | Código de nota de intercâmbio |
|||||

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

| Propriedade | Obrigatório | Type | Descrição |
|----------|----------|------|-------------|
| senderPartnerName | Não | String | Nome do parceiro do remetente de mensagem X12 |
| receiverPartnerName | Não | String | Nome do parceiro do receptor de mensagem X12 |
| senderQualifier | Sim | String | Enviar qualificação de parceiro |
| senderIdentifier | Sim | String | Enviar identificador de parceiro |
| receiverQualifier | Sim | String | Receba qualificação de parceiros |
| receiverIdentifier | Sim | String | Receba identificador de parceiro |
| agreementName | Não | String | O nome do acordo X12 para o qual as mensagens são resolvidas |
| direction | Sim | Enum | Direção do fluxo de mensagem, receber ou enviar |
| interchangeControlNumber | Não | String | Número de controle de intercâmbio |
| functionalGroupControlNumber | Não | String | Número de controle funcional |
| gsSegment | Não | String | Segmento gs de mensagem |
| isTechnicalAcknowledgmentExpected | Sim | Boolean | Se o reconhecimento técnico está configurado no acordo X12 |
| isFunctionalAcknowledgmentExpected | Sim | Boolean | Se o reconhecimento funcional está configurado no acordo X12 |
| isMessageFailed | Sim | Boolean | Se a mensagem X12 falhou |
| gs01 | Não | String | Código identificador funcional |
| gs02 | Não | String | Código do remetente do aplicativo |
| gs03 | Não | String | Código do receptor de aplicativo |
| gs04 | Não | String | Data do grupo funcional |
| gs05 | Não | String | Tempo de grupo funcional |
| gs07 | Não | String | Código da agência responsável |
| gs08 | Não | String | Código identificador para a versão, versão ou indústria |
|||||

## <a name="x12-functional-group-acknowledgment-tracking-schema"></a>Esquema de rastreamento de reconhecimento de grupo funcional X12

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

| Propriedade | Obrigatório | Type | Descrição |
|----------|----------|------|-------------|
| senderPartnerName | Não | String | Nome do parceiro do remetente de mensagem X12 |
| receiverPartnerName | Não | String | Nome do parceiro do receptor de mensagem X12 |
| senderQualifier | Sim | String | Enviar qualificação de parceiro |
| senderIdentifier | Sim | String | Enviar identificador de parceiro |
| receiverQualifier | Sim | String | Receba qualificação de parceiros |
| receiverIdentifier | Sim | String | Receba identificador de parceiro |
| agreementName | Não | String | Nome do contrato X12 para o qual as mensagens são resolvidas |
| direction | Sim | Enum | Direção do fluxo de `receive` mensagem, que é ou ou`send` |
| interchangeControlNumber | Não | String | Número de controle de intercâmbio, que preenche para o lado de envio quando um reconhecimento técnico é recebido de parceiros |
| functionalGroupControlNumber | Não | String | Número de controle de grupo funcional do reconhecimento técnico, que preenche para o lado de envio quando um reconhecimento técnico é recebido dos parceiros |
| isaSegment | Não | String | O mesmo que o número de controle de intercâmbio, mas povoado apenas em casos específicos |
| gsSegment | Não | String | O mesmo que o número de controle de grupo funcional, mas preenchido apenas em casos específicos |
| respondingfunctionalGroupControlNumber | Não | String | Número de controle do grupo funcional original |
| respondingFunctionalGroupId | Não | String | Mapas para AK101 no ID do grupo funcional de reconhecimento |
| isMessageFailed | Boolean | Se a mensagem X12 falhou |
| statusCode | Sim | Enum | Código de status de reconhecimento `Accepted`com `Rejected`estes valores permitidos: , e`AcceptedWithErrors` |
| processingStatus | Sim | Enum | Status de processamento do reconhecimento com estes valores permitidos: `Received`, `Generated`e`Sent` |
| ak903 | Não | String | Número de conjuntos de transações recebidos |
| ak904 | Não | String | Número de conjuntos de transações aceitos no grupo funcional identificado |
| ak9Segment | Não | String | Se o grupo funcional identificado no segmento AK1 é aceito ou rejeitado, e por que |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Esquemas de acompanhamento do protocolo B2B

Para obter informações sobre esquemas de acompanhamento do protocolo B2B, veja:

* [Esquemas de rastreamento AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de acompanhamento B2B personalizados](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Próximas etapas

* [Monitorar mensagens B2B com os logs do Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)