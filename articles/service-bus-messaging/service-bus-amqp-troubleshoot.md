---
title: Solução de problemas de erros amqp no ônibus de serviço Azure | Microsoft Docs
description: Fornece uma lista de erros AMQP que você pode receber ao usar o Ônibus de Serviço Do Azure e a causa desses erros.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2019
ms.author: aschhab
ms.openlocfilehash: 85d24a9e7c753ec5dba80c4f259dd3fb51d9c14b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "60402778"
---
# <a name="amqp-errors-in-azure-service-bus"></a>Erros amqp no ônibus de serviço do Azure
Este artigo fornece alguns dos erros que você recebe ao usar amqp com ônibus de serviço Azure. São todos comportamentos padrão do serviço. Você pode evitá-las fazendo chamadas de envio/recebimento no link de conexão/conexão, que recria automaticamente a conexão/link.

## <a name="link-is-closed"></a>Link está fechado 
Você vê o erro a seguir quando a conexão amqp e o link estão ativos, mas nenhuma chamada (por exemplo, enviar ou receber) é feita usando o link por 10 minutos. Então, o link está fechado. A conexão ainda está aberta.

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>A conexão está fechada
Você vê o erro a seguir na conexão AMQP quando todos os links na conexão foram fechados porque não havia atividade (ociosa) e um novo link não foi criado em 5 minutos.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>Link não é criado 
Você vê esse erro quando uma nova conexão AMQP é criada, mas um link não é criado dentro de 1 minuto da criação da Conexão AMQP.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o AMQP e o Barramento de Serviço, visite os seguintes links:

* [Visão geral do Barramento de Serviço para AMQP]
* [Guia do protocolo AMQP 1.0]
* [AMQP no Barramento de Serviço para Windows Server]

[Visão geral do Barramento de Serviço para AMQP]: service-bus-amqp-overview.md
[Guia do protocolo AMQP 1.0]: service-bus-amqp-protocol-guide.md
[AMQP no Barramento de Serviço para Windows Server]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
