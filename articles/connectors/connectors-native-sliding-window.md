---
title: Agende tarefas para lidar com dados contíguos
description: Crie e execute tarefas recorrentes que lidam com dados contíguos usando janelas deslizantes em Aplicativos de Lógica do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: ab4bf802772c95d8c48a8cdba48def05e8a2761b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74786904"
---
# <a name="schedule-and-run-tasks-for-contiguous-data-by-using-the-sliding-window-trigger-in-azure-logic-apps"></a>Agende e execute tarefas para dados contíguos usando o gatilho da janela deslizante em aplicativos de lógica do Azure

Para executar regularmente tarefas, processos ou trabalhos que devem lidar com dados em pedaços contíguos, você pode iniciar seu fluxo de trabalho de aplicativo lógico com o gatilho **Janela deslizante.** Você pode definir uma data e hora, bem como um fuso horário para iniciar o fluxo de trabalho e uma recorrência para repetir esse fluxo de trabalho. Se as recorrências forem perdidas por qualquer motivo, este gatilho processa essas recorrências perdidas. Por exemplo, ao sincronizar dados entre o banco de dados e o armazenamento de backup, use o gatilho Janela deslizante para que os dados sejam sincronizados sem incorrer em lacunas. Para obter mais informações sobre os gatilhos e ações do Cronograma incorporado, consulte [Agendar e executar fluxos de trabalho automatizados, tarefas e fluxos de trabalho recorrentes com aplicativos de lógica do Azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Aqui estão alguns padrões que este gatilho suporta:

* Corra imediatamente e repita *cada número n* de segundos, minutos ou horas.

* Comece em uma data e hora específicas, depois execute e repita a cada *n* número de segundos, minutos ou horas. Com este gatilho, você pode especificar um tempo de início no passado, que executa todas as recorrências passadas.

* Atrase cada recorrência por uma duração específica antes de ser executado.

Para obter diferenças entre este gatilho e o gatilho de recorrência ou para obter mais informações sobre o agendamento de fluxos de trabalho recorrentes, consulte [Agendar e executar tarefas, processos e fluxos de trabalho automatizados recorrentes com aplicativos de lógica do Azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Se você quiser acionar seu aplicativo lógico e executar apenas uma vez no futuro, consulte [Executar trabalhos apenas](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)uma vez .

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura, você pode [se inscrever em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [aplicativos lógicos](../logic-apps/logic-apps-overview.md). Se você é novo em aplicativos lógicos, [aprenda a criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-sliding-window-trigger"></a>Adicionar gatilho de janela deslizante

1. Faça login no [portal Azure](https://portal.azure.com). Criar um aplicativo lógico em branco.

1. Depois que o Logic App Designer aparecer, na caixa de pesquisa, digite "janela deslizante" como seu filtro. Na lista de gatilhos, selecione este gatilho como o primeiro passo no fluxo de trabalho do aplicativo lógico: **Janela deslizante**

   ![Selecione o gatilho "Janela deslizante"](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. Defina o intervalo e a frequência da recorrência. Neste exemplo, defina essas propriedades para que executem o fluxo de trabalho toda semana.

   ![Definir o intervalo e a frequência](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | Propriedade | Obrigatório | Nome JSON | Type | Descrição |
   |----------|----------|-----------|------|-------------|
   | **Intervalo** | Sim | intervalo | Integer | Um inteiro positivo que descreve a frequência na qual o fluxo de trabalho é executado com base na frequência. Aqui estão os intervalos mínimos e máximos: <p>– Hora: 1 a 12.000 horas </br>– Minuto: 1 a 72.000 minutos </br>– Segundo: 1 a 9.999.999 segundos<p>Por exemplo, se o intervalo for 6, e a frequência for "Hora", então a recorrência é a cada 6 horas. |
   | **Freqüência** | Sim | frequência | String | A unidade de tempo para a recorrência: **Segundo**, **Minuto**ou **Hora** |
   ||||||

   ![Opções avançadas de recorrência](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   Para obter mais opções de recorrência, abra a lista **Adicionar novos parâmetros.** 
   Todas as opções selecionadas aparecem no gatilho após a seleção.

   | Propriedade | Obrigatório | Nome JSON | Type | Descrição |
   |----------|----------|-----------|------|-------------|
   | **Atrasar** | Não | atrasar | String | A duração para atrasar cada recorrência usando a [especificação de data ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
   | **Fuso horário** | Não | timeZone | String | Aplica-se somente quando você especifica uma hora de início, porque o gatilho não aceita [diferença UTC](https://en.wikipedia.org/wiki/UTC_offset). Selecione o fuso horário que você deseja aplicar. |
   | **Horário de início** | Não | startTime | String | Forneça uma data e hora de início neste formato: <p>AAAA-MM-DDThh:mm:ss se você selecionar um fuso horário <p>-ou- <p>AAAA-MM-DDThh:mm:ssZ se você não selecionar um fuso horário <p>Então, por exemplo, se você quiser 18 de setembro de 2017 às 14:00, então especifique "2017-09-18T14:00:00" e selecione um fuso horário como o Horário Padrão do Pacífico. Ou, especifique "2017-09-18T14:00:00Z" sem um fuso horário. <p>**Observação:** a hora de início deve seguir a [especificação de data e hora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) no [formato de data e hora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), mas sem uma [diferença UTC](https://en.wikipedia.org/wiki/UTC_offset). Se você não selecionar um fuso horário, será necessário adicionar a letra "Z" no final sem espaços. Essa letra "Z" refere-se ao equivalente em [hora náutica](https://en.wikipedia.org/wiki/Nautical_time). <p>Para horários simples, o horário de início é a primeira ocorrência, enquanto para recorrências avançadas, o gatilho não dispara mais cedo do que o horário de início. [*Quais são as maneiras que posso usar a data e hora de início?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. Agora, construa seu fluxo de trabalho restante com outras ações. Para obter mais ações que você pode adicionar, consulte [Conectores para Aplicativos de Lógica Do Azure](../connectors/apis-list.md).

## <a name="workflow-definition---sliding-window"></a>Definição do fluxo de trabalho - Janela deslizante

Na definição de fluxo de trabalho subjacente do seu aplicativo lógico, que usa JSON, você pode visualizar a definição de gatilho da Janela Deslizante com as opções escolhidas. Para ver essa definição, na barra de ferramentas do designer, escolha **'Exibir'.** Para voltar ao designer, escolha na barra de ferramentas do designer, **Designer**.

Este exemplo mostra como uma definição de gatilho da janela deslizante pode parecer em uma definição de fluxo de trabalho subjacente onde o atraso para cada recorrência é de cinco segundos para uma recorrência por hora:

``` json
"triggers": {
   "Recurrence": {
      "type": "SlidingWindow",
      "Sliding_Window": {
         "inputs": {
            "delay": "PT5S"
         },
         "recurrence": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2019-05-13T14:00:00Z",
            "timeZone": "Pacific Standard Time"
         }
      }
   }
}
```

## <a name="next-steps"></a>Próximas etapas

* [Atrase a próxima ação nos fluxos de trabalho](../connectors/connectors-native-delay.md)
* [Conectores para Aplicativos Lógicos](../connectors/apis-list.md)