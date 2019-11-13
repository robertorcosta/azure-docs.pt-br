---
title: 'Início Rápido: Enviar e receber eventos usando Python – Hubs de Eventos do Azure'
description: 'Início Rápido: Este guia passo a passo mostra como criar e executar scripts do Python que enviam ou recebem eventos dos Hubs de Eventos do Azure.'
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: 9b6c3fb03f696f4142721284a14001eb51153a77
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720555"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python"></a>Início Rápido: Enviar e receber eventos com Hubs de Eventos usando o Python

O Hubs de Eventos do Azure é uma plataforma de streaming de Big Data e um serviço de ingestão de eventos que pode receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria de dispositivos e software distribuídos. Os dados enviados para um Hub de Eventos podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Para obter mais informações sobre os Hubs de Eventos, confira [Hubs de Eventos do Azure](event-hubs-about.md) e [Recursos e terminologia no Hubs de Eventos do Azure](event-hubs-features.md).

Este guia de início rápido mostra como criar aplicativos do Python que enviam e recebem eventos de um hub de eventos. 

> [!NOTE]
> Em vez de trabalhar no início rápido, você pode baixar e executar os [aplicativos de exemplo](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) do GitHub. Substitua as cadeias de caracteres `EventHubConnectionString` e `EventHubName` pelos valores de seu hub de eventos. 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisará dos seguintes pré-requisitos:

- Uma assinatura do Azure. Se você não tiver [uma conta gratuita](https://azure.microsoft.com/free/), crie uma antes de começar.
- Um namespace de Hubs de Eventos ativo e um hub de eventos, criados seguindo as instruções no [Início Rápido: Criar um hub de eventos usando o portal do Azure](event-hubs-create.md). Anote o namespace e os nomes do hub de eventos para serem usados mais adiante neste guia. 
- O nome da chave de acesso compartilhado e o valor da chave primária para seu namespace de Hubs de Eventos. Obtenha o nome e o valor da chave de acesso seguindo as instruções em [Obter cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). O nome da chave de acesso padrão é **RootManageSharedAccessKey**. Copie o nome da chave e o valor da chave primária a ser usado posteriormente neste passo a passos. 
- Python 3.4 ou posterior, com `pip` instalado e atualizado.
- O pacote do Python para Hubs de Eventos. Para instalar o pacote, execute este comando em um prompt de comando que tenha o Python no caminho: 
  
  ```cmd
  pip install azure-eventhub
  ```
  
  > [!NOTE]
  > O código neste guia de início rápido usa a versão estável atual 1.3.1 do SDK dos Hubs de Eventos. Para obter o código de exemplo que usa a versão prévia do SDK, confira [https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples).

## <a name="send-events"></a>Enviar eventos

Para criar um aplicativo Python que envia eventos para um hub de eventos:

1. Abra seu editor de Python favorito, como o [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um novo arquivo chamado *send.py*. Esse script envia 100 eventos ao hub de eventos.
3. Cole o código a seguir em *send.py*, substituindo os Hubs de Eventos \<namespace>, \<eventhub>, \<AccessKeyName> e \<valor da chave primária> pelos seus valores: 
   
   ```python
   import sys
   import logging
   import datetime
   import time
   import os
   
   from azure.eventhub import EventHubClient, Sender, EventData
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<namespace>.servicebus.windows.net/eventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   try:
       if not ADDRESS:
           raise ValueError("No EventHubs URL supplied.")
   
       # Create Event Hubs client
       client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
       sender = client.add_sender(partition="0")
       client.run()
       try:
           start_time = time.time()
           for i in range(100):
               print("Sending message: {}".format(i))
               message = "Message {}".format(i)
               sender.send(EventData(message))
       except:
           raise
       finally:
           end_time = time.time()
           client.stop()
           run_time = end_time - start_time
           logger.info("Runtime: {} seconds".format(run_time))
   
   except KeyboardInterrupt:
       pass
   ```
   
4. Salve o arquivo. 

Para executar o script, no diretório em que você salvou *send.py*, execute este comando:

```cmd
start python send.py
```

Parabéns! Agora você enviou mensagens para um hub de eventos.

## <a name="receive-events"></a>Receber eventos

Para criar um aplicativo do Python que receba eventos de um hub de eventos:

1. No editor do Python, crie um arquivo chamado *recv.py*.
2. Cole o código a seguir em *recv.py*, substituindo os Hubs de Eventos \<namespace>, \<eventhub>, \<AccessKeyName> e \<valor da chave primária> pelos seus valores: 
   
   ```python
   import os
   import sys
   import logging
   import time
   from azure.eventhub import EventHubClient, Receiver, Offset
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   
   CONSUMER_GROUP = "$default"
   OFFSET = Offset("-1")
   PARTITION = "0"
   
   total = 0
   last_sn = -1
   last_offset = "-1"
   client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
   try:
       receiver = client.add_receiver(
           CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
       client.run()
       start_time = time.time()
       for event_data in receiver.receive(timeout=100):
           print("Received: {}".format(event_data.body_as_str(encoding='UTF-8')))
           total += 1
   
       end_time = time.time()
       client.stop()
       run_time = end_time - start_time
       print("Received {} messages in {} seconds".format(total, run_time))
   
   except KeyboardInterrupt:
       pass
   finally:
       client.stop()
   ```
   
4. Salve o arquivo.

Para executar o script, no diretório em que você salvou *recv.py*, execute este comando:

```cmd
start python recv.py
```

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre Hubs de Eventos, confira os seguintes artigos:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Recursos e terminologia nos Hubs de Eventos do Azure](event-hubs-features.md)
- [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)

