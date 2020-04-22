---
title: 'Tutorial: Integração ao Power Virtual Agents – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, aprimore a qualidade da sua base de dados de conhecimento com o aprendizado ativo. Examine, aceite, rejeite ou adicione sem remover ou alterar as perguntas existentes.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 03/11/2020
ms.author: diberry
ms.openlocfilehash: 4557dee995c8a01067f7e6ad0e79bb7115b6ecdb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402822"
---
# <a name="tutorial-add-your-knowledge-base-to-power-virtual-agents"></a>Tutorial: Adicionar sua base de dados de conhecimento ao Power Virtual Agents
Crie e estenda um bot do [Power Virtual Agents](https://powervirtualagents.microsoft.com/) para fornecer respostas da sua base de dados de conhecimento.

Neste tutorial, você aprenderá como:

<!-- green checkmark -->
> [!div class="checklist"]
> * Criar um bot do Power Virtual Agents
> * Criar um tópico de fallback do sistema
> * Adicionar o QnA Maker como uma ação para um tópico como um fluxo do Power Automate
> * Criar uma solução do Power Automate
> * Adicionar um fluxo do Power Automate à sua solução
> * Publicar Power Virtual Agents
> * Testar o Power Virtual Agents e receber uma resposta da sua base de dados de conhecimento do QnA Maker

## <a name="integrate-an-agent-with-a-knowledge-base"></a>Integrar um agente a uma base de dados de conhecimento

O [Power Virtual Agents](https://powervirtualagents.microsoft.com/) permite que as equipes criem bots avançados usando uma interface gráfica orientada e sem código. Você não precisa de cientistas de dados ou desenvolvedores.

No Power Virtual Agents, você cria um agente com uma série de tópicos (áreas de assunto), a fim de responder às perguntas do usuário por meio da execução de ações. Se uma resposta não puder ser encontrada, um fallback do sistema poderá retornar uma.

Configure o agente para enviar a pergunta para a sua base de dados de conhecimento como parte da ação de um tópico ou como parte do caminho do tópico de *Fallback do Sistema*. Os dois usam uma ação para se conectar à base de dados de conhecimento e retornar uma resposta.

## <a name="power-automate-connects-to-generateanswer-action"></a>O Power Automate conecta-se a ação `GenerateAnswer`

Para conectar o agente à base de dados de conhecimento, use o Power Automate para criar a ação. O Power Automate fornece um fluxo de processo, que se conecta à API `GenerateAnswer` do QnA Maker.

Depois de projetar e salvar o fluxo, ele estará disponível em uma solução do Power Automate. Use essa solução como uma ação em seu agente.

## <a name="connect-an-agent-to-your-knowledge-base"></a>Conectar um agente à sua base de dados de conhecimento

Aqui está uma visão geral das etapas para conectar um agente do Power Virtual Agents a uma base de dados de conhecimento no QnA Maker.

* No portal do [QnA Maker](https://www.qnamaker.ai/):
    * Crie e publique sua base de dados de conhecimento.
    * Copie os detalhes da base de dados de conhecimento, incluindo a ID, a chave do ponto de extremidade de runtime e o host do ponto de extremidade de runtime.
* No portal do [Power Virtual Agents](https://powerva.microsoft.com/):
    * Crie um tópico de agente.
    * Chame uma ação (para o fluxo do Power Automate).
* No portal do [Power Automate](https://us.flow.microsoft.com/):
    * Crie um fluxo com um conector para [GenerateAnswer do QnA Maker](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/).
        * Informações publicadas da base de dados de conhecimento do QnA Maker:
            * ID da base de dados de conhecimento
            * Host do ponto de extremidade de recurso do QnA Maker
            * Chave do ponto de extremidade de recurso do QnA Maker
        * Entrada – consulta de usuário
        * Saída – resposta da base de dados de conhecimento
    * Crie uma solução e adicione o fluxo.
* Volte para o Power Virtual Agents:
    * Selecione a saída da solução como uma mensagem para um tópico.

## <a name="create-and-publish-a-knowledge-base"></a>Criar e publicar uma base de dados de conhecimento

1. Siga o [início rápido](../Quickstarts/create-publish-knowledge-base.md) para criar uma base de dados de conhecimento. Não conclua a última seção, sobre a criação de um bot. Em vez disso, use este tutorial para criar um bot com o Power Virtual Agents.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela das configurações da base de dados de conhecimento publicada](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    Insira as configurações da base de dados de conhecimento publicadas encontradas na página **Configurações** no portal do [QnA Maker](https://www.qnamaker.ai/). Você precisará dessas informações para a [etapa do Power Automate](#create-a-power-automate-flow-to-connect-to-your-knowledge-base) para configurar a conexão `GenerateAnswer` do QnA Maker.

1. No portal do QnA Maker, na página **Configurações**, encontre a chave do ponto de extremidade, o host do ponto de extremidade e a ID da base de dados de conhecimento.

## <a name="create-an-agent-in-power-virtual-agents"></a>Criar um agente no Power Virtual Agents

1. [Entre no Power Virtual Agents](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409). Use seu email corporativo ou de estudante.
1. Se esse for o seu primeiro bot, você estará na **Página Inicial** do agente. Se esse não for seu primeiro bot, selecione o bot na área superior direita da página e selecione **+ Novo Bot**.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela da página inicial do Power Virtual Agents](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

1. Insira as configurações da base de dados de conhecimento publicadas, encontradas na página **Configurações** no portal do [QnA Maker](https://www.qnamaker.ai/).

## <a name="topics-provided-in-the-bot"></a>Tópicos fornecidos no bot

O agente usa a coleção de tópicos para responder a perguntas na sua área de assunto. Neste tutorial, o agente tem muitos tópicos fornecidos para você, divididos em tópicos de usuário e tópicos do sistema.

> [!div class="mx-imgBorder"]
> ![Captura de tela dos tópicos fornecidos no agente](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-the-system-fallback-topic"></a>Criar o tópico de fallback do sistema

Embora o agente possa se conectar à base de dados de conhecimento usando qualquer tópico, este tutorial usa o tópico de *fallback do sistema*. O tópico de fallback é usado quando o agente não consegue encontrar uma resposta. O agente transmite o texto do usuário para a API `GenerateAnswer` do QnA Maker, recebe a resposta da base de dados de conhecimento e a exibe ao usuário como uma mensagem.

1. No portal do [Power Virtual Agents](https://powerva.microsoft.com/#/), no canto superior direito, selecione **Configurações** (o ícone de engrenagem). Em seguida, selecione **Fallback do Sistema**.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela do item de menu do Power Virtual Agents para o Fallback do Sistema](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. Selecione **+ Adicionar** para adicionar um tópico de fallback do sistema.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela da adição de um tópico de fallback.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. Depois que o tópico for adicionado, selecione **Ir para o tópico de Fallback** para criar o tópico de fallback na tela de criação.

    > [!TIP]
    > Se você precisar voltar ao tópico de fallback, ele ficará disponível na seção **Tópicos**, como parte dos tópicos do **Sistema**.

## <a name="use-the-authoring-canvas-to-add-an-action"></a>Usar a tela de criação para adicionar uma ação

Use a tela de criação do Power Virtual Agents para conectar o tópico de fallback à sua base de dados de conhecimento. O tópico começa com o texto de usuário não reconhecido. Adicione uma ação que transmite esse texto ao QnA Maker e, em seguida, mostra a resposta como uma mensagem. A última etapa da exibição de uma resposta será tratada como uma [etapa separada](#add-your-solutions-flow-to-power-virtual-agents) mais adiante neste tutorial.

Esta seção criará o fluxo de conversa do tópico de fallback.

1. A nova ação de fallback já pode ter elementos do fluxo de conversa. Exclua o item **Escalar** selecionando o menu **Opções**.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela do tópico de fallback do Power Virtual Agents](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fallback-topic-delete-escalate.png)

1. Selecione o conector **+** fluindo da caixa **Mensagem** e, em seguida, escolha **Chamar uma ação**.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela de Chamar uma ação](../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png)

1. Escolha **Criar um fluxo**. O processo leva você ao portal do Power Automate.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela de Criar um fluxo](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)

## <a name="create-a-power-automate-flow-to-connect-to-your-knowledge-base"></a>Criar um fluxo do Power Automate para se conectar à sua base de dados de conhecimento

O seguinte procedimento cria um fluxo do Power Automate que:
* Usa o texto de entrada do usuário e o envia para o QnA Maker.
* Atribui a resposta principal do QnA Maker a uma variável e envia a variável (resposta principal) como a resposta de volta para o agente.

1. No **Power Automate**, o **Modelo de Fluxo** é iniciado para você. No item de fluxo dos **Power Virtual Agents**, selecione **Editar** para configurar a variável de entrada proveniente do agente para a base de dados de conhecimento. A variável de entrada baseada em texto é a pergunta de texto enviada pelo usuário do seu agente.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela da opção do Power Automate para configurar sua variável de entrada como uma cadeia de texto](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable.png)

1. Adicione uma entrada de texto e dê o nome `InputText` à variável com a descrição `IncomingUserQuestion`. Essa nomenclatura ajuda a distinguir o texto de entrada do texto de saída que você criará posteriormente.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela da opção do Power Automate para configurar o nome e a descrição da variável de entrada](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable-name-and-description.png)

1. Selecione o conector **+** fluindo da caixa do **Power Virtual Agents** para inserir uma nova etapa no fluxo (antes de **Retornar valores para o Power Virtual Agent**). Em seguida, selecione **Adicionar uma ação**.

1. Pesquise `Qna` para localizar as ações do **QnA Maker** e, em seguida, selecione **Gerar resposta**.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela de Gerar resposta](../media/how-to-integrate-power-virtual-agent/generate-answer-action-selected.png)

    As configurações de conexão necessárias para o QnA Maker são exibidas na ação e nas configurações de perguntas do agente.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela das configurações de conexão necessárias](../media/how-to-integrate-power-virtual-agent/generate-answer-knowledge-base-settings.png)

1. Configure a ação com a ID da sua base de dados de conhecimento, o host do ponto de extremidade e a chave do ponto de extremidade. Eles podem ser encontrados na página **Configurações** da base de dados de conhecimento, no portal do QnA Maker.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela das configurações da base de dados de conhecimento publicada](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

1. Para configurar a **Pergunta**, selecione a caixa de texto e, em seguida, o `InputText` na lista.

1. Para inserir uma nova etapa no fluxo, selecione o conector **+** fluindo da caixa de ações **Gerar resposta**. Em seguida, selecione **Adicionar uma ação**.

1. Para adicionar uma variável para capturar o texto de resposta retornado de `GenerateAnswer`, pesquise e selecione a ação `Initialize variable`.

    Defina o nome da variável como `OutgoingQnAAnswer` e selecione o tipo como **Cadeia de caracteres**. Não defina o **Valor**.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela da inicialização da variável de saída](../media/how-to-integrate-power-virtual-agent/initialize-output-variable-for-qna-answer.png)

1. Para inserir uma nova etapa no fluxo, selecione o conector **+** fluindo da caixa de ações **Inicializar variável**. Em seguida, selecione **Adicionar uma ação**.

1. Para definir a resposta JSON de toda a base de dados de conhecimento para a variável, procure e selecione a ação `Apply to each`. Selecione o `GenerateAnswer` `answers`.

1. Para retornar apenas a resposta principal, na mesma caixa **Aplicar a cada**, escolha **Adicionar uma ação**. Procure e selecione **Definir variável**.

    Na caixa **Definir variável**, selecione a caixa de texto de **Nome** e, em seguida, selecione **OutgoingQnAAnswer** na lista.

    Selecione a caixa de texto de **Valor** e, em seguida, escolha **Resposta das Respostas** na lista.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela da definição do nome e do valor da variável](../media/how-to-integrate-power-virtual-agent/power-automate-flow-apply-to-each-set-variable.png)

1. Para retornar a variável (e o valor), escolha o item de fluxo **Retornar valores para o Power Virtual Agent**. Em seguida, selecione **Editar** > **Adicionar uma saída**. Selecione um tipo de saída de **Texto** e, em seguida, insira o **Título** de `FinalAnswer`. Selecione a caixa de texto para o **Valor** e a variável `OutgoingQnAAnswer`.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela da definição do valor retornado](../media/how-to-integrate-power-virtual-agent/power-automate-flow-return-value.png)

