---
title: Evento de exclusão do pool do lote do Azure concluído
description: Referência de exclusão do pool de lote evento inicial. Esse evento é emitido quando uma operação de exclusão de pool é concluída.
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
ms.openlocfilehash: 56dc4c91566cc5c41de6c91618d09e7a0ebb7172
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76929855"
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
