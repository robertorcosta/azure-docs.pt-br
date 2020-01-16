---
title: Enviar ou receber eventos usando Python-hubs de eventos do Azure | Microsoft Docs
description: Este artigo fornece instruções para a criação de um aplicativo Python que envia eventos para os hubs de eventos do Azure.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 01/08/2020
ms.author: spelluru
ms.openlocfilehash: 11548ba180a2dd6541240431d670812448c502b8
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981581"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-python"></a>Enviar eventos para ou receber eventos de hubs de eventos usando o Python

Os Hubs de Eventos do Azure são uma plataforma de streaming de Big Data e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos pelos dispositivos e software distribuídos. Os dados enviados para um Hub de Eventos podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Para obter uma visão detalhada dos Hubs de Eventos, confira [Visão geral de Hubs de Eventos](event-hubs-about.md) e [Recursos de Hubs de Eventos](event-hubs-features.md).

Este tutorial descreve como criar aplicativos Python para enviar eventos ou receber eventos de um hub de eventos.

> [!IMPORTANT]
> Este guia de início rápido usa a versão 5 do SDK do Python de hubs de eventos do Azure. Para um início rápido que usa a versão 1 antiga do SDK do Python, consulte [Este artigo](event-hubs-python-get-started-send.md). Se você estiver usando a versão 1 do SDK, recomendamos que migre seu código para a versão mais recente. Para obter detalhes, consulte o [Guia de migração](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

- Uma assinatura do Azure. Se você não tiver [uma conta gratuita](https://azure.microsoft.com/free/), crie uma antes de começar.
- Um namespace de hubs de eventos ativo e um hub de eventos, criados seguindo as instruções em [início rápido: criar um hub de eventos usando portal do Azure](event-hubs-create.md). Anote o namespace e os nomes do hub de eventos para serem usados mais adiante neste guia.
- O nome da chave de acesso compartilhado e o valor da chave primária para seu namespace de Hubs de Eventos. Obtenha o nome e o valor da chave de acesso seguindo as instruções em [Obter cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). O nome da chave de acesso padrão é **RootManageSharedAccessKey**. Copie o nome da chave e o valor da chave primária a ser usado posteriormente neste passo a passos.
- Python 2,7 e 3,5 ou posterior, com `pip` instalado e atualizado.
- O pacote do Python para Hubs de Eventos. Para instalar o pacote, execute este comando em um prompt de comando que tenha o Python no caminho:

    ```cmd
    pip install azure-eventhub
    ```

    Instale este pacote para receber os eventos usando um armazenamento de BLOBs do Azure como o armazenamento de ponto de verificação.

    ```cmd
    pip install azure-eventhub-checkpointstoreblobaio
    ```

## <a name="send-events"></a>Enviar eventos
Nesta seção, você cria um script Python para enviar eventos para o Hub de eventos criado anteriormente.

1. Abra seu editor de Python favorito, como o [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um script chamado **send.py**. Esse script envia um lote de eventos para o Hub de eventos que você criou anteriormente.
3. Cole o código a seguir em send.py. Consulte os comentários de código para obter detalhes.

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData

    async def run():
        # create a producer client to send messages to the event hub
        # specify connection string to your event hubs namespace and
            # the event hub name
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # create a batch
            event_data_batch = await producer.create_batch()

            # add events to the batch
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))

            # send the batch of events to the event hub
            await producer.send_batch(event_data_batch)

    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())

    ```

    > [!NOTE]
    > Para obter o código-fonte completo com comentários muito úteis, consulte [este arquivo no GitHub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py)

## <a name="receive-events"></a>Receber eventos
Este início rápido usa um armazenamento de BLOBs do Azure como um armazenamento de ponto de verificação. O armazenamento de ponto de verificação é usado para persistir pontos de verificação (última posição de leitura).  

### <a name="create-an-azure-storage-and-a-blob-container"></a>Criar um armazenamento do Azure e um contêiner de BLOBs
Siga estas etapas para criar uma conta de armazenamento do Azure em um contêiner de BLOBs.

1. [Crie uma conta de Armazenamento do Azure](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Crie um contêiner de blob](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Obter a cadeia de conexão para a conta de armazenamento](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Anote a cadeia de conexão e o nome do contêiner. Você os usará no código de recebimento.


### <a name="create-python-script-to-receive-events"></a>Criar script Python para receber eventos

Nesta seção, você cria um script Python para receber eventos do hub de eventos:

1. Abra seu editor de Python favorito, como o [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um script chamado **recv.py**.
3. Cole o código a seguir em recv.py. Consulte os comentários de código para obter detalhes.

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore


    async def on_event(partition_context, event):
        # print the event data
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))

        # update the checkpoint so that the program doesn't read the events
        # that it has already read when you run it next time
        await partition_context.update_checkpoint(event)

    async def main():
        # create an Azure blob checkpoint store to store the checkpoints
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")

        # create a consumer client for the event hub
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # call the receive method
            await client.receive(on_event=on_event)

    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # run the main method
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > Para obter o código-fonte completo com comentários muito úteis, consulte [este arquivo no GitHub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py)


### <a name="run-the-receiver-app"></a>Executar o aplicativo receptor

Para executar o script, abra um prompt de comando que tenha Python no caminho e, em seguida, execute este comando:

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>Executar o aplicativo remetente

Para executar o script, abra um prompt de comando que tenha Python no caminho e, em seguida, execute este comando:

```bash
python send.py
```

Você deve ver as mensagens que foram enviadas para o Hub de eventos na janela do destinatário.


## <a name="next-steps"></a>Próximos passos
Neste guia de início rápido, você enviou e recebe eventos de forma assíncrona. Para saber como enviar e receber eventos de forma síncrona, consulte exemplos neste [local](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

Você pode encontrar todos os exemplos (sincronização e Async) no GitHub [aqui](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples).
