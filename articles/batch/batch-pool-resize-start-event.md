---
title: Evento inicial de redimensionamento do pool do lote do Azure
description: Referência de redimensionamento do pool de lote evento inicial. Exemplo mostra o corpo de um evento de início de redimensionamento de pool para um redimensionamento de pool de 0 a 2 nós com um redimensionamento manual.
services: batch
author: ju-shim
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: jushiman
ms.openlocfilehash: 8e2df1c2264392ab79e2dcbb7e58977f341b14d9
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76026621"
---
# <a name="pool-resize-start-event"></a>Evento inicial de redimensionamento de pool

 Esse evento é emitido quando um redimensionamento de pool é iniciado. Como o redimensionamento do pool é um evento assíncrono, você pode esperar que um evento completo de redimensionamento de pool seja emitido quando a operação de redimensionamento é concluída.

 O exemplo a seguir mostra o corpo de um evento inicial de redimensionamento de pool para um redimensionamento de 0 a 2 nós com redimensionamento manual.

```
{
    "id": "myPool1",
    "nodeDeallocationOption": "Invalid",
    "currentDedicatedNodes": 0,
    "targetDedicatedNodes": 2,
    "currentLowPriorityNodes": 0,
    "targetLowPriorityNodes": 2,
    "enableAutoScale": false,
    "isAutoPool": false
}
```

|Elemento|Tipo|Observações|
|-------------|----------|-----------|
|`id`|String|A ID do pool.|
|`nodeDeallocationOption`|String|Especifica quando os nós poderão ser removidos do pool, se o tamanho do pool estiver diminuindo.<br /><br /> Os valores possíveis são:<br /><br /> **colocar novamente na fila** – Finalize as tarefas em execução e coloque-as novamente na fila. As tarefas serão executadas novamente quando o trabalho for habilitado. Remova nós assim que tarefas forem terminadas.<br /><br /> **terminar** – Termine as tarefas em execução. As tarefas não serão executadas novamente. Remova nós assim que tarefas forem terminadas.<br /><br /> **taskcompletion** – Permita a conclusão das tarefas atualmente em execução. Não agende novas tarefas enquanto aguarda. Remova nós quando todas as tarefas forem concluídas.<br /><br /> **Retaineddata** – Permita que as tarefas atualmente em execução sejam concluídas e aguarde que os todos os períodos de retenção de dados das tarefas expirem. Não agende novas tarefas enquanto aguarda. Remova nós quando todos os períodos de retenção de tarefa expirem.<br /><br /> O valor padrão é colocar novamente na fila.<br /><br /> Se o tamanho do pool aumentar, o valor será definido como **inválido**.|
|`currentDedicatedNodes`|Int32|O número de nós de computação atualmente atribuídos ao pool.|
|`targetDedicatedNodes`|Int32|O número de nós de computação solicitados para o pool.|
|`currentLowPriorityNodes`|Int32|O número de nós de computação atualmente atribuídos ao pool.|
|`targetLowPriorityNodes`|Int32|O número de nós de computação solicitados para o pool.|
|`enableAutoScale`|Bool|Especifica se o tamanho do pool é ajustado automaticamente com o tempo.|
|`isAutoPool`|Bool|Especifica se o pool foi criado por meio de um mecanismo de AutoPool do trabalho.|
