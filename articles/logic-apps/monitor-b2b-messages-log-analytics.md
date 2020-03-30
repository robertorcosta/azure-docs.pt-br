---
title: Monitore mensagens B2B usando o Monitor Azure
description: Soluciona problemas as mensagens AS2, X12 e EDIFACT configurando logs do Monitor Do Azure e coletando dados de diagnóstico para aplicativos de lógica do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: e9ba5a516293eb72a715dc9d0df7db4d5a4ea3c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76907975"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-b2b-messages-in-azure-logic-apps"></a>Configure registros do Monitor do Azure e colete dados de diagnóstico para mensagens B2B em Aplicativos de Lógica do Azure

Depois de configurar a comunicação B2B entre parceiros comerciais em sua conta de integração, esses parceiros podem trocar mensagens usando protocolos como AS2, X12 e EDIFACT. Para verificar se essa comunicação funciona da maneira que você espera, você pode configurar [registros do Monitor Do Azure](../azure-monitor/platform/data-platform-logs.md) para sua conta de integração. [O Azure Monitor](../azure-monitor/overview.md) ajuda você a monitorar seus ambientes em nuvem e no local para que você possa manter mais facilmente sua disponibilidade e desempenho. Usando os registros do Azure Monitor, você pode gravar e armazenar dados sobre dados e eventos de tempo de execução, como eventos de gatilho, eventos de execução e eventos de ação em um [espaço de trabalho do Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md). Para mensagens, o registro também coleta informações como:

* Status e contagem de mensagens
* Status de confirmações
* Correlações entre mensagens e reconhecimentos
* Descrição de erro detalhada para falhas

O Azure Monitor permite que você crie [consultas de log](../azure-monitor/log-query/log-query-overview.md) para ajudá-lo a encontrar e revisar essas informações. Você também pode [usar esses dados de diagnóstico com outros serviços do Azure,](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data)como o Azure Storage e o Azure Event Hubs.

