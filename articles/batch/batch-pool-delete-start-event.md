---
title: Evento inicial de exclusão do pool do Lote do Azure
description: Referência para excluir um pool de lote evento inicial. Esse evento é emitido quando uma operação de exclusão de pool é iniciada.
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: d4fac833b6c77258e48b38838f6b7b133738a6e8
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83723928"
---
# <a name="pool-delete-start-event"></a>Evento inicial de exclusão de pool

 Esse evento é emitido quando uma operação de exclusão de pool é iniciada. Como a exclusão do pool é um evento assíncrono, você pode esperar que um evento completo de conclusão de exclusão de pool seja emitido quando a operação de exclusão é concluída.

 O exemplo a seguir mostra o corpo de um evento de início de exclusão de pool.

```
{
    "id": "myPool1"
}
```

|Elemento|Type|Observações|
|-------------|----------|-----------|
|`id`|String|A ID do pool.|
