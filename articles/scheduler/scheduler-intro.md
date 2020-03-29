---
title: O que é o Agendador do Azure?
description: Crie cronograma e execute trabalhos automatizados que chamam serviços dentro ou fora do Azure
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: e851da0013cf3a9ff6bb1a0fc1c073b5b796c54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898546"
---
# <a name="what-is-azure-scheduler"></a>O que é o Agendador do Azure?

> [!IMPORTANT]
> [A Azure Logic Apps](../logic-apps/logic-apps-overview.md) está substituindo o Azure Scheduler, que está [sendo aposentado](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Para continuar trabalhando com os trabalhos que você criou no Scheduler, [migre para o Azure Logic Apps o](../scheduler/migrate-from-scheduler-to-logic-apps.md) mais rápido possível. 
>
> O agendador não está mais disponível no portal Azure, mas os cmdlets [Do API e](/rest/api/scheduler) [do Azure Scheduler PowerShell](scheduler-powershell-reference.md) permanecem disponíveis neste momento para que você possa gerenciar seus trabalhos e coletas de empregos.

O [Agendador do Azure](https://azure.microsoft.com/services/scheduler/) ajuda você a criar [trabalhos](../scheduler/scheduler-concepts-terms.md) que são executados na nuvem, descrevendo declarativamente ações. O serviço então agenda e executa automaticamente essas ações. Por exemplo, você pode chamar serviços dentro e fora do Azure, como chamar pontos de extremidade HTTP ou HTTPS, e também publicar mensagens em filas de Armazenamento do Azure e filas ou tópicos do Barramento de Serviço do Azure. Você pode executar trabalhos imediatamente ou em um momento posterior. O Agendador dá facilmente suporte para [agendas complexas e recorrência avançadas](../scheduler/scheduler-advanced-complexity.md). O Agendador especifica quando executar trabalhos, mantém um histórico dos resultados do trabalho que você pode examinar e, em seguida, agenda de modo previsível e confiável cargas de trabalho para serem executadas.

Outros recursos de agendamento do Azure também usam o Agendador em segundo plano, por exemplo, [Azure WebJobs](../app-service/webjobs-create.md), que é um recurso de [Aplicativos Web](https://azure.microsoft.com/services/app-service/web/) no Serviço de Aplicativo do Azure. Você pode gerenciar a comunicação para essas ações usando a [API Do Agendador REST](https://docs.microsoft.com/rest/api/scheduler/), que ajuda a gerenciar a comunicação para essas ações.

Aqui estão alguns cenários em que o Agendador pode ajudá-lo:

* Executar ações recorrentes de aplicativo: por exemplo, periodicamente, colete dados do Twitter em um feed.

* Executar a manutenção diária: como remoção diária de logs, realização de backups e outras tarefas de manutenção.

  Por exemplo, como administrador, você talvez queira fazer backup de seu banco de dados à 1h diariamente nos próximos nove meses.

Embora você possa usar o Agendador para criar, manter e executar cargas de trabalho agendadas, o Agendador não hospeda cargas de trabalho nem executa código. O serviço apenas *invoca* os serviços ou o código hospedados em outro lugar, por exemplo, no Azure, localmente ou com outro provedor. O Agendador pode invocar por meio de HTTP, HTTPS, uma fila de Armazenamento, uma fila do Barramento de Serviço ou um tópico do Barramento de Serviço.

Para criar, agendar, gerenciar, atualizar ou excluir empregos e [coleções de empregos,](../scheduler/scheduler-concepts-terms.md)você pode usar o código, a [API Do Agendador REST](https://docs.microsoft.com/rest/api/scheduler/)ou os [cmdlets PowerShell do Agendador Azure.](scheduler-powershell-reference.md)

## <a name="next-steps"></a>Próximas etapas

* [Conceitos, terminologia e hierarquia de entidades do Agendador do Azure](scheduler-concepts-terms.md)
* [Planos e Cobrança do Agendador do Azure](scheduler-plans-billing.md)
* [Como compilar agendas complexas e recorrência avançada com o Agendador do Microsoft Azure](scheduler-advanced-complexity.md)
* [Referência da API REST do Agendador do Azure](/rest/api/scheduler)
* [Referência de cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md)
