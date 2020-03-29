---
title: Editar uma base de dados de conhecimento – QnA Maker
description: O QnA Maker permite que você gerencie o conteúdo de sua base de dados de conhecimento, fornecendo uma experiência de edição fácil de usar.
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 223ad3a607adc20bbe608598da9742d56788b2c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131648"
---
# <a name="edit-qna-sets-in-your-knowledge-base"></a>Editar conjuntos de QnA em sua base de conhecimento

O QnA Maker permite que você gerencie o conteúdo de sua base de dados de conhecimento, fornecendo uma experiência de edição fácil de usar.

Os conjuntos de QnA são adicionados a partir de uma fonte de dados, como um arquivo ou URL, ou adicionados como uma fonte editorial. Uma fonte editorial indica que o conjunto QnA foi adicionado manualmente no portal QnA. Todos os conjuntos de QnA estão disponíveis para edição.

## <a name="add-an-editorial-qna-set"></a>Adicione um conjunto de QnA editorial
1. Faça login no [portal QnA](https://www.qnamaker.ai/)e selecione a base de conhecimento para adicionar o QnA definido.
1. Na página **EDIT** da base de conhecimento, selecione **Adicionar qnA definido** para adicionar um novo conjunto de QnA.

1. Na nova linha de set qnA, adicione os campos de **perguntas** e **respostas** necessários. Os outros campos são opcionais. Todos os campos podem ser alterados a qualquer momento.

1. Opcionalmente, adicione **frases alternativas**. Frasealternativa é qualquer forma de pergunta que seja significativamente diferente da pergunta original, mas que deva fornecer a mesma resposta.

    Quando sua base de conhecimento é publicada e você tem o aprendizado ativo ligado, o QnA Maker coleta escolhas alternativas de frases para você aceitar. Essas escolhas são selecionadas para aumentar a precisão da previsão.

1. Opcionalmente, adicione **metadados**. Para exibir metadados, **selecione Exibir opções** no menu de contexto. Os Metadados fornecem filtros para as respostas que o aplicativo cliente, como um bot de bate-papo, fornece.

1. Opcionalmente, adicione **instruções de acompanhamento**. As solicitações de acompanhamento fornecem caminhos de conversa adicionais para o aplicativo cliente para apresentar ao usuário.

1. Selecione **Salvar e treinar** para ver previsões, incluindo o novo conjunto de QnA.

## <a name="edit-a-qna-set"></a>Editar um conjunto de QnA

Qualquer campo em qualquer conjunto de QnA pode ser editado, independentemente da fonte de dados original. Alguns campos podem não estar visíveis devido às configurações atuais **de opções de exibição,** encontradas na barra de ferramentas de contexto.

## <a name="delete-a-qna-set"></a>Exclua um conjunto de QnA

Para excluir um QnA, clique no ícone **excluir** à direita da linha QnA. Essa é uma operação permanente. Não pode ser desfeito. Considere exportar seu KB da página **Publicar** antes de excluir conjuntos.

![Excluir conjunto QnA](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-set-id"></a>Encontre o ID conjunto QnA

Se você precisar encontrar o ID conjunto qnA, você pode encontrá-lo em dois lugares:

* Passar o tempo no ícone de exclusão na linha de set QnA que você está interessado. O texto do hover inclui o conjunto QnA ID.
* Exporte a base de conhecimento. Cada QnA definido na base de conhecimento inclui o ID conjunto QnA.

## <a name="add-alternate-questions"></a>Adicionar perguntas alternativas

Adicione perguntas alternativas a um conjunto de QnA existente para melhorar a probabilidade de uma correspondência a uma consulta de usuário.

![Adicionar Perguntas Alternativas](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-sets"></a>Vinculando conjuntos QnA

A vinculação de conjuntos QnA é fornecida com [instruções de acompanhamento](multiturn-conversation.md). Esta é uma conexão lógica entre conjuntos qnA, gerenciado no nível de base de conhecimento. Você pode editar solicitações de acompanhamento no portal QnA Maker.

Você não pode vincular conjuntos de QnA nos metadados da resposta.

## <a name="add-metadata"></a>Adicionar metadados

Adicionar conjuntos de metadados selecionando primeiro **opções de exibição**e, em seguida, selecionando **Mostrar metadados**. Isso exibe a coluna de metadados. Em seguida, **+** selecione o sinal para adicionar um conjunto de metadados. Este conjunto consiste em uma chave e um valor.

## <a name="save-changes-to-the-qna-sets"></a>Salvar alterações nos conjuntos de QnA

Selecione periodicamente **Salvar e treinar** depois de fazer edições para evitar perder alterações.

![Adicionar Metadados](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Colaborar em uma base de dados de conhecimento](./collaborate-knowledge-base.md)

* [Gerenciar os recursos do Azure usados pelo QnA Maker](set-up-qnamaker-service-azure.md)