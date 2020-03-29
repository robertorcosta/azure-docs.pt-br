---
title: Entrega e repetição - Azure Event Grid IoT Edge | Microsoft Docs
description: Entrega e nova tentativa em Event Grid em IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7df283b12a0d04d2b785c13a2f12b03115581e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841705"
---
# <a name="delivery-and-retry"></a>Entregar e tentar novamente

A entrega proporcionada pela Grade de Eventos tem um tempo de duração. Ele tenta entregar cada mensagem pelo menos uma vez para cada assinatura correspondente imediatamente. Se o ponto final de um assinante não reconhecer o recebimento de um evento ou se houver uma falha, a Event Grid retentará a entrega com base em um cronograma fixo de **reavaliação** e **política de repetição**.  Por padrão, o módulo Event Grid oferece um evento de cada vez ao assinante. A carga útil é, no entanto, uma matriz com um único evento. Você pode fazer com que o módulo entregue mais de um evento por vez, habilitando o recurso de loteamento de saída. Para obter detalhes sobre esse recurso, consulte [o lote de saída](delivery-output-batching.md).  

> [!IMPORTANT]
>Não há suporte de persistência para dados de eventos. Isso significa que a reimplantação ou a reinicialização do módulo Event Grid farão com que você perca quaisquer eventos que ainda não foram entregues.

## <a name="retry-schedule"></a>Cronograma de repetição

Event Grid espera até 60 segundos por uma resposta após a entrega de uma mensagem. Se o ponto final do assinante não aderse à resposta, então a mensagem será enfileirada em uma de nossas filas de back off para repetições subseqüentes.

Existem duas filas de recuo pré-configuradas que determinam o cronograma no qual uma nova tentativa será tentada. Eles são:

| Agenda | Descrição |
| ---------| ------------ |
| 1 minuto | Mensagens que acabam aqui são tentadas a cada minuto.
| 10 minutos | Mensagens que acabam aqui são tentadas a cada 10 minutos.

### <a name="how-it-works"></a>Como ele funciona

1. A mensagem chega ao módulo Event Grid. Tenta-se entregá-lo imediatamente.
1. Se a entrega falhar, a mensagem é enfileirada em fila de 1 minuto e tenta novamente após um minuto.
1. Se a entrega continuar falhando, a mensagem é enfileirada em fila de 10 minutos e tenta novamente a cada 10 minutos.
1. As entregas são tentadas até que os limites de política bem-sucedidos ou de repetição sejam alcançados.

## <a name="retry-policy-limits"></a>Retente os limites da política

Existem duas configurações que determinam a política de reteste. Eles são:

* Número máximo de tentativas
* Tempo de evento ao vivo (TTL)

Um evento será descartado se qualquer um dos limites da política de repetição for atingido. O cronograma de repetição foi descrito na seção Agenda de Repetição. A configuração desses limites pode ser feita tanto para todos os assinantes quanto por base de assinatura. A seção a seguir descreve que cada um é mais detalhado.

## <a name="configuring-defaults-for-all-subscribers"></a>Configuração de padrões para todos os assinantes

Existem duas `brokers__defaultMaxDeliveryAttempts` propriedades: e `broker__defaultEventTimeToLiveInSeconds` que podem ser configuradas como parte da implantação do Event Grid, que controla os padrões de política de reteste para todos os assinantes.

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Número máximo de tentativas para entregar um evento. Valor padrão: 30.
| `broker__defaultEventTimeToLiveInSeconds` | Evento TTL em segundos após o qual um evento será descartado se não for entregue. Valor padrão: **7200** segundos

## <a name="configuring-defaults-per-subscriber"></a>Configuração de padrões por assinante

Você também pode especificar limites de política de repetição por assinatura.
Consulte nossa [documentação de API](api.md) para obter informações sobre como configurar padrões por assinante. Os padrões de nível de assinatura anulam as configurações de nível de módulo.

## <a name="examples"></a>Exemplos

O exemplo a seguir configura a política de repetição no módulo Event Grid com maxNumberOfAttempts = 3 e Event TTL de 30 minutos

```json
{
  "Env": [
    "broker__defaultMaxDeliveryAttempts=3",
    "broker__defaultEventTimeToLiveInSeconds=1800"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

O exemplo a seguir configura uma assinatura de gancho web com maxNumberOfAttempts = 3 e Event TTL de 30 minutos

```json
{
 "properties": {
  "destination": {
   "endpointType": "WebHook",
   "properties": {
    "endpointUrl": "<your_webhook_url>",
    "eventDeliverySchema": "eventgridschema"
   }
  },
  "retryPolicy": {
   "eventExpiryInMinutes": 30,
   "maxDeliveryAttempts": 3
  }
 }
}
```
