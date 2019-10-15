---
title: Criar fluxos de trabalho automatizados baseados em aprovação – Aplicativos Lógicos do Azure
description: Tutorial – Criar um fluxo de trabalho automatizado baseado em aprovação que processa as assinaturas da lista de endereçamento usando os Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/20/2019
ms.openlocfilehash: fb92e882607575e99ae800bd9c6d7c36b5d89d8e
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034580"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Tutorial: Criar fluxos de trabalho automatizados baseados em aprovação usando os Aplicativos Lógicos do Azure

Este tutorial mostra como criar um [aplicativo lógico](../logic-apps/logic-apps-overview.md) que automatiza um fluxo de trabalho baseado em aprovação. Especificamente, este aplicativo lógico processa as solicitações de assinatura para uma lista de endereçamento gerenciada pelo serviço [MailChimp](https://mailchimp.com/). Este aplicativo lógico monitora uma conta de email para essas solicitações, envia essas solicitações para aprovação e adiciona membros aprovados para a lista de endereçamento.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um aplicativo lógico em branco.
> * Adicionar um gatilho que monitora emails com solicitações de assinatura.
> * Adicionar uma ação que envia emails para aprovar ou rejeitar essas solicitações.
> * Adicionar uma condição que verifica a resposta de aprovação.
> * Adicionar uma ação que adiciona membros aprovados à lista de endereçamento.
> * Adicionar uma condição que verifica se esses membros ingressaram na lista com êxito.
> * Adicionar uma ação que envia emails confirmando se esses membros ingressaram na lista com êxito.

Quando terminar, o aplicativo lógico ficará parecido com este fluxo de trabalho em alto nível:

![Visão geral de aplicativo lógico concluído em alto nível](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-overview.png)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso você não tenha uma assinatura, [crie uma conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

* Uma conta do MailChimp que contém e uma lista chamada "test-members-ML", à qual o aplicativo lógico pode adicionar endereços de email para membros aprovados. Se você não tiver uma conta, [inscreva-se em uma conta gratuita](https://login.mailchimp.com/signup/) e, em seguida, saiba [como criar uma lista do MailChimp](https://us17.admin.mailchimp.com/lists/#).

* Uma conta de email no Office 365 Outlook ou no Outlook.com, que dê suporte aos fluxos de trabalho de aprovação. Este artigo usa o Outlook do Office 365. Se você usar uma conta de email diferente, as etapas gerais serão as mesmas, mas a interface do usuário poderá parecer um pouco diferente.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com) com suas credenciais da conta do Azure.

## <a name="create-your-logic-app"></a>Criar seu aplicativo lógico

1. No menu principal do Azure, selecione **Criar um recurso** > **Integração** > **Aplicativo Lógico**.

   ![Criar seu recurso de aplicativo lógico](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. Em **Criar aplicativo lógico**, forneça essas informações sobre seu aplicativo lógico, como mostrado e descrito. Quando terminar, selecione **Criar**.

   ![Fornecer informações sobre seu aplicativo lógico](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Propriedade | Valor | DESCRIÇÃO |
   |----------|-------|-------------|
   | **Nome** | LA-MailingList | O nome do seu aplicativo lógico, que pode conter apenas letras, números, hifens (`-`), sublinhados (`_`), parênteses (`(`, `)`) e pontos (`.`). Este exemplo usa "LA-MailingList". |
   | **Assinatura** | <*nome-da-sua-assinatura-do-Azure*> | Seu nome da assinatura do Azure |
   | **Grupo de recursos** | LA-MailingList-RG | O nome do [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md), usado para organizar os recursos relacionados. Este exemplo usa "LA-MailingList-RG". |
   | **Localidade** | Oeste dos EUA | A região em que as informações de seu aplicativo lógico são armazenadas. Este exemplo usa "Leste dos EUA". |
   | **Log Analytics** | Desativar | Mantenha a configuração **Desligado** para o log de diagnósticos. |
   ||||

1. Depois que o Azure implanta seu aplicativo, na barra de ferramentas do Azure, selecione **Notificações** > **Ir para o recurso** para seu aplicativo lógico implantado.

   ![Ir para o novo recurso de aplicativo lógico](./media/tutorial-process-mailing-list-subscriptions-workflow/go-to-logic-app-resource.png)

   Ou você pode encontrar e selecionar seu aplicativo lógico digitando o nome na caixa de pesquisa.

   O Designer de Aplicativos Lógicos é aberto e mostra uma página com um vídeo de introdução e os gatilhos e padrões de aplicativo lógico normalmente usados. Em **Modelos**, selecione **Aplicativo lógico em branco**.

   ![Selecione o modelo de aplicativo lógico em branco](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Em seguida, adicione um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) que escuta emails de entrada com solicitações de assinatura. Cada aplicativo lógico deve começar com um gatilho, que é disparado quando um evento específico ocorre ou quando novos dados atendem a uma condição específica. Para saber mais, confira [Criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Adiciona gatilho para monitorar emails

1. No Designer de Aplicativos Lógicos, na caixa de pesquisa, insira `when email arrives` como o filtro. Na lista **Gatilhos**, selecione o gatilho **Quando um novo email é recebido** para o seu provedor de email.

   Este exemplo usa o gatilho do Office 365 Outlook:

   ![Selecionar o gatilho "Quando um novo email é recebido" para o provedor de email](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * Para as contas corporativas ou de estudante do Azure, selecione o Outlook do Office 365.
   * Para contas pessoais da Microsoft, selecione Outlook.com.

1. Se solicitado, entre em sua conta de email com suas credenciais, de modo que os Aplicativos Lógicos possam criar uma conexão com a sua conta de email.

1. No gatilho, forneça os critérios para verificar todos os novos emails.

   1. Especifique a pasta, o intervalo e a frequência de verificação de emails.

      ![Especificar a pasta, intervalo e frequência de verificação de emails](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | Propriedade | Valor | DESCRIÇÃO |
      |----------|-------|-------------|
      | **Pasta** | `Inbox` | A pasta de email a ser monitorada |
      | **Intervalo** | `1` | O número de intervalos de espera entre as verificações |
      | **Frequência** | `Hour` | A unidade de tempo a ser usada para a recorrência |
      ||||

   1. Agora adicione outra propriedade ao gatilho, de modo que você possa filtrar por linha do assunto de email. Abra a **lista Adicionar novo parâmetro** e selecione a propriedade **Filtro de Assunto**.

      ![Adicionar a propriedade "Filtro de Assunto" para disparar](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Para obter mais informações sobre as propriedades desse gatilho, confira a [referência do conector do Office 365 Outlook](https://docs.microsoft.com/connectors/office365/) ou a [referência do conector do Outlook.com](https://docs.microsoft.com/connectors/outlook/).

   1. Depois que a propriedade for exibida no gatilho, insira este texto: `subscribe-test-members-ML`

      ![Inserir texto para a propriedade "Filtro de Assunto"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Por enquanto, para ocultar os detalhes do gatilho, clique na barra de título do gatilho.

   ![Recolher forma para ocultar detalhes](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

Seu aplicativo lógico agora está ativo, mas ele não faz nada além de verificar o recebimento de emails. Portanto, adicione uma ação que responde quando o gatilho é acionado.

## <a name="send-approval-email"></a>Enviar email de aprovação

Agora que você tem um gatilho, adicione uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) que envia um email para aprovar ou rejeitar a solicitação.

1. No gatilho, selecione **Nova etapa**. 

1. Em **Escolher uma ação**, na caixa de pesquisa, insira `approval` como o filtro. Na lista de ações, selecione a ação **Enviar email de aprovação** para o seu provedor de email. 

   Este exemplo usa a ação do Office 365 Outlook:

   ![Selecionar a ação "Enviar um email de aprovação"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Forneça as informações sobre essa ação, conforme descrito: 

   ![Propriedades de Enviar email de aprovação](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Propriedade | Valor | DESCRIÇÃO |
   |----------|-------|-------------|
   | **Para** | <*your-email-address*> | Endereço de email do aprovador. Para fins de teste, você pode usar seu próprio endereço. Este exemplo usa o endereço de email fictício "sophia.owen@fabrikam.com". |
   | **Assunto** | `Approve member request for test-members-ML` | Um assunto de email descritivo |
   | **Opções de Usuário** | `Approve, Reject` | As opções de resposta que podem ser selecionadas pelo aprovador. Por padrão, o aprovador pode selecionar "Aprovar" ou "Rejeitar" como resposta. |
   ||||

   Por enquanto, ignore a lista de conteúdo dinâmico que é exibida quando você clica em caixas de edição específicas. A lista permite selecionar a saída disponível das ações anteriores que você pode usar como entradas no fluxo de trabalho.

   Para obter mais informações sobre as propriedades dessa ação, confira a [referência do conector do Office 365 Outlook](https://docs.microsoft.com/connectors/office365/) ou a [referência do conector do Outlook.com](https://docs.microsoft.com/connectors/outlook/).
 
1. Salve seu aplicativo lógico.

Em seguida, adicione uma condição para verificar a resposta selecionada do aprovador.

## <a name="check-approval-response"></a>Verificar resposta de aprovação

1. Na ação **Enviar email de aprovação**, selecione **Nova etapa**".

1. Em **Escolha uma ação**, selecione **Interno**. Na caixa de pesquisa, insira `condition` como o filtro. Na lista de ações, selecione a ação **Condição**.

   ![Localize e selecione a ação "Condição"](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. Renomeie a condição com uma descrição melhor.

   1. Na barra de título da condição, selecione o botão de **reticências** ( **...** ) > **Renomear**.

      ![Renomeie a descrição da condição](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

   1. Renomeie sua condição com esta descrição:`If request approved`

1. Crie uma condição que verifica se o aprovador selecionou **Aprovar**.

   1. Na condição, clique dentro da caixa **Escolher um valor** no lado esquerdo da condição.

   1. Na lista de conteúdo dinâmico exibida, em **Enviar email de aprovação**, selecione a propriedade **SelectedOption**.

      ![Na lista de conteúdo dinâmico, selecione "SelectedOption"](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. Na caixa de comparação intermediária, selecione o operador **é igual a**.

   1. Na caixa **Escolher um valor** no lado direito da condição, insira este texto: `Approve`

      Quando terminar, a condição será semelhante a este exemplo:

      ![Condição concluída para o exemplo Aprovado](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Salve seu aplicativo lógico.

Em seguida, especifique a ação que seu aplicativo lógico executará quando o revisor aprovar a solicitação. 

## <a name="add-member-to-mailchimp-list"></a>Adicionar membros à lista do MailChimp

Agora adicione uma ação que adiciona o membro aprovado à sua lista de endereçamento.

1. No branch **Se verdadeiro** da condição, selecione **Adicionar uma ação**.

1. Em **Escolher uma ação**, insira `mailchimp` como o filtro e selecione a ação **Adicionar membro à lista**.

   ![Selecionar a ação "Adicionar membro à lista"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Caso precise acessar sua conta do MailChimp, entre com suas credenciais do MailChimp.

1. Forneça informações sobre essa ação, conforme mostrado e descrito aqui:

   ![Fornecer informações para "Adicionar membros à lista"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Propriedade | Obrigatório | Value | DESCRIÇÃO |
   |----------|----------|-------|-------------|
   | **ID da lista** | Sim | `test-members-ML` | O nome da lista de endereçamento do MailChimp. Este exemplo usa "test-members-ML". |
   | **Status** | Sim | `subscribed` | Selecione o status da assinatura para o novo membro. Este exemplo usa "subscribed". <p>Para saber mais, confira [Gerenciar assinantes com a API do MailChimp](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/). |
   | **Endereço de Email** | Sim | <*new-member-email-address*> | Na lista de conteúdo dinâmico, selecione **De** em **Quando um novo email é recebido**, que passa o endereço de email para o novo membro. |
   ||||

   Para obter mais informações sobre as propriedades dessa ação, confira a [referência do conector do MailChimp](https://docs.microsoft.com/connectors/mailchimp/).

1. Salve seu aplicativo lógico.

Em seguida, adicione uma condição para que você possa verificar se o novo membro ingressou na lista de endereçamento com êxito. Dessa forma, seu aplicativo lógico notifica se essa operação for bem-sucedida ou apresentou falha.

## <a name="check-for-success-or-failure"></a>Verificar êxito ou falha

1. No branch **Se verdadeiro**, na ação **Adicionar membro à lista**, selecione **Adicionar uma ação**.

1. Em **Escolha uma ação**, selecione **Interno**. Na caixa de pesquisa, insira `condition` como o filtro. Na lista de ações, selecione **Condição**.

1. Renomeie a condição com esta descrição:`If add member succeeded`

1. Crie uma condição que verifica se o membro aprovado teve êxito ou falha ao ingressar na lista de endereçamento:

   1. Na condição, clique dentro da caixa **Escolher um valor**, que está no lado esquerdo da condição. Na lista conteúdo dinâmico, em **Adicionar membro à lista**, selecione a propriedade **Status**.

      Por exemplo, sua condição é semelhante a este exemplo:

      ![Em "Adicionar membro à lista", selecionar "Status"](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. Na caixa de comparação intermediária, selecione o operador **é igual a**.

   1. Na caixa **Escolher um valor** no lado direito da condição, insira este texto: `subscribed`

      Quando terminar, a condição será semelhante a este exemplo:

      ![Condição concluída para o exemplo Assinado](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Em seguida, configure os emails para envio quando o membro aprovado tem êxito ou falha ao ingressar na lista de endereçamento.

## <a name="send-email-if-member-added"></a>Enviar email se o membro foi adicionado

1. Na condição **Se adicionar membro foi bem-sucedido**, no branch **Se verdadeiro**, selecione **Adicionar uma ação**.

   ![No branch "Se verdadeiro", selecione "Adicionar uma ação"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. Em **Escolher uma ação**, na caixa de pesquisa, insira `outlook send email` como o filtro e selecione a ação **Enviar um email**.

   ![Adicionar uma ação "Enviar um email"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Renomeie a ação com esta descrição:`Send email on success`

1. Forneça informações para essa ação, como mostrado e descrito:

   ![Fornecer informações de email de êxito](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Propriedade | Obrigatório | Value | DESCRIÇÃO |
   |----------|----------|-------|-------------|
   | **Para** | Sim | <*your-email-address*> | O endereço de email para onde enviar o email de êxito. Para fins de teste, você pode usar seu próprio endereço de email. |
   | **Assunto** | Sim | <*subject-for-success-email*> | O assunto do email de êxito. Para este tutorial, digite este texto: <p>`Success! Member added to "test-members-ML": ` <p>Na lista conteúdo dinâmico, em **Adicionar membro à lista**, selecione a propriedade **Endereço de Email**. |
   | **Corpo** | Sim | <*body-for-success-email*> | O conteúdo do corpo do email de êxito. Para este tutorial, digite este texto: <p>`New member has joined "test-members-ML":` <p>Na lista conteúdo dinâmico, selecione a propriedade **Endereço de Email**. <p>Na próxima linha, insira este texto: `Member opt-in status: ` <p> Na lista conteúdo dinâmico, em **Adicionar membro à lista**, selecione a propriedade **Status**. |
   |||||

1. Salve seu aplicativo lógico.

## <a name="send-email-if-member-not-added"></a>Enviar email se membro não foi adicionado

1. Na condição **Se adicionar membro foi bem-sucedido**, no branch **Se falso**, selecione **Adicionar uma ação**.

   ![No branch "Se falso", selecione "Adicionar uma ação"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. Em **Escolher uma ação**, na caixa de pesquisa, insira `outlook send email` como o filtro e selecione a ação **Enviar um email**.

   ![Adicionar ação para "Enviar um email"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Renomeie a ação com esta descrição:`Send email on failure`

1. Forneça informações sobre essa ação, conforme mostrado e descrito aqui:

   ![Fornecer informações de email de falha](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Propriedade | Obrigatório | Value | DESCRIÇÃO |
   |----------|----------|-------|-------------|
   | **Para** | Sim | <*your-email-address*> | O endereço de email para onde enviar o email de falha. Para fins de teste, você pode usar seu próprio endereço de email. |
   | **Assunto** | Sim | <*subject-for-failure-email*> | O assunto do email de falha. Para este tutorial, digite este texto: <p>`Failed, member not added to "test-members-ML": ` <p>Na lista conteúdo dinâmico, em **Adicionar membro à lista**, selecione a propriedade **Endereço de Email**. |
   | **Corpo** | Sim | <*body-for-failure-email*> | O conteúdo do corpo do email de falha. Para este tutorial, digite este texto: <p>`Member might already exist. Check your MailChimp account.` |
   |||||

1. Salve seu aplicativo lógico. 

Em seguida, teste seu aplicativo lógico, que agora se parece com este exemplo:

![Exemplo de fluxo de trabalho do aplicativo lógico concluído](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>Executar seu aplicativo lógico

1. Envie uma solicitação de email a si mesmo para ingressar na lista de endereçamento. Aguarde até que a solicitação seja exibida na caixa de entrada.

1. Para executar manualmente o aplicativo lógico, selecione **Executar** na barra de ferramentas do designer. 

   Se seu email tiver uma entidade que corresponde ao filtro de assunto do disparador, o aplicativo lógico envia um email para aprovar a solicitação de assinatura.

1. No email de aprovação, selecione **Aprovar**.

1. Se o endereço de email do assinante não existe na sua lista de endereçamento, o aplicativo lógico adiciona o endereço de email dessa pessoa e envia um email como neste exemplo:

   ![Email de exemplo – assinatura bem-sucedida](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Se seu aplicativo lógico não consegue adicionar o assinante, você recebe um email como neste exemplo:

   ![Email de exemplo – assinatura com falha](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

   Se você não receber nenhum email, verifique a pasta de Lixo eletrônico. O filtro de lixo de email pode redirecionar esses tipos de mensagens. Caso contrário, se você não tiver certeza de que seu aplicativo lógico foi executado corretamente, confira [Solução de problemas do aplicativo lógico](../logic-apps/logic-apps-diagnosing-failures.md).

Parabéns, você criou e executou um aplicativo lógico que integra informações entre serviços Azure, Microsoft e outros aplicativos SaaS.

## <a name="clean-up-resources"></a>Limpar recursos

Quando você não precisar mais do aplicativo lógico de exemplo, exclua o grupo de recursos que contém o aplicativo lógico e os recursos relacionados. 

1. No menu principal do Azure, vá para **Grupos de recursos** e selecione o grupo de recursos do seu aplicativo lógico.

1. No menu do grupo de recursos, selecione **Visão Geral** > **Excluir grupo de recursos**. 

   ![“Visão Geral” > “Excluir grupo de recursos”.](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. Insira o nome do grupo de recursos como confirmação e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um aplicativo lógico que gerencia as aprovações para solicitações de lista de endereçamento. Agora, aprenda a criar um aplicativo lógico que processa e armazena anexos de email integrando serviços do Azure, como o Armazenamento do Azure e o Azure Functions.

> [!div class="nextstepaction"]
> [Processar anexos do email](../logic-apps/tutorial-process-email-attachments-workflow.md)
