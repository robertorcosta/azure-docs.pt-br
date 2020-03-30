---
title: Agendamento de tarefas e fluxos de trabalho recorrentes em Aplicativos de Lógica do Azure
description: Uma visão geral sobre o agendamento de tarefas, processos e fluxos de trabalho automatizados recorrentes com aplicativos de lógica do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 0f6ec158cf6ab855191e6796be3abec7d37439a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270557"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Agendar e executar tarefas automatizadas, processos e fluxos de trabalho recorrentes com Aplicativos Lógicos do Azure

Logic Apps ajuda você a criar e executar tarefas e processos recorrentes automatizados em um cronograma. Ao criar um fluxo de trabalho de aplicativo lógico que começa com um gatilho de recorrência incorporado ou gatilho de janela deslizante, que são gatilhos do tipo Agenda, você pode executar tarefas imediatamente, em um tempo posterior ou em um intervalo recorrente. Você pode ligar para serviços dentro e fora do Azure, como pontos finais HTTP ou HTTPS, postar mensagens para serviços do Azure, como O Azure Storage e O Ônibus de Serviço Do Azure, ou obter arquivos carregados para um compartilhamento de arquivos. Com o gatilho Recorrência, você também pode configurar horários complexos e recorrências avançadas para executar tarefas. Para saber mais sobre os gatilhos e ações do Cronograma integrado, consulte [Gatilhos de agendamento](#schedule-triggers) e [ações de Agendamento.](#schedule-actions) 

> [!TIP]
> Você pode agendar e executar cargas de trabalho recorrentes sem criar um aplicativo lógico separado para cada trabalho programado e correr para o [limite de fluxos de trabalho por região e assinatura](../logic-apps/logic-apps-limits-and-config.md#definition-limits). Em vez disso, você pode usar o padrão de aplicativo lógico criado pelo [modelo Azure QuickStart: Agendador de trabalho logic apps](https://github.com/Azure/azure-quickstart-templates/tree/master/301-logicapps-jobscheduler/).
>
> O modelo de agendador de trabalho do Logic Apps cria um aplicativo de lógica CreateTimerJob que chama um aplicativo de lógica TimerJob. Em seguida, você pode chamar o aplicativo de lógica CreateTimerJob como uma API fazendo uma solicitação HTTP e passando um cronograma como entrada para a solicitação. Cada chamada para o aplicativo de lógica CreateTimerJob também chama o aplicativo de lógica TimerJob, que cria uma nova instância timerJob que é executada continuamente com base no cronograma especificado ou até atingir um limite especificado. Dessa forma, você pode executar quantas instâncias do TimerJob quiser sem se preocupar com os limites do fluxo de trabalho, porque as instâncias não são definições ou recursos de fluxo de trabalho de aplicativos lógicos individuais.

Esta lista mostra alguns exemplos de tarefas que você pode executar com os gatilhos incorporados agendar:

* Obtenha dados internos, como executar um procedimento armazenado sql todos os dias.

* Obtenha dados externos, como relatórios meteorológicos da NOAA a cada 15 minutos.

* Envie dados do relatório, como um resumo por e-mail para todos os pedidos maiores que um valor específico na última semana.

* Dados de processo, como compactar as imagens enviadas hoje todos os dias da semana durante o horário de pico.

* Limpe dados, como excluir todos os tweets com mais de três meses.

* Arquive dados, como empurrar faturas para um serviço de backup à 1:00 AM todos os dias durante os próximos nove meses.

Você também pode usar as ações incorporadas do Agendamento para pausar o fluxo de trabalho antes que a próxima ação sejam executadas, por exemplo:

* Aguardar até um dia útil para enviar uma atualização de status por email.

* Atrasar o fluxo de trabalho até que uma chamada HTTP tenha tempo para ser concluída antes da retomada e da recuperação do resultado.

Este artigo descreve as capacidades para os gatilhos e ações incorporados do Cronograma.

<a name="schedule-triggers"></a>

## <a name="schedule-triggers"></a>Acionamentos de programação

Você pode iniciar seu fluxo de trabalho de aplicativo lógico usando o gatilho de recorrência ou gatilho da janela deslizante, que não está associado a nenhum serviço ou sistema específico, por exemplo, Office 365 Outlook ou SQL Server. Esses gatilhos iniciam e executam seu fluxo de trabalho com base na recorrência especificada onde você seleciona o intervalo e a frequência, como o número de segundos, minutos e horas para ambos os gatilhos, ou o número de dias, semanas ou meses para o gatilho Recorrência. Você também pode definir a data e a hora de início, bem como o fuso horário. Cada vez que um gatilho é acionado, o Logic Apps cria e executa uma nova instância de fluxo de trabalho para o seu aplicativo lógico.

Aqui estão as diferenças entre esses gatilhos:

* **Recorrência**: Executa seu fluxo de trabalho em intervalos de tempo regulares com base no seu cronograma especificado. Se as recorrências forem perdidas, o gatilho Recorrência não processa as recorrências perdidas, mas reinicia as recorrências com o próximo intervalo programado. Você pode especificar uma data e hora de início, bem como o fuso horário. Se você selecionar "Dia", você pode especificar horas do dia e minutos da hora, por exemplo, todos os dias às 2:30. Se você selecionar "Semana", você também pode selecionar dias da semana, como quarta e sábado. Para obter mais informações, consulte [Criar, agendar e executar tarefas e fluxos de trabalho recorrentes com o gatilho Recorrência](../connectors/connectors-native-recurrence.md).

* **Janela deslizante**: Executa seu fluxo de trabalho em intervalos de tempo regulares que lidam com dados em blocos contínuos. Se as recorrências forem perdidas, o gatilho da janela deslizante volta e processa as recorrências perdidas. Você pode especificar uma data e hora de início, fuso horário e uma duração para atrasar cada recorrência em seu fluxo de trabalho. Este gatilho não tem opções para especificar dias, semanas e meses, horas do dia, minutos da hora e dias da semana. Para obter mais informações, consulte [Criar, agendar e executar tarefas e fluxos de trabalho recorrentes com o gatilho janela deslizante](../connectors/connectors-native-sliding-window.md).

<a name="schedule-actions"></a>

## <a name="schedule-actions"></a>Agendar ações

Depois de qualquer ação no fluxo de trabalho do aplicativo lógico, você pode usar o Atraso e O Atraso Até que as ações façam seu fluxo de trabalho esperar antes que a próxima ação seja executada.

* **Atraso**: Aguarde para executar a próxima ação para o número especificado de unidades de tempo, como segundos, minutos, horas, dias, semanas ou meses. Para obter mais informações, consulte [Atrase a próxima ação em fluxos de trabalho](../connectors/connectors-native-delay.md).

* **Atrase até**: Aguarde para executar a próxima ação até a data e hora especificadas. Para obter mais informações, consulte [Atrase a próxima ação em fluxos de trabalho](../connectors/connectors-native-delay.md).

## <a name="patterns-for-start-date-and-time"></a>Padrões para data e hora de início

<a name="start-time"></a>

Aqui estão alguns padrões que mostram como você pode controlar a recorrência com a data e a hora de início, e como o serviço Logic Apps executa essas recorrências:

| Hora de início | Recorrência sem agendamento | Recorrência com cronograma (somente gatilho de recorrência) |
|------------|-----------------------------|----------------------------------------------------|
| {none} | Executa a primeira carga de trabalho imediatamente. <p>Executa futuras cargas de trabalho com base na última hora de execução. | Executa a primeira carga de trabalho imediatamente. <p>Executa futuras cargas de trabalho com base no agendamento especificado. |
| Hora de início no passado | **Gatilho de recorrência:** Calcula os tempos de execução com base no tempo de início especificado e descarta tempos de execução passados. Executa a primeira carga de trabalho na próxima hora de execução no futuro. <p>Executa futuras cargas de trabalho com base em cálculos da última hora de execução. <p><p>**Gatilho da janela deslizante:** calcula os tempos de execução com base no tempo de início especificado e honra os tempos de execução anteriores. <p>Executa cargas de trabalho futuras com base nos cálculos do tempo de início especificado. <p><p>Para obter mais explicações, consulte o exemplo após essa tabela. | Executa a primeira carga de trabalho *não antes* da hora de início, com base no agendamento calculado com base na hora de início. <p>Executa futuras cargas de trabalho com base no agendamento especificado. <p>**Observação:** se você especificar uma recorrência com um agendamento, mas não especificar horas ou minutos para esse agendamento, as horas de execução futuras serão calculadas usando as horas ou os minutos, respectivamente, da primeira hora de execução. |
| Hora de início no presente ou no futuro | Executa a primeira carga de trabalho na hora de início especificada. <p>Executa futuras cargas de trabalho com base em cálculos da última hora de execução. | Executa a primeira carga de trabalho *não antes* da hora de início, com base no agendamento calculado com base na hora de início. <p>Executa futuras cargas de trabalho com base no agendamento especificado. <p>**Observação:** se você especificar uma recorrência com um agendamento, mas não especificar horas ou minutos para esse agendamento, as horas de execução futuras serão calculadas usando as horas ou os minutos, respectivamente, da primeira hora de execução. |
||||

> [!IMPORTANT]
> Quando as recorrências não especificam opções avançadas de agendamento, as recorrências futuras são baseadas no último tempo de execução.
> Os tempos de início dessas recorrências podem cair devido a fatores como latência durante chamadas de armazenamento. Para garantir que seu aplicativo lógico não perca uma recorrência, especialmente quando a frequência estiver em dias ou mais, use uma dessas opções:
> 
> * Forneça um tempo de início para a recorrência.
> 
> * Especifique as horas e minutos para quando executar a recorrência usando as **propriedades Nessas horas** e nessas propriedades **minutos.**
> 
> * Use o [gatilho da janela deslizante,](../connectors/connectors-native-sliding-window.md)em vez do gatilho Recorrência.

*Exemplo para tempo de início passado e recorrência, mas sem cronograma*

Suponha que a data e hora atual é 8 de setembro de 2017 às 13:00. Você especifica a data e a hora de início como 7 de setembro de 2017 às 14:00, que é no passado, e uma recorrência que é executada a cada dois dias.

| Hora de início | Hora atual | Recorrência | Agenda |
|------------|--------------|------------|----------|
| 2017-09-**07**T14:00:00Z <br>(2017-09-**07** às 14:00) | 2017-09-**08**T13:00:00Z <br>(2017-09-**08** às 13:00) | A cada dois dias | {none} |
|||||

Para o gatilho Recorrência, o mecanismo Logic Apps calcula os tempos de execução com base no tempo de início, descarta tempos de execução passados, usa o próximo tempo de início futuro para a primeira corrida e calcula as corridas futuras com base no último tempo de execução.

Veja a aparência dessa recorrência:

| Hora de início | Primeira hora de execução | Horas de execução futuras |
|------------|----------------|------------------|
| 2017-09-**07** às 14h | 2017-09-**09** às 14h | 2017-09-**11** às 14h </br>2017-09-**13** às 14h </br>2017-09-**15** às 14h </br>e assim por diante... |
||||

Então, não importa o quão longe no passado você especifique o horário**05** de início, por exemplo, 2017-09-05 às 14:00 ou 2017-09-01 às 14:00, sua primeira corrida sempre usa o próximo horário de início futuro.**01**

Para o gatilho da janela deslizante, o mecanismo Logic Apps calcula os tempos de execução com base no tempo de início, honra os tempos de execução passados, usa o tempo de início para a primeira corrida e calcula as corridas futuras com base no tempo de início.

Veja a aparência dessa recorrência:

| Hora de início | Primeira hora de execução | Horas de execução futuras |
|------------|----------------|------------------|
| 2017-09-**07** às 14h | 2017-09-**07** às 14h | 2017-09-**09** às 14h </br>2017-09-**11** às 14h </br>2017-09-**13** às 14h </br>2017-09-**15** às 14h </br>e assim por diante... |
||||

Então, não importa o quão longe no passado você especifique o horário**05** de início, por exemplo, 2017-09-05 às 14:00 ou 2017-09-01 às 14:00, sua primeira corrida sempre usa o horário de início especificado.**01**

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Exemplos de recorrências

Aqui estão várias recorrências de exemplo que você pode configurar para os gatilhos que suportam as opções:

| Gatilho | Recorrência | Intervalo | Frequência | Hora de início | Nestes dias | A estas horas | A estes minutos | Observação |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Recorrência <br>Janela Deslizante | Executar a cada 15 minutos (sem data e hora de início) | 15 | Minuto | {none} | {não disponível} | {none} | {none} | Esse agendamento será iniciado imediatamente e, em seguida, calculará as recorrências futuras com base na última hora de execução. |
| Recorrência <br>Janela Deslizante | Executar a cada 15 minutos (com data e hora de início) | 15 | Minuto | *startDate*T*startTime*Z | {não disponível} | {none} | {none} | Esse agendamento não se inicia *antes* da data e hora de início especificada e, em seguida, calcula as recorrências futuras com base na última hora de execução. |
| Recorrência <br>Janela Deslizante | Executar a cada hora, na hora cheia (com data e hora de início) | 1 | Hora | *startDate*Thh:00:00Z | {não disponível} | {none} | {none} | Esse agendamento não se inicia *antes* da data e hora de início especificadas. Recorrências futuras são executadas a cada hora na marca de "00" minutos, que é calculada a partir do momento inicial. <p>Se a frequência for "Semana" ou "Mês", esse agendamento será executado, respectivamente, somente um dia por semana ou um dia por mês. |
| Recorrência <br>Janela Deslizante | Executar a cada hora, todos os dias (sem data e hora de início) | 1 | Hora | {none} | {não disponível} | {none} | {none} | Esse agendamento será iniciado imediatamente e calculará as recorrências futuras com base na última hora de execução. <p>Se a frequência for "Semana" ou "Mês", esse agendamento será executado, respectivamente, somente um dia por semana ou um dia por mês. |
| Recorrência <br>Janela Deslizante | Executar a cada hora, todos os dias (com data e hora de início) | 1 | Hora | *startDate*T*startTime*Z | {não disponível} | {none} | {none} | Esse agendamento não se inicia *antes* da data e hora de início especificada e, em seguida, calcula as recorrências futuras com base na última hora de execução. <p>Se a frequência for "Semana" ou "Mês", esse agendamento será executado, respectivamente, somente um dia por semana ou um dia por mês. |
| Recorrência <br>Janela Deslizante | Executar a cada 15 minutos após a hora cheia, a cada hora (com data e hora de início) | 1 | Hora | *startDate*T00:15:00Z | {não disponível} | {none} | {none} | Esse agendamento não se inicia *antes* da data e hora de início especificadas. As recorrências futuras são executadas na marca de 15 minutos, que é calculada a partir do horário de início, então às 00:15 AM, 1:15 AM, 2:15 AM, e assim por diante. |
| Recorrência | Executar a cada 15 minutos após a hora cheia, a cada hora (sem data e hora de início) | 1 | Dia | {none} | {não disponível} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Esse agendamento é executado às 00h15, 1h15, 2h15 e assim por diante. Além disso, esse agendamento é equivalente a uma frequência de "Hora" e uma hora de início com "15" minutos. |
| Recorrência | Execute a cada 15 minutos as marcas de minutoespecificadas (sem data de início e hora). | 1 | Dia | {none} | {não disponível} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Esse agendamento não se inicia até a próxima marca de 15 minutos especificada. |
| Recorrência | Execute diariamente às 8:00 *mais* a marca de minuto de quando você salvar seu aplicativo lógico | 1 | Dia | {none} | {não disponível} | 8 | {none} | Sem uma data e hora de início, esse cronograma é executado com base na hora em que você salva o aplicativo lógico (operação PUT). |
| Recorrência | Funcionar diariamente às 8:00 AM (com data e horário de início) | 1 | Dia | *startDate*T08:00:00Z | {não disponível} | {none} | {none} | Esse agendamento não se inicia *antes* da data e hora de início especificadas. As ocorrências futuras são realizadas diariamente às 8:00 am. | 
| Recorrência | Funcionar diariamente às 8h30 (sem data e horário de início) | 1 | Dia | {none} | {não disponível} | 8 | 30 | Este horário funciona às 8:30 da manhã todos os dias. |
| Recorrência | Funcionar diariamente às 8h30 e 16h30 | 1 | Dia | {none} | {não disponível} | 8, 16 | 30 | |
| Recorrência | Corra diariamente às 8h30, 8h45, 16h30 e 16h45. | 1 | Dia | {none} | {não disponível} | 8, 16 | 30, 45 | |
| Recorrência | Executar todos os sábados às 17h (sem data e hora de início) | 1 | Semana | {none} | "Sábado" | 17 | 00 | Esse agendamento é executado todos os sábados às 17h. |
| Recorrência | Executar todos os sábados às 17h (com data e hora de início) | 1 | Semana | *startDate*T17:00:00Z | "Sábado" | {none} | {none} | Esse agendamento não se inicia *antes* da data e hora de início especificadas, nesse caso, 9 de setembro de 2017 às 17h. As recorrências futuras serão executadas todos os sábados às 17h. |
| Recorrência | Execute todas as terças-feiras, quintas-feiras às 17:00 *mais* a marca de minuto de quando você salvar o seu aplicativo lógico| 1 | Semana | {none} | "Terça-feira", "Quinta-feira" | 17 | {none} | |
| Recorrência | Executado a cada hora durante o horário comercial | 1 | Semana | {none} | Selecione todos os dias, exceto sábado e domingo. | Selecione as horas do dia que você deseja. | Selecione os minutos da hora que você deseja. | Por exemplo, se o horário de trabalho é de 8h às 17h, selecione "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" como as horas do dia. <p>Se o horário de trabalho é das 8h30 às 17h30, selecione as horas anteriores do dia e selecione também "30" como os minutos da hora. |
| Recorrência | Executar uma vez por dia aos finais de semana | 1 | Semana | {none} | "Sábado", "Domingo" | Selecione as horas do dia que você deseja. | Selecione os minutos da hora conforme apropriado. | Esse agendamento é executado aos sábados e domingos no agendamento especificado. |
| Recorrência | Executar a cada 15 minutos, quinzenalmente, somente às segundas-feiras | 2 | Semana | {none} | "Segunda-feira" | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Esse agendamento é executado em todas as outras segundas-feiras em cada marca de 15 minutos. |
| Recorrência | Corra todos os meses | 1 | Month | *startDate*T*startTime*Z | {não disponível} | {não disponível} | {não disponível} | Este cronograma não *começa antes* da data e hora de início especificadas e calcula as recorrências futuras na data e hora de início. Se você não especificar uma data e hora de início, esse agendamento usará a data e a hora de criação. |
| Recorrência | Executar a cada hora durante um dia por mês | 1 | Month | {consulte a observação} | {não disponível} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {consulte a observação} | Se você não especificar uma data e hora de início, esse agendamento usará a data e a hora de criação. Para controlar os minutos do agendamento da recorrência, especifique os minutos da hora, uma hora de início ou use o tempo de criação. Por exemplo, se a hora de início ou hora de criação for 8h25, esse agendamento será executado às 8h25, 9h25, 10h25 e assim por diante. |
|||||||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Corra só uma vez

Se você quiser executar seu aplicativo lógico apenas uma vez no futuro, você pode usar o **Programador: Execute um** modelo de trabalho. Depois de criar um novo aplicativo lógico, mas antes de abrir o Logic Apps Designer, na seção **Modelos,** na lista **Categoria,** selecione **Agenda**e selecione este modelo:

![Selecione o modelo "Agendador: Execute uma vez empregos"](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

Ou, se você pode iniciar seu aplicativo lógico com o **Quando uma solicitação HTTP é recebida - Solicite** o gatilho e passe o tempo de início como um parâmetro para o gatilho. Para a primeira ação, use o **Atraso até - Agende** a ação e forneça o tempo para quando a próxima ação começar a ser em execução.

## <a name="next-steps"></a>Próximas etapas

* [Crie, agende e execute tarefas e fluxos de trabalho recorrentes com o gatilho Recorrência](../connectors/connectors-native-recurrence.md)
* [Crie, programe e execute tarefas e fluxos de trabalho recorrentes com o gatilho da janela deslizante](../connectors/connectors-native-sliding-window.md)
* [Pausa fluxos de trabalho com ações de atraso](../connectors/connectors-native-delay.md)
