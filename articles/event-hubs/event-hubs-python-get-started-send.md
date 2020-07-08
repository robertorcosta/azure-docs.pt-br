---
title: Enviar ou receber eventos dos Hubs de Eventos do Azure usando o Python (antigo)
description: Este guia passo a passo mostra como criar e executar scripts do Python que enviam ou recebem eventos dos Hubs de Eventos do Azure usando o pacote antigo azure-eventhub versão 1.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 4ce53d2d82a00f98dbbd538bd7a61da9ba44e832
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85314478"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python-azure-eventhub-version-1"></a>Início Rápido: Enviar e receber eventos com Hubs de Eventos usando o Python (azure-eventhub versão 1)
Este início rápido mostra como enviar e receber eventos de um hub de eventos usando o pacote Python **azure-eventhub version 1**. 

> [!WARNING]
> Este início rápido usa o pacote antigo do azure-eventhub versão 1. Para um início rápido que usa a **versão 5** mais recente do pacote, confira [Enviar e receber eventos usando o azure-eventhub versão 5](get-started-python-send-v2.md). Para mudar seu aplicativo do pacote antigo para o novo, confira o [Guia para migrar do azure-eventhub versão 1 para versão 5](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).
 

## <a name="prerequisites"></a>Pré-requisitos
Se você for novo nos Hubs de Eventos do Azure, confira a [Visão geral dos Hubs de Eventos](event-hubs-about.md) antes de prosseguir com este início rápido. 

Para concluir este início rápido, você precisará dos seguintes pré-requisitos:

- **Assinatura do Microsoft Azure**. Para usar os serviços do Azure, incluindo os Hubs de Eventos do Azure, você precisa ter uma assinatura.  Caso não tenha uma conta existente do Azure, inscreva-se em uma [avaliação gratuita](https://azure.microsoft.com/free/) ou use os benefícios do assinante do MSDN quando [criar uma conta](https://azure.microsoft.com).
- Python 3.4 ou posterior, com `pip` instalado e atualizado.
- O pacote do Python para Hubs de Eventos. Para instalar o pacote, execute este comando em um prompt de comando que tenha o Python no caminho: 
  
  ```cmd
  pip install azure-eventhub==1.3.*
  ```
- **Criar um namespace de Hubs de Eventos e um hub de eventos**. A primeira etapa é usar o [portal do Azure](https://portal.azure.com) para criar um namespace do tipo Hubs de eventos e obter as credenciais de gerenciamento das quais que seu aplicativo precisa para se comunicar com o hub de eventos. Para criar um namespace e um hub de eventos, siga o procedimento [nesse artigo](event-hubs-create.md). Em seguida, obtenha o valor da chave de acesso do hub de eventos seguindo as instruções do artigo: [Obter a cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). A chave de acesso será usada no código que você escreverá posteriormente no início rápido. O nome da chave padrão é: **RootManageSharedAccessKey**. 


## <a name="send-events"></a>Enviar eventos

Para criar um aplicativo Python que envia eventos para um hub de eventos:

> [!NOTE]
> Em vez de trabalhar no início rápido, você pode baixar e executar os [aplicativos de exemplo](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) do GitHub. Substitua as cadeias de caracteres `EventHubConnectionString` e `EventHubName` pelos valores de seu hub de eventos.

1. Abra seu editor de Python favorito, como o [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um novo arquivo chamado *send.py*. Esse script envia 100 eventos ao hub de eventos.
3. Cole o seguinte código em *send.py*, substituindo os Hubs de Eventos \<namespace>, \<eventhub>, \<AccessKeyName> e \<primary key value> pelos seus valores: 
   
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
2. Cole o seguinte código em *recv.py*, substituindo os Hubs de Eventos \<namespace>, \<eventhub>, \<AccessKeyName> e \<primary key value> por seus valores: 
   
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

