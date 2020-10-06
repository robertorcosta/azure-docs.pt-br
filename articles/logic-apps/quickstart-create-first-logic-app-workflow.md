---
title: Criar um fluxo de trabalho de integração automatizado
description: Início Rápido – Criar seu primeiro fluxo de trabalho automatizado usando os Aplicativos Lógicos do Azure para soluções de integração de sistema e EAI (integração de aplicativos empresariais)
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 07/23/2020
ms.openlocfilehash: c40bec80d9f61cf46221cbfe7dde80f3a7b46f6f
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89658313"
---
# <a name="quickstart-create-your-first-automated-integration-workflow-by-using-azure-logic-apps---azure-portal"></a>Criar seu primeiro fluxo de trabalho de integração automatizado usando os Aplicativos Lógicos do Azure – portal do Azure

Este início rápido apresenta os conceitos gerais básicos por trás de como criar seu primeiro fluxo de trabalho usando os [Aplicativos Lógicos do Azure](logic-apps-overview.md), como criar um aplicativo lógico em branco, adicionar um gatilho e uma ação e testar o aplicativo lógico. Neste início rápido, você cria um aplicativo lógico que verifica regularmente o RSS feed para novos itens. Se existirem novos itens, o aplicativo lógico envia um email para cada item. Quando terminar, o aplicativo lógico ficará parecido com este fluxo de trabalho em alto nível:

