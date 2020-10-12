---
title: Editar uma base de dados de conhecimento – QnA Maker
description: O QnA Maker permite que você gerencie o conteúdo de sua base de dados de conhecimento, fornecendo uma experiência de edição fácil de usar.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 3940f060209ea5ba55957a3673020656d45de9e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776537"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>Editar pares de QnA na sua base de dados de conhecimento

O QnA Maker permite que você gerencie o conteúdo de sua base de dados de conhecimento, fornecendo uma experiência de edição fácil de usar.

Os pares de QnA são adicionados de uma fonte de arquivos, como um arquivo ou uma URL, ou adicionados como uma fonte editorial. Uma fonte editorial indica que o par QnA foi adicionado manualmente no portal do QnA. Todos os pares de QnA estão disponíveis para edição.

<a name="add-an-editorial-qna-set"></a>

## <a name="add-an-editorial-qna-pair"></a>Adicionar um par editorial QnA

1. Entre no portal do [QnA](https://www.qnamaker.ai/)e selecione a base de dados de conhecimento para adicionar o par QnA.
1. Na página **Editar** da base de dados de conhecimento, selecione **Adicionar par de QnA** para adicionar um novo par de QnA.

    > [!div class="mx-imgBorder"]
    > ![Adicionar par QnA](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

1. Na linha novo par QnA, adicione os campos de pergunta e resposta necessários. Os outros campos são opcionais. Todos os campos podem ser alterados a qualquer momento.

1. Opcionalmente, adicione **[frases alternativas](../Quickstarts/add-question-metadata-portal.md#add-additional-alternatively-phrased-questions)**. A formulação alternativa é qualquer forma da pergunta que seja significativamente diferente da pergunta original, mas deve fornecer a mesma resposta.

    Quando sua base de dados de conhecimento é publicada e você tem o [aprendizado ativo](use-active-learning.md) ativado, QnA Maker coleta opções de frases alternativas para aceitar. Essas opções são selecionadas para aumentar a precisão da previsão.

1. Opcionalmente, adicione **[metadados](../Quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)**. Para exibir metadados, selecione **Opções de exibição** no menu de contexto. Os metadados fornecem filtros às respostas que o aplicativo cliente, como um bate-papo, fornece.

1. Opcionalmente, adicione **[avisos de acompanhamento](multiturn-conversation.md)**. Os prompts de acompanhamento fornecem caminhos de conversa adicionais ao aplicativo cliente para apresentar ao usuário.

1. Selecione **salvar e treinar** para ver previsões, incluindo o novo par QnA.

## <a name="rich-text-editing-for-answer"></a>Edição de texto completo para resposta

A edição de texto completo do seu texto de resposta oferece uma redução do estilo de uma barra de ferramentas simples.

1. Selecione a área de texto para uma resposta, a barra de ferramentas do editor de Rich-Text é exibida na linha do par QnA.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela do editor de Rich-Text com a pergunta e a resposta de uma linha de par de QnA.](../media/qnamaker-how-to-edit-kb/rich-text-control-qna-pair-row.png)

    Qualquer texto que já esteja na resposta será exibido corretamente, pois o usuário o verá de um bot.

1. Edite o texto. Selecione recursos de formatação na barra de ferramentas de edição de Rich Text ou use o recurso de alternância para alternar para a sintaxe de redução.

    > [!div class="mx-imgBorder"]
    > ![Use o editor de Rich-Text para escrever e formatar texto e salvar como redução.](../media/qnamaker-how-to-edit-kb/rich-text-display-image.png)

    |Recursos do editor de Rich-Text|Atalho de teclado|
    |--|--|
    |Alterne entre o editor de Rich-Text e a redução. `</>`|CTRL+M|
    |Aplique. **B**|CTR + LB|
    |Itálico, indicado com um I em itálico ** _I_**|Ctrl+I|
    |Lista não ordenada||
    |Lista ordenada||
    |Estilo de parágrafo||
    |Imagem – adicione uma imagem disponível de uma URL pública.|CTRL+G|
    |Adicionar link à URL disponível publicamente.|CTRL+K|
    |Emoticon – adicionar de uma seleção de emoticons.|CTRL+E|
    |Menu avançado – desfazer|CTRL+Z|
    |Menu avançado – refazer|CTRL+Y|

1. Adicione uma imagem à resposta usando o ícone de imagem na barra de ferramentas de Rich Text. O editor in-loco precisa da URL da imagem publicamente acessível e do texto alternativo para a imagem.


    > [!div class="mx-imgBorder"]
    > ![Captura de tela mostra o editor in-loco com a URL da imagem publicamente acessível e texto alternativo para a imagem inserida.](../media/qnamaker-how-to-edit-kb/add-image-url-alternate-text.png)

1. Adicione um link a uma URL selecionando o texto na resposta, selecionando o ícone de link na barra de ferramentas ou selecionando o ícone vincular na barra de ferramentas e, em seguida, inserindo o novo texto e a URL.

    > [!div class="mx-imgBorder"]
    > ![Use o editor de Rich-Text para adicionar uma imagem publicamente acessível e seu texto ALT.](../media/qnamaker-how-to-edit-kb/add-link-to-answer-rich-text-editor.png)

## <a name="edit-a-qna-pair"></a>Editar um par de QnA

Qualquer campo em qualquer par de QnA pode ser editado, independentemente da fonte de dados original. Alguns campos podem não estar visíveis devido às configurações de **Opções de exibição** atuais, encontradas na barra de ferramentas de contexto.

## <a name="delete-a-qna-pair"></a>Excluir um par de QnA

Para excluir um QnA, clique no ícone **excluir** à direita da linha QnA. Essa é uma operação permanente. Não pode ser desfeito. Considere exportar seu KB da página **publicar** antes de excluir pares.

![Excluir par de QnA](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>Localizar a ID do par de QnA

Se você precisar localizar a ID do par QnA, poderá encontrá-la em dois locais:

* Focalize o ícone de exclusão na linha do par de QnA em que você está interessado. O texto em foco inclui a ID do par QnA.
* Exporte a base de dados de conhecimento. Cada par de QnA na base de dados de conhecimento inclui a ID de par de QnA.

## <a name="add-alternate-questions"></a>Adicionar perguntas alternativas

Adicione perguntas alternativas para um par de QnA existente para melhorar a probabilidade de uma correspondência para uma consulta de usuário.

![Adicionar Perguntas Alternativas](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>Vinculando pares de QnA

A vinculação de pares de QnA é fornecida com [prompts de acompanhamento](multiturn-conversation.md). Essa é uma conexão lógica entre pares de QnA, gerenciada no nível da base de dados de conhecimento. Você pode editar os prompts de acompanhamento no portal de QnA Maker.

Não é possível vincular pares de QnA nos metadados da resposta.

## <a name="add-metadata"></a>Adicionar metadados

Adicione pares de metadados selecionando primeiro **as opções de exibição**e, em seguida, selecionando **Mostrar metadados**. Isso exibe a coluna de metadados. Em seguida, selecione o **+** sinal para adicionar um par de metadados. Esse par consiste em uma chave e um valor.

Saiba mais sobre metadados no início rápido do portal QnA Maker para obter os metadados:
* [Criação – adicionar metadados ao par de pergunta e resposta](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [Previsão de consulta – filtrar respostas por metadados](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

## <a name="save-changes-to-the-qna-pairs"></a>Salvar alterações nos pares de QnA

Selecione periodicamente **salvar e treinar** depois de fazer edições para evitar a perda de alterações.

![Adicionar Metadados](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>Quando usar edição de Rich Text versus redução

A edição de respostas em [Rich Text](#add-an-editorial-qna-set) permite que você, como autor, use uma barra de ferramentas de formatação para selecionar e formatar rapidamente o texto.

A [redução](../reference-markdown-format.md) é uma ferramenta melhor quando você precisa gerar o conteúdo automaticamente para criar bases de dados de conhecimento a serem importadas como parte de um pipeline de CI/CD ou para [testes em lotes](../Quickstarts/batch-testing.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Colaborar em uma base de dados de conhecimento](./collaborate-knowledge-base.md)

* [Gerenciar recursos do Azure usados pelo QnA Maker](set-up-qnamaker-service-azure.md)
