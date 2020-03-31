---
title: Criar sua primeira função no portal do Azure
description: Aprenda a criar sua primeira Função do Azure para a execução sem servidor usando o Portal do Azure.
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.topic: quickstart
ms.date: 03/06/2020
ms.custom: mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: e7bb5e7b387c3ab1140a3fe475911bd0e428e2a5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80057166"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Criar sua primeira função no portal do Azure

O Azure Functions lhe permite executar seu código em um ambiente sem servidor sem que seja preciso primeiro criar uma VM (máquina virtual) ou publicar um aplicativo Web. Neste artigo, você aprende a usar o Azure Functions para criar a função disparada por HTTP "Olá, Mundo" no portal do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Se você é um desenvolvedor de C#, considere [criar sua primeira função no Visual Studio 2019](functions-create-your-first-function-visual-studio.md) em vez de usar o portal. 

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com) com sua conta do Azure.

## <a name="create-a-function-app"></a>Criar um aplicativo de funções

Você deve ter um aplicativo de funções para hospedar a execução de suas funções. Um aplicativo de funções lhe permite agrupar funções como uma unidade lógica para facilitar o gerenciamento, a implantação, o dimensionamento e o compartilhamento de recursos.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Em seguida, crie uma função no novo aplicativo de funções.

## <a name="create-an-http-triggered-function"></a><a name="create-function"></a>Criar uma função disparada por HTTP

1. Expanda seu novo aplicativo de funções e selecione o botão **+** ao lado de **Funções**, escolha **No portal** e, em seguida, selecione **Continuar**.

    ![Início rápido de funções para escolher uma plataforma.](./media/functions-create-first-azure-function/function-app-quickstart-choose-portal.png)

1. Escolha **WebHook + API** e, em seguida, selecione **Criar**.

    ![Início rápido de funções no Portal do Azure.](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

   Uma função é criada usando um modelo específico a um idioma para uma função disparada por HTTP.

Agora você pode executar a nova função enviando uma solicitação HTTP.

## <a name="test-the-function"></a>Testar a função

1. Em sua nova função, selecione **</> Obter URL da função** no canto superior direito. 

1. Na caixa de diálogo **Obter URL da função**, selecione **padrão (chave de função)** na lista suspensa e, em seguida, selecione **Copiar**. 

    ![Copiar a URL da função do Portal do Azure](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Cole a URL de função na barra de endereços do navegador. Adicione o valor da cadeia de caracteres de consulta `&name=<your_name>` ao final desta URL e pressione ENTER para executar a solicitação. 

    O exemplo a seguir mostra a resposta no navegador:

    ![Resposta da função no navegador.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    A URL da solicitação inclui uma chave que é necessária, por padrão, para acessar sua função via HTTP.

1. Quando a função é executada, informações de rastreamento são gravadas nos logs. Para ver a saída do rastreamento da execução anterior, volte para sua função no portal e selecione a seta na parte inferior da tela para expandir os **Logs**.

   ![Visualizador de log de função no Portal do Azure.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

