---
title: Criar uma função usando o Microsoft Azure for Students Starter
description: Saiba como criar uma função do Azure dentro de uma assinatura do Microsoft Azure for Students Starter
Customer intent: As a student, I want to be able to create an HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
author: alexkarcher-msft
ms.topic: how-to
ms.date: 04/29/2020
ms.author: alkarche
ms.openlocfilehash: ffb6378d3dc4cc3fb23ea62157aad393d8ae6642
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122798"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Criar uma função usando o Microsoft Azure for Students Starter

Neste tutorial, criaremos uma função HTTP "Olá, mundo" em uma assinatura inicial do Azure for students. Também examinaremos o que está disponível no Azure Functions nesse tipo de assinatura.

O Microsoft *Azure for Students Starter* oferece uma introdução aos produtos do Azure necessários para desenvolver na nuvem sem nenhum custo para você. [Saiba mais sobre essa oferta aqui.](https://azure.microsoft.com/offers/ms-azr-0144p/)

O Azure Functions lhe permite executar seu código em um ambiente [sem servidor](https://azure.microsoft.com/solutions/serverless/) sem que seja preciso primeiro criar uma VM ou publicar um aplicativo Web. [Saiba mais sobre o Functions aqui.](./functions-overview.md)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function"></a>Criar uma função

 Neste artigo, saiba como usar Azure Functions para criar uma função de gatilho HTTP "Olá, mundo" no portal do Azure.

![Criar um aplicativo de funções no portal do Azure](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com) com sua conta do Azure.

## <a name="create-a-function-app"></a>Criar um aplicativo de funções

Você deve ter um aplicativo de funções para hospedar a execução de suas funções. Um aplicativo de funções lhe permite agrupar funções como uma unidade lógica para facilitar o gerenciamento, a implantação, o dimensionamento e o compartilhamento de recursos.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Em seguida, crie uma nova função no novo aplicativo de funções.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Criar uma função de gatilho HTTP

1. No menu à esquerda da janela **funções** , selecione **funções**e, em seguida, selecione **Adicionar** no menu superior. 
 
1. Na janela **nova função** , selecione **gatilho http**.

    ![Escolher função de gatilho HTTP](./media/functions-create-student-starter/function-app-select-http-trigger.png)

1. Na janela **nova função** , aceite o nome padrão para **nova função**ou insira um novo nome. 

1. Escolha **anônimo** na lista suspensa **nível de autorização** e selecione **criar função**.

    O Azure cria a função de gatilho HTTP. Agora você pode executar a nova função enviando uma solicitação HTTP.

## <a name="test-the-function"></a>Testar a função

1. Na nova função de gatilho HTTP, selecione **código + teste** no menu à esquerda e selecione **obter URL de função** no menu superior.

    ![Selecione obter URL da função](./media/functions-create-student-starter/function-app-select-get-function-url.png)

1. Na caixa de diálogo **obter URL da função** , selecione **padrão** na lista suspensa e, em seguida, selecione o ícone **copiar para área de transferência** . 

    ![Copiar a URL da função do Portal do Azure](./media/functions-create-student-starter/function-app-develop-tab-testing.png)

1. Cole a URL de função na barra de endereços do navegador. Adicione o valor da cadeia de caracteres de consulta `?name=<your_name>` ao final desta URL e pressione ENTER para executar a solicitação. 

    O exemplo a seguir mostra a resposta no navegador:

    ![Resposta da função no navegador.](./media/functions-create-student-starter/function-app-browser-testing.png)

    A URL da solicitação inclui uma chave que é necessária, por padrão, para acessar sua função via HTTP.

1. Quando a função é executada, informações de rastreamento são gravadas nos logs. Para ver a saída do rastreamento, retorne à página **código + teste** no portal e expanda a seta **logs** na parte inferior da página.

   ![Visualizador de log de função no Portal do Azure.](./media/functions-create-student-starter/function-view-logs.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="supported-features-in-azure-for-students-starter"></a>Recursos compatíveis com o Microsoft Azure for Students Starter

No Azure for Students Starter, você tem acesso à maioria dos recursos do tempo de execução de Azure Functions, com várias limitações principais listadas abaixo:

* O gatilho HTTP é o único tipo de gatilho com suporte.
    * Há suporte para todas as associações de entrada e saída! [Veja a lista completa aqui.](functions-triggers-bindings.md)
* Linguagens com suporte: 
    * C# (.NET Core 2)
    * JavaScript (Node.js 8 e 10)
    * F# (.NET Core 2)
    * [Veja as linguagens com suporte em planos superiores aqui](supported-languages.md)
* O Windows é o único sistema operacional com suporte.
* A escala é restrita aos [uma instância de camada gratuita](https://azure.microsoft.com/pricing/details/app-service/windows/) em execução por até 60 minutos por dia. Você serverlessly dimensionar de 0 a 1 instância automaticamente, pois o tráfego HTTP é recebido, mas não há mais.
* Somente as [versões 2.x e posteriores](functions-versions.md) do runtime do Functions são compatíveis.
* Todas as ferramentas de desenvolvedor têm suporte para as funções de edição e publicação. Isso inclui VS Code, Visual Studio, CLI do Azure e portal do Azure. Se você quiser usar algo diferente do portal, precisará primeiro criar um aplicativo no portal e, em seguida, escolher esse aplicativo como um destino de implantação em sua ferramenta preferida.

## <a name="next-steps"></a>Próximas etapas

Agora você concluiu a criação de um aplicativo de funções com uma função simples de gatilho HTTP. Em seguida, você pode explorar as ferramentas locais, mais linguagens, monitoramento e integrações.

 * [Criar sua primeira função usando o Visual Studio](./functions-create-your-first-function-visual-studio.md)
 * [Criar sua primeira função usando o Visual Studio Code](./functions-create-first-function-vs-code.md)
 * [Guia do desenvolvedor de JavaScript do Azure Functions](./functions-reference-node.md)
 * [Usar o Azure Functions para conectar a um Banco de Dados SQL do Azure](./functions-scenario-database-table-cleanup.md)
 * [Saiba mais sobre as associações HTTP do Azure Functions](./functions-bindings-http-webhook.md).
 * [Monitorar o Azure Functions](./functions-monitoring.md)
