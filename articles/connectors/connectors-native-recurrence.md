---
title: Agendar tarefas e fluxos de trabalho recorrentes
description: Agendar e executar tarefas e fluxos de trabalho automatizados recorrentes com o gatilho de recorrência nos aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: a9c167c5767a4156147e13a1e4ae21162e506474
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75445867"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Criar, agendar e executar tarefas e fluxos de trabalho recorrentes com o gatilho de recorrência nos aplicativos lógicos do Azure

Para executar tarefas, processos ou trabalhos regularmente em um agendamento específico, você pode iniciar o fluxo de trabalho do aplicativo lógico com o gatilho de **agenda de recorrência** interno. Você pode definir uma data e hora, bem como um fuso horário para iniciar o fluxo de trabalho e uma recorrência para repetir esse fluxo de trabalho. Se as recorrências forem perdidas por algum motivo, esse gatilho continuará recorrente no próximo intervalo agendado. Para obter mais informações sobre os gatilhos e ações de agendamento internos, consulte [agendar e executar tarefas automatizadas e automáticas e fluxos de trabalho com aplicativos lógicos do Azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Aqui estão alguns padrões aos quais esse gatilho dá suporte, juntamente com recorrências mais avançadas e agendas complexas:

* Executar imediatamente e repetir a cada *n* número de segundos, minutos, horas, dias, semanas ou meses.

* Inicie em uma data e hora específicas, em seguida, execute e repita a cada *n* número de segundos, minutos, horas, dias, semanas ou meses.

* Executar e repetir uma ou mais vezes por dia, por exemplo, às 8h e 17h.

* Executar e repetir a cada semana, mas apenas em dias específicos, como sábado e domingo.

* Executar e repetir a cada semana, mas somente em determinados dias e horas, como de segunda a sexta-feira, às 8h e às 17h.

Para obter as diferenças entre esse gatilho e o gatilho de janela deslizante ou para obter mais informações sobre como agendar fluxos de trabalho recorrentes, consulte [agendar e executar tarefas automatizadas recorrentes, processos e fluxos de trabalho com aplicativos lógicos do Azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Se você quiser disparar seu aplicativo lógico e executar apenas uma vez no futuro, consulte [executar trabalhos apenas uma vez](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [aplicativos lógicos](../logic-apps/logic-apps-overview.md). Se você for novo em aplicativos lógicos, saiba [como criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-recurrence-trigger"></a>Adicionar gatilho de recorrência

1. Entre no [portal do Azure](https://portal.azure.com). Criar um aplicativo lógico em branco.

1. Depois que o designer de aplicativo lógico aparecer, na caixa de `recurrence` pesquisa, insira como seu filtro. Na lista de gatilhos, selecione este gatilho como a primeira etapa no fluxo de trabalho do aplicativo lógico: **recorrência**

   ![Selecione o gatilho "recorrência"](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Defina o intervalo e a frequência da recorrência. Neste exemplo, defina essas propriedades para que executem o fluxo de trabalho toda semana.

   ![Definir o intervalo e a frequência](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Propriedade | Nome JSON | Obrigatório | Type | Descrição |
   |----------|-----------|----------|------|-------------|
   | **Intervalo** | `interval` | Sim | Integer | Um inteiro positivo que descreve a frequência na qual o fluxo de trabalho é executado com base na frequência. Aqui estão os intervalos mínimos e máximos: <p>– Mês: 1 a 16 meses </br>–Dia: 1 a 500 dias </br>– Hora: 1 a 12.000 horas </br>– Minuto: 1 a 72.000 minutos </br>– Segundo: 1 a 9.999.999 segundos<p>Por exemplo, se o intervalo for 6 e a frequência for "Mês", a recorrência será a cada 6 meses. |
   | **Frequência** | `frequency` | Sim | String | A unidade de tempo para a recorrência: **Segundo**, **Minuto**, **Hora**, **Dia**, **Semana** ou **Mês** |
   ||||||

   > [!IMPORTANT]
   > Quando as recorrências não especificam opções de agendamento avançadas, as recorrências futuras são baseadas na última hora de execução.
   > As horas de início para essas recorrências podem ser desligadas devido a fatores como latência durante chamadas de armazenamento. Para certificar-se de que seu aplicativo lógico não perca uma recorrência, especialmente quando a frequência estiver em dias ou mais, use uma destas opções:
   > 
   > * Forneça uma hora de início para a recorrência.
   > 
   > * Especifique as horas e os minutos para quando executar a recorrência usando as propriedades a **seguir horas** e **em minutos** .
   > 
   > * Use o [gatilho de janela deslizante](../connectors/connectors-native-sliding-window.md), em vez do gatilho de recorrência.

1. Para definir opções avançadas de agendamento, abra a lista **Adicionar novo parâmetro** . As opções que você selecionar aparecerão no gatilho após a seleção.

   ![Opções de agendamento avançadas](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Propriedade | Nome JSON | Obrigatório | Type | Descrição |
   |----------|-----------|----------|------|-------------|
   | **Fuso horário** | `timeZone` | Não | String | Aplica-se somente quando você especifica uma hora de início, porque o gatilho não aceita [diferença UTC](https://en.wikipedia.org/wiki/UTC_offset). Selecione o fuso horário que você deseja aplicar. |
   | **Hora de início** | `startTime` | Não | String | Forneça uma data e hora de início neste formato: <p>AAAA-MM-DDThh:mm:ss se você selecionar um fuso horário <p>-ou- <p>AAAA-MM-DDThh:mm:ssZ se você não selecionar um fuso horário <p>Por exemplo, se você quiser 18 de setembro de 2017 às 2:00 PM, especifique "2017-09-18T14:00:00" e selecione um fuso horário como hora padrão do Pacífico. Ou, especifique "2017-09-18T14:00:00Z" sem um fuso horário. <p>**Observação:** Essa hora de início tem um máximo de 49 anos no futuro e deve seguir a [especificação de data e hora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) no [formato de data e hora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), mas sem um [deslocamento UTC](https://en.wikipedia.org/wiki/UTC_offset). Se você não selecionar um fuso horário, será necessário adicionar a letra "Z" no final sem espaços. Essa letra "Z" refere-se ao equivalente em [hora náutica](https://en.wikipedia.org/wiki/Nautical_time). <p>Para agendamentos simples, a hora de início é a primeira ocorrência, enquanto que, para agendamentos complexos, o gatilho não é disparado antes da hora de início. [*Quais são as maneiras que posso usar a data e hora de início?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **Nestes dias** | `weekDays` | Não | Cadeia de caracteres ou matriz de cadeia de caracteres | Se você selecionar "Semana", poderá selecionar um ou mais dias nos quais deseja executar o fluxo de trabalho: **segunda-feira**, **terça-feira**, **quarta-feira**, **quinta-feira**, **Sexta-feira**, **sábado** e **domingo** |
   | **A estas horas** | `hours` | Não | Inteiro ou matriz de inteiros | Se você selecionar "dia" ou "semana", poderá selecionar um ou mais números inteiros de 0 a 23 como as horas do dia para quando você deseja executar o fluxo de trabalho. <p><p>Por exemplo, se você especificar "10", "12" e "14", obterá 10 AM, 12 PM e 2 PM para as horas do dia, mas os minutos do dia serão calculados com base em quando a recorrência for iniciada. Para definir os minutos do dia, especifique o valor para a propriedade a **seguir minutos** . |
   | **A estes minutos** | `minutes` | Não | Inteiro ou matriz de inteiros | Se você selecionar "Dia" ou "Semana", poderá selecionar um ou mais números inteiros, de 0 a 59, como os minutos da hora em que você deseja executar o fluxo de trabalho. <p>Por exemplo, você pode especificar "30" como a marca de minutos e, usando o exemplo anterior como as horas do dia, você obtém 10h30, 12h30 e 14h30. |
   |||||

   Por exemplo, suponha que hoje é segunda-feira, 4 de setembro de 2017. O gatilho de recorrência a seguir não é acionado *antes* da data e hora de início, que é segunda-feira, 18 de setembro de 2017 às 8:00 am PST. No entanto, o agendamento da recorrência está definido para 10h30, 12h30 e 14h30, somente às segundas-feiras. Portanto, a primeira vez que o gatilho será acionado e criará uma instância de fluxo de trabalho do aplicativo lógico será às 10h30. Para saber mais sobre como as horas de início funcionam, consulte estes [exemplos de hora de início](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time).

   As execuções futuras acontecerão às 12h30 e às 14h30 no mesmo dia. Cada recorrência cria sua própria instância de fluxo de trabalho. Depois disso, todo o agendamento se repete novamente na próxima segunda-feira. [*Quais são outras ocorrências de exemplo?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Exemplo de agentamento avançado](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > O gatilho mostra uma visualização da recorrência especificada somente quando você seleciona "Dia" ou "Semana" como a frequência.

1. Agora, crie seu fluxo de trabalho restante com outras ações. Para obter mais ações que você pode adicionar, consulte [conectores para aplicativos lógicos do Azure](../connectors/apis-list.md).

## <a name="workflow-definition---recurrence"></a>Definição de fluxo de trabalho-recorrência

Na definição de fluxo de trabalho subjacente do aplicativo lógico, que usa JSON, você pode exibir a [definição do gatilho de recorrência](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) com as opções escolhidas. Para exibir essa definição, na barra de ferramentas do designer, escolha **exibição de código**. Para retornar ao designer, escolha na barra de ferramentas do designer, **Designer**.

Este exemplo mostra como uma definição de gatilho de recorrência pode parecer em uma definição de fluxo de trabalho subjacente:

``` json
"triggers": {
   "Recurrence": {
      "type": "Recurrence",
      "recurrence": {
         "frequency": "Week",
         "interval": 1,
         "schedule": {
            "hours": [
               10,
               12,
               14
            ],
            "minutes": [
               30
            ],
            "weekDays": [
               "Monday"
            ]
         },
         "startTime": "2017-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

## <a name="next-steps"></a>Próximas etapas

* [Pausar fluxos de trabalho com ações de atraso](../connectors/connectors-native-delay.md)
* [Conectores para Aplicativos Lógicos](../connectors/apis-list.md)
