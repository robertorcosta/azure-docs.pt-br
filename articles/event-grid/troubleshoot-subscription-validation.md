---
title: Grade de Eventos do Azure – Solucionar problemas de validação de assinatura
description: Este artigo mostra como você pode solucionar problemas de validação de assinatura.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: spelluru
ms.openlocfilehash: f292d70eaaca29e714ea35b4f61a141b2d5cd2b6
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83778709"
---
# <a name="troubleshoot-azure-event-grid-subscription-validations"></a>Solucionar problemas de validações de assinatura da Grade de Eventos do Azure
Este artigo fornece informações sobre como solucionar problemas de validações de assinatura de evento. 

> [!IMPORTANT]
> Para informações detalhadas sobre a validação de ponto de extremidade para webhooks, confira [Entrega de eventos de webhook](webhook-event-delivery.md).

## <a name="validate-event-grid-event-subscription-using-postman"></a>Validar a assinatura de evento da Grade de Eventos com o Postman
Confira um exemplo de como usar o Postman para validar uma assinatura de webhook de um evento da Grade de Eventos: 

![Validação de assinatura de evento da Grade de Eventos com o Postman](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

Confira aqui o exemplo JSON **SubscriptionValidationEvent**:

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

Este é um exemplo de resposta bem-sucedida:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Para saber mais sobre a validação de evento da Grade de Eventos para webhooks, confira [Validação de ponto de extremidade com eventos da Grade de Eventos](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).


## <a name="validate-event-grid-event-subscription-using-curl"></a>Validar a assinatura de evento da Grade de Eventos com Curl 
Aqui está um exemplo do comando Curl para validar uma assinatura de webhook em um evento da Grade de Eventos: 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="validate-cloud-event-subscription-using-postman"></a>Validar a assinatura de evento em nuvem com o Postman
Confira um exemplo de como usar o Postman para validar uma assinatura de webhook de um evento em nuvem: 

![Validação de assinatura de evento em nuvem com o Postman](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

Use o método **HTTP OPTIONS** para a validação com eventos em nuvem. Para saber mais sobre a validação de eventos em nuvem para webhooks, confira [Validação de ponto de extremidade com eventos em nuvem](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).

## <a name="error-code-403"></a>Código de erro: 403
Se seu webhook estiver retornando 403 (Proibido) na resposta, verifique se o webhook está atrás de um Gateway de Aplicativo Azure AD ou de um firewall do aplicativo Web. Em caso positivo, você precisará desabilitar as seguintes regras de firewall e executar um HTTP POST novamente:

  - 920300 (Pedido Sem Cabeçalho de Aceitação, podemos consertar isso)
  - 942430 (Detecção de Anomalias de Caractere SQL Restrito (args): # de caracteres especiais excedido (12))
  - 920230 (Várias Codificações de URL Detectadas)
  - 942130 (Ataque de Injeção de SQL: Tautologia do SQL Detectada.)
  - 931130 (Possível Ataque de Remoção de Arquivo (RFI) = link/referência fora do domínio)

## <a name="next-steps"></a>Próximas etapas
Se precisar de mais ajuda, poste seu problema no [fórum do Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) ou abra um [tíquete de suporte](https://azure.microsoft.com/support/options/). 
