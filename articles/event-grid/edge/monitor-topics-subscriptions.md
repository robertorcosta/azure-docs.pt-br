---
title: Monitore tópicos e assinaturas de eventos - Azure Event Grid IoT Edge | Microsoft Docs
description: Monitore tópicos e assinaturas de eventos
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ce7c92f121fb458d528d63d0af0aad025b377386
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086667"
---
# <a name="monitor-topics-and-event-subscriptions"></a>Monitore tópicos e assinaturas de eventos

Event Grid on Edge expõe uma série de métricas para tópicos e assinaturas de eventos no formato de [exposição Prometheus](https://prometheus.io/docs/instrumenting/exposition_formats/). Este artigo descreve as métricas disponíveis e como habilitá-las.

## <a name="enable-metrics"></a>Habilitar a métrica

Configure o módulo para emitir `metrics__reporterType` métricas `prometheus` definindo a variável ambiente para as opções de criação de contêiner:

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

As métricas estarão disponíveis no `5888/metrics` `4438/metrics` módulo para http e https. Por exemplo, `http://<modulename>:5888/metrics?api-version=2019-01-01-preview` para http. Neste ponto, um módulo de métricas pode sondar o ponto final para coletar métricas como neste [exemplo de arquitetura](https://github.com/veyalla/ehm).

## <a name="available-metrics"></a>Métricas disponíveis

Tanto os tópicos quanto as assinaturas de eventos emitem métricas para dar-lhe insights sobre a entrega de eventos e o desempenho do módulo.

### <a name="topic-metrics"></a>Métricas de tópicos

| Métrica | Descrição |
| ------ | ----------- |
| EventosRecebidos | Número de eventos publicados para o tema
| Eventos incomparáveis | Número de eventos publicados para o tópico que não correspondem a uma Assinatura de Evento e são descartados
| Pedidos de sucesso | Número de solicitações de publicação de entrada recebidas pelo tópico
| Solicitações de erro do sistema | Número de solicitações de publicação de entrada falhou devido a um erro interno do sistema
| Solicitações de erro do usuário | Número nas solicitações de publicação de entrada falhou devido a erro do usuário, como JSON malformado
| SuccessRequestLatencyMs | Publicar latência de resposta de solicitação em milissegundos


### <a name="event-subscription-metrics"></a>Métricas de assinatura de eventos

| Métrica | Descrição |
| ------ | ----------- |
| Entregaconta o sucesso | Número de eventos entregues com sucesso no ponto final configurado
| Contagem de falhas de entrega | Número de eventos que não foram entregues no ponto final configurado
| EntregaSucessoDebilidade | Latência de eventos entregues com sucesso em milissegundos
| EntregaFalhade atrasoMs | Latência de falhas de entrega de eventos em milissegundos
| SystemDelayForFirstAttemptMs | Atraso do sistema de eventos antes da primeira tentativa de entrega em milissegundos
| Contagem de tentativas de entrega | Número de tentativas de entrega de eventos - sucesso e fracasso
| Contagem expirada | Número de eventos que expiraram e não foram entregues ao ponto final configurado