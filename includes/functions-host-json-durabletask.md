---
title: incluir arquivo
description: incluir arquivo
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6bb59db4c1b31033b1e116742dedc94621b1c60d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117106"
---
Definições de configuração para [Funções Duráveis](../articles/azure-functions/durable-functions-overview.md).

### <a name="durable-functions-1x"></a>Funções duráveis 1.x

```json
{
  "durableTask": {
    "hubName": "MyTaskHub",
    "controlQueueBatchSize": 32,
    "partitionCount": 4,
    "controlQueueVisibilityTimeout": "00:05:00",
    "workItemQueueVisibilityTimeout": "00:05:00",
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "maxQueuePollingInterval": "00:00:30",
    "azureStorageConnectionStringName": "AzureWebJobsStorage",
    "trackingStoreConnectionStringName": "TrackingStorage",
    "trackingStoreNamePrefix": "DurableTask",
    "traceInputsAndOutputs": false,
    "logReplayEvents": false,
    "eventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName":  "EventGridKey",
    "eventGridPublishRetryCount": 3,
    "eventGridPublishRetryInterval": "00:00:30",
    "eventGridPublishEventTypes": ["Started", "Completed", "Failed", "Terminated"]
  }
}
```

### <a name="durable-functions-2x"></a><a name="durable-functions-2-0-host-json"></a>Funções duráveis 2.x

```json
{
 "extensions": {
  "durableTask": {
    "hubName": "MyTaskHub",
    "storageProvider": {
      "connectionStringName": "AzureWebJobsStorage",
      "controlQueueBatchSize": 32,
      "controlQueueBufferThreshold": 256,
      "controlQueueVisibilityTimeout": "00:05:00",
      "maxQueuePollingInterval": "00:00:30",
      "partitionCount": 4,
      "trackingStoreConnectionStringName": "TrackingStorage",
      "trackingStoreNamePrefix": "DurableTask",
      "workItemQueueVisibilityTimeout": "00:05:00",
    },
    "tracing": {
      "traceInputsAndOutputs": false,
      "traceReplayEvents": false,
    },
    "notifications": {
      "eventGrid": {
        "topicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
        "keySettingName": "EventGridKey",
        "publishRetryCount": 3,
        "publishRetryInterval": "00:00:30",
        "publishEventTypes": [
          "Started",
          "Pending",
          "Failed",
          "Terminated"
        ]
      }
    },
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "extendedSessionsEnabled": false,
    "extendedSessionIdleTimeoutInSeconds": 30,
    "useGracefulShutdown": false
  }
  }
}

```

Nomes de hubs de tarefas devem começar com uma letra e devem ser compostos somente por letras e números. Se não for especificado, o nome do hub de tarefas padrão de um aplicativo de funções será **DurableFunctionsHub**. Para obter mais informações, consulte [Hubs de tarefas](../articles/azure-functions/durable-functions-task-hubs.md).

|Propriedade  |Padrão | Descrição |
|---------|---------|---------|
|hubName|DurableFunctionsHub|Nomes alternativos para [hub de tarefas](../articles/azure-functions/durable-functions-task-hubs.md) podem ser usados para isolar vários aplicativos de Funções Duráveis uns dos outros, mesmo se eles estiverem usando o mesmo back-end de armazenamento.|
|controleQueueBatchSize|32|O número de mensagens para efetuar pull da fila de controle por vez.|
|controleQueueBufferThreshold|256|O número de mensagens de fila de controle que podem ser tamponadas na memória por um momento, momento em que o despachante aguardará antes de enfileirar quaisquer mensagens adicionais.|
|partitionCount |4|A contagem de partição para a fila de controle. Pode ser um número inteiro positivo entre 1 e 16.|
|controleQueueVisibilityTimeout |5 minutos|O limite de tempo de visibilidade das mensagens de remoção da fila de controle.|
|workItemQueueVisibilityTimeout |5 minutos|O limite de tempo de visibilidade das mensagens de remoção da fila de item de trabalho.|
|maxConcurrentActivityFuns |10 vezes o número de processadores no computador atual|O número máximo de funções de atividade que podem ser processadas simultaneamente em uma única instância de host.|
|maxConcurrentOrchestratorFunções |10 vezes o número de processadores no computador atual|O número máximo de funções do orquestrador que podem ser processadas simultaneamente em uma única instância do host.|
|maxQueuePollingInterval|30 segundos|O intervalo máximo de votação de controle e fila de trabalho no formato *hh:mm:ss.* Valores mais altos podem resultar em latências de processamento de mensagens mais altas. Valores mais baixos podem resultar em custos de armazenamento mais altos devido ao aumento das transações de armazenamento.|
|azureArmazenamentoConexãoSeqütoNome |AzureWebJobsStorage|O nome da configuração de aplicativo que tem a cadeia de conexão do Armazenamento do Azure usada para gerenciar os recursos subjacentes do Armazenamento do Azure.|
|rastreamentoStoreConexão-seqüênciaNome||O nome de uma seqüência de conexões a ser usada nas tabelas Histórico e Instâncias. Se não for `azureStorageConnectionStringName` especificado, a conexão é usada.|
|trackingStoreNamePrefix||O prefixo a ser usado `trackingStoreConnectionStringName` nas tabelas Histórico e Instâncias quando for especificado. Se não for definido, o `DurableTask`valor de prefixo padrão será . Se `trackingStoreConnectionStringName` não for especificado, as tabelas `hubName` Histórico e Instâncias usarão `trackingStoreNamePrefix` o valor como seu prefixo e qualquer configuração será ignorada.|
|traceInputsAndOutputs |false|Um valor que indica se as entradas e saídas de chamadas de função sertão rastreadas. O comportamento padrão durante o rastreamento de eventos de execução de função é incluir o número de bytes nas entradas e saídas serializadas para chamadas de função. Esse comportamento fornece informações mínimas sobre como são as entradas e saídas sem inchar os registros ou expor inadvertidamente informações confidenciais. A definição dessa propriedade como true faz com que o log de função padrão registre todo o conteúdo de entradas e saídas da função.|
|logReplayEventos|false|Um valor que indica se é necessário gravar eventos de reprodução de orquestração para o Application Insights.|
|eventGridTopicEndpoint ||A URL de um ponto de extremidade de tópico personalizado da Grade de Eventos do Azure. Quando esta propriedade é definida, eventos de notificação do ciclo de vida da orquestração são publicados neste ponto final. Esta propriedade dá suporte à resolução de Configurações do Aplicativo.|
|eventGridKeySettingNome ||O nome da configuração de aplicativo que contém a chave usada para autenticar com o tópico personalizado da Grade de Eventos do Azure em `EventGridTopicEndpoint`.|
|eventGridPublishRetryCount|0|O número de novas tentativas se a publicação no Tópico de Grade de Eventos falha.|
|eventGridPublishRetryInterval|5 minutos|A Grade de Eventos publica o intervalo de repetição no formato *hh:mm:ss*.|
|eventGridPublishTipos de eventos||Uma lista de tipos de eventos para publicar no Event Grid. Se não for especificado, todos os tipos de eventos serão publicados. Os valores `Completed` `Failed`permitidos incluem, `Terminated` `Started`, , .|
|useGracefulShutdown|false|(Visualização) Habilitar o desligamento graciosamente para reduzir a chance de desligamentos de host falhando em execuções de função no processo.|

Muitas dessas configurações são para otimizar o desempenho. Para obter mais informações, consulte [Desempenho e escala](../articles/azure-functions/durable-functions-perf-and-scale.md).
