---
title: APIs do serviço de medição de marketplace | Mercado Azure
description: Evento de uso para ofertas SaaS no Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 315f36e5aed9dee0a89e1f9f504b18a6bed806e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275740"
---
# <a name="marketplace-metering-service-apis"></a>APIs de serviço de medição do Marketplace

A API do evento de uso permite que você emita eventos de uso para uma entidade comprada específica. A solicitação de evento de uso faz referência à dimensão dos serviços de medição definida pelo editor ao publicar a oferta.

## <a name="usage-event"></a>Evento de uso

**POST**:`https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | A versão da operação a ser usada para esta solicitação. A versão mais recente da API é 2018-08-31. |

*Solicitar cabeçalhos:*

| Content-type       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Valor de seqüência exclusivo para acompanhar a solicitação do cliente, de preferência um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| `x-ms-correlationid` | Valor de cadeia exclusivo para operação no cliente. Este parâmetro correlaciona todos os eventos da operação do cliente com eventos do lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| `authorization`   | [Obtenha o token do portador do token web JSON (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Nota: Ao fazer a `Bearer` solicitação HTTP, prefixo para o token obtido no link referenciado. |

*Solicitação:*

```json
{
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

### <a name="responses"></a>Respostas

Código: 200<br>
OK 

```json
{
  "usageEventId": "Unique identifier associated with the usage event",
  "status": "Accepted",
  "messageTime": "Time this message was created in UTC",
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

Código: 400 <br>
Má solicitação, dados ausentes ou inválidos fornecidos ou expirados

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "The resourceId is required.",
      "target": "ResourceId",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```

Código: 403<br>
Má solicitação, dados ausentes ou inválidos fornecidos ou expirados

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

Código: 409<br>
Conflito, quando recebemos a chamada de uso para o ID de recurso de uso, e uso efetivo, que já existe. A resposta `additionalInfo` conterá campo que contém informações sobre a mensagem aceita.

```json
{
  "code": "Conflict",
  "additionalInfo": {
    "usageEventId": "Unique identifier associated with the usage event",
    "status": "Accepted|NotProcessed|Expired",
    "messageTime": "Time this message was created in UTC",
    "resourceId": "Identifier of the resource against which usage is emitted",
    "quantity": 5.0,
    "dimension": "Dimension identifier",
    "effectiveStartTime": "Time in UTC when the usage event occurred",
    "planId": "Plan associated with the purchased offer"
  }
}
```

## <a name="batch-usage-event"></a>Evento de uso em lote

A API de evento de uso em lote permite que você emita eventos de uso para mais de uma entidade comprada de uma só vez. A solicitação de evento de uso em lote faz referência à dimensão dos serviços de medição definida pelo editor ao publicar a oferta.

>[!Note]
>Você pode registrar várias ofertas SaaS no mercado comercial da Microsoft. Cada oferta saas registrada tem um aplicativo Azure AD exclusivo que está registrado para fins de autenticação e autorização. Os eventos emitidos em lote devem pertencer a ofertas com o mesmo aplicativo Azure AD no momento do registro da oferta.

**POST:**`https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | A versão da operação a ser usada para esta solicitação. A versão mais recente da API é 2018-08-31. |

*Solicitar cabeçalhos:*

| Content-type       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | Valor de seqüência exclusivo para acompanhar a solicitação do cliente, de preferência um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| `x-ms-correlationid` | Valor de cadeia exclusivo para operação no cliente. Este parâmetro correlaciona todos os eventos da operação do cliente com eventos do lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| `authorization`      | [Obtenha o token do portador do token web JSON (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Nota: Ao fazer a `Bearer` solicitação HTTP, prefixo para o token obtido no link referenciado.  |

*Solicitação:*
```json
{
  "request": [
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    },
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    }
  ]
}
```
### <a name="responses"></a>Respostas

Código: 200<br>
OK

```json
{
  "count": 2,
  "result": [
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    },
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    }
  ]
}
```

Descrição do código de `BatchUsageEvent` status referenciado na resposta à API:

| Código de status  | Descrição |
| ---------- | -------------------- |
| `Accepted` | Código aceito. |
| `Expired` | Uso expirado. |
| `Duplicate` | Uso duplicado fornecido. |
| `Error` | Código do erro. |
| `ResourceNotFound` | O recurso de uso fornecido é inválido. |
| `ResourceNotAuthorized` | Você não está autorizado a fornecer uso para este recurso. |
| `InvalidDimension` | A dimensão para a qual o uso é passado é inválida para esta oferta/plano. |
| `InvalidQuantity` | A quantidade passada é < 0. |
| `BadArgument` | A entrada está faltando ou malformada. |

Código: 400<br>
Má solicitação, dados ausentes ou inválidos fornecidos ou expirados

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "Invalid data format.",
      "target": "usageEventRequest",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```
Código: 403<br>
O usuário não está autorizado a fazer esta chamada

```json
{
  "code": "Forbidden",
  "message": "User is not allowed to call this"
}
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [o faturamento medido do SaaS](./saas-metered-billing.md).