1. Selecione **Salvar** para salvar o fluxo.

## <a name="create-a-solution-and-add-the-flow"></a>Criar uma solução e adicionar o fluxo

Para que o agente encontre o fluxo e se conecte ao fluxo, ele precisará ser incluído em uma solução do Power Automate.

1. Ainda no portal do Power Automate, escolha **Soluções** do painel de navegação do lado esquerdo.

1. Escolha **+ Nova solução**.

1. Insira um nome de exibição. A lista de soluções inclui todas as soluções da sua organização ou da sua escola. Escolha uma convenção de nomenclatura que ajude a filtrar apenas suas soluções. Por exemplo, você pode usar seu email como prefixo do nome da solução: `jondoe-power-virtual-agent-qnamaker-fallback`.

1. Escolha o editor na lista de opções.

1. Aceite os valores padrão para o nome e a versão.

1. Selecione **Criar** para concluir o processo.

## <a name="add-your-flow-to-the-solution"></a>Adicionar seu fluxo à solução

1. Na lista de soluções, escolha a solução recém-criada. Ela deverá estar no início da lista. Caso contrário, pesquise seu nome de email, que faz parte do nome da solução.

1. Na solução, selecione **+ Adicionar existente** e, em seguida, selecione **Fluxo** na lista.

1. Encontre o fluxo e, em seguida, escolha **Adicionar** para concluir o processo. Se houver muitos fluxos, observe a coluna **Modificado** para localizar o fluxo mais recente.

