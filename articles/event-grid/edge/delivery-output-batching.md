---
title: Loteamento de saída em Azure Event Grid IoT Edge | Microsoft Docs
description: Loteamento de saída em Event Grid em IoT Edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: a6f033af34088081090251f2e5e7cd4a07ce43cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841740"
---
# <a name="output-batching"></a>Envio em lote de saída

Event Grid tem suporte para entregar mais de um evento em uma única solicitação de entrega. Esse recurso permite aumentar o throughput de entrega global sem pagar as despesas gerais por solicitação. O lote é desligado por padrão e pode ser ligado por assinatura.

> [!WARNING]
> A duração máxima permitida para processar cada solicitação de entrega não muda, embora o código do assinante potencialmente tenha que fazer mais trabalho por solicitação em lote. O tempo de intervalo de entrega é padrão para 60 segundos.

## <a name="batching-policy"></a>Política de loteamento

O comportamento de loteamento do Event Grid pode ser personalizado por assinante, ajustando as duas configurações a seguir:

* Eventos máximos por lote

  Essa configuração estabelece um limite superior no número de eventos que podem ser adicionados a uma solicitação de entrega em lote.

* Tamanho do lote preferido em kilobytes

  Este botão é usado para controlar ainda mais o número máximo de kilobytes que podem ser enviados por solicitação de entrega

## <a name="batching-behavior"></a>Comportamento de loteamento

* Tudo ou nenhum

  Event Grid opera com semântica tudo ou nenhum. Não suporta o sucesso parcial de uma entrega em lote. Os assinantes devem ter o cuidado de pedir apenas o máximo de eventos por lote que puderem lidar razoavelmente em 60 segundos.

* Loteamento otimista

  As configurações da diretiva de loteamento não são limites rígidos sobre o comportamento de loteamento e são respeitadas com base no melhor esforço. Com baixas taxas de eventos, muitas vezes você observará o tamanho do lote sendo menor do que os eventos máximos solicitados por lote.

* O padrão é definido como OFF

  Por padrão, event grid adiciona apenas um evento a cada solicitação de entrega. A maneira de ativar o loteamento é definir qualquer uma das configurações mencionadas anteriormente no artigo no evento de assinatura JSON.

* Valores padrão

  Não é necessário especificar as configurações (Máximo de eventos por lote e tamanho de lote aproximado em bytes de quilo) ao criar uma assinatura de evento. Se apenas uma configuração estiver definida, a Grade de Eventos usará os valores padrão (configuráveis). Consulte as seções a seguir para os valores padrão e como substituí-los.

## <a name="turn-on-output-batching"></a>Ativar o lote amento de saída

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
             {
                "endpointUrl": "<your_webhook_url>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 64
             }
        },
    }
}
```

## <a name="configuring-maximum-allowed-values"></a>Configuração de valores máximos permitidos

As configurações de tempo de implantação a seguir controlam o valor máximo permitido ao criar uma assinatura de evento.

| Nome da propriedade | Descrição |
| ------------- | ----------- | 
| `api__deliveryPolicyLimits__maxpreferredBatchSizeInKilobytes` | Valor máximo permitido `PreferredBatchSizeInKilobytes` para o botão. Padrão `1033`.
| `api__deliveryPolicyLimits__maxEventsPerBatch` | Valor máximo permitido `MaxEventsPerBatch` para o botão. Padrão `50`.

## <a name="configuring-runtime-default-values"></a>Configuração de valores padrão de tempo de execução

As configurações de tempo de implantação a seguir controlam o valor padrão de tempo de execução de cada botão quando ele não está especificado na Assinatura de Evento. Para reiterar, pelo menos um botão deve ser configurado na Assinatura de Eventos para ativar o comportamento de loteamento.

| Nome da propriedade | Descrição |
| ------------- | ----------- |
| `broker__defaultMaxBatchSizeInBytes` | Tamanho máximo da `MaxEventsPerBatch` solicitação de entrega quando apenas é especificado. Padrão `1_058_576`.
| `broker__defaultMaxEventsPerBatch` | Número máximo de eventos a serem `MaxBatchSizeInBytes` adicionados a um lote quando apenas é especificado. Padrão `10`.
