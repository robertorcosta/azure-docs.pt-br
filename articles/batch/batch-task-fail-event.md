---
title: Evento de falha da tarefa azure Batch
description: Referência de evento de falha de tarefa de lote. Esse evento será emitido além de um evento de conclusão de tarefa e pode ser usado para detectar falha de uma tarefa.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/15/2019
ms.author: labrenne
ms.openlocfilehash: 2bc958d6dca2b3caae665e6f9b080c651ace9ea0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022878"
---
# <a name="task-fail-event"></a>Evento de falha da tarefa

 Esse evento é emitido quando uma tarefa é concluída com falha. Atualmente, todos os códigos de saída diferente de zero são considerados falhas. Esse evento será emitido *além* de um evento de conclusão de tarefa e pode ser usado para detectar falha de uma tarefa.


 O exemplo a seguir mostra o corpo de um evento de falha da tarefa.

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 0,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "startTime": "2016-09-08T16:32:23.799Z",
        "endTime": "2016-09-08T16:34:00.666Z",
        "exitCode": 1,
        "retryCount": 2,
        "requeueCount": 0
    }
}
```

|Nome do elemento|Type|Observações|
|------------------|----------|-----------|
|`jobId`|String|A id do trabalho contendo a tarefa.|
|`id`|String|A id da tarefa.|
|`taskType`|String|O tipo de tarefa. Pode ser “JobManager” indicando que é uma tarefa do gerenciador de trabalhos ou “Usuário”, indicando que não é uma tarefa do gerenciador de trabalhos. Esse evento não é emitido para tarefas de preparação, lançamento ou inicialização de trabalho.|
|`systemTaskVersion`|Int32|Esse é o contador interno de repetição de uma tarefa. Internamente, o serviço em lotes pode repetir uma tarefa para contabilizar problemas transitórios. Esses problemas podem incluir erros internos de agendamento ou tentativa de recuperar nós de computação em estado inválido.|
|[`nodeInfo`](#nodeInfo)|Tipo complexo|Contém informações sobre o nó de computação em que a tarefa é executada.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Tipo complexo|Especifica que a tarefa é uma tarefa com várias instâncias que precisa de vários nós de computação.  Veja [`multiInstanceSettings`](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) os detalhes.|
|[`constraints`](#constraints)|Tipo complexo|As restrições de execução aplicáveis a essa tarefa.|
|[`executionInfo`](#executionInfo)|Tipo complexo|Contém informações sobre a execução da tarefa.|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a> nodeInfo

|Nome do elemento|Type|Observações|
|------------------|----------|-----------|
|`poolId`|String|O ID da piscina em que a tarefa foi realizada.|
|`nodeId`|String|A id do nó em que a tarefa foi realizada.|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a>configurações de várias instâncias

|Nome do elemento|Type|Observações|
|------------------|----------|-----------|
|`numberOfInstances`|Int32|O número de nós de computação que a tarefa precisa.|

###  <a name="constraints"></a><a name="constraints"></a>Restrições

|Nome do elemento|Type|Observações|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|O número máximo de vezes que a tarefa pode ser repetida. O serviço em lotes repetirá uma tarefa se seu código de saída for diferente de zero.<br /><br /> Observe que esse valor controla especificamente o número de tentativas. O serviço em lotes tentará a tarefa uma vez e, em seguida, pode tentar novamente até esse limite. Por exemplo, se a contagem máxima de repetição for 3, o lote tentará uma tarefa até 4 vezes (uma tentativa inicial e 3 repetições).<br /><br /> Se a contagem máxima de repetição for 0, o serviço em lote não tentará repetir a tarefas.<br /><br /> Se a contagem máxima de repetição for -1, o serviço em lotes repetirá as tarefas ilimitadamente.<br /><br /> O valor padrão é 0 (sem novas tentativas).|


###  <a name="executioninfo"></a><a name="executionInfo"></a>Executioninfo

|Nome do elemento|Type|Observações|
|------------------|----------|-----------|
|`startTime`|Datetime|A hora em que a tarefa começou a ser executada. “Em execução” corresponde ao estado de **execução**, portanto, se a tarefa especificar arquivos de recursos ou pacotes de aplicativos, a hora de início refletirá a hora na qual a tarefa começou a baixar ou implantar esses arquivos ou pacotes.  Se a tarefa foi reiniciada ou repetida, esta é a última vez em que a tarefa começou a ser executada.|
|`endTime`|Datetime|A hora e conclusão da tarefa.|
|`exitCode`|Int32|O código de saída da tarefa.|
|`retryCount`|Int32|O número de vezes que a tarefa foi repetida pelo serviço em lotes. A tarefa será repetida se a saída tiver um código de saída diferente de zero, até a MaxTaskRetryCount especificada.|
|`requeueCount`|Int32|O número de vezes que a tarefa foi colocada em fila novamente pelo serviço em lotes, como resultado de uma solicitação de usuário.<br /><br /> Quando o usuário remove nós de um pool (redimensionando ou reduzindo o pool) ou quando o trabalho é desabilitado, o usuário pode especificar que as tarefas em execução nos nós sejam colocadas novamente na fila de execução. Essa contagem controla quantas vezes a tarefa foi colocada novamente em fila por esses motivos.|
