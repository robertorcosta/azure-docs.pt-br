---
title: Monitorar tópicos e assinaturas de evento-grade de eventos do Azure IoT Edge | Microsoft Docs
description: Monitorar tópicos e assinaturas de evento
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ce7c92f121fb458d528d63d0af0aad025b377386
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086667"
---
# <a name="monitor-topics-and-event-subscriptions"></a>Monitorar tópicos e assinaturas de evento

A grade de eventos no Edge expõe um número de métricas para tópicos e assinaturas de evento no [formato Prometheus exposição](https://prometheus.io/docs/instrumenting/exposition_formats/). Este artigo descreve as métricas disponíveis e como habilitá-las.

## <a name="enable-metrics"></a>Habilitar a métrica

Configure o módulo para emitir métricas definindo a variável de ambiente `metrics__reporterType` como `prometheus` nas opções de criação de contêiner:

 ```json
        {
          "Env": [
            "metrics__reporterType=prometheus"
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

As métricas estarão disponíveis em `5888/metrics` do módulo para http e `4438/metrics` para https. Por exemplo, `http://<modulename>:5888/metrics?api-version=2019-01-01-preview` para http. Neste ponto, um módulo de métricas pode sondar o ponto de extremidade para coletar métricas como nesta [arquitetura de exemplo](https://github.com/veyalla/ehm).

## <a name="available-metrics"></a>Métricas disponíveis

Os tópicos e as assinaturas de evento emitem as métricas para fornecer informações sobre a entrega de eventos e o desempenho do módulo.

### <a name="topic-metrics"></a>Métricas de tópico

| Métrica | DESCRIÇÃO |
| ------ | ----------- |
| EventsReceived | Número de eventos publicados no tópico
| UnmatchedEvents | Número de eventos publicados no tópico que não correspondem a uma assinatura de evento e são removidos
| SuccessRequests | Número de solicitações de publicação de entrada recebidas pelo tópico
| SystemErrorRequests | Número de solicitações de publicação de entrada com falha devido a um erro interno do sistema
| UserErrorRequests | Número de solicitações de publicação de entrada com falha devido a um erro do usuário, como JSON malformado
| SuccessRequestLatencyMs | Latência de resposta de solicitação de publicação em milissegundos


### <a name="event-subscription-metrics"></a>Métricas de assinatura de evento

| Métrica | DESCRIÇÃO |
| ------ | ----------- |
| deliverySuccessCounts | Número de eventos entregues com êxito ao ponto de extremidade configurado
| deliveryFailureCounts | Número de eventos que falharam ao serem entregues ao ponto de extremidade configurado
| deliverySuccessLatencyMs | Latência de eventos entregues com êxito em milissegundos
| deliveryFailureLatencyMs | Latência de falhas de entrega de eventos em milissegundos
| systemDelayForFirstAttemptMs | Atraso do sistema de eventos antes da primeira tentativa de entrega em milissegundos
| deliveryAttemptsCount | Número de tentativas de entrega de eventos-êxito e falha
| expiredCounts | Número de eventos que expiraram e não foram entregues ao ponto de extremidade configurado