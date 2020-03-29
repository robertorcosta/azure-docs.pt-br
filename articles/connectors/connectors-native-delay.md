---
title: Atrase a próxima ação nos fluxos de trabalho
description: Aguarde para executar a próxima ação em fluxos de trabalho de aplicativos lógicos usando o Delay ou Delay Until ações in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
tags: connectors
ms.openlocfilehash: 5348ade1ba6eec6cbd360849411b4520cb3c2b19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74787329"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>Demore a executar a próxima ação no Azure Logic Apps

Para que seu aplicativo lógico espere um pouco antes de executar a próxima ação, você pode adicionar a ação de atraso incorporado **- Agende** antes de uma ação no fluxo de trabalho do seu aplicativo lógico. Ou, você pode adicionar o Atraso incorporado **até - Agendar** a ação para esperar até uma data e hora específicas antes de executar a próxima ação. Para obter mais informações sobre as ações e gatilhos do Cronograma incorporado, consulte [Agendar e executar fluxos de trabalho automatizados, tarefas e fluxos de trabalho recorrentes com aplicativos de lógica do Azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

* **Atraso**: Aguarde o número especificado de unidades de tempo, como segundos, minutos, horas, dias, semanas ou meses, antes da próxima ação ser executada.

* **Atrase até**: Aguarde até a data e hora especificadas antes que a próxima ação seja executada.

Aqui estão algumas maneiras de usar essas ações:

* Aguardar até um dia útil para enviar uma atualização de status por email.

* Atrase seu fluxo de trabalho até que uma chamada HTTP termine antes de retomar e recuperar dados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura, você pode [se inscrever em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [aplicativos lógicos](../logic-apps/logic-apps-overview.md). Antes de usar uma ação, seu aplicativo lógico deve começar com um gatilho. Você pode usar qualquer gatilho que quiser e adicionar outras ações antes de adicionar uma ação de atraso. Este tópico usa um gatilho do Office 365 Outlook. Se você é novo em aplicativos lógicos, [aprenda a criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>Adicionar a ação Atraso

1. No Logic App Designer, a etapa em que você deseja adicionar a ação de atraso, escolha **Nova etapa**.

   Para adicionar a ação de atraso entre as etapas, mova o ponteiro sobre a seta que conecta as etapas. Escolha o sinal de mais (+) que aparece e **selecione Adicionar uma ação**.

1. Na caixa de pesquisa, digite "delay" como seu filtro. Na lista de ações, selecione esta ação: **Atraso**

   ![Adicionar ação "Retardar"](./media/connectors-native-delay/add-delay-action.png)

1. Especifique a quantidade de tempo para esperar antes que a próxima ação seja executada.

   ![Definir o tempo para o atraso](./media/connectors-native-delay/delay-time-intervals.png)

   | Propriedade | Nome JSON | Obrigatório | Type | Descrição |
   |----------|-----------|----------|------|-------------|
   | Contagem | count | Sim | Integer | O número de unidades de tempo a serem atrasadas |
   | Unidade | unit | Sim | String | A unidade de tempo, `Second` `Minute`por `Hour` `Day`exemplo: , , , , `Week`ou`Month` |
   ||||||

1. Adicione quaisquer outras ações que você deseja executar em seu fluxo de trabalho.

1. Quando terminar, salve o aplicativo lógico.

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>Adicione a ação Delay-till

1. No Logic App Designer, a etapa em que você deseja adicionar a ação de atraso, escolha **Nova etapa**.

   Para adicionar a ação de atraso entre as etapas, mova o ponteiro sobre a seta que conecta as etapas. Escolha o sinal de mais (+) que aparece e **selecione Adicionar uma ação**.

1. Na caixa de pesquisa, digite "delay" como seu filtro. Na lista de ações, selecione esta ação: **Atrase até**

   ![Adicionar ação "Atrasar até"](./media/connectors-native-delay/add-delay-until-action.png)

1. Forneça a data e a hora final para quando você deseja retomar o fluxo de trabalho.

   ![Especificar carimbo de data para quando terminar o atraso](./media/connectors-native-delay/delay-until-timestamp.png)

   | Propriedade | Nome JSON | Obrigatório | Type | Descrição |
   |----------|-----------|----------|------|-------------|
   | Timestamp |  timestamp | Sim | String | A data final e a hora para a retomada do fluxo de trabalho usando este formato: <p>YYYY-MM-DDThh:mm:ssZ <p>Então, por exemplo, se você quiser 18 de setembro de 2017 às 14:00, especifique "2017-09-18T14:00:00Z". <p>**Nota:** Este formato de tempo deve seguir a [especificação de data ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) no [formato de data UTC,](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)mas sem uma [compensação UTC](https://en.wikipedia.org/wiki/UTC_offset). Sem fuso horário, você deve adicionar a letra "Z" no final sem espaços. Essa letra "Z" refere-se ao equivalente em [hora náutica](https://en.wikipedia.org/wiki/Nautical_time). |
   ||||||

1. Adicione quaisquer outras ações que você deseja executar em seu fluxo de trabalho.

1. Quando terminar, salve o aplicativo lógico.

## <a name="next-steps"></a>Próximas etapas

* [Crie, agende e execute tarefas e fluxos de trabalho recorrentes com o gatilho Recorrência](../connectors/connectors-native-recurrence.md)
* [Conectores para Aplicativos Lógicos](../connectors/apis-list.md)