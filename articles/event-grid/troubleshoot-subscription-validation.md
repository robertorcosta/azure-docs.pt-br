---
title: Grade de eventos do Azure-Solucionando problemas de validação de assinatura
description: Este artigo mostra como você pode solucionar problemas de validações de assinatura.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: spelluru
ms.openlocfilehash: a052d4c268fadc60f754630156fe0bc0d33acf3b
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82630181"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Solucionar erros de grade de eventos do Azure
Este guia de solução de problemas fornece informações sobre como solucionar problemas de validações de assinatura de evento. 


## <a name="troubleshoot-event-subscription-validation"></a>Solucionar problemas de validação de assinatura de evento

Durante a criação da assinatura do evento, se você estiver vendo uma mensagem `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation`de erro como, isso indica que há uma falha no handshake de validação. Para resolver esse erro, verifique os seguintes aspectos:

- Faça um HTTP POST para a URL do webhook com um corpo de solicitação [SubscriptionValidationEvent de exemplo](webhook-event-delivery.md#validation-details) usando o postmaster ou a ondulação ou ferramenta semelhante.
- Se o webhook estiver implementando o mecanismo de handshake de validação síncrona, verifique se o ValidationCode é retornado como parte da resposta.
- Se o seu webhook estiver implementando o mecanismo de handshake de validação assíncrona, verifique se você é o HTTP POST está retornando 200 OK.
- Se seu webhook estiver retornando 403 (proibido) na resposta, verifique se o webhook está atrás de um gateway de Aplicativo Azure ou de um firewall do aplicativo Web. Se for, você precisará desabilitar essas regras de firewall e executar um HTTP POST novamente:

  920300 (a solicitação não tem um cabeçalho Accept, podemos corrigir isso)

  942430 (detecção de anomalias de caracteres SQL restritos (args): número de caracteres especiais excedido (12))

  920230 (várias codificações de URL detectadas)

  942130 (ataque de injeção de SQL: SQL tautology detectado.)

  931130 (possível ataque de RFI (inclusão de arquivo remoto) = link/referência fora do domínio)

> [!IMPORTANT]
> Para obter informações detalhadas sobre a validação de ponto de extremidade para WebHooks, consulte [entrega de eventos de webhook](webhook-event-delivery.md).

## <a name="validate-event-grid-event-subscription-using-postman"></a>Validar a assinatura de evento da grade de eventos usando o postmaster
Veja um exemplo de como usar o postmaster para validar uma assinatura de webhook de um evento de grade de eventos: 

![Validação de assinatura de evento de grade de eventos usando o postmaster](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

Aqui está um exemplo de JSON SubscriptionValidationEvent:

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

Este é o exemplo de resposta bem-sucedida:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Para saber mais sobre a validação de eventos da grade de eventos para WebHooks, consulte [validação do ponto de extremidade com eventos da grade de eventos](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).

## <a name="validate-cloud-event-subscription-using-postman"></a>Validar a assinatura de evento de nuvem usando o postmaster
Veja um exemplo de como usar o postmaster para validar uma assinatura de webhook de um evento de nuvem: 

![Validação de assinatura de evento de nuvem usando o postmaster](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

Use o método de **Opções http** para validação com eventos de nuvem. Para saber mais sobre a validação de eventos de nuvem para WebHooks, consulte [validação de ponto de extremidade com eventos de nuvem](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).

## <a name="event-grid-event-subscription-validation-using-curl"></a>Validação de assinatura de evento de grade de eventos usando ondulação 
Aqui está o exemplo de comando de ondulação para validar uma assinatura de webhook de um evento de grade de eventos: 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="next-steps"></a>Próximas etapas
Se precisar de mais ajuda, poste seu problema no [Fórum de Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) ou abra um [tíquete de suporte](https://azure.microsoft.com/support/options/). 
