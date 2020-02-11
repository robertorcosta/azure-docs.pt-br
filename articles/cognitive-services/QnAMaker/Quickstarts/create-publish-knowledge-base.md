---
title: 'Início Rápido: Criar, treinar e publicar base de dados de conhecimento – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Este início rápido mostra como criar uma KB (base de dados de conhecimento) do QnA Maker com base em conteúdo próprio, como perguntas frequentes ou manuais de produto. A base de dados de conhecimento do QnA Maker é criada com base em uma página da Web simples de perguntas frequentes, a fim de solucionar dúvidas sobre a recuperação da chave do BitLocker.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 01/29/2020
ms.author: diberry
ms.openlocfilehash: a3bdc118be96630ebcf3bf63a2948976dc9b4261
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901683"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Início Rápido: Criar, treinar e publicar sua base de dados de conhecimento do QnA Maker

É possível criar uma KB (base de dados de conhecimento) do QnA Maker com base no seu próprio conteúdo, como perguntas frequentes ou manuais de produto. Este artigo inclui um exemplo de como criar uma base de dados de conhecimento do QnA Maker com base em uma página da Web simples de perguntas frequentes para responder perguntas sobre a recuperação da chave do BitLocker.

Inclua uma personalidade de bate-papo para tornar suas informações mais interessantes para seus usuários.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisite"></a>Pré-requisito

> [!div class="checklist"]
> * Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-new-qna-maker-knowledge-base"></a>Criar uma base de dados de conhecimento do QnA Maker