Para configurar o registro para sua conta de integração, [instale a solução Logic Apps B2B](#install-b2b-solution) no portal Azure. Esta solução fornece informações agregadas para eventos de mensagem B2B. Em seguida, para habilitar o registro e a criação de consultas para essas informações, configure [os logs do Monitor do Azure](#set-up-resource-logs).

Este artigo mostra como ativar o registro do Azure Monitor para sua conta de integração.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Um espaço de trabalho do Log Analytics. Se você não tiver um espaço de trabalho do Log Analytics, saiba[como criar um espaço de trabalho do Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

* Um aplicativo lógico que é configurado com o registro do Azure Monitor e envia essas informações para um espaço de trabalho do Log Analytics. Saiba [como configurar os logs do Azure Monitor para o seu aplicativo lógico](../logic-apps/monitor-logic-apps.md).

* Uma conta de integração vinculada ao aplicativo lógico. Saiba [como vincular sua conta de integração ao seu aplicativo lógico](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

<a name="install-b2b-solution"></a>

## <a name="install-logic-apps-b2b-solution"></a>Instalar a solução de B2B de aplicativos lógicos

Antes que os logs do Monitor do Azure possam rastrear as mensagens B2B para o seu aplicativo lógico, adicione a solução **Logic Apps B2B** ao seu espaço de trabalho Log Analytics.

1. Na caixa de pesquisa do portal `log analytics workspaces` [Azure,](https://portal.azure.com)digite e selecione espaços de trabalho do Log **Analytics**.

   ![Selecione "Espaços de trabalho do Log Analytics"](./media/monitor-b2b-messages-log-analytics/find-select-log-analytics-workspaces.png)

1. Em **Log Analytics,** selecione seu espaço de trabalho.

   ![Selecionar o espaço de trabalho do Log Analytics](./media/monitor-b2b-messages-log-analytics/select-log-analytics-workspace.png)

1. No painel Visão geral, em **Iniciar com o Log Analytics** > **Configure soluções de monitoramento,** selecione **Exibir soluções**.

   ![No painel Visão Geral, selecione "Exibir soluções"](./media/monitor-b2b-messages-log-analytics/log-analytics-workspace.png)

1. No painel Visão geral, selecione **Adicionar**.

   ![No painel de visão geral, adicione uma nova solução](./media/monitor-b2b-messages-log-analytics/add-logic-apps-management-solution.png)

1. Depois que o **Marketplace abrir,** `logic apps b2b`na caixa de pesquisa, digite e selecione **Logic Apps B2B**.

   ![No Marketplace, selecione "Logic Apps Management"](./media/monitor-b2b-messages-log-analytics/select-logic-apps-b2b-solution.png)

1. No painel de descrição da solução, selecione **Criar**.

   ![Selecione "Criar" para adicionar a solução "Logic Apps B2B"](./media/monitor-b2b-messages-log-analytics/create-logic-apps-b2b-solution.png)

1. Revise e confirme o espaço de trabalho do Log Analytics onde deseja instalar a solução e selecione **Criar** novamente.

   ![Selecione "Criar" para "Logic Apps B2B"](./media/monitor-b2b-messages-log-analytics/confirm-log-analytics-workspace.png)

   Depois que o Azure implanta a solução para o grupo de recursos Do Azure que contém seu espaço de trabalho do Log Analytics, a solução aparece no painel de resumo do seu espaço de trabalho. Quando as mensagens B2B são processadas, a contagem de mensagens neste painel é atualizada.

   ![Painel de resumo do espaço de trabalho](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Configurar os logs do Azure Monitor

Você pode habilitar o registro do Azure Monitor diretamente da sua conta de integração.

1. No [portal do Azure](https://portal.azure.com), encontre e selecione sua conta de integração.

   ![Localize e selecione a conta de integração](./media/monitor-b2b-messages-log-analytics/find-integration-account.png)

1. No menu da sua conta de integração, em **Monitoramento,** selecione **Configurações de diagnóstico**. Selecione **Adicionar configuração de diagnóstico**.

   ![Em "Monitoramento", selecione "Configurações de diagnóstico"](./media/monitor-b2b-messages-log-analytics/monitor-diagnostics-settings.png)

1. Para criar a configuração, siga estas etapas:

   1. Forneça um nome para a configuração.

   1. Selecione **Enviar para o Log Analytics**.

   1. Para **assinatura**, selecione a assinatura do Azure associada ao seu espaço de trabalho do Log Analytics.

   1. Para **o Log Analytics Workspace,** selecione o espaço de trabalho que deseja usar.

   1. Em **log,** selecione a categoria **IntegraçãoAccountTrackingEvents,** que especifica a categoria de evento que você deseja gravar.

   1. Quando terminar, selecione **Salvar**.

   Por exemplo:  

   ![Configure registros do Monitor do Azure para coletar dados de diagnóstico](./media/monitor-b2b-messages-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-message-status"></a>

## <a name="view-message-status"></a>Exibir status da mensagem

Depois que seu aplicativo de lógica é executado, você pode visualizar o status e os dados sobre essas mensagens no espaço de trabalho do Log Analytics.

1. Na caixa de pesquisa do [portal Azure,](https://portal.azure.com) encontre e abra seu espaço de trabalho do Log Analytics.

1. No menu do seu espaço de trabalho, selecione Aplicativo de lógica de **espaço de** > trabalho**B2B**.

   ![Painel de resumo do espaço de trabalho](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

   > [!NOTE]
   > Se o azulejo B2B do Logic Apps não mostrar imediatamente os resultados após uma execução, tente selecionar **Atualizar** ou esperar por um curto período de tempo antes de tentar novamente.

   Por padrão, o bloco **Logic Apps B2B** mostra dados com base em um único dia. Para alterar o escopo de dados para um intervalo diferente, selecione o controle de escopo na parte superior da página:

   ![Alterar intervalo](./media/monitor-b2b-messages-log-analytics/change-summary-interval.png)

1. Após a exibição do painel de status de mensagens, você poderá exibir mais detalhes de um tipo de mensagem específico, que mostra dados com base em um único dia. Selecione o azulejo para **AS2,** **X12**ou **EDIFACT**.

   ![Exibir status para mensagens](./media/monitor-b2b-messages-log-analytics/workspace-summary-b2b-messages.png)

   Uma lista de mensagens é exibida para o bloco escolhido. Por exemplo, veja abaixo a aparência de uma lista de mensagens AS2:

   ![Status e detalhes para mensagens AS2](./media/monitor-b2b-messages-log-analytics/as2-message-results-list.png)

   Para saber mais sobre as propriedades de cada tipo de mensagem, consulte estas descrições das propriedades de mensagens:

   * [Propriedades de mensagens AS2](#as2-message-properties)
   * [Propriedades de mensagens X12](#x12-message-properties)
   * [Propriedades de mensagens EDIFACT](#EDIFACT-message-properties)

<!--
1. To view or export the inputs and outputs for specific messages, select those messages, and select **Download**. When you're prompted, save the .zip file to your local computer, and then extract that file.

   The extracted folder includes a folder for each selected message. If you set up acknowledgements, the message folder also includes files with acknowledgement details. Each message folder has at least these files:
   
   * Human-readable files with the input payload and output payload details
   * Encoded files with the inputs and outputs

   For each message type, you can find the folder and file name formats here:

   * [AS2 folder and file name formats](#as2-folder-file-names)
   * [X12 folder and file name formats](#x12-folder-file-names)
   * [EDIFACT folder and file name formats](#edifact-folder-file-names)

   ![Download message files](./media/monitor-b2b-messages-log-analytics/download-messages.png)

1. To view all actions that have the same run ID, on the **Log Search** page, select a message from the message list.

   You can sort these actions by column, or search for specific results.

   * To search results with prebuilt queries, select **Favorites**.

   * Learn [how to build queries by adding filters](../logic-apps/create-monitoring-tracking-queries.md). Or learn more about [how to find data with log searches in Azure Monitor logs](../log-analytics/log-analytics-log-searches.md).

   * To change query in the search box, update the query with the columns and values that you want to use as filters.
-->

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Descrições de propriedade e formatos de nome de mensagens AS2, X12 e EDIFACT

Para cada tipo de mensagem, estas são as descrições de propriedade e os formatos de nome dos arquivos de mensagem baixados.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Descrições de propriedade da mensagem AS2

Estas são as descrições das propriedades de cada mensagem AS2.

| Propriedade | Descrição |
|----------|-------------|
| **Remetente** | O parceiro convidado especificado em **Configurações de Recebimento** ou o parceiro host especificado em **Configurações de Envio** de um contrato do AS2 |
| **Receptor** | O parceiro host especificado em **Configurações de Recebimento** ou o parceiro convidado em **Configurações de Envio** de um contrato do AS2 |
| **App lógico** | O aplicativo lógico no qual as ações do AS2 são configuradas |
| **Status** | O status da mensagem AS2 <br>Êxito = recebimento ou envio de uma mensagem AS2 válida. Nenhum MDN está configurado. <br>Êxito = recebimento ou envio de uma mensagem AS2 válida. O MDN está configurado e é recebido ou o MDN é enviado. <br>Com Falha = recebimento de uma mensagem AS2 inválida. Nenhum MDN está configurado. <br>Pendente = recebimento ou envio de uma mensagem AS2 válida. O MDN está configurado e o MDN é esperado. |
| **Ack** | O status da mensagem MDN <br>Aceito = recebimento ou envio de um MDN positivo. <br>Pendente = aguardando recebimento ou envio de um MDN. <br>Rejeitado = recebimento ou envio de um MDN negativo. <br>Não Obrigatório = o MDN não está configurado no contrato. |
| **Direção** | A direção da mensagem AS2 |
| **ID de rastreamento** | A ID que correlaciona todos os gatilhos e todas as ações em um aplicativo lógico |
| **ID da Mensagem** | ID da mensagem AS2 dos cabeçalhos da mensagem AS2 |
| **Timestamp** | A hora em que a ação do AS2 processou a mensagem |
|||

<!--
<a name="as2-folder-file-names"></a>

### AS2 name formats for downloaded message files

Here are the name formats for each downloaded AS2 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_outputs.txt |
|||
-->

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>Descrições das propriedades da mensagem X12

Estas são as descrições das propriedades de cada mensagem X12.

| Propriedade | Descrição |
|----------|-------------|
| **Remetente** | O parceiro convidado especificado em **Configurações de Recebimento** ou o parceiro host especificado em **Configurações de Envio** de um contrato do X12 |
| **Receptor** | O parceiro host especificado em **Configurações de Recebimento** ou o parceiro convidado especificado em **Configurações de Envio** de um contrato do X12 |
| **App lógico** | O aplicativo lógico no qual as ações do X12 são configuradas |
| **Status** | O status da mensagem X12 <br>Êxito = recebimento ou envio de uma mensagem X12 válida. Nenhuma confirmação funcional está configurada. <br>Êxito = recebimento ou envio de uma mensagem X12 válida. Uma confirmação funcional está configurada e é recebida ou uma confirmação funcional é enviada. <br>Com Falha = recebimento ou envio de uma mensagem X12 inválida. <br>Pendente = recebimento ou envio de uma mensagem X12 válida. Uma confirmação funcional está configurada e uma confirmação funcional é esperada. |
| **Ack** | Status da Confirmação Funcional (997) <br>Aceito = recebimento ou envio de uma confirmação funcional positiva. <br>Rejeitado = recebimento ou envio de uma confirmação funcional negativa. <br>Pendente = aguardando uma confirmação funcional, mas não recebida. <br>Pendente = geração de uma confirmação funcional, mas não foi possível enviá-la ao parceiro. <br>Não Obrigatório = uma confirmação funcional não está configurada. |
| **Direção** | A direção da mensagem X12 |
| **ID de rastreamento** | A ID que correlaciona todos os gatilhos e todas as ações em um aplicativo lógico |
| **Tipo de mensagem** | O tipo de mensagem EDI X12 |
| **ICN** | O Número de Controle de Intercâmbio da mensagem X12 |
| **TSCN** | O Número de Controle do Conjunto de Transações da mensagem X12 |
| **Timestamp** | A hora em que a ação do X12 processou a mensagem |
|||

<!--
<a name="x12-folder-file-names"></a>

### X12 name formats for downloaded message files

Here are the name formats for each downloaded X12 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_outputs.txt |
|||
-->

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>Descrições das propriedades da mensagem EDIFACT

Estas são as descrições das propriedades de cada mensagem EDIFACT.

| Propriedade | Descrição |
|----------|-------------|
| **Remetente** | O parceiro convidado especificado em **Configurações de Recebimento** ou o parceiro host especificado em **Configurações de Envio** de um contrato do EDIFACT |
| **Receptor** | O parceiro host especificado em **Configurações de Recebimento** ou o parceiro convidado especificado em **Configurações de Envio** de um contrato do EDIFACT |
| **App lógico** | O aplicativo lógico no qual as ações do EDIFACT são configuradas |
| **Status** | O status da mensagem EDIFACT <br>Êxito = recebimento ou envio de uma mensagem EDIFACT válida. Nenhuma confirmação funcional está configurada. <br>Êxito = recebimento ou envio de uma mensagem EDIFACT válida. Uma confirmação funcional está configurada e é recebida ou uma confirmação funcional é enviada. <br>Com Falha = recebimento ou envio de uma mensagem EDIFACT inválida <br>Pendente = recebimento ou envio de uma mensagem EDIFACT válida. Uma confirmação funcional está configurada e uma confirmação funcional é esperada. |
| **Ack** | Status funcional de Ack (CONTRL) <br>Aceito = recebimento ou envio de uma confirmação funcional positiva. <br>Rejeitado = recebimento ou envio de uma confirmação funcional negativa. <br>Pendente = aguardando uma confirmação funcional, mas não recebida. <br>Pendente = geração de uma confirmação funcional, mas não foi possível enviá-la ao parceiro. <br>Não Obrigatório = uma confirmação funcional não está configurada. |
| **Direção** | A direção da mensagem EDIFACT |
| **ID de rastreamento** | A ID que correlaciona todos os gatilhos e todas as ações em um aplicativo lógico |
| **Tipo de mensagem** | O tipo da mensagem EDIFACT |
| **ICN** | O Número de Controle de Intercâmbio da mensagem EDIFACT |
| **TSCN** | O Número de Controle do Conjunto de Transações da mensagem EDIFACT |
| **Timestamp** | A hora em que a ação do EDIFACT processou a mensagem |
|||

<!--
<a name="edifact-folder-file-names"></a>

### EDIFACT name formats for downloaded message files

Here are the name formats for each downloaded EDIFACT message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_outputs.txt |
|||
-->

## <a name="next-steps"></a>Próximas etapas

* [Criar consultas de monitoramento e acompanhamento](../logic-apps/create-monitoring-tracking-queries.md)