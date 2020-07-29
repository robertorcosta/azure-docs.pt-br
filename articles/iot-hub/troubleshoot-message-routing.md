---
title: Solucionar problemas de roteamento de mensagens do Azure IoT
description: Como executar a solução de problemas para o roteamento de mensagens do Azure IoT
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: asrastog
ms.openlocfilehash: 871a4c7d99fc44cf9868f19e41560e6e7a2e22f1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84793277"
---
# <a name="troubleshooting-message-routing"></a>Solucionando problemas de roteamento de mensagens

Este artigo fornece diretrizes de monitoramento e solução de problemas para problemas comuns e resolução para o [Roteamento de mensagens](iot-hub-devguide-messages-d2c.md)do Hub IOT. 

## <a name="monitoring-message-routing"></a>Monitorando roteamento de mensagens

[Métricas do Hub IoT](iot-hub-metrics.md) lista todas as métricas que são habilitadas por padrão para o Hub IoT. Recomendamos que você monitore as métricas relacionadas ao roteamento e aos pontos de extremidade de mensagens para fornecer uma visão geral das mensagens enviadas. Além disso, ative [os logs de diagnóstico](iot-hub-monitor-resource-health.md) em Azure monitor configurações de diagnóstico para controlar as operações de **rotas**. Esses logs de diagnóstico podem ser enviados para Azure Monitor logs, hubs de eventos ou armazenamento do Azure para processamento personalizado. Saiba como [configurar e usar logs de diagnóstico e métricas com um hub IOT](tutorial-use-metrics-and-diags.md).

