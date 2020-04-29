---
title: Criar um aplicativo de funções do Portal do Azure no Linux
description: Aprenda a criar sua primeira Função do Azure para a execução sem servidor usando o Portal do Azure.
ms.topic: how-to
ms.date: 02/28/2019
ms.openlocfilehash: b2de36faf07ad661ff8817adc48b726f54990ceb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80754121"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Criar um aplicativo de funções em um Plano do Serviço de Aplicativo do Azure

Azure Functions permite hospedar funções no Linux em um contêiner de Serviço de Aplicativo do Azure padrão. Este artigo explica como usar a CLI do Azure para criar o [portal do Azure](https://portal.azure.com) para criar um aplicativo de funções hospedado em Linux que é executado em um [Plano do Serviço de Aplicativo](functions-scale.md#app-service-plan). Você também pode [usar seu próprio contêiner](functions-create-function-linux-custom-image.md).

![Criar um aplicativo de funções no portal do Azure](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no portal do Azure em <https://portal.azure.com> com sua conta do Azure.

## <a name="create-a-function-app"></a>Criar um aplicativo de funções

Você deve ter um aplicativo de funções para hospedar a execução de suas funções no Linux. O aplicativo de funções fornece um ambiente para execução do seu código de função. Ele permite que você agrupe funções como uma unidade lógica para facilitar o gerenciamento, a implantação, o dimensionamento e o compartilhamento de recursos. Neste artigo, você criará um plano do serviço de aplicativo quando criar seu aplicativo de funções.

1. Selecione o botão **Criar um recurso** no canto superior esquerdo do portal do Azure e selecione **Computação** > **Aplicativo de funções**.

    ![Criar um aplicativo de funções no portal do Azure](./media/create-function-app-linux-app-service-plan/function-app-create-flow.png)

2. Use as configurações do aplicativo de funções especificadas na tabela abaixo da imagem.

    ![Definir novas configurações do aplicativo de funções](./media/create-function-app-linux-app-service-plan/function-app-create-flow2.png)

    | Configuração      | Valor sugerido  | DESCRIÇÃO                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome do aplicativo** | Nome globalmente exclusivo | Nome que identifica seu novo aplicativo de funções. Os caracteres válidos são `a-z`, `0-9` e `-`.  | 
    | **Assinatura** | Sua assinatura | A assinatura na qual este novo aplicativo de funções será criado. | 
    | **[Grupo de Recursos](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Nome do novo grupo de recursos no qual criar o seu aplicativo de funções. |
    | **SO** | Linux | O aplicativo de funções é executado no Linux. |
    | **Publicar** | Código | O contêiner do Linux padrão para sua **pilha de runtime** foi usado. Tudo o que você precisa fornecer é o código de projeto de aplicativo de função. Outra opção é publicar uma [imagem do Docker](functions-create-function-linux-custom-image.md) personalizada. |
    | **[Plano de hospedagem](functions-scale.md)** | Plano do Serviço de Aplicativo | Plano de hospedagem que define como os recursos são alocados para seu aplicativo de funções. Quando você executa em um Plano do Serviço de Aplicativo, deve gerenciar o [dimensionamento do aplicativo de funções](functions-scale.md).  |
    | **Plano/localização de serviço de aplicativo** | Criar plano | Escolha **Criar novo** e dê um nome ao **Plano de Serviço de Aplicativo**. Escolha um **local** em uma [região](https://azure.microsoft.com/regions/) perto de você ou perto de outros serviços acessados pelas suas funções. Escolha o **[tipo de preço](https://azure.microsoft.com/pricing/details/app-service/linux/)** desejado. <br/>É possível executar aplicativos de funções do Windows e Linux no mesmo plano do serviço de aplicativo. |
    | **Pilha de runtime** | Linguagem preferencial | Escolha um runtime compatível com sua linguagem de programação de funções favorita. Escolha **.NET** para funções C# e F#. |
    | **[Armazenamento](../storage/common/storage-account-create.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento usada pelo seu aplicativo de funções. Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas. Você também pode usar uma conta existente, mas ela precisa atender aos [requisitos da conta de armazenamento](storage-considerations.md#storage-account-requirements). |
    | **[Application Insights](functions-monitoring.md)** | habilitado | O Application Insights está desabilitado por padrão. É recomendável habilitar a integração do Application Insights agora e escolher um local de hospedagem perto da localização do plano de serviço de aplicativo. Se você quiser fazer isso mais tarde, consulte [Monitorar o Azure Functions](functions-monitoring.md).  |

3. Selecione **Criar** para provisionar e implantar o aplicativo de funções.

4. Selecione o ícone da Notificação no canto superior direito do portal e veja se a mensagem **Implantação concluída com êxito** é exibida.

    ![Definir novas configurações do aplicativo de funções](./media/create-function-app-linux-app-service-plan/function-app-create-notification.png)

5. Selecione **Ir para recursos** para exibir o novo aplicativo de funções.

Em seguida, crie uma nova função no novo aplicativo de funções. Mesmo depois que seu aplicativo de funções estiver disponível, ele poderá levar alguns minutos para ser totalmente inicializado.

## <a name="create-an-http-triggered-function"></a><a name="create-function"></a>Criar uma função disparada por HTTP

Esta seção mostra como criar uma função no novo aplicativo de funções no portal.

> [!NOTE]
> A experiência de desenvolvimento do portal pode ser útil para experimentar o Azure Functions. Na maioria dos cenários, considere desenvolver suas funções localmente e publicar o projeto de seu aplicativo de funções usando um [Visual Studio Code](functions-create-first-function-vs-code.md#create-an-azure-functions-project) ou [Azure Functions Core Tools](functions-run-local.md#create-a-local-functions-project).  

1. Em seu novo aplicativo de função, escolha a guia **Visão geral** e, em seguida, depois que ele carregar completamente, escolha **+ Nova função**.

    ![Crie uma nova função a partir da guia Visão geral](./media/create-function-app-linux-app-service-plan/overview-create-function.png)

1. Na guia de **Início Rápido**, escolha **No portal**e selecione **Continuar**.

    ![Escolha a sua plataforma de desenvolvimento de função.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-choose-portal.png)

1. Escolha **WebHook + API** e, em seguida, selecione **Criar**.

    ![Início rápido de funções no Portal do Azure.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-node-webhook.png)

Uma função é criada usando um modelo específico a um idioma para uma função disparada por HTTP.

Agora você pode executar a nova função enviando uma solicitação HTTP.

## <a name="test-the-function"></a>Testar a função

1. Em sua nova função, clique em **</> Obter URL da função** na parte superior direita, selecione **padrão (Tecla de função)** e depois clique em **Copiar**. 

    ![Copiar a URL da função do Portal do Azure](./media/create-function-app-linux-app-service-plan/function-app-develop-tab-testing.png)

2. Cole a URL de função na barra de endereços do navegador. Adicione o valor da cadeia de caracteres de consulta `&name=<yourname>` ao final desta URL e pressione `Enter` em seu teclado para executar a solicitação. Você deverá ver a resposta retornada pela função exibida no navegador.  

    O exemplo a seguir mostra a resposta no navegador:

    ![Resposta da função no navegador.](./media/create-function-app-linux-app-service-plan/function-app-browser-testing.png)

    A URL da solicitação inclui uma chave que é necessária, por padrão, para acessar sua função via HTTP.

3. Quando a função é executada, informações de rastreamento são gravadas nos logs. Para ver a saída do rastreamento da execução anterior, volte para sua função no portal e clique na seta na parte inferior da tela para expandir os **Logs**.

   ![Visualizador de log de função no Portal do Azure.](./media/create-function-app-linux-app-service-plan/function-view-logs.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Você criou um aplicativo de funções com uma função simples disparada por HTTP.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obter mais informações, veja [Associações HTTP do Azure Functions](functions-bindings-http-webhook.md).
