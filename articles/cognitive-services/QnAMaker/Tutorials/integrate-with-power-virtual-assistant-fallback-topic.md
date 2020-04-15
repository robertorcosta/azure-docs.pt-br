---
title: 'Tutorial: Integração ao Power Virtual Agent – QnA Maker'
description: Neste tutorial, aprimore a qualidade da sua base de dados de conhecimento com o aprendizado ativo. Examine, aceite ou rejeite, adicione sem remover ou altere as perguntas existentes.
ms.topic: tutorial
ms.date: 03/11/2020
ms.openlocfilehash: 283667c587e395a1d712f82f3385582b4c5c3227
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398628"
---
# <a name="tutorial-add-knowledge-base-to-power-virtual-agent"></a>Tutorial: Adicionar uma base de dados de conhecimento ao Power Virtual Agent
Crie e estenda um bot do [Power Virtual Agent](https://powervirtualagents.microsoft.com/) para fornecer respostas da sua base de dados de conhecimento.

**Neste tutorial, você aprenderá a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Criar um Power Virtual Agent
> * Criar um tópico de Fallback do sistema
> * Adicionar o QnA Maker como uma ação ao tópico como um fluxo do Power Automate
> * Criar uma solução do Power Automate
> * Adicionar um fluxo do Power Automate à solução
> * Publicar o Power Virtual Agent
> * Testar o Power Virtual Agent e receber a resposta da base de dados de conhecimento do QnA Maker

## <a name="integrate-a-power-virtual-agent-with-a-knowledge-base"></a>Integrar um Power Virtual Agent a uma base de dados de conhecimento

Os [Power Virtual Agents](https://powervirtualagents.microsoft.com/) permitem que as equipes criem bots avançados com facilidade usando uma interface gráfica orientada sem código e sem a necessidade de cientistas de dados ou desenvolvedores.

Um Power Virtual Agent é criado com uma série de tópicos (áreas de assunto), a fim de responder às perguntas do usuário executando ações. Se uma resposta não puder ser encontrada, um fallback do sistema poderá retornar uma.

Configure o agente para enviar a pergunta para a sua base de dados de conhecimento como parte da ação de um tópico ou como parte do caminho do tópico de **Fallback do Sistema**. Os dois usam o mesmo mecanismo de uma ação para se conectar à base de dados de conhecimento e retornar uma resposta.

## <a name="power-automate-connects-to-generateanswer-action"></a>O Power Automate conecta-se à ação GenerateAnswer

Para conectar o agente à base de dados de conhecimento, use o Power Automate para criar a ação. O Power Automate fornece um **fluxo** de processo, que se conecta à API GenerateAnswer do QnA Maker.

Depois que o **fluxo** é projetado e salvo, ele fica disponível em uma **solução** do Power Automate.  Depois que o fluxo de GenerateAnswer é adicionado a uma solução, use essa solução como uma ação no agente.

## <a name="process-steps-to-connect-an-agent-to-your-knowledge-base"></a>Etapas do processo para conectar um agente à sua base de dados de conhecimento

As etapas a seguir são apresentadas como uma visão geral para ajudar você a entender como as etapas se relacionam com a meta de conectar um Power Virtual Agent a uma base de dados de conhecimento do QnA Maker.

Etapas para uso de um Power Virtual Agent com o QnA Maker:
* No portal do [QnA Maker](https://www.qnamaker.ai/)
    * Crie e publique a base de dados de conhecimento
    * Copie os detalhes da base de dados de conhecimento, incluindo a ID da base de dados de conhecimento, a chave do ponto de extremidade de runtime e o host do ponto de extremidade de runtime.
* No [portal](https://powerva.microsoft.com/) do Power Virtual Agent
    * Crie um tópico do agente
    * Chame uma ação (para o fluxo do Power Automate)
* No portal do [Power Automate](https://us.flow.microsoft.com/)
    * Crie um fluxo com o conector para [GenerateAnswer do QnA Maker](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)
        * Informações publicadas da base de dados de conhecimento do QnA Maker
            * ID da base de dados de conhecimento
            * Host do ponto de extremidade de recurso do QnA Maker
            * Chave do ponto de extremidade de recurso do QnA Maker
        * Entrada – consulta de usuário
        * Saída – resposta da base de dados de conhecimento
    * Criar a solução e adicionar um fluxo
* Voltar ao Power Virtual Agent
    * Selecionar a saída da solução como uma mensagem para o tópico

## <a name="create-and-publish-a-knowledge-base"></a>Criar e publicar uma base de dados de conhecimento

1. Siga o [início rápido](../Quickstarts/create-publish-knowledge-base.md) para criar uma base de dados de conhecimento. Não conclua a última seção para criar um bot. Este tutorial é uma substituição da última seção do início rápido, pois este tutorial usa o Power Virtual Agent para criar um bot, em vez do Bot Framework no início rápido.

    > [!div class="mx-imgBorder"]
    > ![Insira as configurações da base de dados de conhecimento publicadas encontradas na página **Configurações** no portal do [QnA Maker](https://www.qnamaker.ai/).](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    Você precisará dessas informações para a [etapa do Power Automate](#create-power-automate-flow-to-connect-to-your-knowledge-base) para configurar a conexão de GenerateAnswer do QnA Maker.

1. Encontre a chave do ponto de extremidade, o host do ponto de extremidade e a ID da base de dados de conhecimento na página **Configurações** no portal do QnA Maker.

## <a name="create-power-virtual-agent"></a>Criar um Power Virtual Agent

1. [Entre](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409) no Power Virtual Agent com a sua conta de email de trabalho ou de estudante.
1. Se esse for o seu primeiro bot, você deverá estar na **Página Inicial** do agente. Se este não for o seu primeiro Power Virtual Agent, selecione o bot na navegação superior direita e escolha **+ Novo Bot**.

    > [!div class="mx-imgBorder"]
    > ![Insira as configurações da base de dados de conhecimento publicadas encontradas na página **Configurações** no portal do [QnA Maker](https://www.qnamaker.ai/).](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

## <a name="several-topics-are-provided-in-the-bot"></a>Vários tópicos são fornecidos no bot

O agente usa a coleção de tópicos para responder a perguntas na sua área de assunto. Neste tutorial, o agente tem muitos tópicos fornecidos para você, divididos em **Tópicos de usuário** e **Tópicos do sistema**.

> [!div class="mx-imgBorder"]
> ![O agente usa a coleção de tópicos para responder a perguntas na sua área de assunto. Neste tutorial, o agente tem muitos tópicos fornecidos para os **Tópicos de usuário** e os **Tópicos do sistema**.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-power-virtual-agents-system-fallback-topic"></a>Criar um tópico de Fallback do sistema do Power Virtual Agent

Embora o agente possa se conectar à base de dados de conhecimento usando qualquer tópico, este tutorial usa o tópico de **Fallback do sistema**. O tópico de fallback é usado quando o agente não consegue encontrar uma resposta. O agente transmite o texto do usuário para a API GenerateAnswer do QnA Maker, recebe a resposta da base de dados de conhecimento e a exibe novamente ao usuário como uma mensagem.

1. No portal dos [Power Virtual Agents](https://powerva.microsoft.com/#/), no canto superior direito da navegação, selecione a página **Configurações**. O ícone dessa página é a engrenagem. Escolha **Fallback do Sistema**.

    > [!div class="mx-imgBorder"]
    > ![Item de menu do Power Virtual Agent para o Fallback do Sistema](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. Na janela pop-up **Configurações**, escolha **+ Adicionar** para adicionar um tópico de Fallback do Sistema.

    > [!div class="mx-imgBorder"]
    > ![Na janela Configurações, adicione o tópico de fallback.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. Depois que o tópico for adicionado, selecione **Ir para o tópico de Fallback** para criar o tópico de fallback na tela de criação.

    > [!TIP]
    > Se você precisar voltar ao tópico de Fallback, ele ficará disponível na seção **Tópicos**, como parte dos tópicos do **Sistema**.

## <a name="use-authoring-canvas-to-add-an-action"></a>Usar a tela de criação para adicionar uma ação

Use a tela de criação dos Power Virtual Agents para conectar o tópico de Fallback à sua base de dados de conhecimento. O tópico começa com o **texto de usuário não reconhecido**. Adicione uma ação que transmite esse texto ao QnA Maker e, em seguida, mostra a resposta como uma mensagem. A última etapa da exibição de uma resposta será tratada como uma [etapa separada](#add-solutions-flow-to-power-virtual-agent) mais adiante neste tutorial.

Esta seção criará o fluxo de conversa do tópico de fallback.

1. A nova ação de Fallback já pode ter elementos do fluxo de conversa. Exclua o item **Escalar** escolhendo o menu Opções.

    > [!div class="mx-imgBorder"]
    > ![Iniciar uma ação de fallback com frases de gatilho](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fallback-topic-delete-escalate.png)

1. Selecione o conector **+** fluindo da caixa **Mensagem** e, em seguida, escolha **Chamar uma ação**.

    > [!div class="mx-imgBorder"]
    > ![Chamar uma ação](../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png)

1. Escolha **Criar um fluxo**. O processo levará você ao **Power Automate**, outro portal baseado em navegador.

    > [!div class="mx-imgBorder"]
    > ![Chamar uma ação](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)

## <a name="create-power-automate-flow-to-connect-to-your-knowledge-base"></a>Criar um fluxo do Power Automate para se conectar à sua base de dados de conhecimento

O seguinte procedimento cria um fluxo do **Power Automate** que:
* usa o texto de entrada do usuário
* envia-o para o QnA Maker
* atribui a resposta principal do QnA Maker a uma variável
* envia a variável (resposta principal) como a resposta para o agente

1. No **Power Automate**, o **Modelo de Fluxo** é iniciado para você. No item de fluxo dos **Power Virtual Agents**, selecione **Editar** para configurar a variável de entrada proveniente do agente para a base de dados de conhecimento. A variável de entrada baseada em texto é a pergunta de texto enviada pelo usuário do seu agente.

    > [!div class="mx-imgBorder"]
    > ![Configurar a variável de entrada como uma cadeia de texto](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable.png)

1. Adicione uma entrada de texto e dê o nome `InputText` à variável com a descrição `IncomingUserQuestion`. Essa nomenclatura ajuda a distinguir o texto de entrada do texto de saída que você criará posteriormente.

    > [!div class="mx-imgBorder"]
    > ![Adicionar uma entrada de texto e nomear a variável `InputText` com a descrição `UserQuestion`](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable-name-and-description.png)

1. Selecione o conector **+** fluindo da caixa dos **Power Virtual Agents** para inserir uma nova etapa no fluxo (antes dos **Valores retornados do Power Virtual Agent**) e, em seguida, escolha **Adicionar uma ação**.

1. Pesquise `Qna` para localizar as ações do **QnA Maker** e, em seguida, selecione **Gerar resposta**.

    > [!div class="mx-imgBorder"]
    > ![Pesquise `Qna` para localizar as ações do **QnA Maker** e, em seguida, escolha **Gerar resposta**](../media/how-to-integrate-power-virtual-agent/generate-answer-action-selected.png)

    As três (3) configurações de conexão necessárias para o QnA Maker são exibidas na ação e nas configurações de perguntas do Power Virtual Agent.

    > [!div class="mx-imgBorder"]
    > ![As configurações de conexões do QnA Maker são exibidas na ação.](../media/how-to-integrate-power-virtual-agent/generate-answer-knowledge-base-settings.png)

1. Configure a ação com a sua ID da base de dados de conhecimento, o host do ponto de extremidade e a chave do ponto de extremidade. Eles podem ser encontrados na página **Configurações** da base de dados de conhecimento, no portal do QnA Maker.

    > [!div class="mx-imgBorder"]
    > ![Insira as configurações da base de dados de conhecimento publicadas encontradas na página **Configurações** no portal do [QnA Maker](https://www.qnamaker.ai/).](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

1. Para configurar a **Pergunta**, selecione a caixa de texto e, em seguida, o `InputText` na lista.

1. Para inserir uma nova etapa no fluxo, selecione o conector **+** fluindo da caixa de ações **Gerar resposta** e, em seguida, escolha **Adicionar uma ação**.

1. Para adicionar uma variável para capturar o texto de resposta retornado de GenerateAnswer, procure e selecione a ação `Initialize variable`.

    Defina o nome da variável como `OutgoingQnAAnswer` e selecione o tipo como **Cadeia de caracteres**. Não defina o **Valor**.

    > [!div class="mx-imgBorder"]
    > ![Defina o nome da variável como `QnAAnswer` e selecione o tipo como **Cadeia de caracteres**](../media/how-to-integrate-power-virtual-agent/initialize-output-variable-for-qna-answer.png)

1. Para inserir uma nova etapa no fluxo, selecione o conector **+** fluindo da caixa de ações **Inicializar variável** e, em seguida, escolha **Adicionar uma ação**.

1. Para definir a resposta JSON de toda a base de dados de conhecimento para a variável, procure e selecione a ação `Apply to each`. Selecione as `answers`de GenerateAnswer.

1. Para retornar apenas a resposta principal, na mesma caixa **Aplicar a cada**, escolha **Adicionar uma ação**. Procure e selecione **Definir variável**.

    Na caixa **Definir variável**, selecione a caixa de texto de **Nome** e, em seguida, selecione **OutgoingQnAAnswer** na lista.

    Selecione a caixa de texto de **Valor** e, em seguida, escolha **Resposta das Respostas** na lista.

    > [!div class="mx-imgBorder"]
    > ![Definir o nome e o valor da variável ](../media/how-to-integrate-power-virtual-agent/power-automate-flow-apply-to-each-set-variable.png)

1. Para retornar a variável (e o valor), escolha o item de fluxo **Valores retornados do Power Virtual Agent**, depois, **Editar** e, em seguida, **Adicionar uma saída**. Selecione um tipo de saída de **Texto** e, em seguida, insira o **Título** de `FinalAnswer`. Selecione a caixa de texto para o **Valor** e, em seguida, a variável `OutgoingQnAAnswer`.

    > [!div class="mx-imgBorder"]
    > ![Definir o valor retornado](../media/how-to-integrate-power-virtual-agent/power-automate-flow-return-value.png)

1. Escolha **Salvar** para salvar o fluxo.

## <a name="create-solution-and-add-flow"></a>Criar a solução e adicionar um fluxo

Para que o Power Virtual Agent encontre o fluxo e se conecte a ele, o fluxo precisará ser incluído em uma solução do Power Automate.

1. Ainda no portal do Power Automate, escolha **Soluções** do painel de navegação do lado esquerdo.

1. Escolha **+ Nova solução**.

1. Insira um nome de exibição. A lista de soluções inclui todas as soluções da sua organização ou da sua escola. Escolha uma convenção de nomenclatura que ajude você a filtrar apenas as suas soluções, como colocação de um prefixo no email do nome da solução: `jondoe-power-virtual-agent-qnamaker-fallback`.

1. Escolha o editor na lista de opções.

1. Aceite os valores padrão para o nome e a versão.

1. Selecione **Criar** para concluir o processo.

## <a name="add-flow-to-solution"></a>Adicionar um fluxo à solução

1. Na lista de soluções, escolha a solução recém-criada. Ela deverá estar no início da lista. Caso contrário, pesquise seu nome de email, que faz parte do nome da solução.

1. Na solução, selecione **+ Adicionar existente** e, em seguida, **Fluxo** na lista.

1. Encontre o fluxo e, em seguida, escolha **Adicionar** para concluir o processo. Se houver muitos fluxos, observe a coluna **Modificado** para localizar o fluxo mais recente.

## <a name="add-solutions-flow-to-power-virtual-agent"></a>Adicionar um fluxo da solução ao Power Virtual Agent

1. Volte à guia do navegador com o Power Virtual Agent. A tela de criação ainda deverá estar aberta.

1. Selecione o conector **+** na caixa de ação **Mensagem** para inserir uma nova etapa no fluxo e, em seguida, escolha **Chamar uma ação**.

1. Na nova ação, selecione o valor de entrada de **UnrecognizedTriggerPhrase**. Isso transmitirá o texto do agente para o fluxo.

    > [!div class="mx-imgBorder"]
    > ![Na nova ação, selecione o valor de entrada **UnrecognizedTriggerPhrase**.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-select-unrecognized-trigger-phrase.png)

1. Selecione o conector **+** na caixa **Ação** para inserir uma nova etapa no fluxo e, em seguida, escolha **Mostrar uma mensagem**.

1. Insira o texto da mensagem, `Your answer is:`, e escolha `FinalAnswer` como uma variável de contexto usando a função da barra de ferramentas in-loco.

    > [!div class="mx-imgBorder"]
    > ![Insira o texto da mensagem e a `FinalAnswer` do fluxo do Power Automate.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-show-message-final-answer.png)

1. Escolha **Salvar** na barra de ferramentas de contexto para salvar os detalhes da tela de criação do tópico.

A tela final é mostrada abaixo.

> [!div class="mx-imgBorder"]
> ![Tela final do agente](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-power-virtual-agent"></a>Testar o Power Virtual Agent

1. No painel de teste, alterne para **Acompanhamento entre os tópicos**. Isso permite que você inspecione à progressão entre tópicos, bem como em um só tópico.

1. Teste o agente inserindo o texto do usuário na ordem fornecida abaixo. A tela de criação relata as etapas bem-sucedidas com uma marca de seleção verde.

|Ordem de perguntas|Testar perguntas|Finalidade|
|--|--|--|
|1|Olá|Iniciar a conversa|
|2|Horário de funcionamento|Tópico de exemplo – configurado para você sem nenhum trabalho adicional da sua parte.|
|3|Sim|Em resposta a `Did that answer your question?`|
|4|Excelente|Em resposta a `Please rate your experience.`|
|5|Sim|Em resposta a `Can I help with anything else?`|
|6|O que é uma base de dados de conhecimento?|Essa pergunta dispara a ação de fallback, que envia o texto à base de dados de conhecimento para que ela forneça a resposta e, em seguida, a resposta é exibida. |

> [!div class="mx-imgBorder"]
> ![Tela final do agente](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png)

## <a name="publish-your-bot"></a>Publicar seu bot

Para disponibilizar o agente para todos os membros da sua escola ou da sua organização, você precisará publicá-lo.

1. Selecione **Publicar** no painel de navegação à esquerda e, em seguida, escolha **Publicar** na página.

1. Experimente o bot no site de demonstração, fornecido como um link abaixo do botão **Publicar**.

    Uma nova página da Web será aberta com o bot. Faça ao bot a mesma pergunta de teste: `What is a knowledge base?`

    > [!div class="mx-imgBorder"]
    > ![Tela final do agente](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>Compartilhar seu bot

Para compartilhar o site de demonstração, configure-o como um canal.

1. Selecione **Gerenciar** e, em seguida, **Canais** do painel de navegação à esquerda.

1. Selecione **Site de demonstração** na lista de canais.

1. Copie o link e escolha **Salvar**. Cole o link para o site de demonstração em um email para os membros da sua escola ou da sua organização.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar de usar a base de dados de conhecimento, remova os recursos do QnA Maker no portal do Azure.

## <a name="next-step"></a>Próxima etapa

[Obter análises na base de dados de conhecimento](../How-To/get-analytics-knowledge-base.md)

Saiba mais sobre:
* [Power Virtual Agents](https://docs.microsoft.com/power-virtual-agents/)
* [Power Automate](https://docs.microsoft.com/power-automate/)
* [Conector do QnA Maker](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/) e as [configurações do conector](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)