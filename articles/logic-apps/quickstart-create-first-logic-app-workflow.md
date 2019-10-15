---
title: Início Rápido – Criar e automatizar seu primeiro fluxo de trabalho com os Aplicativos Lógicos do Azure
description: Crie seu primeiro aplicativo lógico que automatiza tarefas, processos e fluxos de trabalho usando os Aplicativos Lógicos do Azure. Crie aplicativos lógicos para integração do sistema e soluções de EAI (Integração de Aplicativos Empresariais) para seus sistemas e serviços de nuvem.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/20/2019
ms.openlocfilehash: c85b3e3ced661eb36d9cb1eb0ae443a5ab21a913
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029447"
---
# <a name="quickstart-create-your-first-automated-workflow-with-azure-logic-apps---azure-portal"></a>Início Rápido: Crie seu primeiro fluxo de trabalho automatizado com os Aplicativos Lógicos do Azure – Portal do Azure

Este início rápido apresenta como criar seu primeiro fluxo de trabalho automatizado com o [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md). Neste artigo, você cria um aplicativo lógico que verifica regularmente o RSS feed para novos itens. Se existirem novos itens, o aplicativo lógico envia um email para cada item. Quando terminar, o aplicativo lógico ficará parecido com este fluxo de trabalho em alto nível:

