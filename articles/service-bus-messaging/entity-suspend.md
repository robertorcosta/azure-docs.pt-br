---
title: Ônibus de Serviço Azure - suspender entidades de mensagens
description: Este artigo explica como suspender e reativar temporariamente as entidades de mensagens de ônibus de serviço do Azure (filas, tópicos e assinaturas).
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 7386932f19eee064926184eb17f5e92e30add98e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760378"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Suspender e reativar as entidades de mensagens (desabilitar)

Filas, tópicos e assinaturas podem ser suspensos temporariamente. A suspensão coloca a entidade em um estado desabilitado em que todas as mensagens são mantidas no armazenamento. No entanto, as mensagens não podem ser removidas ou adicionadas e as respectivas operações de protocolo geram erros.

A suspensão de uma entidade normalmente é feita por razões administrativas urgentes. Um cenário é ter implantado um destinatário com falha que recebe as mensagens da fila, falha no processamento e ainda completa as mensagens de forma incorreta e as remove. Se esse comportamento é diagnosticado, a fila pode ser desabilitada para recebimentos até que o código corrigido seja implantado e ainda mais a perda de dados causada pelo código com falha possa ser evitada.

Uma suspensão ou reativação pode ser executada pelo usuário ou pelo sistema. O sistema suspende entidades apenas por razões administrativas graves como atingir a limite de gastos de assinatura. As entidades desabilitadas pelo sistema não podem ser reativadas pelo usuário, mas serão restauradas quando a causa a suspensão tiver sido resolvida.

No portal, a seção **Propriedades** para a respectiva entidade permite alterar o estado; a captura de tela a seguir mostra o alternador para uma fila:

![][1]

O portal permite apenas desabilitar completamente filas. Você também pode desabilitar as operações de envio e recebimento separadamente usando as APIs de Barramento de Serviço [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) no SDK do .NET Framework, ou com um modelo do Azure Resource Manager por meio da CLI do Azure ou o Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="suspension-states"></a>Estados de suspensão

Os estados que podem ser definidos para uma fila são:

-   **Active**: a fila está ativa.
-   **Disabled**: a fila está suspensa.
-   **SendDisabled**: a fila está parcialmente suspensa, com o recebimento sendo permitido.
-   **ReceiveDisabled**: a fila está parcialmente suspensa, com o envio sendo permitido.

Para assinaturas e tópicos, é possível definir apenas **Active** e **Disabled**.

A enumeração [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) também define um conjunto de estados de transição que podem ser definidos apenas pelo sistema. O comando do PowerShell para desabilitar uma fila é mostrado no exemplo a seguir. O comando de reativação é equivalente, definindo `Status` como **Active**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as mensagens do Barramento de Serviço, consulte os seguintes tópicos:

* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png

