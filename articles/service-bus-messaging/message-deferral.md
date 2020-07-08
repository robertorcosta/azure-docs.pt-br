---
title: Barramento de serviço do Azure-adiamento de mensagem
description: Este artigo explica como adiar a entrega de mensagens do barramento de serviço do Azure. A mensagem permanece na fila ou assinatura, mas é reservada.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: f4fe231c56a1bcdea4f15de90cb0e9406f0284a3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85341214"
---
# <a name="message-deferral"></a>Adiamento de mensagens

Quando um cliente de fila ou assinatura recebe uma mensagem que está disposto a processar, mas para a qual o processamento não é possível no momento devido a circunstâncias especiais dentro do aplicativo, ele tem a opção de “adiar” a recuperação da mensagem para um ponto posterior. A mensagem permanece na fila ou assinatura, mas é reservada.

O adiamento é um recurso criado especificamente para cenários de processamento de fluxo de trabalho. As estruturas de fluxo de trabalho podem exigir que determinadas operações sejam processadas em uma ordem específica e talvez precisem adiar o processamento de algumas mensagens recebidas até o trabalho anterior prescrito informado por outras mensagens ter sido concluído.

Um exemplo ilustrativo simples é uma sequência de processamento de ordem em que uma notificação de pagamento de um provedor de pagamento externo aparece em um sistema antes que a ordem de compra correspondente tenha sido propagada da frente da loja para o sistema de atendimento. Nesse caso, o sistema de atendimento pode adiar o processamento da notificação de pagamento até que haja um pedido ao qual associá-la. Em cenários de reunião, em que mensagens de origens diferentes movem o fluxo de trabalho adiante, a ordem de execução em tempo real pode realmente estar correta, mas as mensagens refletindo os resultados podem chegar fora de ordem.

Por fim, o adiamento ajuda na reordenação das mensagens da ordem de chegada em uma ordem na qual elas possam ser processadas, deixando essas mensagens com segurança no repositório de mensagens para as quais o processamento precisa ser adiado.

## <a name="message-deferral-apis"></a>APIs de adiamento de mensagens

A API é [BrokeredMessage. Defer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) ou [BrokeredMessage. DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync) no cliente .NET Framework, [MessageReceiver. DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) no cliente .net Standard e [IMessageReceiver. Defer](/java/api/com.microsoft.azure.servicebus.imessagereceiver.defer?view=azure-java-stable) ou [IMessageReceiver. DeferAsync](/java/api/com.microsoft.azure.servicebus.imessagereceiver.deferasync?view=azure-java-stable) no cliente Java. 

As mensagens adiadas permanecem na fila principal junto com todas as outras mensagens ativas (ao contrário de mensagens mortas que residem em uma subfila), mas elas não podem mais ser recebidas usando as funções Receive/ReceiveAsync regulares. Mensagens adiadas podem ser descobertas por meio da [procura de mensagens](message-browsing.md) se um aplicativo perder o controle delas.

Para recuperar uma mensagem adiada, seu proprietário é responsável por memorizar o [NúmerodeSequência](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) conforme ele a adia. Todo destinatário que souber um número de sequência de uma mensagem adiada pode receber depois a mensagem explicitamente com `Receive(sequenceNumber)`.

Se uma mensagem não puder ser processada porque um recurso específico para lidar com essa mensagem está temporariamente indisponível, mas o processamento de mensagem não deve ser suspenso sumariamente, uma maneira de colocar essa mensagem de lado por alguns minutos é lembrar o **NúmerodeSequência** em uma [mensagem agendada](message-sequencing.md) para ser postada em alguns minutos, e recuperar a mensagem adiada novamente quando a mensagem agendada chegar. Se um manipulador de mensagens depender de um banco de dados para todas as operações e o banco de dados estiver temporariamente indisponível, ele não deverá usar o adiamento, mas suspender o recebimento de mensagens completamente até o banco de dados estar disponível novamente.


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as mensagens do Barramento de Serviço, consulte os seguintes tópicos:

* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)
