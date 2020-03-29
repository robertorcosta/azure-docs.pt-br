---
title: Monitore o status, veja o histórico e configure alertas
description: Solucionar problemas de aplicativos lógicos verificando o status de execução, revisando o histórico de gatilhos e ativando alertas no Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 495877f1c839de2cf3583a37180054c91bd9f139
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76907767"
---
# <a name="monitor-run-status-review-trigger-history-and-set-up-alerts-for-azure-logic-apps"></a>Monitore o status de execução, revise o histórico do gatilho e configure alertas para aplicativos azure logic

Depois [de criar e executar um aplicativo lógico,](../logic-apps/quickstart-create-first-logic-app-workflow.md)você pode verificar o status de execução do aplicativo lógico, [executar o histórico,](#review-runs-history) [o histórico de gatilhos](#review-trigger-history)e o desempenho. Para obter notificações sobre falhas ou outros possíveis problemas, configure [alertas](#add-azure-alerts). Por exemplo, você pode criar um alerta que detecta “quando mais de cinco execuções falham em uma hora”.

Para monitoramento de eventos em tempo real e depuração mais rica, configure o registro de diagnósticos para o seu aplicativo lógico usando [logs do Monitor Do Azure](../azure-monitor/overview.md). Este serviço do Azure ajuda você a monitorar seus ambientes em nuvem e no local para que você possa manter mais facilmente sua disponibilidade e desempenho. Em seguida, você pode encontrar e visualizar eventos, como eventos de gatilho, eventos de execução e eventos de ação. Ao armazenar essas informações nos [registros do Monitor do Azure,](../azure-monitor/platform/data-platform-logs.md)você pode criar consultas de [log](../azure-monitor/log-query/log-query-overview.md) que o ajudam a encontrar e analisar essas informações. Você também pode usar esses dados de diagnóstico com outros serviços do Azure, como o Azure Storage e o Azure Event Hubs. Para obter mais informações, consulte [os aplicativos de lógica do Monitor usando o Azure Monitor](../logic-apps/monitor-logic-apps-log-analytics.md).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

<a name="review-runs-history"></a>

## <a name="review-runs-history"></a>Examinar o histórico de execuções

Cada vez que o gatilho é acionado para um item ou evento, o mecanismo Logic Apps cria e executa uma instância de fluxo de trabalho separada para cada item ou evento. Por padrão, cada instância de fluxo de trabalho é executada em paralelo para que nenhum fluxo de trabalho tenha que esperar antes de iniciar uma execução. Você pode rever o que aconteceu durante essa execução, incluindo o status de cada etapa do fluxo de trabalho mais as entradas e saídas para cada etapa.

1. No [portal Azure,](https://portal.azure.com)encontre e abra seu aplicativo lógico no Logic App Designer.

   Para encontrar seu aplicativo lógico, na caixa de `logic apps`pesquisa principal do Azure, digite e selecione **Logic Apps**.

   ![Encontre e selecione o serviço "Logic Apps"](./media/monitor-logic-apps/find-your-logic-app.png)

   O portal Azure mostra todos os aplicativos lógicos associados às suas assinaturas do Azure. Você pode filtrar esta lista com base no nome, assinatura, grupo de recursos, localização e assim por diante.

   ![Ver aplicativos lógicos associados a assinaturas](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Selecione seu aplicativo lógico e, em seguida, selecione **Visão geral**.

   No painel de visão geral, em **Executa histórico**, todo o passado, atual, e qualquer espera é executada para o seu aplicativo lógico aparecer. Se a lista mostrar muitas corridas e você não encontrar a entrada desejada, tente filtrar a lista. Se você não encontrar os dados esperados, experimente selecionar **Atualizar** na barra de ferramentas.

   ![Visão geral, executa histórico e outras informações lógicas do aplicativo](./media/monitor-logic-apps/overview-pane-logic-app-details-run-history.png)

   Aqui estão os possíveis status para uma execução de aplicativo lógico:

   | Status | Descrição |
   |--------|-------------|
   | **Cancelado** | O fluxo de trabalho estava em execução, mas recebeu um pedido de cancelamento |
   | **Falha** | Pelo menos uma ação falhou, e nenhuma ação posterior no fluxo de trabalho foi criada para lidar com a falha |
   | **Em execução** | O fluxo de trabalho está em execução atualmente. <p>Esse status também pode aparecer para fluxos de trabalho estrangulados ou devido ao plano de preços atual. Para saber mais, consulte os [limites de ações na página de preço](https://azure.microsoft.com/pricing/details/logic-apps/). Se você configurar [o registro de diagnósticos,](../logic-apps/monitor-logic-apps.md)você pode obter informações sobre quaisquer eventos de aceleração que aconteçam. |
   | **Conseguiu** | Todas as ações foram bem sucedidas. <p>**Nota**: Se alguma falha ocorreu em uma ação específica, uma ação posterior no fluxo de trabalho lidou com essa falha. |
   | **Aguardando** | O fluxo de trabalho não foi iniciado ou está pausado, por exemplo, devido a um fluxo de trabalho anterior que ainda está sendo executado. |
   |||

1. Para revisar as etapas e outras informações para uma execução específica, em **'Executa histórico',** selecione essa execução.

   ![Selecione uma corrida específica para revisar](./media/monitor-logic-apps/select-specific-logic-app-run.png)

   O painel **de execução do aplicativo Logic** mostra cada passo na execução selecionada, o status de execução de cada etapa e o tempo de execução de cada passo a ser executado, por exemplo:

   ![Cada ação na execução específica](./media/monitor-logic-apps/logic-app-run-pane.png)

   Para visualizar essas informações no formulário de lista, na barra de ferramentas **do aplicativo Logic,** selecione **Executar detalhes**.

   ![Na barra de ferramentas, selecione "Executar detalhes"](./media/monitor-logic-apps/select-run-details-on-toolbar.png)

   A exibição 'Detalhes de execução' mostra cada passo, seu status e outras informações.

   ![Revisar detalhes sobre cada etapa da corrida](./media/monitor-logic-apps/review-logic-app-run-details.png)

   Por exemplo, você pode obter a propriedade **DeScoride** de correlação da execução, que você pode precisar quando você usa a [API REST para aplicativos lógicos](https://docs.microsoft.com/rest/api/logic).

1. Para obter mais informações sobre uma etapa específica, selecione qualquer opção:

   * No **painel de execução** do aplicativo Logic selecione o passo para que a forma se expanda. Agora você pode visualizar informações como entradas, saídas e quaisquer erros que aconteceram nessa etapa, por exemplo:

     ![No painel de execução do aplicativo lógico, ver passo falho](./media/monitor-logic-apps/specific-step-inputs-outputs-errors.png)

   * No **painel de detalhes do aplicativo Logic,** selecione o passo que deseja.

     ![No painel de detalhes de execução, ver passo falho](./media/monitor-logic-apps/select-failed-step-in-failed-run.png)

     Agora você pode visualizar informações como entradas e saídas para essa etapa, por exemplo:

   > [!NOTE]
   > Todos os eventos e detalhes de runtime são criptografados no serviço Aplicativos Lógicos. Eles são descriptografados apenas quando um usuário solicita a exibição desses dados. Você pode [ocultar entradas e saídas no histórico de execução](../logic-apps/logic-apps-securing-a-logic-app.md#obfuscate) ou controlar o acesso do usuário a essas informações usando [o RBAC (Azure Role-Based Access Control, controle de acesso baseado em função)](../role-based-access-control/overview.md).

<a name="review-trigger-history"></a>

## <a name="review-trigger-history"></a>Examinar histórico de gatilho

Cada execução de aplicativo lógico começa com um gatilho. O histórico de gatilhos lista todas as tentativas de gatilho que seu aplicativo lógico fez e informações sobre as entradas e saídas para cada tentativa de gatilho.

1. No [portal Azure,](https://portal.azure.com)encontre e abra seu aplicativo lógico no Logic App Designer.

   Para encontrar seu aplicativo lógico, na caixa de `logic apps`pesquisa principal do Azure, digite e selecione **Logic Apps**.

   ![Encontre e selecione o serviço "Logic Apps"](./media/monitor-logic-apps/find-your-logic-app.png)

   O portal Azure mostra todos os aplicativos lógicos associados às suas assinaturas do Azure. Você pode filtrar esta lista com base no nome, assinatura, grupo de recursos, localização e assim por diante.

   ![Ver aplicativos lógicos associados a assinaturas](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Selecione seu aplicativo lógico e, em seguida, selecione **Visão geral**.

1. No menu do aplicativo lógico, selecione **Visão geral**. Na seção **Resumo,** em **Avaliação,** selecione **Ver histórico de gatilhos**.

   ![Exibir histórico de gatilhos para o seu aplicativo lógico](./media/monitor-logic-apps/overview-pane-logic-app-details-trigger-history.png)

   O painel de histórico de gatilho mostra todas as tentativas de gatilho que seu aplicativo lógico fez. Cada vez que o gatilho é acionado para um item ou evento, o mecanismo Logic Apps cria uma instância de aplicativo de lógica separada que executa o fluxo de trabalho. Por padrão, cada instância é executada em paralelo para que nenhum fluxo de trabalho precise esperar para iniciar uma execução. Portanto, se o aplicativo lógico for acionado em vários itens ao mesmo tempo, uma entrada de gatilho com a mesma data e hora será exibida para cada item.

   ![Múltiplas tentativas de gatilho para diferentes itens](./media/monitor-logic-apps/logic-app-trigger-history.png)

   Aqui estão os possíveis status para uma tentativa de disparo:

   | Status | Descrição |
   |--------|-------------|
   | **Falha** | Ocorreu um erro. Para examinar as mensagens de erro geradas para um disparador com falha, selecione essa tentativa de disparo e escolha **Saídas**. Por exemplo, você pode descobrir entradas que não são válidas. |
   | **Ignorada** | O gatilho verificou o ponto de extremidade, mas não encontrou dados disponíveis. |
   | **Conseguiu** | O gatilho verificou o ponto de extremidade e encontrou dados disponíveis. Normalmente, um status de "Disparado" também é exibida junto com esse status. Caso contrário, a definição do gatilho pode ter uma condição ou comando `SplitOn` que não foi atendido. <p>Esse status pode se aplicar a um gatilho manual, gatilho recorrente ou gatilho de sondagem. Um gatilho pode ser executado com êxito, mas a execução em si ainda pode falhar quando as ações gerarem erros sem tratamento. |
   |||

   > [!TIP]
   > Você pode verificar novamente o gatilho sem aguardar a próxima recorrência. Na barra de ferramentas da visão geral, **selecione Executar gatilho**e selecione o gatilho, que força uma verificação. Ou então, selecione **Executar** na barra de ferramentas do Designer de Aplicativos Lógicos.

1. Para visualizar informações sobre uma tentativa de gatilho específica, no painel do gatilho, selecione o evento do gatilho. Se a lista mostrar muitas tentativas de gatilho e você não conseguir encontrar a entrada desejada, tente filtrar a lista. Se você não encontrar os dados esperados, experimente selecionar **Atualizar** na barra de ferramentas.

   ![Exibir tentativa de gatilho específica](./media/monitor-logic-apps/select-trigger-event-for-review.png)

   Agora você pode rever informações sobre o evento de gatilho selecionado, por exemplo:

   ![Exibir informações específicas do gatilho](./media/monitor-logic-apps/view-specific-trigger-details.png)

<a name="add-azure-alerts"></a>

## <a name="set-up-monitoring-alerts"></a>Configure alertas de monitoramento

Para obter alertas com base em métricas específicas ou limites excedidos para o seu aplicativo lógico, configure [alertas no Azure Monitor](../azure-monitor/platform/alerts-overview.md). Saiba mais sobre as [métricas no Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md). Para configurar alertas sem usar [o Monitor Azure,](../log-analytics/log-analytics-overview.md)siga estas etapas.

1. No menu do aplicativo lógico, em **Monitoramento,** selecione **Alertas** > **Nova regra de alerta**.

   ![Adicionar um alerta ao aplicativo lógico](./media/monitor-logic-apps/add-new-alert-rule.png)

1. No **painel Criar regra,** em **Recurso,** selecione seu aplicativo lógico, se ainda não estiver selecionado. Em **Condição,** **selecione Adicionar** para que você possa definir a condição que aciona o alerta.

   ![Adicione uma condição para a regra](./media/monitor-logic-apps/add-condition-for-rule.png)

1. No **painel lógica de sinal Configurar,** encontre e selecione o sinal para o qual deseja receber um alerta. Você pode usar a caixa de pesquisa ou classificar os sinais em ordem alfabética, selecione o cabeçalho da coluna **nome do sinal.**

   Por exemplo, se você quiser enviar um alerta quando um gatilho falhar, siga estas etapas:

   1. Na coluna **Nome do sinal,** encontre e selecione o sinal **de falha dos gatilhos.**

      ![Selecione o sinal para criar alerta](./media/monitor-logic-apps/find-and-select-signal.png)

   1. No painel de informações que abre para o sinal selecionado, **a lógica Alerta,** configure sua condição, por exemplo:

   1. Para **Operador,** selecione **Maior ou igual a**.

   1. Para **o tipo de agregação,** selecione **Contagem**.

   1. Para o valor `1` **limite,** digite .

   1. Em **Condição de visualização,** confirme se sua condição aparece correta.

   1. Em **Avaliação com base em**, configure o intervalo e a freqüência para executar a regra de alerta. Para **granularidade de agregação (Período)**, selecione o período para agrupar os dados. Para **freqüência de avaliação,** selecione quantas vezes deseja verificar a condição.

   1. Quando estiver pronto, selecione **Pronto**.

   Aqui está a condição final:

   ![Configurar condição para alerta](./media/monitor-logic-apps/set-up-condition-for-alert.png)

   A página **Criar regra** agora mostra a condição que você criou e o custo para executar esse alerta.

   ![Novo alerta na página "Criar regra"](./media/monitor-logic-apps/finished-alert-condition-cost.png)

1. Especifique um nome, descrição opcional e nível de gravidade para o seu alerta. Deixe a **regra Ativar na** configuração de criação ativada ou desligue até que esteja pronto para ativar a regra.

1. Quando terminar, selecione **Criar regra de alerta**.

> [!TIP]
> Para executar um aplicativo lógico em um alerta, você pode incluir o [gatilho de solicitação](../connectors/connectors-native-reqres.md) no fluxo de trabalho, o que permite realizar tarefas como estes exemplos:
> 
> * [Postar no Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Enviar um texto](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Adicionar uma mensagem a uma fila](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

## <a name="next-steps"></a>Próximas etapas

* [Monitore aplicativos lógicos usando o Azure Monitor](../logic-apps/monitor-logic-apps-log-analytics.md)