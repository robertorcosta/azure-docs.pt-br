---
title: Enviar ou receber eventos dos Hubs de Eventos do Azure usando o Python (mais recente)
description: Este artigo fornece um passo a passo para criar um aplicativo Python que envia/recebe eventos para/de Hubs de Eventos do Azure usando o pacote azure-eventhub versão 5 mais recente.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: spelluru
ms.custom: tracking-python
ms.openlocfilehash: 3a79097a5939feb7380475a3cc3a80c4cb15f655
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/12/2020
ms.locfileid: "84753396"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub-version-5"></a>Enviar eventos para Hubs de Eventos ou receber eventos deles usando o Python (azure-eventhub versão 5)
Este início rápido mostra como enviar e receber eventos de um hub de eventos usando o pacote Python **azure-eventhub versão 5**.

> [!IMPORTANT]
> Este início rápido usa o pacote mais recente do azure-eventhub versão 5. Para um início rápido que usa o pacote antigo do azure-eventhub versão 1, confira [Enviar e receber eventos usando o azure-eventhub versão 1](event-hubs-python-get-started-send.md). 

## <a name="prerequisites"></a>Pré-requisitos
Se você estiver conhecendo agora os Hubs de Eventos do Azure, confira [Visão geral dos Hubs de Eventos](event-hubs-about.md) antes de prosseguir com este início rápido. 

Para concluir este início rápido, você precisará dos seguintes pré-requisitos:

- **Assinatura do Microsoft Azure**. Para usar os serviços do Azure, incluindo os Hubs de Eventos do Azure, você precisa ter uma assinatura.  Caso não tenha uma conta existente do Azure, inscreva-se em uma [avaliação gratuita](https://azure.microsoft.com/free/) ou use os benefícios do assinante do MSDN quando [criar uma conta](https://azure.microsoft.com).
- Python 2.7 ou 3.5 ou posterior, com PIP instalado e atualizado.
- O pacote do Python para Hubs de Eventos. 

    Para instalar o pacote, execute este comando em um prompt de comando que tenha o Python no caminho:

    ```cmd
    pip install azure-eventhub
    ```

    Instale o seguinte pacote para receber os eventos usando o Armazenamento de Blobs do Azure como o repositório de pontos de verificação:

    ```cmd
    pip install azure-eventhub-checkpointstoreblob-aio
    ```
- **Criar um namespace de Hubs de Eventos e um hub de eventos**. A primeira etapa é usar o [portal do Azure](https://portal.azure.com) para criar um namespace do tipo Hubs de eventos e obter as credenciais de gerenciamento das quais que seu aplicativo precisa para se comunicar com o hub de eventos. Para criar um namespace e um hub de eventos, siga o procedimento [nesse artigo](event-hubs-create.md). Em seguida, obtenha a **cadeia de conexão para o namespace dos Hubs de Eventos** seguindo as instruções do artigo: [Obter a cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Você usa a cadeia de conexão posteriormente no início rápido.

## <a name="send-events"></a>Enviar eventos
Nesta seção, você criará um script Python para enviar eventos para o hub de eventos que você criou anteriormente.

1. Abra seu editor favorito do Python, como o [Visual Studio Code](https://code.visualstudio.com/).
2. Crie um script chamado *send.py*. Esse script envia um lote de eventos para o hub de eventos que você criou anteriormente.
3. Cole o seguinte código em *sender.py*:

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData

    async def run():
        # Create a producer client to send messages to the event hub.
        # Specify a connection string to your event hubs namespace and
            # the event hub name.
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # Create a batch.
            event_data_batch = await producer.create_batch()

            # Add events to the batch.
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))

            # Send the batch of events to the event hub.
            await producer.send_batch(event_data_batch)

    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())

    ```

    > [!NOTE]
    > Para obter o código-fonte completo, incluindo comentários informativos, acesse a página [send_async.py do GitHub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).
    

## <a name="receive-events"></a>Receber eventos
Este início rápido usa o Armazenamento de Blobs do Azure como um repositório de pontos de verificação. O repositório de pontos de verificação é usado para persistir pontos de verificação (ou seja, as últimas posições de leitura).  

> [!NOTE]
> Se você estiver executando o Azure Stack Hub, essa plataforma poderá dar suporte a uma versão diferente do SDK do Storage Blob do que aquelas normalmente disponíveis no Azure. Por exemplo, se a execução estiver sendo feita [no Azure Stack Hub versão 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview), a versão superior disponível para o serviço de Armazenamento será a versão 2017-11-09. Nesse caso, além de seguir as etapas desta seção, você também precisará adicionar o código para ter como destino a versão de API 2017-11-09 do serviço de Armazenamento. Para obter um exemplo de como direcionar uma versão de API específica do Armazenamento, confira as amostras [síncronas](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py) e [assíncronas](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py) no GitHub. Para obter mais informações sobre as versões do serviço de Armazenamento do Azure compatível com o Azure Stack Hub, confira [Armazenamento do Azure Stack Hub: diferenças e considerações](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences).


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Criar uma conta de armazenamento e um contêiner de blobs do Azure
Crie uma conta de armazenamento do Azure e um contêiner de blobs nela executando as seguintes etapas:

1. [Crie uma conta de Armazenamento do Azure](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Crie um contêiner de blob](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Obtenha a cadeia de conexão para a conta de armazenamento](../storage/common/storage-configure-connection-string.md)

Registre a cadeia de conexão e o nome do contêiner para usá-los posteriormente no código de recebimento.


### <a name="create-a-python-script-to-receive-events"></a>Criar um script do Python para receber eventos

Nesta seção, você criará um script Python para receber eventos do hub de eventos:

1. Abra seu editor favorito do Python, como o [Visual Studio Code](https://code.visualstudio.com/).
2. Crie um script chamado *recv.py*.
3. Cole o seguinte código em *recv.py*:

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore


    async def on_event(partition_context, event):
        # Print the event data.
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))

        # Update the checkpoint so that the program doesn't read the events
        # that it has already read when you run it next time.
        await partition_context.update_checkpoint(event)

    async def main():
        # Create an Azure blob checkpoint store to store the checkpoints.
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")

        # Create a consumer client for the event hub.
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # Call the receive method. Read from the beginning of the partition (starting_position: "-1")
            await client.receive(on_event=on_event,  starting_position="-1")

    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # Run the main method.
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > Para obter o código-fonte completo, incluindo comentários informativos adicionais, acesse a [página recv_with_checkpoint_store_async.py do GitHub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py).


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

A janela do destinatário deve exibir as mensagens que foram enviadas para o hub de eventos.


## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você enviou e recebeu eventos de maneira assíncrona. Para saber como enviar e receber eventos de maneira síncrona, acesse a [página sync_samples do GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

Para obter todos os exemplos (síncronos e assíncronos) no GitHub, acesse os [exemplos da biblioteca de clientes dos Hubs de Eventos do Azure para Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples).
