---
title: arquivo de inclusão
description: arquivo de inclusão
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a0d1de622eefad4ae5e55a427f8b0b1bf4360c0a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84792075"
---
Você pode usar a integridade do [ponto de extremidade Get](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) da API REST para obter o status de integridade dos pontos de extremidade. É recomendável usar as [métricas do Hub IOT](../articles/iot-hub/iot-hub-metrics.md) relacionadas à latência da mensagem de roteamento para identificar e depurar erros quando a integridade do ponto de extremidade está inativa ou não íntegra, pois esperamos que a latência seja maior quando o ponto de extremidade estiver em um desses Estados.


|Status de integridade|Descrição|
|---|---|
|Healthy|O ponto de extremidade está aceitando mensagens conforme o esperado.|
|não íntegro|O ponto de extremidade não está aceitando mensagens e o Hub IoT está tentando novamente enviar mensagens para esse ponto de extremidade.|
|unknown|O Hub IoT não tentou entregar mensagens a este ponto de extremidade.|
|degradado|O ponto de extremidade está aceitando mensagens mais lentas do que o esperado ou está se recuperando de um estado não íntegro.|
|mortos|O Hub IoT não está mais entregando mensagens para este ponto de extremidade. Falha ao tentar enviar mensagens para este ponto de extremidade.|
