---
title: Conversas multi-volta - QnA Maker
description: Use prompts e contexto para gerenciar as várias voltas, conhecidas como multi-turn, para o seu bot de uma pergunta para outra. Multi-turn é a capacidade de ter uma conversa de ida e volta onde o contexto da pergunta anterior influencia a próxima pergunta e resposta.
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 8ef244e1b6baf480189bb90ea5ff53138a6f377a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261458"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Usar avisos de acompanhamento para criar várias rodadas de uma conversa

Use prompts de acompanhamento e contexto para gerenciar as várias voltas, conhecidas como _multi-turn,_ para o seu bot de uma pergunta para outra.

Para ver como funciona o multiturno, veja o seguinte vídeo de demonstração:

[![Conversa multi-volta no QnA Maker](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>O que é uma conversa de várias voltas?

Algumas perguntas não podem ser respondidas em um único turno. Quando você projeta conversas de aplicativo de cliente (chat bot), um usuário pode fazer uma pergunta que precisa ser filtrada ou refinada para determinar a resposta correta. Você faz esse fluxo através das perguntas possíveis, apresentando ao usuário *instruções de acompanhamento*.

Quando um usuário faz uma pergunta, o QnA Maker retorna a resposta _e_ quaisquer solicitações de acompanhamento. Esta resposta permite que você apresente as perguntas de acompanhamento como escolhas.

> [!CAUTION]
> Os prompts de vários turnos não são extraídos de documentos faq. Se você precisar de extração de vários turnos, remova os pontos de interrogação que designam os pares de QnA como PERGUNTAS FREQUENTES.

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Exemplo de conversa multi-turno com o bot de bate-papo

Com o multi-turn, um bot de bate-papo gerencia uma conversa com um usuário para determinar a resposta final, conforme mostrado na imagem a seguir:

![Uma caixa de diálogo de várias voltas com solicitações que guiam um usuário através de uma conversa](../media/conversational-context/conversation-in-bot.png)

Na imagem anterior, um usuário iniciou uma conversa inserindo **Minha conta**. A base de conhecimento tem três pares de perguntas e respostas ligados. Para refinar a resposta, o usuário seleciona uma das três opções na base de conhecimento. A pergunta (#1), tem três prompts de acompanhamento, que são apresentados no bot de bate-papo como três opções (#2).

Quando o usuário seleciona uma opção (#3), a próxima lista de opções de refino (#4) é apresentada. Esta seqüência continua (#5) até que o usuário determine a resposta final correta (#6).


### <a name="use-multi-turn-in-a-bot"></a>Use multi-turn em um bot

Depois de publicar seu KB, você pode selecionar o botão **Criar bot** para implantar seu bot QnA Maker no serviço de bot szure. Os prompts serão exibidos nos clientes de bate-papo que você habilitou para o seu bot.

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Crie uma conversa de vários turnos a partir da estrutura de um documento

Quando você cria uma base de conhecimento, a seção **Populate your KB** exibe uma **extração de várias voltas de URLs, .pdf ou .docx files** seleção.

![Caixa de seleção para habilitar extração de várias voltas](../media/conversational-context/enable-multi-turn.png)

Quando você seleciona essa opção, o QnA Maker extrai a hierarquia presente na estrutura do documento. A hierarquia é convertida para acompanhar os prompts e a raiz da hierarquia serve como qnA pai. Em alguns documentos a raiz da hierarquia não tem conteúdo que poderia servir como resposta, você pode fornecer o 'Texto de Resposta Padrão' para ser usado como um texto de resposta substituto para extrair tais hierarquias.

A estrutura de vários turnos só pode ser inferida a partir de ARQUIVOS URLs, arquivos PDF ou arquivos DOCX. Para um exemplo de estrutura, visualize uma imagem de um [arquivo PDF manual do usuário do Microsoft Surface](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf).

![! [Exemplo de estrutura em um manual do usuário] (.. /mídia/contexto de conversação/importação-arquivo-com-estrutura conversacional.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

### <a name="building-your-own-multi-turn-document"></a>Construindo seu próprio documento multiturno

Se você estiver criando um documento de vários turnos, tenha em mente as seguintes diretrizes:

* Use títulos e subtítulos para denotar hierarquia. Por exemplo, você pode h1 para denotar o QnA pai e h2 para denotar o QnA que deve ser tomado como solicitação. Use o tamanho da posição pequena para denotar a hierarquia subseqüente. Não use estilo, cor ou algum outro mecanismo para implicar estrutura em seu documento, o QnA Maker não extrairá as solicitações de várias voltas.

* O primeiro caractere da posição deve ser capitalizado.

* Não termine um título com `?`um ponto de interrogação, .

* Você pode usar o [documento de exemplo](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx) como exemplo para criar seu próprio documento multiturno.

### <a name="adding-files-to-a-multi-turn-kb"></a>Adicionando arquivos a um KB de vários turnos

Quando você adiciona um documento hierárquico, o QnA Maker determina solicitações de acompanhamento da estrutura para criar fluxo de conversação.

1. No QnA Maker, selecione uma base de conhecimento existente que foi criada com **a extração de várias voltas de URLs, .pdf ou .docx.** Habilitado.
1. Vá para a página **Configurações,** selecione o arquivo ou URL para adicionar.
1. **Salve e treine** a base de conhecimento.

> [!Caution]
> O suporte para o uso de um arquivo base de conhecimento multiturno TSV ou XLS exportado como fonte de dados para uma base de conhecimento nova ou vazia não é suportado. Você precisa **importar** esse tipo de arquivo, a partir da página **Configurações** do portal QnA Maker, a fim de adicionar solicitações multi-turno exportadas a uma base de conhecimento.


## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Crie base de conhecimento com prompts de multiturno com a API Criar

Você pode criar um case de conhecimento com solicitações de vários turnos usando a [API QnA Maker Create](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create). Os prompts estão `context` adicionando `prompts` na matriz da propriedade.

## <a name="show-questions-and-answers-with-context"></a>Mostrar perguntas e respostas com contexto

Reduza os pares de perguntas e respostas exibidos apenas àqueles com conversas contextuais.

Selecione **'Exibir opções'** e, em seguida, **selecione Mostrar contexto**. A lista exibe pares de perguntas e respostas que contêm solicitações de acompanhamento.

![Filtrar pares de perguntas e respostas por conversas contextuais](../media/conversational-context/filter-question-and-answers-by-context.png)

O contexto de várias voltas é exibido na primeira coluna.

![! [A coluna "Contexto (PREVIEW)" (.. /mídia/contexto de conversação/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

Na imagem anterior, **#1** indica texto em negrito na coluna, o que significa a pergunta atual. A pergunta dos pais é o item principal da linha. Todas as perguntas abaixo são os pares de perguntas e respostas vinculados. Esses itens são selecionáveis, para que você possa ir imediatamente para os outros itens de contexto.

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Adicione um par de perguntas e respostas existentes como um prompt de acompanhamento

A pergunta original, **Minha conta,** tem solicitações de acompanhamento, como **Contas e login.**

![As respostas "Contas e login" e solicitações de acompanhamento](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Adicione um prompt de acompanhamento a um par de perguntas e respostas existentes que não está vinculado no momento. Como a pergunta não está vinculada a nenhum par de perguntas e respostas, a configuração de exibição atual precisa ser alterada.

1. Para vincular um par de perguntas e respostas existentes como um prompt de acompanhamento, selecione a linha para o par de perguntas e respostas. Para o manual surface, procure **sair** para reduzir a lista.
1. Na linha de **inscrição ,** na coluna **Responder,** selecione **Adicionar solicitação de acompanhamento**.
1. Nos campos na janela **pop-up prompt follow-up,** digite os seguintes valores:

    |Campo|Valor|
    |--|--|
    |Exibir texto|Digite **Desligue o dispositivo**. Este é um texto personalizado para exibir no prompt de seguimento.|
    |Somente contexto| Selecione esta caixa de seleção. Uma resposta só é devolvida se a pergunta especificar o contexto.|
    |Link para responder|Digite **Use a tela de login** para encontrar o par de perguntas e respostas existentes.|


1.  Uma partida é devolvida. Selecione esta resposta como o seguimento e, em seguida, **selecione Salvar**.

    ![A página "Follow-up prompt (PREVIEW)"](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Depois de adicionar o prompt de seguimento, **selecione Salvar e treinar** na navegação superior.

### <a name="edit-the-display-text"></a>Editar o texto de exibição

Quando um prompt de acompanhamento é criado e um par de perguntas e respostas existentes é inserido como o **Link to Answer,** você pode inserir um novo **texto de exibição**. Este texto não substitui a pergunta existente, e não adiciona uma nova pergunta alternativa. É separado desses valores.

1. Para editar o texto de exibição, pesquise e selecione a pergunta no campo **Contexto.**
1. Na linha para essa pergunta, selecione o prompt de acompanhamento na coluna de resposta.
1. Selecione o texto de exibição que deseja editar e, em seguida, **selecione Editar**.

    ![O comando Editar para o texto de exibição](../media/conversational-context/edit-existing-display-text.png)

1. Na janela **pop-up prompt follow-up,** altere o texto de exibição existente.
1. Quando terminar de editar o texto de exibição, **selecione Salvar**.
1. Na barra de navegação superior, **salve e treine**.


## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Adicione um novo par de perguntas e respostas como um prompt de acompanhamento

Quando você adiciona um novo par de perguntas e respostas à base de conhecimento, cada par deve ser vinculado a uma pergunta existente como um prompt de acompanhamento.

1. Na barra de ferramentas da base de conhecimento, procure e selecione o par de perguntas e respostas existentes para **Contas e faça login.**

1. Na coluna **Responder** para esta pergunta, selecione **Adicionar solicitação de acompanhamento**.
1. Em **'Preview' prompt (Preview) 'Criar**um novo prompt de acompanhamento inserindo os seguintes valores:

    |Campo|Valor|
    |--|--|
    |Exibir texto|*Crie uma conta do Windows*. O texto personalizado a ser exibido no prompt de seguimento.|
    |Somente contexto|Selecione esta caixa de seleção. Esta resposta só é devolvida se a pergunta especificar o contexto.|
    |Link para responder|Digite o seguinte texto como resposta:<br>* [Crie](https://account.microsoft.com/) uma conta do Windows com uma conta de e-mail nova ou existente.*<br>Quando você salvar e treinar o banco de dados, este texto será convertido. |
    |||

    ![Crie uma nova pergunta e resposta imediata](../media/conversational-context/create-child-prompt-from-parent.png)


1. Selecione **Criar novo**e, em seguida, **selecione Salvar**.

    Essa ação cria um novo par de perguntas e respostas e vincula a pergunta selecionada como um prompt de acompanhamento. A coluna **Contexto,** para ambas as questões, indica uma relação imediata de acompanhamento.

1. Selecione **'Exibir opções'** e, em seguida, [**selecione Mostrar contexto (PREVIEW)**](#show-questions-and-answers-with-context).

    A nova pergunta mostra como está ligada.

    ![Crie um novo prompt de acompanhamento](../media/conversational-context/new-qna-follow-up-prompt.png)

    A pergunta dos pais mostra uma nova pergunta como uma de suas escolhas.

    ![! [A coluna Contexto, para ambas as perguntas, indica uma relação rápida de acompanhamento] (.. /mídia/contexto de conversação/criança-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Depois de adicionar o prompt de seguimento, **selecione Salvar e treinar** na barra de navegação superior.

<a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>

## <a name="view-multi-turn-during-testing-of-follow-up-prompts"></a>Exibir multi-turno durante o teste de solicitações de acompanhamento

Quando você testa a pergunta com instruções de seguimento no painel **teste,** a resposta inclui as solicitações de acompanhamento.

![A resposta inclui os prompts de acompanhamento](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>Um pedido JSON para retornar uma resposta inicial e solicitações de acompanhamento

Use o `context` objeto vazio para solicitar a resposta à pergunta do usuário e incluir solicitações de acompanhamento.

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Uma resposta JSON para retornar uma resposta inicial e solicitações de acompanhamento

A seção anterior solicitou uma resposta e quaisquer solicitações de acompanhamento para **Contas e login.** A resposta inclui as informações imediatas, que estão localizadas em *answers[0].context*, e o texto a ser exibido ao usuário.

```JSON
{
    "answers": [
        {
            "questions": [
                "Accounts and signing in"
            ],
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way he or she likes. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 100.0,
            "id": 15,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start, and right-click your name. Then select Sign out. ",
            "score": 38.01,
            "id": 18,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Turn off the device"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 27.53,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

A `prompts` matriz fornece `displayText` texto na `qnaId` propriedade e no valor. Você pode mostrar essas respostas como as próximas escolhas exibidas no fluxo de conversação e, em seguida, enviar o selecionado `qnaId` de volta para O Criador de QnA na seguinte solicitação.

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Uma solicitação JSON para retornar uma resposta não inicial e solicitações de acompanhamento

Preencha `context` o objeto para incluir o contexto anterior.

Na seguinte solicitação json, a pergunta atual é *Usar o Windows Hello para fazer login* e a pergunta anterior era contas e *login.*

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "qnaId": 17,
  "context": {
    "previousQnAId": 15,
    "previousUserQuery": "accounts and signing in"
  }
}
```

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Uma resposta JSON para retornar uma resposta não inicial e solicitações de acompanhamento

A resposta QnA Maker _GenerateAnswer_ JSON inclui as `context` solicitações de acompanhamento na `answers` propriedade do primeiro item no objeto:

```JSON
{
    "answers": [
        {
            "questions": [
                "Use Windows Hello to sign in"
            ],
            "answer": "**Use Windows Hello to sign in**\n\nSince Surface Pro 4 has an infrared (IR) camera, you can set up Windows Hello to sign in just by looking at the screen. \n\nIf you have the Surface Pro 4 Type Cover with Fingerprint ID (sold separately), you can set up your Surface sign you in with a touch. \n\nFor more info, see What is Windows Hello? on Windows.com. ",
            "score": 100.0,
            "id": 17,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Meet Surface Pro 4"
            ],
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere's a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you're ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3" display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth&reg; \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
            "score": 21.92,
            "id": 3,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 4,
                        "qna": null,
                        "displayText": "Ports and connectors"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 19.04,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>Consultar a base de conhecimento com o QnA Maker ID

Se você estiver construindo um aplicativo personalizado usando recurso de multi-turn. Na resposta da pergunta inicial, qualquer solicitação de `qnaId` acompanhamento e sua associada é devolvida. Agora que você tem o ID, você pode passar isso no corpo de solicitação do prompt de acompanhamento. Se o corpo `qnaId`de solicitação contiver o , e o objeto de contexto (que contém as propriedades anteriores do QnA Maker), então o GenerateAnswer retornará a pergunta exata por ID, em vez de usar o algoritmo de classificação para encontrar a resposta pelo texto da pergunta.


## <a name="display-order-is-supported-in-the-update-api"></a>A ordem de exibição é suportada na API atualizar

O [texto de exibição e a ordem de exibição](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), retornado na resposta JSON, é suportado para edição pela [API Update](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update).

## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Adicionar ou excluir solicitações de vários turnos com a API atualizar

Você pode adicionar ou excluir solicitações de vários turnos usando a [API De atualização do fabricante de qna](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update).  Os prompts estão `context` adicionando `promptsToAdd` na matriz `promptsToDelete` da propriedade e na matriz.

## <a name="export-knowledge-base-for-version-control"></a>Base de conhecimento de exportação para controle de versão

O QnA Maker suporta o controle de versão, incluindo etapas de conversação de vários turnos no arquivo exportado.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre conversas contextuais a partir desta amostra de [diálogo](https://aka.ms/qnamakermultiturnsample) ou saiba mais sobre [o design conceitual do bot para conversas em vários turnos](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Migrar uma base de dados de conhecimento](../Tutorials/migrate-knowledge-base.md)
