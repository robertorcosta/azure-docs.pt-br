---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 2604a1608f21d7239db755027e15b8198fb3f9f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81791649"
---
### <a name="functions-2x-and-higher"></a>Funções 2.x e posteriores

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------|
|maxBatchSize|10|A contagem máxima de eventos recebidos por loop de recebimento.|
|prefetchCount|300|A contagem da pré-busca padrão usada pelo `EventProcessorHost` subjacente.|
|batchCheckpointFrequency|1|O número de lotes de eventos para processar antes de criar um ponto de verificação do cursor do EventHub.|

> [!NOTE]
> Para obter uma referência de host.json no Azure Functions 2.x e posterior, confira [Referência de host.json para o Azure Functions](../articles/azure-functions/functions-host-json.md).

### <a name="functions-1x"></a>Funções 1.x

```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------| 
|maxBatchSize|64|A contagem máxima de eventos recebidos por loop de recebimento.|
|prefetchCount|n/a|A pré-busca padrão que será usada pelo `EventProcessorHost` subjacente.| 
|batchCheckpointFrequency|1|O número de lotes de eventos para processar antes de criar um ponto de verificação do cursor do EventHub.| 

> [!NOTE]
> Para obter uma referência de host.json no Azure Functions 1.x, confira [Referência de host.json para o Azure Functions 1.x](../articles/azure-functions/functions-host-json-v1.md).

