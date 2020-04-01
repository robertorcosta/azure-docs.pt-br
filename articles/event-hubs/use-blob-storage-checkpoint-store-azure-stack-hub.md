---
title: Use o Blob Storage como armazenamento de ponto de verificação no Azure Stack Hub (visualização)
description: Este artigo descreve como usar o Blob Storage como uma loja de pontos de verificação no Event Hubs no Azure Stack Hub (visualização).
services: event-hubs
documentationcenter: na
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.date: 03/18/2020
ms.author: spelluru
ms.openlocfilehash: 1f0e4dea44007ef82cb4b700ff0be4a5579541d8
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398916"
---
# <a name="use-blob-storage-as-checkpoint-store---event-hubs-on-azure-stack-hub-preview"></a>Use o Blob Storage como loja de pontos de verificação - Hubs de eventos no Azure Stack Hub (visualização)
Se você estiver usando o Azure Blob Storage como o armazenamento de pontos de verificação em um ambiente que suporta uma versão diferente do Storage Blob SDK do que os que normalmente estão disponíveis no Azure, você precisará usar o código para alterar a versão aPI do serviço de armazenamento para a versão específica suportada por esse ambiente. Por exemplo, se você estiver executando [hubs de eventos em uma versão 2002 do Azure Stack Hub,](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)a versão mais alta disponível para o serviço de armazenamento é a versão 2017-11-09. Neste caso, você precisa usar o código para segmentar a versão de API do serviço de armazenamento para 2017-11-09. Para obter um exemplo sobre como segmentar uma versão específica da API de armazenamento, consulte essas amostras no GitHub: 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithOlderStorageVersion.java). 
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.js) ou [TypeScript,](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.ts) 
- Python - [Síncrono,](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/event_processor_blob_storage_example_with_storage_api_version.py) [Assíncrono](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/event_processor_blob_storage_example_with_storage_api_version.py)

> [!IMPORTANT]
> Event Hubs no Azure Stack Hub está atualmente em [pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) e é gratuito. 

Se você executar o receptor Event Hubs que usa o Blob Storage como o armazenamento de pontos de verificação sem direcionar a versão que o Azure Stack Hub suporta, você receberá a seguinte mensagem de erro:

```
The value for one of the HTTP headers is not in the correct format
```


## <a name="sample-error-message-in-python"></a>Mensagem de erro de exemplo em Python
Para Python, um `azure.core.exceptions.HttpResponseError` erro de é `on_error(partition_context, error)` `EventHubConsumerClient.receive()`passado para o manipulador de erros de . Mas o `receive()` método não levanta uma exceção. `print(error)`imprimirá as seguintes informações de exceção:

```bash
The value for one of the HTTP headers is not in the correct format.

RequestId:f048aee8-a90c-08ba-4ce1-e69dba759297
Time:2020-03-17T22:04:13.3559296Z
ErrorCode:InvalidHeaderValue
Error:None
HeaderName:x-ms-version
HeaderValue:2019-07-07
```

O logger registrará dois avisos como os seguintes:

```bash
WARNING:azure.eventhub.extensions.checkpointstoreblobaio._blobstoragecsaio: 
An exception occurred during list_ownership for namespace '<namespace-name>.eventhub.<region>.azurestack.corp.microsoft.com' eventhub 'python-eh-test' consumer group '$Default'. 

Exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07')

WARNING:azure.eventhub.aio._eventprocessor.event_processor:EventProcessor instance '26d84102-45b2-48a9-b7f4-da8916f68214' of eventhub 'python-eh-test' consumer group '$Default'. An error occurred while load-balancing and claiming ownership. 

The exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07'). Retrying after 71.45254944090853 seconds
```



## <a name="next-steps"></a>Próximas etapas

Veja o artigo a seguir saiba sobre particionamento e verificação: [Equilibrar carga de partição em várias instâncias do seu aplicativo](event-processor-balance-partition-load.md)
