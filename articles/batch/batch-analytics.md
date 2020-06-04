---
title: Análise de Lote do Azure
description: Os tópicos na Análise de lote contêm informações de referência para os eventos e alertas disponíveis para recursos de serviço em lotes.
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: b07d362c3cc2d3b086cf0a82325025604907a393
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726852"
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