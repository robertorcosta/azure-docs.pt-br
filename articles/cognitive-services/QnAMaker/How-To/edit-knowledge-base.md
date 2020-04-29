---
title: Editar uma base de dados de conhecimento – QnA Maker
description: O QnA Maker permite que você gerencie o conteúdo de sua base de dados de conhecimento, fornecendo uma experiência de edição fácil de usar.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3cf5dcedd4d2cfa82fda002b71abca1e5bea4b18
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80756725"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>Editar pares de QnA na sua base de dados de conhecimento

O QnA Maker permite que você gerencie o conteúdo de sua base de dados de conhecimento, fornecendo uma experiência de edição fácil de usar.

Os pares de QnA são adicionados de uma fonte de arquivos, como um arquivo ou uma URL, ou adicionados como uma fonte editorial. Uma fonte editorial indica que o par QnA foi adicionado manualmente no portal do QnA. Todos os pares de QnA estão disponíveis para edição.

## <a name="add-an-editorial-qna-pair"></a>Adicionar um par editorial QnA
1. Entre no portal do [QnA](https://www.qnamaker.ai/)e selecione a base de dados de conhecimento para adicionar o par QnA.
1. Na página **Editar** da base de dados de conhecimento, selecione **Adicionar par de QnA** para adicionar um novo par de QnA.

1. Na linha novo par QnA, adicione os campos de **pergunta** e **resposta** necessários. Os outros campos são opcionais. Todos os campos podem ser alterados a qualquer momento.

1. Opcionalmente, adicione **frases alternativas**. A formulação alternativa é qualquer forma da pergunta que seja significativamente diferente da pergunta original, mas deve fornecer a mesma resposta.

    Quando sua base de dados de conhecimento é publicada e você tem o [aprendizado ativo](use-active-learning.md) ativado, QnA Maker coleta opções de frases alternativas para aceitar. Essas opções são selecionadas para aumentar a precisão da previsão.

1. Opcionalmente, adicione **metadados**. Para exibir metadados, selecione **Opções de exibição** no menu de contexto. Os metadados fornecem filtros às respostas que o aplicativo cliente, como um bate-papo, fornece.

1. Opcionalmente, adicione **avisos de acompanhamento**. Os prompts de acompanhamento fornecem caminhos de conversa adicionais ao aplicativo cliente para apresentar ao usuário.

1. Selecione **salvar e treinar** para ver previsões, incluindo o novo par QnA.

## <a name="edit-a-qna-pair"></a>Editar um par de QnA

Qualquer campo em qualquer par de QnA pode ser editado, independentemente da fonte de dados original. Alguns campos podem não estar visíveis devido às configurações de **Opções de exibição** atuais, encontradas na barra de ferramentas de contexto.

## <a name="delete-a-qna-pair"></a>Excluir um par de QnA

Para excluir um QnA, clique no ícone **excluir** à direita da linha QnA. Essa é uma operação permanente. Não pode ser desfeito. Considere exportar a KB da página de **publicação** antes de excluir os conjuntos.

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

Adicione conjuntos de metadados selecionando primeiro **as opções de exibição**e, em seguida, selecionando **Mostrar metadados**. Isso exibe a coluna de metadados. Em seguida, selecione **+** o sinal para adicionar um conjunto de metadados. Esse conjunto consiste em uma chave e um valor.

## <a name="save-changes-to-the-qna-pairs"></a>Salvar alterações nos pares de QnA

Selecione periodicamente **salvar e treinar** depois de fazer edições para evitar a perda de alterações.

![Adicionar Metadados](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Colaborar em uma base de dados de conhecimento](./collaborate-knowledge-base.md)

* [Gerenciar recursos do Azure usados pelo QnA Maker](set-up-qnamaker-service-azure.md)