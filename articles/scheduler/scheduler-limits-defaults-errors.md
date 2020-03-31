---
title: Limites e cotas e limitações no Agendador do Azure
description: Saiba mais sobre limites, cotas, valores padrão e limitações do Agendador do Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 610232dab776648bb3dcc7c301ec292e9acad9fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898519"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Limites e cotas e limitações no Agendador do Azure

> [!IMPORTANT]
> [A Azure Logic Apps](../logic-apps/logic-apps-overview.md) está substituindo o Azure Scheduler, que está [sendo aposentado](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Para continuar trabalhando com os trabalhos que você criou no Scheduler, [migre para o Azure Logic Apps o](../scheduler/migrate-from-scheduler-to-logic-apps.md) mais rápido possível. 
>
> O agendador não está mais disponível no portal Azure, mas os cmdlets [Do API e](/rest/api/scheduler) [do Azure Scheduler PowerShell](scheduler-powershell-reference.md) permanecem disponíveis neste momento para que você possa gerenciar seus trabalhos e coletas de empregos.

## <a name="limits-quotas-and-thresholds"></a>Limites, cotas e limitações

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>Cabeçalho x-ms-request-id

Cada solicitação feita contra o serviço Agendador retorna um cabeçalho de resposta chamado **x-ms-request-id**. Este cabeçalho contém um valor opaco que identifica exclusivamente a solicitação. Portanto, se você confirmar que uma solicitação está formatada corretamente e ela falhar de forma consistente, relate o erro à Microsoft fornecendo o valor do cabeçalho de resposta **x-ms-request-id** e incluindo estes detalhes: 

* O valor de **x-ms-request-id**
* O tempo aproximado quando a solicitação foi feita 
* Os identificadores da assinatura do Azure, da coleção de trabalhos e do trabalho 
* O tipo de operação que a solicitação tentou

## <a name="next-steps"></a>Próximas etapas

* [Conceitos, terminologia e hierarquia de entidades do Agendador do Azure](scheduler-concepts-terms.md)
* [Planos e Cobrança do Agendador do Azure](scheduler-plans-billing.md)
* [Referência da API REST do Agendador do Azure](/rest/api/scheduler)
* [Referência de cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md)