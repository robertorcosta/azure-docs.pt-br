---
title: Recursos-LUIS
titleSuffix: Azure Cognitive Services
description: Use o LUIS (Serviço Inteligente de Reconhecimento Vocal) para adicionar recursos de aplicativos que podem melhorar a detecção ou previsão de intenções e entidades que as categorias e padrões
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: diberry
ms.openlocfilehash: 12445ec5b14f4c274e471bf1b061a3b221664d20
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592297"
---
# <a name="use-features-to-boost-signal-of-word-list"></a>Usar recursos para impulsionar o sinal da lista de palavras

É possível adicionar recursos ao aplicativo de LUIS para melhorar a precisão. Os recursos ajudam o LUIS fornecendo dicas de que certas palavras e frases fazem parte de um vocabulário do domínio do aplicativo.

Examine os [conceitos](luis-concept-feature.md) para entender quando e por que usar um recurso.

## <a name="add-phrase-list-as-a-feature"></a>Adicionar lista de frases como um recurso

1. Entre no portal do [Luis](https://www.luis.ai)e selecione sua **assinatura** e recurso de **criação** para ver os aplicativos atribuídos a esse recurso de criação.
1. Abra seu aplicativo selecionando seu nome na página **meus aplicativos** .
1. Selecione **Compilar**e, em seguida, selecione **recursos** no painel esquerdo do seu aplicativo.

1. Na página **recursos** , selecione **+ criar**.

1. Na caixa de diálogo **criar novo recurso de lista de frases** , insira um nome como `Cities` . Na caixa **valor** , insira exemplos das cidades, como `Seattle` . É possível digitar um valor por vez ou um conjunto de valores separados por vírgulas e, em seguida, pressionar **Enter**.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela da adição de cidades de recurso (lista de frases)](./media/luis-add-features/add-phrase-list-cities.png)

    Depois de inserir valores suficientes para LUIS, serão exibidas sugestões. Você pode **+ adicionar todos** os valores propostos ou selecionar termos individuais.

1. Mantenha **esses valores intercambiáveis** verificados se as frases podem ser usadas de forma intercambiável.

1. A lista de frases pode ser aplicada a todo o aplicativo com a configuração **global** ou a um modelo específico (intenção ou entidade). Se você criar a lista de frases, como um _recurso_ de uma intenção ou entidade, a alternância não será definida para global. Nesse caso, o significado da alternância é que o recurso é local somente para esse modelo, portanto, _não global_ para o aplicativo.

1. Selecione **Concluído**. O novo recurso é adicionado à página de **recursos do ml** .

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Você pode excluir ou desativar uma lista de frases da barra de ferramentas contextual na página de **recursos do ml** .

## <a name="next-steps"></a>Próximas etapas

Depois de adicionar, editar, excluir ou desativar um recurso, [treine e teste o aplicativo](luis-interactive-test.md) novamente para ver se o desempenho melhora.
