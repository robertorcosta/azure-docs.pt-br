---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 2604a1608f21d7239db755027e15b8198fb3f9f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81791649"
---
### <a name="functions-2x-and-higher"></a>Funções 2. x e posteriores

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
|prefetchCount|300|A contagem de pré-busca padrão usada pelo subjacente `EventProcessorHost`.|
|batchCheckpointFrequency|1|O número de lotes de eventos para processar antes de criar um ponto de verificação do cursor do EventHub.|

> [!NOTE]
> Para obter uma referência de host. JSON no Azure Functions 2. x e posterior, consulte [referência de host. JSON para Azure Functions](../articles/azure-functions/functions-host-json.md).

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
|prefetchCount|N/D|A pré-busca padrão que será usada pelo subjacente `EventProcessorHost`.| 
|batchCheckpointFrequency|1|O número de lotes de eventos para processar antes de criar um ponto de verificação do cursor do EventHub.| 

> [!NOTE]
> Para obter uma referência de host. JSON no Azure Functions 1. x, consulte [referência de host. JSON para Azure Functions 1. x](../articles/azure-functions/functions-host-json-v1.md).

