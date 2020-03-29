---
title: Criar um novo aplicativo - LUIS
titleSuffix: Azure Cognitive Services
description: Criar e gerenciar seus aplicativos na página da Web do Serviço Inteligente de Reconhecimento Vocal (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: d3f8696388a33a8ea112aae438c6bbe9af520c61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220826"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Criar um aplicativo LUIS no portal do LUIS
Há algumas maneiras de criar um aplicativo do LUIS. Você pode criar um aplicativo do LUIS no portal do LUIS ou por meio das [APIs](developer-reference-resource.md) de criação do LUIS.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="using-the-luis-portal"></a>Usando o portal do LUIS

Você pode criar um novo aplicativo no portal de visualização de várias maneiras:

* Inicie com um aplicativo vazio e crie intenções, declarações e entidades.
* Inicie com um aplicativo vazio e adicione um [domínio predefinido](luis-how-to-use-prebuilt-domains.md).
* Importe um aplicativo `.lu` `.json` LUIS de um ou arquivo que já contenha intenções, declarações e entidades.

## <a name="using-the-authoring-apis"></a>Usando as APIs de criação
Você pode criar um aplicativo com as APIs de criação de várias maneiras:

* [Adicionar aplicativo](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) - comece com um aplicativo vazio e crie intenções, declarações e entidades.
* [Adicionar aplicativo pré-construído](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5) - comece com um domínio pré-construído, incluindo intenções, declarações e entidades.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>Criar aplicativo no LUIS

1. Na página **Meus aplicativos,** selecione sua assinatura e o recurso de criação em seguida **+ Criar**. Se você estiver usando a chave de avaliação gratuita, aprenda a [criar um recurso de criação](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal).

    ![Lista de aplicativos LUIS](./media/create-app-in-portal.png)


1. Na caixa de diálogo, digite o `Pizza Tutorial`nome do seu aplicativo, como .

    ![Caixa de diálogo Criar novo aplicativo](./media/create-pizza-tutorial-app-in-portal.png)

1. Escolha sua cultura de aplicativos e, em seguida, selecione **Feito**. A descrição e o recurso de previsão são opcionais neste momento. Você pode definir em seguida, a qualquer momento na seção **Gerenciar** do portal.

    > [!NOTE]
    > A cultura não poderá ser alterada depois que o aplicativo for criado. 

    Depois que o aplicativo é criado, o portal `None` LUIS mostra a lista **intenções** com a intenção já criada para você. Agora você tem um aplicativo vazio. 
    
    > [!div class="mx-imgBorder"]
    > ![Lista de intenções com nenhuma intenção criada sem enunciados de exemplo.](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available"></a>Outras ações disponíveis

A barra de ferramentas de contexto fornece outras ações:

* Renomear aplicativo
* Importar de `.lu` arquivo usando ou`.json`
* Aplicativo de `.lu` exportação como `.json`(para [LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)), ou `.zip` (para contêiner [LUIS](luis-container-howto.md))
* Importar logs de ponto final de contêiner, para revisar as declarações de ponto final
* Registros de ponto final `.csv`de exportação, como a , para análise off-line
* Excluir aplicativo

## <a name="next-steps"></a>Próximas etapas

Se o design do aplicativo incluir detecção de intenções, [crie novas intenções](luis-how-to-add-intents.md)e adicione enunciados de exemplo. Se o design do aplicativo for apenas extração de dados, adicione expressões de exemplo à intenção De Nenhum e, em seguida, [crie entidades](luis-how-to-add-example-utterances.md)e rotule as expressões de exemplo com essas entidades. 