1. Entre no portal [QnAMaker.ai](https://QnAMaker.ai) com suas credenciais do Azure.

1. No portal do QnA Maker, selecione **Criar uma base de dados de conhecimento**.

1. Na página **Criar**, selecione **Criar um serviço de QnA**. Você será direcionado para o [portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) para configurar o serviço do QnA Maker na sua assinatura.

1. No portal do Azure, crie o recurso. Lembre-se de sua ID e assinatura do Azure Active Directory, bem como do nome do recurso do QnA que você selecionou ao criar o recurso.
1. Retorne ao portal do QnA Maker e atualize a página da Web no portal para continuar criando sua base de dados de conhecimento. Selecione um locatário existente, uma assinatura e o novo recurso. Selecione o idioma. Esse será o idioma usado para todas as bases de dados de conhecimento nesse serviço QnA Maker.

   ![Captura de tela da seleção de uma base de dados de conhecimento do serviço do QnA Maker](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. Dê à sua base de dados de conhecimento o nome **Minha base de dados de QnA de exemplo**.

1. Adicione um documento do Word de exemplo como uma URL:

    `https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`

1. Selecione `+ Add URL`.

1. Adicione o Bate-Papo **_Professional_** à sua KB.

1. Selecione **Criar sua KB**.

    O processo de extração leva alguns minutos para ler o documento e identificar perguntas e respostas.

    Depois que o QnA Maker criar com êxito a base de dados de conhecimento, a página **Base de dados de conhecimento** será aberta. Você pode editar o conteúdo da base de dados de conhecimento nesta página.

## <a name="add-a-new-question-and-answer-set"></a>Adicionar um novo par de pergunta e resposta

1. No portal do QnA Maker, na página **Editar**, selecione **+ Adicionar par de P e R** na barra de ferramentas de contexto.
1. Adicione a seguinte pergunta:

    `How many Azure services are used by a knowledge base?`

1. Adicione a resposta formatada com _Markdown_:

    ` * Azure QnA Maker service\n* Azure Cognitive Search\n* Azure web app\n* Azure app plan`

    ![ Adicione a pergunta como texto e a resposta formatada com Markdown.](../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png)

    O símbolo de Markdown, `*`, é usado para marcadores. O `\n` é usado para uma nova linha.

    A página **Editar** mostra o Markdown. Quando você usar o painel **Testar** posteriormente, verá o Markdown exibido corretamente.

## <a name="save-and-train"></a>Salvar e treinar

Na parte superior direito, selecione **Salvar e treinar** para salvar suas edições e treinar o modelo do QnA Maker. As edições não serão mantidas se não forem salvas.

## <a name="test-the-knowledge-base"></a>Testar a base de dados de conhecimento

1. No portal do QnA Maker, selecione **Testar** para verificar se as alterações entraram em vigor.
1. Insira um exemplo de consulta de usuário na caixa de texto.

    `How many Azure services are used by a knowledge base?`

    ![ Insira um exemplo de consulta de usuário na caixa de texto. ](../media/qnamaker-create-publish-knowledge-base/test-panel-in-qna-maker.png)

1. Selecione **Inspecionar** para examinar a resposta mais detalhadamente. A janela de teste é usada para testar as alterações na base de dados de conhecimento antes que ela seja publicada.

1. Selecione **Testar** novamente para fechar o painel **Testar**.

## <a name="publish-the-knowledge-base"></a>Publicar a base de dados de conhecimento

Ao publicar uma base de dados de conhecimento, o conteúdo da base de dados de conhecimento é movido do índice `test` para um índice `prod` no Azure Search.

![Captura de tela da movimentação do conteúdo de sua base de dados de conhecimento](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. No portal do QnA Maker, selecione **Publicar**. Para confirmar, selecione **Publicar** na página.

    O serviço do QnA Maker foi publicado com êxito. É possível usar o ponto de extremidade no aplicativo ou código de bot.

    ![Captura de tela da publicação bem-sucedida](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Criar um bot

Após a publicação, é possível criar um bot na página **Publicar**:

* Você pode criar vários bots rapidamente, todos apontando para a mesma base de dados conhecimento para diferentes regiões ou planos de preço para os bots individuais.
* Se você quiser apenas um bot para a base de dados de conhecimento, use o link **Exibir todos os bots no portal do Azure** para exibir uma lista de seus bots atuais.

Quando você faz alterações na base de dados de conhecimento e republica, não é necessário fazer mais nada com o bot. Ele já está configurado para trabalhar com a base de dados de conhecimento e trabalha com todas as alterações futuras na base de dados de conhecimento. Sempre que você publica uma base de dados de conhecimento, todos os bots conectados a ela são atualizados automaticamente.

1. No portal do QnA Maker, na página **Publicar**, selecione **Criar bot**. Esse botão será exibido somente depois que você tiver publicado a base de dados de conhecimento.

    ![Captura de tela da criação de um bot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. É aberta uma nova guia do navegador para o portal do Azure com a página de criação do Serviço de Bot do Azure. Configure o serviço de bot do Azure. O bot e o QnA Maker podem compartilhar o plano do serviço de aplicativo Web, mas não podem compartilhar o aplicativo Web. Isso significa que o **nome do aplicativo** para o bot deve ser diferente do nome do aplicativo usado para o serviço do QnA Maker.

    * **Deve ser feito**
        * Alterar o identificador de bot se ele não for exclusivo.
        * Selecionar a linguagem de programação do SDK. Depois que o bot for criado, você poderá baixar o código em seu ambiente de desenvolvimento local e continuar o processo de desenvolvimento.
    * **Não deve ser feito**
        * Alterar as configurações a seguir no portal do Azure ao criar o bot. Elas são preenchidas previamente para sua base de dados de conhecimento existente:
           * Chave de autenticação do QnA
           * Plano e local do serviço de aplicativo


1. Depois que o bot for criado, abra o recurso **Serviço de bot**.
1. Em **Gerenciamento de Bot**, selecione **Testar no Webchat**.
1. No prompt de chat de **Digite sua mensagem**, digite:

    `Azure services?`

    O chatbot responde com uma resposta de sua base de dados de conhecimento.

    ![Insira uma consulta de usuário no Webchat de teste.](../media/qnamaker-create-publish-knowledge-base/test-web-chat.png)

## <a name="what-did-you-accomplish"></a>O que você realizou?

Você criou uma base de dados de conhecimento, adicionou uma URL pública à base de dados de conhecimento, adicionou seu conjunto do QnA, treinou, testou e publicou a base de dados de conhecimento.

Depois de publicar a base de dados de conhecimento, você criou e testou um bot.

Isso foi realizado em alguns minutos, sem a necessidade de escrever nenhum código nem de limpar o conteúdo.

## <a name="clean-up-resources"></a>Limpar os recursos

Limpe os recursos do QnA Maker e do Bot Framework no portal do Azure.

## <a name="next-steps"></a>Próximas etapas

Para mais informações:

* [Formato de Markdown nas respostas](../reference-markdown-format.md)
* [Fontes de dados](../concepts/knowledge-base.md) do QnA Maker.
* [Definições de configuração do recurso de bot](../tutorials/create-qna-bot.md).

> [!div class="nextstepaction"]
> [Adicionar perguntas com metadados](add-question-metadata-portal.md)
