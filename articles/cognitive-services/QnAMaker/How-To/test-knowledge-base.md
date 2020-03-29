---
title: Como testar uma base de dados de conhecimento – QnA Maker
description: Testar a base de conhecimento do QnA Maker é uma parte importante de um processo iterativo para melhorar a precisão das respostas que estão sendo retornadas. É possível testar a base de dados de conhecimento por meio de uma interface de chat avançada que também permite fazer edições.
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: da4988ced0b077952ce64e6227d16e58d40ae329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927272"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>Teste sua base de conhecimento no QnA Maker

Testar a base de conhecimento do QnA Maker é uma parte importante de um processo iterativo para melhorar a precisão das respostas que estão sendo retornadas. É possível testar a base de dados de conhecimento por meio de uma interface de chat avançada que também permite fazer edições.

## <a name="interactively-test-in-qna-maker-portal"></a>Teste interativo no portal QnA Maker

1. Acesse a base de dados de conhecimento, selecionando o nome na página **Minhas bases de dados de conhecimento**.
1. Para acessar o painel deslizante de Teste, selecione **Testar** no painel superior do aplicativo.
1. Insira uma consulta na caixa de texto e selecione Enter.
1. A resposta da melhor correspondência da base de dados de conhecimento é retornada como a resposta.

### <a name="clear-test-panel"></a>Limpar painel de teste

Para limpar todas as consultas de teste inseridas e seus resultados do console de teste, selecione **Iniciar de novo** no canto superior esquerdo do Painel de teste.

### <a name="close-test-panel"></a>Fechar painel de teste

Para fechar o painel de **Teste**, selecione o botão Testar novamente. Enquanto o painel de Teste estiver aberto, não será possível editar o conteúdo da Base de Dados de Conhecimento.

### <a name="inspect-score"></a>Inspecionar pontuação

Inspecione detalhes do resultado do teste no painel Inspecionar.

1.  Com o painel deslizante de Teste aberto, selecione **Inspecionar** para obter mais detalhes sobre essa resposta.

    ![Inspecione as respostas](../media/qnamaker-how-to-test-kb/inspect.png)

2.  O painel de Inspeção é exibido. O painel inclui a principal intenção de pontuação e as entidades identificadas. O painel mostra o resultado da declaração selecionada.

### <a name="correct-the-top-scoring-answer"></a>Corrigir a resposta de pontuação máxima

Se a resposta de pontuação mais alta estiver incorreta, selecione a resposta correta na lista e selecione **Salvar e Treinar**.

![Corrigir a resposta de pontuação máxima](../media/qnamaker-how-to-test-kb/choose-answer.png)

### <a name="add-alternate-questions"></a>Adicionar perguntas alternativas

É possível adicionar formas alternativas de uma pergunta a uma determinada resposta. Digite as respostas alternativas na caixa de texto e clique em entrar para adicioná-las. Selecione **Salvar e Treinar** para armazenar as atualizações.

![Adicionar perguntas alternativas](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Adicionar uma nova resposta

Você poderá adicionar uma nova resposta, se alguma das respostas existentes que foram correspondidas estiver incorreta ou a resposta não existir na base de dados de conhecimento (nenhuma boa correspondência encontrada na base de dados de conhecimento).

Na parte inferior da lista de respostas, use a caixa de texto para inserir uma nova resposta e pressione enter para adicioná-la.

Selecione **Salvar e Treinar** para persistir essa resposta. Um novo par de perguntas e respostas foi adicionado à base de dados de conhecimento.

> [!NOTE]
> Todas as edições da base de dados de conhecimento somente serão salvas quando você pressionar o botão **Salvar e Treinar**.

### <a name="test-the-published-knowledge-base"></a>Teste a base de conhecimento publicada

Você pode testar a versão publicada da base de conhecimento no painel de teste. Depois de publicar o KB, selecione a caixa **KB publicada** e envie uma consulta para obter resultados do KB publicado.

![Teste contra um KB publicado](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="batch-test-with-tool"></a>Teste em lote com ferramenta

Use a ferramenta de teste em lote quando quiser:

* determinar a resposta superior e pontuação para um conjunto de perguntas
* validar resposta esperada para conjunto de perguntas

Leia o [tutorial](../Quickstarts/batch-testing.md) de teste em lote para obter instruções passo a passo.

O teste em lote é fornecido com a ferramenta de teste em lote. Esta ferramenta está disponível como um [executável fechado](https://aka.ms/qnamakerbatchtestingtool) para download ou como [código fonte C#](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting).

[A documentação de referência sobre a ferramenta](../reference-tsv-format-batch-testing.md) inclui:

* o exemplo de linha de comando da ferramenta
* o formato para arquivos de entrada e arquivo tsv

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Publicar uma base de dados de conhecimento](./publish-knowledge-base.md)
