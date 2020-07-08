---
title: incluir arquivo
description: incluir arquivo
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 04/28/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 362c13771e7382ead1ba5aebd99a69fd86cd718c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84793267"
---
<!-- operation names for the diag logs for IoT Hub -->

|Nome de operação|Nível|Descrição|
|------------- |-----|-----------|
|UndefinedRouteEvaluation|Informações|A mensagem não pode ser avaliada com uma condição de concessão. Por exemplo, se uma propriedade na condição de consulta de rota estiver ausente na mensagem. Saiba mais sobre a [sintaxe de consulta de roteamento](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax).|
|RouteEvaluationError|Erro|Erro ao avaliar a mensagem devido a um problema com o formato da mensagem. Por exemplo, esse erro será registrado se a codificação de conteúdo não for especificada ou o tipo de conteúdo não for válido na mensagem. Elas devem ser definidas nas [Propriedades do sistema](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties).|
|DroppedMessage|Erro|A mensagem foi removida e não foi roteada. Isso pode ser devido a motivos, pois a mensagem não correspondeu a nenhuma consulta de roteamento ou o ponto de extremidade estava inativo e a mensagem não pôde ser entregue após várias tentativas. É recomendável obter mais detalhes sobre o ponto de extremidade usando a [integridade de ponto de extremidade Get](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)da API REST.|
|EndpointUnhealthy|Erro|O ponto de extremidade não está aceitando mensagens do Hub IoT e o Hub IoT está tentando reenviar as mensagens. É recomendável observar o último erro conhecido por meio da API REST [obter integridade do ponto de extremidade](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth).|
|EndpointDead|Erro|O ponto de extremidade não está aceitando mensagens do Hub IoT por mais de uma hora. É recomendável observar o último erro conhecido por meio da API REST [obter integridade do ponto de extremidade](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth).|
|EndpointHealthy|Informações|O ponto de extremidade está íntegro e recebendo mensagens do Hub IoT. Essa mensagem não é registrada continuamente, mas registrada somente quando o ponto de extremidade se torna íntegro novamente. Essa mensagem significa que o Hub IoT não pôde enviar mensagens para o ponto de extremidade, mas o ponto de extremidade agora está íntegro.|
|OrphanedMessage|Informações|A mensagem não corresponde a nenhuma rota.|
|InvalidMessage|Erro|A mensagem é inválida devido à incompatibilidade com o ponto de extremidade. É recomendável verificar as configurações do ponto de extremidade.|


As operações *UndefinedRouteEvaluation*, *RouteEvaluationError* e *OrphanedMessage* são limitadas e não registradas mais de uma vez por minuto por Hub IOT.