## <a name="add-your-solutions-flow-to-power-virtual-agents"></a>Adicionar o fluxo da sua solução ao Power Virtual Agents

1. Volte à guia do navegador com seu agente no Power Virtual Agents. A tela de criação ainda deverá estar aberta.

1. Para inserir uma nova etapa no fluxo, na caixa de ação **Mensagem**, selecione o conector **+** . Em seguida, selecione **Chamar uma ação**.

1. Na nova ação, selecione o valor de entrada de **UnrecognizedTriggerPhrase**. Isso transmitirá o texto do agente para o fluxo.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela da opção do Power Virtual Agents para selecionar frase de gatilho não reconhecido](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-select-unrecognized-trigger-phrase.png)

1. Para inserir uma nova etapa no fluxo, na caixa **Ação**, selecione o conector **+** . Em seguida, selecione **Mostrar uma mensagem**.

1. Insira o texto da mensagem, `Your answer is:`. Selecione `FinalAnswer` como uma variável de contexto usando a função da barra de ferramentas in-loco.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela da opção do Power Virtual Agents para inserir o texto da mensagem](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-show-message-final-answer.png)

1. Na barra de ferramentas de contexto, selecione **Salvar** para salvar os detalhes da tela de criação do tópico.

Veja a aparência final da tela do agente.

