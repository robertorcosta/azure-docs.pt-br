---
title: 'Início Rápido: Usar filas do Barramento de Serviço do Azure com Python'
description: Este artigo mostra como usar o Python para criar, enviar e receber mensagens das filas do Barramento de Serviço do Azure.
services: service-bus-messaging
documentationcenter: python
author: axisc
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.custom: seo-python-october2019
ms.openlocfilehash: acb0b0e84804ecf6025e05590133dee9b0d54c48
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478646"
---
# <a name="quickstart-use-azure-service-bus-queues-with-python"></a>Início Rápido: Usar filas do Barramento de Serviço do Azure com Python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Este artigo mostra como usar o Python para criar, enviar e receber mensagens das filas do Barramento de Serviço do Azure. 

Para obter mais informações sobre as bibliotecas do Barramento de Serviço do Azure do Python, confira [Bibliotecas do Barramento de Serviço para Python](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="prerequisites"></a>Pré-requisitos
- Uma assinatura do Azure. Ative seus [benefícios de assinante do Visual Studio ou do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscreva-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Um namespace do Barramento de Serviço, criado seguindo as etapas em [Início Rápido: Usar o portal do Azure para criar assinaturas e um tópico do Barramento de Serviço](service-bus-quickstart-topics-subscriptions-portal.md). Copie a cadeia de conexão primária da tela **Políticas de acesso compartilhado** para usar posteriormente neste artigo. 
- Python 3.4x ou superior, com o pacote do [Barramento de Serviço do Azure do Python][Python Azure Service Bus package] instalado. Para obter mais informações, consulte o [Guia de instalação do Python](/azure/developer/python/azure-sdk-install). 

## <a name="create-a-queue"></a>Criar uma fila

Um objeto **ServiceBusClient** permite que você trabalhe com filas. Para acessar programaticamente o Barramento de Serviço, adicione a seguinte linha perto da parte superior do arquivo do Python:

```python
from azure.servicebus import ServiceBusClient
```

Adicione o código a seguir para criar um objeto **ServiceBusClient**. Substitua `<connectionstring>` pelo valor da cadeia de conexão primária do Barramento de Serviço. Você pode encontrar esse valor em **Políticas de acesso compartilhado** no namespace do Barramento de Serviço no [portal do Azure][Azure portal].

```python
sb_client = ServiceBusClient.from_connection_string('<connectionstring>')
```

O código a seguir usa o método `create_queue` do **ServiceBusClient** para criar uma fila chamada `taskqueue` com as configurações padrão:

```python
sb_client.create_queue("taskqueue")
```

Você pode usar as opções para substituir as configurações de fila padrão, como TTL (vida útil) da mensagem ou tamanho máximo do tópico. O código a seguir cria uma fila chamada `taskqueue` com tamanho máximo de fila de 5 GB e o valor de TTL de 1 minuto:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

## <a name="send-messages-to-a-queue"></a>Enviar mensagens a uma fila

Para enviar uma mensagem para uma fila do Barramento de Serviço, um aplicativo chamará o método `send` no objeto **ServiceBusClient**. O exemplo de código a seguir cria um cliente de fila e envia uma mensagem de teste para a fila de `taskqueue`. Substitua `<connectionstring>` pelo valor da cadeia de conexão primária do Barramento de Serviço. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

### <a name="message-size-limits-and-quotas"></a>Cotas e limites de tamanho de mensagem

As filas do Barramento de Serviço dão suporte ao tamanho máximo de mensagem de 256 KB na [camada Standard](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicativo padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há limite no número de mensagens que uma fila pode conter, mas há um limite no tamanho total das mensagens que a fila contém. Você pode definir o tamanho da fila no momento da criação, com um limite superior de 5 GB. 

Para saber mais sobre cotas, consulte [Cotas do Barramento de Serviço][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila

O cliente de fila recebe mensagens de uma fila usando o método `get_receiver` no objeto **ServiceBusClient**. O exemplo de código a seguir cria um cliente de fila e recebe uma mensagem da fila de `taskqueue`. Substitua `<connectionstring>` pelo valor da cadeia de conexão primária do Barramento de Serviço. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

### <a name="use-the-peek_lock-parameter"></a>Usar o parâmetro peek_lock

O parâmetro opcional `peek_lock` de `get_receiver` determina se o Barramento de Serviço exclui as mensagens da fila conforme elas são lidas. O modo padrão para recebimento de mensagens é *PeekLock* ou `peek_lock` definido como **True**, que lê (espia) e bloqueia mensagens sem excluí-las da fila. Cada mensagem deve ser explicitamente concluída para removê-la da fila.

Para excluir mensagens da fila conforme elas são lidas, você pode definir o parâmetro `peek_lock` de `get_receiver` como **False**. A exclusão de mensagens como parte da operação de recebimento é o modelo mais simples, mas funcionará apenas se o aplicativo puder tolerar mensagens ausentes caso haja uma falha. Para entender esse comportamento, considere um cenário no qual o consumidor emite uma solicitação de recebimento e, em seguida, ocorre falha antes de processá-la. Se a mensagem foi excluída ao ser recebida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem recebida antes da falha.

Se o seu aplicativo não puder tolerar mensagens perdidas, o recebimento será uma operação de duas fases. O PeekLock localiza a próxima mensagem a ser consumida, bloqueia-a para evitar que outros consumidores a recebam e a retorna para o aplicativo. Após o processamento ou o armazenamento da mensagem, o aplicativo conclui a segunda fase do processo de recebimento chamando o método `complete` no objeto **Message**.  O método `complete` marcará a mensagem como sendo consumida e a removerá da assinatura.

## <a name="handle-application-crashes-and-unreadable-messages"></a>Tratar falhas do aplicativo e mensagens ilegíveis

O Barramento de Serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não conseguir processar a mensagem por algum motivo, ele chamará o método `unlock` no objeto **Message**. O Barramento de Serviço desbloqueia a mensagem na fila e a disponibiliza para ser recebida novamente pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite para mensagens bloqueadas na fila. Se um aplicativo não conseguir processar uma mensagem antes que o tempo limite de bloqueio expire (por exemplo, se o aplicativo travar), o Barramento de Serviço desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Caso o aplicativo falhe após o processamento da mensagem, mas antes que o método `complete` seja chamado, a mensagem será entregue novamente ao aplicativo quando ele for reiniciado. Esse comportamento geralmente é chamado de *Processamento pelo menos uma vez*. Cada mensagem é processada pelo menos uma vez, mas em determinadas situações, a mesma mensagem poderá ser reenviada. Se o seu cenário não puder tolerar o processamento duplicado, você poderá usar a propriedade **MessageId**, que permanece constante entre as tentativas de entrega, para lidar com a entrega de mensagens duplicada. 

> [!TIP]
> É possível gerenciar os recursos do Barramento de Serviço com o [Gerenciador de Barramento de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador de Barramento de Serviço permite que você se conecte a um namespace do Barramento de Serviço e administre facilmente as entidades de mensagens. A ferramenta fornece recursos avançados, como a funcionalidade de importação/exportação e a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu as noções básicas sobre as filas do Barramento de Serviço, confira [Filas, tópicos e assinaturas][Queues, topics, and subscriptions] para saber mais.

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md