Também recomendamos habilitar a [rota de fallback](iot-hub-devguide-messages-d2c.md#fallback-route) se você quiser manter mensagens que não correspondam à consulta em nenhuma das rotas. Eles podem ser retidos no [ponto de extremidade interno](iot-hub-devguide-messages-read-builtin.md) para a quantidade de dias de retenção configurados. 

## <a name="top-issues"></a>Principais problemas

A seguir estão os problemas mais comuns observados com o roteamento de mensagens. Para iniciar a solução de problemas, clique no problema para obter as etapas detalhadas.

* [As mensagens de meus dispositivos não estão sendo roteadas conforme o esperado](#messages-from-my-devices-are-not-being-routed-as-expected)
* [Repentinamente, parei de receber mensagens no ponto de extremidade dos hubs de eventos internos](#i-suddenly-stopped-getting-messages-at-the-built-in-endpoint)

### <a name="messages-from-my-devices-are-not-being-routed-as-expected"></a>As mensagens de meus dispositivos não estão sendo roteadas conforme o esperado

Para solucionar esse problema, analise o seguinte.

#### <a name="the-routing-metrics-for-this-endpoint"></a>As métricas de roteamento para este ponto de extremidade
Todas as [métricas do Hub IOT](iot-hub-devguide-endpoints.md) relacionadas ao roteamento são prefixadas com o *Roteamento*. Você pode combinar informações de várias métricas para identificar a causa raiz de problemas. Por exemplo, use a **entrega de roteamento** de métricas para identificar o número de mensagens que foram entregues a um ponto de extremidade ou foram descartadas quando não corresponderem a consultas em nenhuma das rotas e a rota de fallback foi desabilitada. Verifique a métrica de **latência de roteamento** para observar se a latência de entrega de mensagem está estável ou aumentando. Uma latência crescente pode indicar um problema com um ponto de extremidade específico e é recomendável verificar [a integridade do ponto de extremidade](#the-health-of-the-endpoint). Essas métricas de roteamento também têm [dimensões](iot-hub-metrics.md#dimensions) que fornecem detalhes sobre a métrica como o tipo de ponto de extremidade, nome de ponto de extremidade específico e um motivo pelo qual a mensagem não foi entregue.

#### <a name="the-diagnostic-logs-for-any-operational-issues"></a>Os logs de diagnóstico para quaisquer problemas operacionais 
Observe os **routes** [logs de diagnóstico](iot-hub-monitor-resource-health.md#routes) de rotas para obter mais informações sobre as [operações](#operation-names) de roteamento e ponto de extremidade, ou identificar erros e [código de erro](#common-error-codes) relevante para entender ainda mais o problema. Por exemplo, o nome da operação **RouteEvaluationError** no log indica que a rota não pôde ser avaliada devido a um problema com o formato da mensagem. Use as dicas fornecidas para os [nomes de operação](#operation-names) específicos para atenuar o problema. Quando um evento é registrado como um erro, o log também fornecerá mais informações sobre o motivo da falha na avaliação. Por exemplo, se o nome da operação for **EndpointUnhealthy**, os [códigos de erro](#common-error-codes) 403004 indicarão que o ponto de extremidade ficou sem espaço.

#### <a name="the-health-of-the-endpoint"></a>A integridade do ponto de extremidade
Use a integridade de ponto de [extremidade Get](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) da API REST para obter o [status de integridade](iot-hub-devguide-endpoints.md#custom-endpoints) dos pontos de extremidade. A API *obter integridade do ponto de extremidade* também fornece informações sobre a última vez em que uma mensagem foi enviada com êxito ao ponto de extremidade, o [último erro conhecido](#last-known-errors-for-iot-hub-routing-endpoints), o último tempo de erro conhecido e a última vez que uma tentativa de envio foi feita para esse ponto de extremidade. Use a atenuação possível fornecida para o [último erro conhecido](#last-known-errors-for-iot-hub-routing-endpoints)específico.

### <a name="i-suddenly-stopped-getting-messages-at-the-built-in-endpoint"></a>Repentinamente, parei de receber mensagens no ponto de extremidade interno

Para solucionar esse problema, analise o seguinte.

#### <a name="was-a-new-route-created"></a>Uma nova rota foi criada?
Depois que uma rota é criada, os dados param de fluir para o ponto de extremidade interno, a menos que uma rota seja criada para esse ponto de extremidade. Para garantir que as mensagens continuem a fluir para o ponto de extremidade interno se uma nova rota for adicionada, configure uma rota para o ponto de extremidade de *eventos* . 

#### <a name="was-the-fallback-route-disabled"></a>A rota de fallback foi desabilitada?
A rota de fallback envia todas as mensagens que não satisfazem as condições de consulta em nenhuma das rotas existentes para os [hubs de eventos internos](iot-hub-devguide-messages-read-builtin.md) (mensagens/eventos) que são compatíveis com os [hubs de eventos](https://docs.microsoft.com/azure/event-hubs/). Se o roteamento de mensagens estiver habilitado, você poderá habilitar a funcionalidade de rota de fallback. Se não houver nenhuma rota para o ponto de extremidade interno e houver uma rota de fallback habilitada, somente as mensagens que não corresponderem a nenhuma condição de consulta nas rotas serão enviadas ao ponto de extremidade interno. Além disso, se todas as rotas existentes forem excluídas, a rota de fallback precisará ser habilitada para receber todos os dados no ponto de extremidade interno.

Você pode habilitar/desabilitar a rota de fallback na folha de roteamento de mensagens portal do Azure >. Você também pode usar o Azure Resource Manager para [FallbackRouteProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) para usar um ponto de extremidade personalizado para a rota de fallback.

## <a name="last-known-errors-for-iot-hub-routing-endpoints"></a>Últimos erros conhecidos para pontos de extremidade de roteamento do Hub IoT

<a id="last-known-errors"></a>
[!INCLUDE [iot-hub-include-last-known-errors](../../includes/iot-hub-include-last-known-errors.md)]

## <a name="routes-diagnostic-logs"></a>Encaminha os logs de diagnóstico

A seguir estão os nomes de operação e os códigos de erro registrados nos [logs de diagnóstico](iot-hub-monitor-resource-health.md#routes).

<a id="diagnostics-operation-names"></a>
### <a name="operation-names"></a>Nomes de operação

[!INCLUDE [iot-hub-diagnostics-operation-names](../../includes/iot-hub-diagnostics-operation-names.md)]

<a id="diagnostics-error-codes"></a>
### <a name="common-error-codes"></a>Códigos de erro comuns

[!INCLUDE [iot-hub-diagnostics-error-codes](../../includes/iot-hub-diagnostics-error-codes.md)]

## <a name="next-steps"></a>Próximas etapas

Se precisar de mais ajuda, você pode contatar os especialistas do Azure nos [fóruns do MSDN do Azure e nos fóruns do Stack Overflow](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
