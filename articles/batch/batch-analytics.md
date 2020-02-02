---
title: Análise de lote do Azure
description: Os tópicos na Análise de lote contêm informações de referência para os eventos e alertas disponíveis para recursos de serviço em lotes.
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
ms.openlocfilehash: 5f5063ff2b285a0613d8c7bf367007997afcb778
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935254"
---
# <a name="batch-analytics"></a>Análise de lote
Os tópicos na Análise de lote contêm informações de referência para os eventos e alertas disponíveis para recursos de serviço em lotes.

Consulte [Registro em log de diagnóstico de Lote do Azure](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) para obter mais informações sobre como habilitar e consumir logs de diagnóstico de lote.

## <a name="diagnostic-logs"></a>Logs de diagnóstico

O serviço Lote do Azure emite os eventos de log de diagnóstico a seguir durante o tempo de vida de determinados recursos do lote.

**Eventos do Log de Serviço**
* [Criação de pool](batch-pool-create-event.md)
* [Início de exclusão de pool](batch-pool-delete-start-event.md)
* [Conclusão da exclusão de pool](batch-pool-delete-complete-event.md)
* [Início de redimensionamento de pool](batch-pool-resize-start-event.md)
* [Conclusão de redimensionamento de pool](batch-pool-resize-complete-event.md)
* [Início da tarefa](batch-task-start-event.md)
* [Conclusão da tarefa](batch-task-complete-event.md)
* [Falha da tarefa](batch-task-fail-event.md)