---
title: APIs de serviço de medição – Marketplace comercial da Microsoft
description: A API de evento de uso permite emitir eventos de uso para ofertas de SaaS no Microsoft AppSource e no Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 95eba648219413923ce27d433a5236877c4953f3
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725458"
---
# <a name="marketplace-metering-service-apis"></a>APIs de serviço de medição do Marketplace

A API de evento de uso permite emitir eventos de uso para uma entidade específica que tenha sido adquirida. A solicitação de evento de uso faz referência à dimensão de serviços de medição definida pelo publicador ao publicar a oferta.

## <a name="usage-event"></a>Evento de uso

**POST**: `https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | A versão da operação a ser usada para esta solicitação. A versão mais recente da API é 2018-08-31. |

*Cabeçalhos de solicitação:*

| Content-type       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| `x-ms-correlationid` | Valor de cadeia de caracteres exclusivo para a operação no cliente. Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor. Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
| `authorization`   | [Obtenha o token de portador Token Web JSON (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Observação: Quando fizer a solicitação HTTP, coloque o prefixo `Bearer` para o token obtido do link referenciado. |

>[!Note]
>Para planos de Aplicativos Gerenciados do Aplicativo Azure, a `resourceId` é a `resourceUsageId` encontrada no `billingDetails` do objeto de metadados do Aplicativo Gerenciado.  Um script de exemplo para buscá-los pode ser encontrado em [usando o token de identidades gerenciadas pelo Azure](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token).  Para ofertas de SaaS, a `resourceId` é a ID de assinatura do SaaS.  Para obter mais detalhes sobre assinaturas de SaaS, veja [listar assinaturas](./pc-saas-fulfillment-api-v2.md#list-subscriptions).

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
Solicitação inválida, dados ausentes ou inválidos fornecidos ou expirados

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
Solicitação inválida, dados ausentes ou inválidos fornecidos ou expirados

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

Código: 409<br>
Conflito, quando recebemos a chamada de uso para a ID de recurso de uso e o uso efetivo, que já existe. A resposta conterá o campo `additionalInfo` que contém informações sobre a mensagem aceita.

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

A API de evento de uso em lote permite emitir eventos de uso para mais uma entidade que tenha sido adquirida, de uma só vez. A solicitação de evento de uso em lote faz referência à dimensão de serviços de medição definida pelo publicador ao publicar a oferta.

>[!Note]
>Você pode registrar várias ofertas de SaaS no Marketplace comercial da Microsoft. Cada oferta de SaaS registrada tem um aplicativo exclusivo do Azure AD que é registrado para fins de autenticação e autorização. Os eventos emitidos em lote devem pertencer a ofertas com o mesmo aplicativo do Azure AD no momento do registro da oferta.

**POST:** `https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | A versão da operação a ser usada para esta solicitação. A versão mais recente da API é 2018-08-31. |

*Cabeçalhos de solicitação:*

| Content-type       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | Valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
| `x-ms-correlationid` | Valor de cadeia de caracteres exclusivo para a operação no cliente. Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor. Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
| `authorization`      | [Obtenha o token de portador Token Web JSON (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Observação: Quando fizer a solicitação HTTP, coloque o prefixo `Bearer` para o token obtido do link referenciado.  |

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

Descrição do código de status referenciado na resposta da API `BatchUsageEvent`:

| Código de status  | Descrição |
| ---------- | -------------------- |
| `Accepted` | Código aceito. |
| `Expired` | Uso expirado. |
| `Duplicate` | Uso duplicado fornecido. |
| `Error` | Código do erro. |
| `ResourceNotFound` | O recurso de uso fornecido é inválido. |
| `ResourceNotAuthorized` | Você não está autorizado a fornecer uso para este recurso. |
| `InvalidDimension` | A dimensão para a qual o uso foi passado é inválida para esta oferta/plano. |
| `InvalidQuantity` | A quantidade passada é < 0. |
| `BadArgument` | A entrada está ausente ou mal formulada. |

Código: 400<br>
Solicitação inválida, dados ausentes ou inválidos fornecidos ou expirados

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

Para obter mais informações, veja [Cobrança limitada de SaaS](./saas-metered-billing.md).
