---
title: Automatizar os processos do Azure Application Insights com o Microsoft Flow
description: Saiba como você pode usar o Microsoft Flow para automatizar rapidamente os processos repetidos usando o conector do Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/29/2019
ms.openlocfilehash: 7d9c966051c2756d7936fa079a0aac68e694425d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75407537"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Automatizar os processos do Azure Application Insights com o conector para o Microsoft Flow

Você está executando repetidamente as mesmas consultas nos dados telemétricos para verificar se o serviço está funcionando corretamente? Deseja automatizar essas consultas para descobrir tendências e anomalias e então criar seus próprios fluxos de trabalho para elas? O conector do Aplicativo Azure insights para Microsoft Flow é a ferramenta certa para essas finalidades.

Com essa integração, você agora pode automatizar diversos processos sem a necessidade de escrever nenhuma linha de código. Depois de você criar um fluxo usando uma ação do Application Insights, o fluxo executa automaticamente a consulta do Application Insights Analytics.

Adicione também outras ações. O Microsoft Flow disponibiliza centenas de ações. Por exemplo, você pode usar o Microsoft Flow para enviar uma notificação por email automaticamente ou criar um bug no Azure DevOps. Use também um dos vários [modelos](https://ms.flow.microsoft.com/connectors/shared_applicationinsights/?slug=azure-application-insights) disponíveis para o conector para o Microsoft Flow. Esses modelos aceleram o processo de criação de um fluxo.

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). -->

## <a name="create-a-flow-for-application-insights"></a>Criar um fluxo para o Application Insights

Neste tutorial, você aprenderá a criar um fluxo que usa o algoritmo de autocluster do Analytics para agrupar atributos nos dados de um aplicativo Web. O fluxo envia automaticamente os resultados por email, apenas um exemplo de como você pode usar o Microsoft Flow e o Application Insights Analytics juntos.

### <a name="step-1-create-a-flow"></a>Etapa 1: Criar um fluxo

1. Entre no [Microsoft Flow](https://flow.microsoft.com) e, em seguida, selecione **Meus Fluxos**.
2. Clique em **novo** , **agendado**, em branco.

    ![Criar novo fluxo de em branco agendado](./media/automate-with-flow/1-create.png)

### <a name="step-2-create-a-trigger-for-your-flow"></a>Etapa 2: Criar um gatilho para o fluxo

1. No pop-up, **crie um fluxo agendado**, preencha o nome do fluxo e com que frequência você deseja que o fluxo seja executado.

    ![Configurar a recorrência de agendamento com a frequência e o intervalo de entrada](./media/automate-with-flow/2-schedule.png)

1. Clique em **Criar**.

### <a name="step-3-add-an-application-insights-action"></a>Etapa 3: adicionar uma ação do Application Insights

1. Procure **Application insights**.
2. Clique em **aplicativo Azure insights – Visualizar consulta do Analytics**.

    ![Escolha uma ação: consulta de análise de Aplicativo Azure insights Visualizar](./media/automate-with-flow/3-visualize.png)

3. Selecione **Nova etapa**.

### <a name="step-4-connect-to-an-application-insights-resource"></a>Etapa 4: conectar-se a um recurso do Application Insights

Para concluir esta etapa, você precisa de uma ID do Aplicativo e uma Chave de API do recurso. Você pode recuperá-las no Portal do Azure, conforme mostrado no seguinte diagrama:

![ID do Aplicativo no portal do Azure](./media/automate-with-flow/5apiaccess.png)

![Chave de API no portal do Azure](./media/automate-with-flow/6apikey.png)

Forneça um nome para a conexão, junto com a ID do aplicativo e a Chave de API.

   ![Janela de conexão do Microsoft Flow](./media/automate-with-flow/4-connection.png)

Se a caixa de conexão não aparecer imediatamente e, em vez disso, chegar diretamente à inserção da consulta, clique nas reticências na parte superior direita da caixa. Em seguida, selecione minhas conexões ou use uma existente.

Clique em **Criar**.

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Etapa 5: especificar o tipo de consulta do Analytics e o tipo de gráfico
Esta consulta de exemplo seleciona as solicitações com falha no último dia e correlaciona-as com as exceções que ocorreram como parte da operação. O Analytics correlaciona-os com base no identificador operation_Id. Em seguida, a consulta segmenta os resultados usando o algoritmo de cluster automático.

Quando criar suas próprias consultas, verifique se elas estão funcionando corretamente no Analytics antes de adicioná-las ao fluxo.

- Adicione a seguinte consulta de análise e selecione o tipo de gráfico de tabela HTML. Em seguida, selecione **nova etapa**.

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```
    
    ![Janela de configuração de consulta do Analytics](./media/automate-with-flow/5-query.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Etapa 6: Configurar o fluxo para enviar email

1. Pesquise por **Office 365 Outlook**.
2. Clique em **Office 365 Outlook – enviar um e-mail**.

    ![Janela de seleção do Office 365 Outlook](./media/automate-with-flow/6-outlook.png)

1. Na janela **enviar um email** :

   a. Digite o endereço de email do destinatário.

   b. Digite um assunto para o email.

   c. Clique em qualquer lugar na caixa **Corpo** e, no menu de conteúdo dinâmico que se abre à direita, selecione **Corpo**.

   e. Selecione **Mostrar opções avançadas**

1. No menu de conteúdo dinâmico:

    a. Selecione o **Nome do Anexo**.

    b. Selecione o **Conteúdo do Anexo**.
    
    c. Na caixa **É HTML**, selecione **Sim**.

    ![Configuração do Office 365 Outlook](./media/automate-with-flow/7-email.png)

### <a name="step-7-save-and-test-your-flow"></a>Etapa 7: Salvar e testar o fluxo

Clique em **Save** (Salvar).

Você pode aguardar o gatilho executar esta ação ou clicar em ![ícone de teste do Beaker](./media/automate-with-flow/testicon.png) **teste** na parte superior.

Depois de selecionar **teste**:

1. Selecione **eu executarei a ação do gatilho**.
2. Selecione **executar fluxo**.

Quando o fluxo é executado, os destinatários que você especificou na lista de emails recebem uma mensagem de email como a exibida abaixo.

![Email de exemplo](./media/automate-with-flow/flow9.png)

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre como criar [consultas do Analytics](../../azure-monitor/log-query/get-started-queries.md).
- Saiba mais sobre o [Microsoft Flow](https://ms.flow.microsoft.com).

<!--Link references-->
