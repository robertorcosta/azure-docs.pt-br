---
title: Como testar uma base de dados de conhecimento – QnA Maker
titleSuffix: Azure Cognitive Services
description: Testar a base de conhecimento do QnA Maker é uma parte importante de um processo iterativo para melhorar a precisão das respostas que estão sendo retornadas. É possível testar a base de dados de conhecimento por meio de uma interface de chat avançada que também permite fazer edições.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 64b4b9a4ad5ceb0b3c33ae022b34daeafda93a62
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491214"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>Teste sua base de dados de conhecimento no QnA Maker

Testar a base de conhecimento do QnA Maker é uma parte importante de um processo iterativo para melhorar a precisão das respostas que estão sendo retornadas. É possível testar a base de dados de conhecimento por meio de uma interface de chat avançada que também permite fazer edições.

## <a name="interactively-test-in-qna-maker-portal"></a>Teste interativamente no portal QnA Maker

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

Na parte inferior da lista de respostas, use a caixa de texto para inserir uma nova resposta e pressione ENTER para adicioná-la. 

Selecione **Salvar e Treinar** para persistir essa resposta. Um novo par de perguntas e respostas foi adicionado à base de dados de conhecimento. 

> [!NOTE]
> Todas as edições da base de dados de conhecimento somente serão salvas quando você pressionar o botão **Salvar e Treinar**.

### <a name="test-the-published-knowledge-base"></a>Testar a base de dados de conhecimento publicada

Você pode testar a versão publicada da base de dados de conhecimento no painel de teste. Depois de publicar a KB, selecione a caixa **KB publicados** e envie uma consulta para obter os resultados da KB publicada.

![Teste em relação a uma base de conhecimento publicada](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="batch-test-with-tool"></a>Teste em lote com a ferramenta

Use a ferramenta de teste do lote quando desejar:

* determinar a resposta principal e a pontuação de um conjunto de perguntas
* validar a resposta esperada para o conjunto de perguntas

Os testes em lote são fornecidos com a ferramenta de teste em lote. Essa ferramenta está disponível como um [executável compactado](https://qnamakerstore.blob.core.windows.net/qnamakerdata/batchtesting/bt.zip) para download ou como [ C# código-fonte](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting). 

[A documentação de referência sobre a ferramenta](../reference-tsv-format-batch-testing.md) inclui:

* o exemplo de linha de comando da ferramenta
* o formato para arquivos de entrada TSV e de outfile 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Publicar uma base de dados de conhecimento](./publish-knowledge-base.md)