![Arte conceitual mostrando um exemplo de alto nível de um fluxo de trabalho de aplicativo lógico.](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

Para este cenário, você precisa de uma assinatura do Azure ou precisa [inscrever-se para uma conta gratuita do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), uma conta de email de qualquer serviço de email compatível com os Aplicativos Lógicos do Azure, como Office 365 Outlook, Outlook.com ou Gmail. Para outros serviços de email com suporte, [verifique a lista de conectores aqui](/connectors/). Neste exemplo, o aplicativo lógico usa uma conta corporativa ou de estudante. Se você usar um serviço de email diferente, as etapas gerais serão as mesmas, mas a interface do usuário poderá ser um pouco diferente.

> [!IMPORTANT]
> Se você quiser usar o conector do Gmail, somente as contas comerciais do G Suite poderão usar esse conector sem restrição nos aplicativos lógicos. Se você tiver uma conta de consumidor do Gmail, poderá usar esse conector somente com serviços específicos do Google aprovados ou poderá [criar um aplicativo cliente do Google para usar para autenticação com o conector do Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Para obter mais informações, confira [Políticas de privacidade e segurança de dados para os conectores do Google nos Aplicativos Lógicos do Azure](../connectors/connectors-google-data-security-privacy-policy.md).

## <a name="create-your-logic-app"></a>Criar seu aplicativo lógico

1. Entre no [portal do Azure](https://portal.azure.com) com suas credenciais da conta do Azure.

1. Na caixa de pesquisa do portal do Azure, insira `logic apps` e selecione **Aplicativos Lógicos**.

   ![Captura de tela mostrando a caixa de pesquisa do portal do Azure com "aplicativos lógicos" como o termo de pesquisa e "Aplicativos Lógicos" como o resultado de pesquisa selecionado.](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. Na página **Aplicativos Lógicos**, selecione **Adicionar**.

   ![Captura de tela mostrando uma lista de aplicativos lógicos e o botão "Adicionar" selecionado.](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. No painel **Aplicativo Lógico**, forneça os detalhes sobre seu aplicativo lógico, conforme mostrado abaixo.

   ![Captura de tela mostrando o painel de criação de aplicativos lógicos com os detalhes do novo aplicativo lógico.](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Propriedade. | Valor | Descrição |
   |----------|-------|-------------|
   | **Nome** | <*logic-app-name*> | O nome de seu aplicativo lógico, que precisa ser exclusivo em todas as regiões e pode conter somente letras, números, hifens (`-`), sublinhados (`_`), parênteses (`(`, `)`) e pontos (`.`). Este exemplo usa "My-First-Logic-App". |
   | **Assinatura** | <*Azure-subscription-name*> | Seu nome da assinatura do Azure |
   | **Grupo de recursos** | <*Azure-resource-group-name*> | O nome do [grupo de recursos do Azure](../azure-resource-manager/management/overview.md), que precisa ser exclusivo em todas as regiões e é usado para organizar os recursos relacionados. Este exemplo usa "My-First-LA-RG". |
   | **Localidade** | <*Azure-region*> | A região em que as informações de seu aplicativo lógico serão armazenadas. Este exemplo usa "Oeste dos EUA". |
   | **Log Analytics** | Desativado | Mantenha a configuração **Desligado** para o log de diagnósticos. |
   ||||

1. Quando estiver pronto, selecione **Examinar + Criar**. Confirme os detalhes fornecidos e selecione **Criar**.

1. Após o Azure implantar o aplicativo com êxito, selecione **Ir para o recurso**.

   ![Captura de tela mostrando a página de implantação do recurso e o botão "Ir para o recurso" selecionado.](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   Ou você pode encontrar e selecionar seu aplicativo lógico digitando o nome na caixa de pesquisa.

   O Designer de Aplicativos Lógicos é exibido e mostra uma página com um vídeo de introdução e os gatilhos normalmente usados. Em **Modelos**, selecione **Aplicativo lógico em branco**.

   ![Captura de tela mostrando a galeria de modelos do Designer de Aplicativos Lógicos e o modelo "Aplicativo Lógico em Branco" selecionado.](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Em seguida, adicione um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) que é acionado quando um novo item de RSS feed aparece. Cada aplicativo lógico deve começar com um gatilho, que é disparado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de Aplicativos Lógicos do Azure cria uma instância de aplicativo lógico que inicia e executa o fluxo de trabalho.

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>Adicionar o gatilho do RSS

1. No **Designer de Aplicativos Lógicos**, embaixo da caixa de pesquisa, selecione **Todos**.

1. Para encontrar o conector RSS, digite `rss` na caixa de pesquisa. Na lista de gatilhos, selecione o gatilho RSS **Quando um item do feed é publicado**.

   ![Captura de tela mostrando o Designer de Aplicativos Lógicos com "rss" na caixa de pesquisa e o gatilho "Quando um item do feed é publicado" selecionado.](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Forneça as informações sobre o gatilho, conforme descrito nesta etapa:

   ![Captura de tela mostrando o Designer de Aplicativos Lógicos com as configurações do gatilho RSS, incluindo a URL, a frequência e o intervalo do RSS.](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

   | Propriedade. | Valor | Descrição |
   |----------|-------|-------------|
   | **A URL do RSS feed** | <*RSS-feed-URL*> | O link do RSS feed que você deseja monitorar. Este exemplo usa o RSS feed do Wall Street Journal em `https://feeds.a.dj.com/rss/RSSMarketsMain.xml`, mas, se desejar, use a URL de seu RSS feed. |
   | **Intervalo** | 1 | O número de intervalos de espera entre as verificações |
   | **Frequência** | Minuto | A unidade de tempo para cada intervalo entre verificações |
   ||||

   Juntos, o intervalo e a frequência definem o agendamento para o gatilho do aplicativo lógico. Este aplicativo lógico verifica o feed a cada minuto.

1. Por enquanto, para recolher os detalhes do gatilho, clique na barra de título do gatilho.

   ![Captura de tela mostrando o Designer de Aplicativos Lógicos com a forma do aplicativo lógico recolhido.](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

Seu aplicativo lógico agora está ativo, mas ele não faz nada além de verificar o RSS feed. Portanto, adicione uma ação que responde quando o gatilho é acionado.

## <a name="add-the-send-email-action"></a>Selecione a ação "enviar email"

Agora, adicione uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) que envia email quando um novo item é exibido no RSS feed.

1. No gatilho **Quando um item do feed é publicado**, selecione **Nova etapa**.

   ![Captura de tela mostrando o Designer de Aplicativos Lógicos com "Nova etapa".](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. Em **Escolher uma ação** e embaixo da caixa de pesquisa, selecione **Todos**.

1. Na caixa de pesquisa, insira `send an email` para encontrar conectores que oferecem esta ação. Para filtrar a lista de ações para um aplicativo ou um serviço específico, primeiro selecione o aplicativo ou o serviço em questão.

   Por exemplo, se estiver usando uma conta corporativa ou de estudante da Microsoft e quiser usar o Office 365 Outlook, selecione **Office 365 Outlook**. Ou, se estiver usando um conta Microsoft pessoal, selecione Outlook.com. Este exemplo continua com o Office 365 Outlook:

   ![Captura de tela mostrando o Designer de Aplicativos Lógicos e o conector do Office 365 Outlook selecionado.](./media/quickstart-create-first-logic-app-workflow/select-connector.png)

   Agora, você pode encontrar e selecionar com mais facilidade a ação que deseja usar, por exemplo, `send an email`:

   ![Captura de tela mostrando o Designer de Aplicativos Lógicos e a lista com ações filtradas.](./media/quickstart-create-first-logic-app-workflow/filtered-actions-list.png)

1. Se o conector de email selecionado solicitar a autenticação da sua identidade, conclua essa etapa agora para criar uma conexão entre o aplicativo lógico e o serviço de email.

   > [!NOTE]
   > Neste exemplo específico, autentique manualmente sua identidade. No entanto, os conectores que exigem a autenticação diferem em relação aos tipos de autenticação com os quais são compatíveis. Você também tem opções para configurar a forma como deseja lidar com a autenticação. Por exemplo, quando você usa os modelos do Azure Resource Manager para implantação, é possível parametrizar e melhorar a segurança nas entradas que você queira alterar com frequência ou com facilidade, como informações de conexão. Para saber mais, consulte esses tópicos:
   >
   > * [Parâmetros de modelo para implantação](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [Autorizar conexões do OAuth](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [Autenticar o acesso com identidades gerenciadas](../logic-apps/create-managed-service-identity.md)
   > * [Autenticar as conexões para a implantação de aplicativo lógico](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. Na ação **Enviar um email**, especifique as informações a serem incluídas no email.

   1. Na caixa **Para**, insira o endereço de email do destinatário. Para fins de teste, você pode usar seu endereço de email.

      Por enquanto, ignore a lista **Adicionar conteúdo dinâmico** que forem exibidas. Quando você clica dentro de algumas caixas de edição, essa lista é exibida e mostra as saídas disponíveis da etapa anterior que você pode usar como entradas para a ação atual.

   1. Na caixa **Assunto**, digite este texto com um espaço em branco à direita: `New RSS item: `

      ![Captura de tela mostrando o Designer de Aplicativos Lógicos com a ação "Enviar um email" e o cursor dentro da caixa de propriedade "Assunto".](./media/quickstart-create-first-logic-app-workflow/send-email-subject.png)

   1. Na lista **Adicionar conteúdo dinâmico**, selecione **Título do feed**, que é a saída do gatilho "Quando um item do feed é publicado", que disponibiliza o título do item RSS para uso.

      ![Captura de tela mostrando o Designer de Aplicativos Lógicos com a ação "Enviar um email" e o cursor dentro da caixa de propriedade "Assunto", com uma lista de conteúdo dinâmico aberta e a saída "Título do feed" selecionada.](./media/quickstart-create-first-logic-app-workflow/send-email-subject-dynamic-content.png)

      > [!TIP]
      > Na lista de conteúdo dinâmico, se não aparecer nenhuma saída do gatilho "Quando um item do feed é publicado" ao lado do cabeçalho da ação, selecione **Ver mais**.
      > 
      > ![Captura de tela mostrando o Designer de Aplicativos Lógicos com a lista de conteúdo dinâmico aberta e "Ver mais" selecionado para o gatilho.](./media/quickstart-create-first-logic-app-workflow/dynamic-content-list-see-more-actions.png)

      Quando você tiver acabado, o assunto do email deve ser semelhante ao exemplo a seguir:

      ![Captura de tela mostrando o Designer de Aplicativos Lógicos com a ação "Enviar um email" e um exemplo de assunto de email com a propriedade "Título do feed" incluída.](./media/quickstart-create-first-logic-app-workflow/send-email-feed-title.png)

      Se um loop "For each" for exibida no designer, então você selecionou um token para uma matriz, por exemplo, o token **categories-Item**. Para esses tipos de token, o designer adiciona automaticamente esse loop em torno da ação que faz referência a esse token. Dessa forma, seu aplicativo lógico executará a mesma ação em cada item da matriz. Para remover o loop, selecione as **elipses** (**...**) na barra de título do loop e, em seguida, selecione **Excluir**.

   1. Na caixa **Corpo**, insira o texto e selecione esses tokens para o corpo do email. Para adicionar linhas em branco em uma caixa de edição, pressione Shift + Enter.

      ![Captura de tela mostrando o Designer de Aplicativos Lógicos com a ação "Enviar um email" e as propriedades selecionadas dentro da caixa "Corpo".](./media/quickstart-create-first-logic-app-workflow/send-email-body.png)

      | Propriedade. | Descrição |
      |----------|-------------|
      | **Título do feed** | O título do item |
      | **Feed publicado em** | A data e a hora de publicação do item |
      | **Link principal do feed** | A URL para o item |
      |||

1. Salve seu aplicativo lógico.

Em seguida, teste o seu aplicativo lógico.

## <a name="run-your-logic-app"></a>Executar seu aplicativo lógico

Para executar manualmente o aplicativo lógico, selecione **Executar** na barra de ferramentas do designer. Ou então, aguarde até que seu aplicativo lógico verifique o feed RSS com base em seu agendamento especificado (cada minuto).

![Captura de tela mostrando o Designer de Aplicativos Lógicos com o botão "Executar" selecionado na barra de ferramentas do designer.](./media/quickstart-create-first-logic-app-workflow/run-logic-app-test.png)

Se o RSS feed possuir novos itens, o seu aplicativo lógico enviará um email para cada novo item. Caso contrário, o aplicativo lógico aguarda até o próximo intervalo antes de verificar novamente. Se você não receber nenhum email, verifique a sua pasta de Lixo eletrônico.

Por exemplo, aqui está um email de exemplo enviado por esse aplicativo lógico.

![Captura de tela mostrando um exemplo de email recebido quando um novo item de RSS feed é exibido.](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Tecnicamente, quando o gatilho verifica o RSS feed e localiza novos itens, o gatilho é acionado e o mecanismo de Aplicativos Lógicos do Azure cria uma instância do seu fluxo de trabalho de aplicativo lógico que executa as ações no fluxo de trabalho. Se o gatilho não localizar novos itens, ele não é acionado e "ignora" instanciando o fluxo de trabalho.

Parabéns, você compilou e executou seu primeiro aplicativo lógico com o portal do Azure.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você não precisar mais deste exemplo, exclua o grupo de recursos que contém o aplicativo lógico e os recursos relacionados.

1. Na caixa de pesquisa do Azure, insira `resource groups` e selecione **Grupos de recursos**.

   ![Captura de tela mostrando a caixa de pesquisa do portal do Azure com o termo de pesquisa "grupos de recursos".](./media/quickstart-create-first-logic-app-workflow/find-resource-groups.png)

1. Localize e selecione o grupo de recursos de seu aplicativo lógico. No painel **Visão geral**, selecione **Excluir grupo de recursos**.

   ![Captura de tela mostrando o portal do Azure com o grupo de recursos selecionado e o botão "Excluir grupo de recursos".](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Quando o painel de confirmação for exibido, insira o nome do grupo de recursos e selecione **Excluir**.

   ![Captura de tela mostrando o portal do Azure com o painel de confirmação e o nome do grupo de recursos que será excluído.](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> Ao excluir um aplicativo lógico, nenhuma nova execução será instanciada. Todas as execuções em andamento e pendentes serão canceladas. Se você tiver milhares de execuções, o cancelamento poderá demorar um tempo significativo até ser concluído.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou seu primeiro aplicativo lógico que procura atualizações RSS com base em um agendamento específico (cada minuto) e executa uma ação (envia email) quando há atualizações. Para saber mais, continue com este tutorial que cria fluxos de trabalho mais avançados baseadas em agendamento:

> [!div class="nextstepaction"]
> [Verificar o tráfego com um aplicativo lógico baseado em agendamento](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
