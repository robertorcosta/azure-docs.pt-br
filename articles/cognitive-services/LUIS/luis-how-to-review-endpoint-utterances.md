---
title: Revisar declarações de usuários - LUIS
titleSuffix: Azure Cognitive Services
description: Revisar expressões capturadas pelo aprendizado ativo para selecionar intenções e marcar entidades para declarações de mundo de leitura; aceitar mudanças, treinar e publicar.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: diberry
ms.openlocfilehash: 95b7c7446a47fafd26d00b0da4d880786340fcd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219847"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>Como melhorar o aplicativo LUIS revisando declarações de ponto final

O processo de revisão de declarações de ponto final para previsões corretas é chamado [de aprendizagem ativa](luis-concept-review-endpoint-utterances.md). O aprendizado ativo captura consultas de ponto final e seleciona as declarações de ponto final do usuário das quais ele não tem certeza. Você revisa essas declarações para selecionar as intenções e marcar entidades para essas declarações de mundo de leitura. Aceite essas alterações em suas expressões de exemplo e depois treine e publique. LUIS então identifica expressões com mais precisão.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="enable-active-learning"></a>Habilite o aprendizado ativo

Para habilitar o aprendizado ativo, você deve registrar consultas de usuário. Isso é feito chamando a consulta `log=true` de ponto [final](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) com o parâmetro e o valor de consulta.

Use o portal LUIS para construir a consulta de ponto final correto.

1. No [portal DE pré-visualização LUIS](https://preview.luis.ai/), selecione seu aplicativo na lista de aplicativos.
1. Vá para a seção **Gerenciar** e selecione **os recursos do Azure**.
1. Para o recurso de previsão atribuído, selecione **Alterar parâmetros de consulta**.

    > [!div class="mx-imgBorder"]
    > ![Use o portal LUIS para salvar logs, que é necessário para o aprendizado ativo.](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. Alternar **salvar logs** e salvar selecionando **Feito**.

    > [!div class="mx-imgBorder"]
    > ![Use o portal LUIS para salvar logs, que é necessário para o aprendizado ativo.](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     Essa ação altera a URL de exemplo adicionando o parâmetro querystring `log=true`. Copie e use a URL de consulta de exemplo alterada ao fazer consultas de previsão para o ponto de extremidade do runtime.

## <a name="correct-intent-predictions-to-align-utterances"></a>Corrigir previsões de intenções para alinhar expressões

Cada declaração tem uma intenção sugerida exibida na coluna **Intenção alinhada**.

> [!div class="mx-imgBorder"]
> [![Reveja as declarações de ponto final que LUIS não tem certeza](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

Se você concordar com essa intenção, selecione a marca de verificação. Se você não concordar com a sugestão, selecione a intenção correta na lista suspensa de intenções alinhadas, e marque a caixa de seleção à direita da intenção alinhada. Depois de selecionar na marca de seleção, a enunciada é movida para a intenção e removida da lista **Desporto de Revisão Enunciados.**

> [!TIP]
> É importante ir à página de detalhes da Intenção para revisar e corrigir as previsões da entidade de todas as declarações de exemplo da lista **Desporto de Revisão.**

## <a name="delete-utterance"></a>Excluir a declaração

Cada declaração pode ser excluída da lista de revisão. Depois de excluída, ela não aparecerá na lista novamente. Isso será verdadeiro mesmo se o usuário inserir a mesma declaração do ponto de extremidade.

Se você não tiver certeza se deve excluir o enunciado, mova-o `miscellaneous` para a intenção De Nenhum ou crie uma nova intenção, como e mova a expressão para essa intenção.

## <a name="disable-active-learning"></a>Desativar o aprendizado ativo

Para desativar o aprendizado ativo, não registre consultas de usuário. Isso é feito definindo a consulta `log=false` de ponto [final](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) com o parâmetro e o valor de consulta string ou não usando o valor de consulta porque o valor padrão é falso.

## <a name="next-steps"></a>Próximas etapas

Para testar como o desempenho melhora depois que você rotula as declarações sugeridas, você poderá acessar o console de teste selecionando **Teste** no painel superior. Para obter instruções sobre como testar seu aplicativo usando o console de teste, consulte [Treinar e testar seu aplicativo](luis-interactive-test.md).
