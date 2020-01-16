---
title: Evento de exclusão de pool – Azure | Microsoft Docs
description: Referência de exclusão do pool de lote evento inicial.
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
ms.openlocfilehash: bcbfe009e2880c0155a067305cc28317678cbfa6
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76026702"
---
# <a name="pool-delete-complete-event"></a>Evento de conclusão de exclusão de pool

 Esse evento é emitido quando uma operação de exclusão de pool é concluída.

 O exemplo a seguir mostra o corpo de um evento de conclusão de exclusão de pool.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Elemento|Tipo|Observações|
|-------------|----------|-----------|
|`id`|String|A ID do pool.|
|`startTime`|DateTime|A hora de início da exclusão do pool.|
|`endTime`|DateTime|A hora de conclusão da exclusão do pool.|

## <a name="remarks"></a>Comentários
Para obter mais informações sobre estados e códigos de erro para a operação de redimensionamento do pool, consulte [Excluir um pool de uma conta](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).
