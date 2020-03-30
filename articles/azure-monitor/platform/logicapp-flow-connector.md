---
title: Use logs do Monitor do Azure com aplicativos de lógica do Azure e automatize o poder
description: Saiba como usar o Azure Logic Apps e o Power Automate para automatizar rapidamente processos repetíveis usando o conector Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/13/2020
ms.openlocfilehash: 6961b7bd94c9b3fe70365055851c488efa2cbeca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480004"
---
# <a name="azure-monitor-logs-connector-for-logic-apps-and-flow"></a>Conector de logs do Monitor do Azure para aplicativos lógicos e fluxo
[O Azure Logic Apps](/azure/logic-apps/) e [o Power Automate](https://ms.flow.microsoft.com) permitem criar fluxos de trabalho automatizados usando centenas de ações para uma variedade de serviços. O conector Azure Monitor Logs permite que você crie fluxos de trabalho que recuperem dados de um espaço de trabalho do Log Analytics ou de um aplicativo Application Insights no Azure Monitor. Este artigo descreve as ações incluídas no conector e fornece um passo a passo para construir um fluxo de trabalho usando esses dados.

Por exemplo, você pode criar um aplicativo lógico para usar os dados de log do Azure Monitor em uma notificação de e-mail do Office 365, criar um bug no Azure DevOps ou postar uma mensagem do Slack.  Você pode disparar um fluxo de trabalho com um agendamento simples ou a partir de alguma ação em um serviço conectado, por exemplo, quando um email ou tweet é recebido. 

## <a name="actions"></a>Ações
A tabela a seguir descreve as ações incluídas no conector Azure Monitor Logs. Ambos permitem executar uma consulta de log contra um espaço de trabalho do Log Analytics ou do aplicativo Application Insights. A diferença está na forma como os dados são devolvidos.

> [!NOTE]
> O conector Azure Monitor Logs substitui o [conector Azure Log Analytics](https://docs.microsoft.com/connectors/azureloganalytics/) e o [conector Azure Application Insights](https://docs.microsoft.com/connectors/applicationinsights/). Este conector fornece a mesma funcionalidade que os outros e é o método preferido para executar uma consulta contra um espaço de trabalho do Log Analytics ou um aplicativo Application Insights.


| Ação | Descrição |
|:---|:---|
| [Executar consulta e listar resultados](https://docs.microsoft.com/connectors/azuremonitorlogs/#run-query-and-list-results) | Retorna cada linha como seu próprio objeto. Use esta ação quando quiser trabalhar com cada linha separadamente no resto do fluxo de trabalho. A ação é tipicamente seguida por um [Para cada atividade](../../logic-apps/logic-apps-control-flow-loops.md#foreach-loop). |
| [Executar consulta e visualizar resultados](https://docs.microsoft.com/connectors/azuremonitorlogs/#run-query-and-visualize-results) | Retorna todas as linhas no conjunto de resultados como um único objeto formatado. Use esta ação quando quiser usar o resultado definido em conjunto no resto do fluxo de trabalho, como enviar os resultados em um e-mail.  |

## <a name="walkthroughs"></a>Passo a passo
Os tutoriais a seguir ilustram o uso dos conectores do Monitor Azure no Azure Logic Apps. Você pode executar este mesmo exemplo com o Power Automate, a única diferença é como criar o fluxo de trabalho inicial e executá-lo quando concluído. A configuração do fluxo de trabalho e das ações é a mesma entre ambos. Consulte [Criar um fluxo de um modelo no Power Automate](https://docs.microsoft.com/power-automate/get-started-logic-template) para começar.


### <a name="create-a-logic-app"></a>Criar um aplicativo lógico

Vá para **Apps lógicos** no portal Azure e clique **em Adicionar**. Selecione uma **assinatura,** **grupo de**recursos e **região** para armazenar o novo aplicativo lógico e, em seguida, dê-lhe um nome exclusivo. Você pode ativar a configuração **do Log Analytics** para coletar informações sobre dados e eventos em tempo de execução, conforme descrito nos [registros do Azure Monitor e coletar dados de diagnóstico para aplicativos de lógica do Azure](../../logic-apps/monitor-logic-apps-log-analytics.md). Esta configuração não é necessária para usar o conector Azure Monitor Logs.

![Criar aplicativo lógico](media/logicapp-flow-connector/create-logic-app.png)


Clique **em Rever + criar** e, em seguida, **criar**. Quando a implantação estiver concluída, clique **em Ir para recurso** para abrir o Logic Apps **Designer**.

### <a name="create-a-trigger-for-the-logic-app"></a>Crie um gatilho para o aplicativo lógico
Em **Iniciar com um gatilho comum,** selecione **Recorrência**. Isso cria um aplicativo lógico que é executado automaticamente em um intervalo regular. Na caixa **De freqüência** da ação, selecione **Dia** e na caixa **Intervalo,** digite **1** para executar o fluxo de trabalho uma vez por dia.

![Ação de recorrência](media/logicapp-flow-connector/recurrence-action.png)

## <a name="walkthrough-mail-visualized-results"></a>Passo a passo: Resultados visualizados pelo correio
O tutorial a seguir mostra como criar um aplicativo lógico que envia os resultados de uma consulta de log do Azure Monitor por e-mail. 

### <a name="add-azure-monitor-logs-action"></a>Adicionar a ação Logs do Monitor do Azure
Clique **em + Novo passo** para adicionar uma ação que é executada após a ação de recorrência. Em **Escolha uma ação,** **digite monitor azure** e selecione **Registros de monitor do Azure**.

![A ação do Azure Monitor Logs](media/logicapp-flow-connector/select-azure-monitor-connector.png)

Clique em **Azure Log Analytics – Executar a consulta e visualizar os resultados**.

![Executar consulta e visualizar a ação de resultados](media/logicapp-flow-connector/select-query-action-visualize.png)


### <a name="add-azure-monitor-logs-action"></a>Adicionar a ação Logs do Monitor do Azure

Selecione o **Grupo de Assinaturas** e **Recursos** para o espaço de trabalho do Log Analytics. Selecione *O espaço de trabalho do Log Analytics* para o tipo de **recurso** e, em seguida, selecione o nome do espaço de trabalho em Nome **de Recurso**.

Adicione a consulta de logs a seguir à janela **Consultar**.  

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1day)
| summarize TotalErrors=count() by Computer
| sort by Computer asc   
```

Selecione *Definir na consulta* para o intervalo de **tempo** e **tabela HTML** para o tipo **de gráfico**.
   
![Executar consulta e visualizar a ação de resultados](media/logicapp-flow-connector/run-query-visualize-action.png)

O e-mail será enviado pela conta associada à conexão atual. Você pode especificar outra conta clicando em **Alterar conexão**.

### <a name="add-email-action"></a>Adicionar ação de e-mail

Clique **em + Nova etapa**e clique em + Adicione uma **ação**. Em **Escolha uma ação,** digite **uma perspectiva** e selecione **Office 365 Outlook**.

![Selecione o conector Outlook](media/logicapp-flow-connector/select-outlook-connector.png)

Selecione **Enviar um e-mail (V2)**.

![Janela de seleção do Office 365 Outlook](media/logicapp-flow-connector/select-mail-action.png)

Clique em qualquer lugar da caixa **Corpo** para abrir uma janela **de conteúdo dinâmico** aberta com valores das ações anteriores no aplicativo lógico. Selecione **Ver mais** e, em seguida, **Corpo** que é o resultado da consulta na ação Log Analytics.

![Selecione corpo](media/logicapp-flow-connector/select-body.png)

Especifique o endereço de email de um destinatário na janela **Para** e um assunto para o email em **Assunto**. 

![Ação de correio](media/logicapp-flow-connector/mail-action.png)


### <a name="save-and-test-your-logic-app"></a>Salvar e testar o aplicativo lógico
Clique **em Salvar** e, em seguida, **execute** uma execução de teste do aplicativo lógico.

![Salvar e Executar](media/logicapp-flow-connector/save-run.png)


Quando o aplicativo lógico for concluído, verifique o e-mail do destinatário especificado.  Você deve ter recebido um email com um corpo semelhante ao seguinte:

![Email de exemplo](media/logicapp-flow-connector/sample-mail.png)



## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [consultas de log no Azure Monitor](../log-query/log-query-overview.md).
- Saiba mais sobre [aplicativos lógicos](/azure/logic-apps/)
- Saiba mais sobre o [Microsoft Flow](https://ms.flow.microsoft.com).

