---
title: Sessões de mensagens do Barramento de Serviço do Azure | Microsoft Docs
description: Este artigo explica como usar sessões para permitir o manuseio conjunto e ordenado de seqüências ilimitadas de mensagens relacionadas.
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
ms.openlocfilehash: 1e22641e9d4f9959c26cd2043ea2acd7e260e0f0
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314050"
---
# <a name="message-sessions"></a>Sessões de mensagem
As sessões do Barramento de Serviço do Microsoft Azure permitem o tratamento conjunto e ordenado de sequências não associadas de mensagens relacionadas. As sessões podem ser usadas em padrões de primeira entrada, primeiro fora (FIFO) e de solicitação-resposta. Este artigo mostra como usar sessões para implementar esses padrões ao usar o Service Bus. 

## <a name="first-in-first-out-fifo-pattern"></a>Primeiro-in, primeiro fora (FIFO) padrão
Para realizar uma garantia FIFO no Service Bus, use sessões. O Service Bus não é prescritivo sobre a natureza da relação entre as mensagens, e também não define um modelo específico para determinar onde uma seqüência de mensagens começa ou termina.

> [!NOTE]
> O nível básico do Service Bus não suporta sessões. As camadas Standard e Premium dão suporte a sessões. Para obter diferenças entre esses níveis, consulte [preços de Ônibus de Serviço](https://azure.microsoft.com/pricing/details/service-bus/).

Qualquer remetente pode criar uma sessão ao enviar mensagens em uma fila ou tópico definindo a propriedade [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) como algum identificador definido pelo aplicativo que é exclusivo para a sessão. No nível do protocolo AMQP 1.0, esse valor é mapeado para a propriedade *group-id*.

Em filas ou assinaturas com reconhecimento de sessões, as sessões surgem quando há pelo menos uma mensagem com o [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId)da sessão . Uma vez que uma sessão exista, não há tempo ou API definidos para quando a sessão expirar ou desaparecer. Teoricamente, uma mensagem pode ser recebida em uma sessão hoje, a próxima mensagem em um ano e se a **SessionId** corresponder, a sessão é a mesma da perspectiva do Barramento de Serviço.

Normalmente, no entanto, um aplicativo tem uma noção clara de onde um conjunto de mensagens relacionadas começa e termina. O Service Bus não estabelece regras específicas.

Um exemplo de como delinear uma sequência para transferir um arquivo é definir a propriedade **Label** para a primeira mensagem como **start**, para as mensagens intermediárias como **content** e para a última mensagem como **end**. A posição relativa das mensagens de conteúdo pode ser computada como o delta de *SequenceNumber* da mensagem atual com relação ao *SequenceNumber* da mensagem **start**.

O recurso de sessão no Barramento de Serviço habilita uma operação de recebimento específica, na forma de [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) nas APIs Java e C#. Habilite o recurso definindo a propriedade [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) na fila ou assinatura por meio do Azure Resource Manager ou definindo o sinalizador no portal. É necessário antes de tentar usar as operações de API relacionadas.

No portal, defina o sinalizador com a caixa de seleção a seguir:

![][2]

> [!NOTE]
> Quando as sessões são ativadas em uma fila ou uma assinatura, os aplicativos ***clientes não*** podem mais enviar/receber mensagens regulares. Todas as mensagens devem ser enviadas como parte de uma sessão (definindo o id da sessão) e recebidas recebendo a sessão.

As APIs para sessões existem em clientes de fila e assinatura. Há um modelo imperativo que controla quando sessões e mensagens são recebidas, e um modelo baseado em manipulador, semelhante ao *OnMessage,* que esconde a complexidade de gerenciar o loop de recebimento.

### <a name="session-features"></a>Recursos de sessão

As sessões fornecem a demultiplexação simultânea de fluxos de mensagens intercaladas enquanto preserva e garante a entrega ordenada.

![][1]

Um destinatário de [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) é criado pelo cliente aceitando uma sessão. O cliente chama [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) ou [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) no C#. No modelo de retorno de chamada reativo, ele registra um manipulador de sessão.

Quando o objeto [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) é aceito e enquanto é mantido por um cliente, esse cliente mantém um bloqueio exclusivo em todas as mensagens com o [SessionId](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) dessa sessão que existem na fila ou assinatura, e também em todas as mensagens com esse **SessionId** que ainda chegam enquanto a sessão é realizada.

O bloqueio é liberado quando **close** ou **closeasync** são chamados, ou quando o bloqueio expira nos casos em que o aplicativo não pode fazer a operação de fechamento. O bloqueio da sessão deve ser tratado como um bloqueio exclusivo em um arquivo, o que significa que o aplicativo deve encerrar a sessão assim que não precisar mais e/ou não esperar mais mensagens.

Quando vários destinatários simultâneos efetuam o pull da fila, as mensagens que pertencem a uma sessão específica são expedidas para um destinatário específico que atualmente mantém o bloqueio para a sessão. Com essa operação, um fluxo de mensagens intercalado em uma fila ou assinatura é desmultiplexado para diferentes receptores e esses receptores também podem viver em diferentes máquinas clientes, uma vez que o gerenciamento de bloqueio acontece no lado do serviço, dentro do Service Bus.

A ilustração anterior mostra três receptores de sessão concomitantes. Uma Sessão com `SessionId` = 4 não tem nenhum cliente proprietário e ativo, o que significa que nenhuma mensagem é entregue dessa sessão específica. Uma sessão atua de várias maneiras, como uma subfila.

O bloqueio da sessão mantido pelo destinatário da sessão é um abrangente para os bloqueios de mensagem usados pelo modo de liquidação de *bloqueio de pico*. Um receptor não pode ter duas mensagens simultaneamente "em vôo", mas as mensagens devem ser processadas em ordem. Uma nova mensagem só pode ser obtida quando a mensagem anterior foi concluída ou definida como morta. Abandonar uma mensagem faz com que a mesma mensagem seja atendida novamente com a próxima operação de recebimento.

### <a name="message-session-state"></a>Gerenciar estado de sessão

Quando os fluxos de trabalho são processados em sistemas de nuvem de alta escala e alta disponibilidade, o manipulador de fluxo de trabalho associado a uma sessão específica deve ser capaz de se recuperar de falhas inesperadas e pode retomar o trabalho parcialmente concluído em um processo ou máquina diferente de onde o trabalho começou.

O recurso de estado de sessão permite uma anotação definida pelo aplicativo de uma sessão de mensagem dentro do agente, de forma que o estado de processamento registrado relativo a essa sessão seja disponibilizado automaticamente quando a sessão é adquirida por um novo processador.

Da perspectiva do Barramento de Serviço, o estado de sessão da mensagem é um objeto binário opaco que pode conter dados do tamanho de uma mensagem, que é 256 KB para o Barramento de Serviço e 1 MB para o Barramento de Serviço Premium. O estado de processamento relativo a uma sessão pode ser mantido dentro do estado de sessão ou o estado de sessão pode apontar para um registro de banco de dados ou local de armazenamento que contém tal informação.

As APIs para gerenciar o estado de sessão, [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) e [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState), podem ser encontradas no objeto [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) nas APIs de Java e C#. Uma sessão que anteriormente não tinha nenhum estado de sessão definido retorna uma referência **nula** para **GetState**. A limpeza do estado de sessão definido anteriormente é feita com [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

O estado da sessão permanece enquanto não estiver esclarecido (retornando **nulo),** mesmo que todas as mensagens em uma sessão sejam consumidas.

Todas as sessões existentes em uma fila ou assinatura podem ser enumeradas com o método **SessionBrowser** na API Java e com [getMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) no [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) e [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient) no cliente .NET Framework.

O estado de sessão mantido em uma fila ou em uma assinatura conta para a cota de armazenamento dessa entidade. Quando o aplicativo é concluído com uma sessão, é recomendável, que o aplicativo limpe seu estado retido para evitar custos de gerenciamento externo.

### <a name="impact-of-delivery-count"></a>Impacto da contagem de entregas

A definição da contagem de entregas por mensagem no contexto das sessões varia ligeiramente da definição na ausência de sessões. Aqui está uma tabela resumindo quando a contagem de entregas é incrementada.

| Cenário | A contagem de entrega da mensagem é incrementada |
|----------|---------------------------------------------|
| A sessão é aceita, mas o bloqueio da sessão expira (devido ao tempo extra) | Sim |
| A sessão é aceita, as mensagens dentro da sessão não são concluídas (mesmo que estejam bloqueadas), e a sessão é encerrada | Não |
| Sessão é aceita, mensagens são concluídas, e então a sessão é explicitamente fechada | N/A (É o fluxo padrão. Aqui as mensagens são removidas da sessão) |

## <a name="request-response-pattern"></a>Padrão de solicitação-resposta
O [padrão solicitação-resposta](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) é um padrão de integração bem estabelecido que permite que o aplicativo remetente envie uma solicitação e fornece uma maneira para o receptor enviar corretamente uma resposta de volta para o aplicativo de remetente. Esse padrão normalmente precisa de uma fila ou tópico de curta duração para que o aplicativo envie respostas. Nesse cenário, as sessões oferecem uma solução alternativa simples com semântica comparável. 

Vários aplicativos podem enviar suas solicitações para uma única fila de solicitação, com um parâmetro de cabeçalho específico definido para identificar exclusivamente o aplicativo de remetente. O aplicativo receptor pode processar as solicitações que entram na fila e enviar respostas em uma fila habilitada para sessões, definindo o ID da sessão para o identificador exclusivo que o remetente havia enviado na mensagem de solicitação. O aplicativo que enviou a solicitação pode então receber mensagens em um ID de sessão específico e processar corretamente as respostas.

> [!NOTE]
> O aplicativo que envia as solicitações iniciais `SessionClient.AcceptMessageSession(SessionID)` deve saber sobre o ID da sessão e usar para bloquear a sessão em que está esperando a resposta. É uma boa ideia usar um GUID que identifica exclusivamente a instância do aplicativo como um id de sessão. Não deve haver manipulador `AcceptMessageSession(timeout)` de sessão ou na fila para garantir que as respostas estejam disponíveis para serem bloqueadas e processadas por receptores específicos.

## <a name="next-steps"></a>Próximas etapas

- Consulte as [amostras microsoft.Azure.ServiceBus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions) ou [microsoft.serviceBus.mensagens](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) para obter um exemplo que usa o cliente .NET Framework para lidar com mensagens com reconhecimento de sessão. 

Para saber mais sobre as mensagens do Barramento de Serviço, consulte os seguintes tópicos:

* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
