---
title: Barramento de serviço do Azure – suspender entidades de mensagens
description: Este artigo explica como suspender e reativar temporariamente as entidades de mensagem do barramento de serviço do Azure (filas, tópicos e assinaturas).
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: f89e17e494cc777691b7f7ca47538cd29114d2dc
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575207"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Suspender e reativar as entidades de mensagens (desabilitar)

Filas, tópicos e assinaturas podem ser suspensos temporariamente. A suspensão coloca a entidade em um estado desabilitado em que todas as mensagens são mantidas no armazenamento. No entanto, as mensagens não podem ser removidas ou adicionadas e as respectivas operações de protocolo geram erros.

A suspensão de uma entidade normalmente é feita por razões administrativas urgentes. Um cenário é ter implantado um destinatário com falha que recebe as mensagens da fila, falha no processamento e ainda completa as mensagens de forma incorreta e as remove. Se esse comportamento é diagnosticado, a fila pode ser desabilitada para recebimentos até que o código corrigido seja implantado e ainda mais a perda de dados causada pelo código com falha possa ser evitada.

Uma suspensão ou reativação pode ser executada pelo usuário ou pelo sistema. O sistema suspende entidades apenas por razões administrativas graves como atingir a limite de gastos de assinatura. As entidades desabilitadas pelo sistema não podem ser reativadas pelo usuário, mas serão restauradas quando a causa a suspensão tiver sido resolvida.

## <a name="queue-status"></a>Status da fila 
Os estados que podem ser definidos para uma fila são:

-   **Active**: a fila está ativa.
-   **Disabled**: a fila está suspensa. É equivalente a definir **SendDisabled** e **ReceiveDisabled**. 
-   **SendDisabled**: a fila está parcialmente suspensa, com o recebimento sendo permitido.
-   **ReceiveDisabled**: a fila está parcialmente suspensa, com o envio sendo permitido.

### <a name="change-the-queue-status-in-the-azure-portal"></a>Altere o status da fila no portal do Azure: 

1. Na portal do Azure, navegue até o namespace do barramento de serviço. 
1. Selecione a fila para a qual você deseja alterar o status. Você vê as filas no painel inferior no meio. 
1. Na página **fila do barramento de serviço** , consulte o status atual da fila como um hiperlink. Se a **visão geral** não estiver selecionada no menu à esquerda, selecione-a para ver o status da fila. Selecione o status atual da fila para alterá-la. 

    :::image type="content" source="./media/entity-suspend/select-state.png" alt-text="Selecione o estado da fila":::
4. Selecione o novo status para a fila e selecione **OK**. 

    :::image type="content" source="./media/entity-suspend/entity-state-change.png" alt-text="Selecione o estado da fila":::
    
O portal permite apenas desabilitar completamente filas. Você também pode desabilitar as operações de envio e recebimento separadamente usando as APIs de Barramento de Serviço [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) no SDK do .NET Framework, ou com um modelo do Azure Resource Manager por meio da CLI do Azure ou o Azure PowerShell.

### <a name="change-the-queue-status-using-azure-powershell"></a>Alterar o status da fila usando Azure PowerShell
O comando do PowerShell para desabilitar uma fila é mostrado no exemplo a seguir. O comando de reativação é equivalente, definindo `Status` como **Active**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="topic-status"></a>Status do tópico
Alterar o status do tópico no portal do Azure é semelhante à alteração do status de uma fila. Ao selecionar o status atual do tópico, você verá a página a seguir que permite alterar o status. 

:::image type="content" source="./media/entity-suspend/topic-state-change.png" alt-text="Selecione o estado da fila":::

Os Estados que podem ser definidos para um tópico são:
- **Ativo**: o tópico está ativo.
- **Desabilitado**: o tópico é suspenso.
- **SendDisabled**: mesmo efeito como **desabilitado**.

## <a name="subscription-status"></a>Status da assinatura
Alterar o status da assinatura no portal do Azure é semelhante à alteração do status de um tópico ou de uma fila. Ao selecionar o status atual da assinatura, você verá a página a seguir que permite alterar o status. 

:::image type="content" source="./media/entity-suspend/subscription-state-change.png" alt-text="Selecione o estado da fila":::

Os Estados que podem ser definidos para um tópico são:
- **Ativo**: o tópico está ativo.
- **Desabilitado**: o tópico é suspenso.
- **ReceiveDisabled**: mesmo efeito como **desabilitado**.

## <a name="other-statuses"></a>Outros status
A enumeração [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) também define um conjunto de estados de transição que podem ser definidos apenas pelo sistema. 


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as mensagens do Barramento de Serviço, consulte os seguintes tópicos:

* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png