![Exemplo de fluxo de trabalho do aplicativo lógico de alto nível](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

Para seguir este guia de início rápido, você precisa de uma conta de email com qualquer provedor de email compatível com aplicativos lógicos do Azure, como o Office 365 Outlook, Outlook.com ou Gmail. Para outros provedores, [revise a lista de conectores aqui](https://docs.microsoft.com/connectors/). Esso aplicativo lógico usa uma conta do Outlook do Office 365. Se você usar outra conta de email, as etapas gerais são as mesmos, mas a interface do usuário pode ser ligeiramente diferente.

Caso você não tenha uma assinatura do Azure, [crie uma conta gratuita do Azure](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com) com suas credenciais da conta do Azure.

## <a name="create-your-logic-app"></a>Criar seu aplicativo lógico

1. No menu principal do Azure, selecione **Criar um recurso** > **Integração** > **Aplicativo Lógico**.

   ![Criar aplicativo lógico – portal do Azure](./media/quickstart-create-first-logic-app-workflow/create-new-logic-app.png)

1. Em **Criar aplicativo lógico**, forneça os detalhes sobre seu aplicativo lógico, conforme mostrado aqui. Quando terminar, selecione **Criar**.

   ![Forneça detalhes para o novo aplicativo lógico](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Propriedade | Valor | DESCRIÇÃO |
   |----------|-------|-------------|
   | **Nome** | <*logic-app-name*> | O nome do aplicativo lógico, que pode conter apenas letras, números, hifens (`-`), sublinhados (`_`), parênteses(`(`, `)`) e pontos (`.`). Este exemplo usa "My-First-Logic-App". |
   | **Assinatura** | <*Azure-subscription-name*> | Seu nome da assinatura do Azure |
   | **Grupo de recursos** | <*Azure-resource-group-name*> | O nome do [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) usado para organizar os recursos relacionados. Este exemplo usa "My-First-LA-RG". |
   | **Localidade** | <*Azure-region*> | A região em que as informações de seu aplicativo lógico serão armazenadas. Este exemplo usa "Leste dos EUA". |
   | **Log Analytics** | Desativar | Mantenha a configuração **Desligado** para o log de diagnósticos. |
   ||||

1. Depois que o Azure implanta seu aplicativo, na barra de ferramentas do Azure, selecione **Notificações** > **Ir para o recurso** para seu aplicativo lógico implantado.

   ![Vá para o recurso de aplicativo lógico recém-criado](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   Ou você pode encontrar e selecionar seu aplicativo lógico digitando o nome na caixa de pesquisa.

   O Designer de Aplicativos Lógicos é exibido e mostra uma página com um vídeo de introdução e os gatilhos normalmente usados. Em **Modelos**, selecione **Aplicativo lógico em branco**.

   ![Selecione o modelo de aplicativo lógico em branco](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Em seguida, adicione um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) que é acionado quando um novo item de RSS feed aparece. Cada aplicativo lógico deve começar com um gatilho, que é disparado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o disparador é acionado, o mecanismo de Aplicativos Lógicos cria uma instância de aplicativo lógico que inicia e executa o fluxo de trabalho.

<a name="add-rss-trigger"></a>

## <a name="check-rss-feed-with-a-trigger"></a>Verifique o RSS feed com um gatilho

1. No Designer do Aplicativo Lógico, na caixa de pesquisa, selecione **Tudo**.

1. Na caixa de pesquisa, digite "rss". Na lista de gatilhos, selecione este gatilho: **Quando um item do feed é publicado – RSS**

   ![Selecione o gatilho "Quando um item do feed é publicado"](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Forneça essas informações para o gatilho como mostrado e descrito aqui:

   ![Configurar um gatilho com o RSS feed, frequência e intervalo](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

   | Propriedade | Valor | DESCRIÇÃO |
   |----------|-------|-------------|
   | **A URL do RSS feed** | `http://feeds.reuters.com/reuters/topNews` | O link do RSS feed do site que você deseja monitorar |
   | **Intervalo** | 1 | O número de intervalos de espera entre as verificações |
   | **Frequência** | Minuto | A unidade de tempo para cada intervalo entre verificações  |
   ||||

   Juntos, o intervalo e a frequência definem o agendamento para o gatilho do aplicativo lógico. Este aplicativo lógico verifica o feed a cada minuto.

1. Por enquanto, para ocultar os detalhes do gatilho, clique na barra de título do gatilho.

   ![Recolha a forma do aplicativo lógico para ocultar detalhes](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

Seu aplicativo lógico agora está ativo, mas ele não faz nada além de verificar o RSS feed. Portanto, adicione uma ação que responde quando o gatilho é acionado.

## <a name="send-email-with-an-action"></a>Enviar email com uma ação

Agora adicione uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) que envia email quando um novo item é exibido no RSS feed.

1. No gatilho **Quando um item do feed é publicado**, selecione **Nova etapa**.

   ![No gatilho, selecione "Nova etapa"](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. Em **Escolha uma ação** e na caixa de pesquisa, selecione **Todas**.

1. Na caixa de pesquisa, digite "enviar um email". Na lista de ações, selecione a ação "enviar um email" para o provedor de email que você deseja.

   ![Selecione a ação "Enviar um email" para o Office 365 Outlook](./media/quickstart-create-first-logic-app-workflow/add-action-send-email.png)

   Para filtrar a lista de ações para um aplicativo específico ou um serviço, você pode selecionar esse aplicativo ou serviço primeiro:

   * Para as contas corporativas ou de estudante do Azure, selecione o Outlook do Office 365.
   * Para contas pessoais da Microsoft, selecione Outlook.com.

1. Se forem solicitadas credenciais, entre na sua conta de email para que os Aplicativos Lógicos criem uma conexão à sua conta de email.

1. Na ação **Enviar um email**, especifique os dados que você deseja incluir no email.

   1. Na caixa **Para**, insira o endereço de email do destinatário. Para fins de teste, você pode usar seu próprio endereço de email.

      Por enquanto, ignore a lista **Adicionar conteúdo dinâmico** que forem exibidas. Quando você clica em algumas caixas de edição, essa lista é exibida e mostra os parâmetros disponíveis de etapa anterior que você pode incluir em seu fluxo de trabalho como entradas.

   1. Na caixa **Assunto**, digite este texto com um espaço em branco à direita: ```New RSS item:```

      ![Na propriedade "Subject", insira o assunto do email](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject.png)

   1. Na lista **Adicionar conteúdo dinâmico**, selecione **Título do feed** para incluir o título do item do RSS.

      ![Na lista de conteúdo dinâmico, selecione a propriedade "Título do feed"](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject-dynamic-content.png)

      Quando você tiver acabado, o assunto do email deve ser semelhante ao exemplo a seguir:

      ![Exemplo de assunto de email concluído para o título de feed adicionado](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-feed-title.png)

      Se um loop "For each" for exibida no designer, então você selecionou um token para uma matriz, por exemplo, o token **categories-Item**. Para esses tipos de token, o designer adiciona automaticamente esse loop em torno da ação que faz referência a esse token. Dessa forma, seu aplicativo lógico executará a mesma ação em cada item da matriz. Para remover o loop, selecione as **elipses** ( **...** ) na barra de título do loop e, em seguida, selecione **Excluir**.

   1. Na caixa **Corpo**, insira o texto e selecione esses tokens para o corpo do email. Para adicionar linhas em branco em uma caixa de edição, pressione Shift + Enter.

      ![Selecionar propriedades para o conteúdo do corpo do email](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-body.png)

      | Propriedade | DESCRIÇÃO |
      |----------|-------------|
      | **Título do feed** | O título do item |
      | **Feed publicado em** | A data e a hora de publicação do item |
      | **Link principal do feed** | A URL para o item |
      |||

1. Salve seu aplicativo lógico.

Em seguida, teste o seu aplicativo lógico.

## <a name="run-your-logic-app"></a>Executar seu aplicativo lógico

Para executar manualmente o aplicativo lógico, selecione **Executar** na barra de ferramentas do designer. Ou então, aguarde até que seu aplicativo lógico verifique o feed RSS com base em seu agendamento especificado (cada minuto). Se o RSS feed possuir novos itens, o seu aplicativo lógico enviará um email para cada novo item. Caso contrário, o aplicativo lógico aguarda até o próximo intervalo antes de verificar novamente. Se você não receber nenhum email, verifique a sua pasta de Lixo eletrônico.

Por exemplo, aqui está um email de exemplo enviado por esse aplicativo lógico.

![Email de exemplo enviado quando um novo item de RSS feed é exibido](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Tecnicamente, quando o gatilho verifica o RSS feed e localiza novos itens, o gatilho é acionado e o mecanismo de Aplicativos Lógicos cria uma instância do seu fluxo de trabalho de aplicativo lógico que executa as ações no fluxo de trabalho. Se o gatilho não localizar novos itens, ele não é acionado e "ignora" instanciando o fluxo de trabalho.

Parabéns, você compilou e executou seu primeiro aplicativo lógico com o portal do Azure.

## <a name="clean-up-resources"></a>Limpar recursos

Quando você não precisar mais deste exemplo, exclua o grupo de recursos que contém o aplicativo lógico e os recursos relacionados.

1. No menu principal do Azure, selecione **Grupos de recursos** e selecione o grupo de recursos do seu aplicativo lógico. No painel **Visão geral**, selecione **Excluir grupo de recursos**.

   ![Localizar, selecionar e excluir um grupo de recursos](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Insira o nome do grupo de recursos como confirmação e selecione **Excluir**.

   ![Para confirmar a exclusão, selecione "Excluir"](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> Ao excluir um aplicativo lógico, nenhuma nova execução será instanciada. Todas as execuções em andamento e pendentes serão canceladas. Se você tiver milhares de execuções, o cancelamento poderá demorar um tempo significativo até ser concluído.

## <a name="get-support"></a>Obtenha suporte

Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/home?forum=azurelogicapps).

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou seu primeiro aplicativo lógico que procura atualizações RSS com base em um agendamento específico (cada minuto) e executa uma ação (envia email) quando há atualizações. Para saber mais, continue com este tutorial que cria fluxos de trabalho mais avançados baseadas em agendamento:

> [!div class="nextstepaction"]
> [Verificar o tráfego com um aplicativo lógico baseado em agendamento](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