> [!div class="mx-imgBorder"]
> ![Captura de tela da tela final do agente](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-the-agent"></a>Testar o agente

1. No painel de teste, alterne para **Acompanhamento entre os tópicos**. Isso permite que você inspecione à progressão entre tópicos, bem como dentro de um só tópico.

1. Teste o agente inserindo o texto do usuário na ordem a seguir. A tela de criação relata as etapas bem-sucedidas com uma marca de seleção verde.

    |Ordem de perguntas|Testar perguntas|Finalidade|
    |--|--|--|
    |1|Olá|Iniciar a conversa|
    |2|Horário de funcionamento|Tópico de exemplo. É configurado para você sem nenhum trabalho adicional da sua parte.|
    |3|Sim|Em resposta a `Did that answer your question?`|
    |4|Excelente|Em resposta a `Please rate your experience.`|
    |5|Sim|Em resposta a `Can I help with anything else?`|
    |6|O que é uma base de dados de conhecimento?|Essa pergunta dispara a ação de fallback, que envia o texto à base de dados de conhecimento para que ela forneça a resposta. Em seguida, a resposta é mostrada. |

> [!div class="mx-imgBorder"]
> ![Captura de tela da tela final do agente](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png)

## <a name="publish-your-bot"></a>Publicar seu bot

Para disponibilizar o agente para todos os membros da sua escola ou da sua organização, você precisará publicá-lo.

1. Na barra de navegação à esquerda, selecione **Publicar**. Em seguida, selecione **Publicar** na página.

1. Experimente o bot no site de demonstração (procure o link em **Publicar**).

    Uma nova página da Web será aberta com o bot. Faça ao bot a mesma pergunta de teste: `What is a knowledge base?`

    > [!div class="mx-imgBorder"]
    > ![Captura de tela da tela final do agente](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>Compartilhar seu bot

Para compartilhar o site de demonstração, configure-o como um canal.

1. Na barra de navegação à esquerda, selecione **Gerenciar** > **Canais**.

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