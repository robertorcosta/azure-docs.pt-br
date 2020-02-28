---
title: Sessões de mensagens do Barramento de Serviço do Azure | Microsoft Docs
description: Este artigo explica como usar sessões para habilitar o manuseio e a manipulação de sequências não associadas de mensagens relacionadas.
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
ms.openlocfilehash: 4df6396d156c3fe1b75e3cac3d3f4aad7f23553a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660658"
---
# <a name="message-sessions"></a>Sessões de mensagem
As sessões do Barramento de Serviço do Microsoft Azure permitem o tratamento conjunto e ordenado de sequências não associadas de mensagens relacionadas. As sessões podem ser usadas nos padrões PEPS (primeiro a entrar, primeiro a sair) e solicitação-resposta. Este artigo mostra como usar sessões para implementar esses padrões ao usar o barramento de serviço. 

## <a name="first-in-first-out-fifo-pattern"></a>Padrão PEPS (primeiro a entrar, primeiro a sair)
Para obter uma garantia FIFO no barramento de serviço, use sessões. O barramento de serviço não é prescritiva sobre a natureza da relação entre as mensagens e também não define um modelo específico para determinar onde uma sequência de mensagens inicia ou termina.

> [!NOTE]
> A camada básica do barramento de serviço não dá suporte a sessões. As camadas Standard e Premium dão suporte a sessões. Para obter as diferenças entre essas camadas, consulte [preços do barramento de serviço](https://azure.microsoft.com/pricing/details/service-bus/).

Qualquer remetente pode criar uma sessão ao enviar mensagens em uma fila ou tópico definindo a propriedade [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) como algum identificador definido pelo aplicativo que é exclusivo para a sessão. No nível do protocolo AMQP 1.0, esse valor é mapeado para a propriedade *group-id*.

Em filas ou assinaturas com reconhecimento de sessão, as sessões entram em existência quando há pelo menos uma mensagem com a [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId)da sessão. Quando uma sessão existe, não há tempo definido ou API para quando a sessão expira ou desaparece. Teoricamente, uma mensagem pode ser recebida em uma sessão hoje, a próxima mensagem em um ano e se a **SessionId** corresponder, a sessão é a mesma da perspectiva do Barramento de Serviço.

Normalmente, no entanto, um aplicativo tem uma noção clara de onde um conjunto de mensagens relacionadas começa e termina. O barramento de serviço não define nenhuma regra específica.

Um exemplo de como delinear uma sequência para transferir um arquivo é definir a propriedade **Label** para a primeira mensagem como **start**, para as mensagens intermediárias como **content** e para a última mensagem como **end**. A posição relativa das mensagens de conteúdo pode ser computada como o delta de *SequenceNumber* da mensagem atual com relação ao **SequenceNumber** da mensagem *start*.

O recurso de sessão no Barramento de Serviço habilita uma operação de recebimento específica, na forma de [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) nas APIs Java e C#. Habilite o recurso definindo a propriedade [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) na fila ou assinatura por meio do Azure Resource Manager ou definindo o sinalizador no portal. É necessário antes de tentar usar as operações de API relacionadas.

No portal, defina o sinalizador com a caixa de seleção a seguir:

![][2]

> [!NOTE]
> Quando as sessões são habilitadas em uma fila ou em uma assinatura, os aplicativos cliente ***não podem mais*** enviar/receber mensagens regulares. Todas as mensagens devem ser enviadas como parte de uma sessão (definindo a ID de sessão) e recebidas recebendo a sessão.

As APIs para sessões existem em clientes de fila e assinatura. Há um modelo imperativo que controla quando as sessões e mensagens são recebidas e um modelo baseado em manipulador, semelhante ao *onMessage*, que oculta a complexidade do gerenciamento do loop de recebimento.

### <a name="session-features"></a>Recursos de sessão

As sessões fornecem a demultiplexação simultânea de fluxos de mensagens intercaladas enquanto preserva e garante a entrega ordenada.

![][1]

Um destinatário de [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) é criado pelo cliente aceitando uma sessão. O cliente chama [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) ou [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) no C#. No modelo de retorno de chamada reativo, ele registra um manipulador de sessão.

Quando o objeto [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) é aceito e enquanto é mantido por um cliente, esse cliente mantém um bloqueio exclusivo em todas as mensagens com a [SessionID](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) da sessão que existe na fila ou assinatura e também em todas as mensagens com essa **SessionID** que ainda chegam enquanto a sessão é mantida.

O bloqueio é liberado quando **Close** ou **CloseAsync** são chamados ou quando o bloqueio expira em casos em que o aplicativo não consegue fazer a operação de fechamento. O bloqueio de sessão deve ser tratado como um bloqueio exclusivo em um arquivo, o que significa que o aplicativo deve fechar a sessão assim que não precisar mais dela e/ou não esperar nenhuma mensagem adicional.

Quando vários destinatários simultâneos efetuam o pull da fila, as mensagens que pertencem a uma sessão específica são expedidas para um destinatário específico que atualmente mantém o bloqueio para a sessão. Com essa operação, um fluxo de mensagens intercaladas em uma fila ou assinatura é limpo de forma limpa para destinatários diferentes e esses receptores também podem residir em diferentes computadores cliente, já que o gerenciamento de bloqueios ocorre no lado do serviço, dentro do barramento de serviço.

A ilustração anterior mostra três receptores de sessão concomitantes. Uma Sessão com `SessionId` = 4 não tem nenhum cliente proprietário e ativo, o que significa que nenhuma mensagem é entregue dessa sessão específica. Uma sessão atua de várias maneiras, como uma subfila.

O bloqueio da sessão mantido pelo destinatário da sessão é um abrangente para os bloqueios de mensagem usados pelo modo de liquidação de *bloqueio de pico*. Um receptor não pode ter duas mensagens simultaneamente "em trânsito", mas as mensagens devem ser processadas na ordem. Uma nova mensagem só pode ser obtida quando a mensagem anterior foi concluída ou definida como morta. Abandonar uma mensagem faz com que a mesma mensagem seja atendida novamente com a próxima operação de recebimento.

### <a name="message-session-state"></a>Gerenciar estado de sessão

Quando os fluxos de trabalho são processados em sistemas de nuvem de alta disponibilidade e de grande escala, o manipulador de fluxo de trabalho associado a uma sessão específica deve ser capaz de recuperar-se de falhas inesperadas e pode retomar trabalhos parcialmente concluídos em um processo ou máquina diferente de onde o trabalho começou.

O recurso de estado de sessão permite uma anotação definida pelo aplicativo de uma sessão de mensagem dentro do agente, de forma que o estado de processamento registrado relativo a essa sessão seja disponibilizado automaticamente quando a sessão é adquirida por um novo processador.

Da perspectiva do Barramento de Serviço, o estado de sessão da mensagem é um objeto binário opaco que pode conter dados do tamanho de uma mensagem, que é 256 KB para o Barramento de Serviço e 1 MB para o Barramento de Serviço Premium. O estado de processamento relativo a uma sessão pode ser mantido dentro do estado de sessão ou o estado de sessão pode apontar para um registro de banco de dados ou local de armazenamento que contém tal informação.

As APIs para gerenciar o estado de sessão, [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) e [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState), podem ser encontradas no objeto [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) nas APIs de Java e C#. Uma sessão que anteriormente não tinha nenhum estado de sessão definido retorna uma referência **nula** para **GetState**. A limpeza do estado de sessão definido anteriormente é feita com [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

O estado da sessão permanece contanto que não seja apagado (retornando **NULL**), mesmo que todas as mensagens em uma sessão sejam consumidas.

Todas as sessões existentes em uma fila ou assinatura podem ser enumeradas com o método **SessionBrowser** na API de Java e com [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) em [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) e [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) no cliente .NET.

O estado de sessão mantido em uma fila ou em uma assinatura conta para a cota de armazenamento dessa entidade. Quando o aplicativo é concluído com uma sessão, é recomendável, que o aplicativo limpe seu estado retido para evitar custos de gerenciamento externo.

### <a name="impact-of-delivery-count"></a>Impacto da contagem de entrega

A definição de contagem de entrega por mensagem no contexto de sessões varia um pouco da definição na ausência de sessões. Aqui está uma tabela Resumindo quando a contagem de entrega é incrementada.

| Cenário | A contagem de entrega da mensagem é incrementada |
|----------|---------------------------------------------|
| A sessão é aceita, mas o bloqueio de sessão expira (devido ao tempo limite) | Sim |
| A sessão é aceita, as mensagens dentro da sessão não são concluídas (mesmo se estiverem bloqueadas) e a sessão é fechada | Não |
| A sessão é aceita, as mensagens são concluídas e, em seguida, a sessão é fechada explicitamente | N/A (é o fluxo padrão. Estas mensagens são removidas da sessão) |

## <a name="request-response-pattern"></a>Padrão de solicitação-resposta
O [padrão de solicitação-resposta](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) é um padrão de integração bem estabelecido que permite ao aplicativo do remetente enviar uma solicitação e fornece uma maneira para o destinatário enviar corretamente uma resposta de volta para o aplicativo do remetente. Esse padrão normalmente precisa de uma fila ou tópico de curta duração para o aplicativo enviar respostas. Nesse cenário, as sessões fornecem uma solução alternativa simples com semântica comparável. 

Vários aplicativos podem enviar suas solicitações para uma única fila de solicitações, com um parâmetro de cabeçalho específico definido para identificar exclusivamente o aplicativo do remetente. O aplicativo receptor pode processar as solicitações recebidas na fila e enviar respostas em uma fila habilitada para sessões, definindo a ID de sessão para o identificador exclusivo que o remetente enviou na mensagem de solicitação. O aplicativo que enviou a solicitação pode receber mensagens em uma ID de sessão específica e processar as respostas corretamente.

> [!NOTE]
> O aplicativo que envia as solicitações iniciais deve saber sobre a ID da sessão e usar `SessionClient.AcceptMessageSession(SessionID)` para bloquear a sessão na qual ela está esperando a resposta. É uma boa ideia usar um GUID que identifique exclusivamente a instância do aplicativo como uma ID de sessão. Não deve haver nenhum manipulador de sessão ou `AcceptMessageSession(timeout)` na fila para garantir que as respostas estejam disponíveis para serem bloqueadas e processadas por destinatários específicos.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

- Consulte os exemplos de [Microsoft. Azure. ServiceBus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions) ou [Microsoft. ServiceBus. Messaging](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) para obter um exemplo que usa o cliente .NET Framework para lidar com mensagens com reconhecimento de sessão. 

Para saber mais sobre as mensagens do Barramento de Serviço, consulte os seguintes tópicos:

* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
