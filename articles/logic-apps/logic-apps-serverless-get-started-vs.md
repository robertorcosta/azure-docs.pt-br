---
title: Construa o primeiro aplicativo sem servidor no Visual Studio
description: Crie, implante e gerencie um aplicativo sem servidor usando aplicativos azure logic e funções do Azure no Visual Studio
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: 2e27958dd9379a26ca7e7f4d7e427e5afa216e29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981159"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Construa seu primeiro aplicativo sem servidor usando aplicativos azure logic e funções do Azure no Visual Studio

Você pode desenvolver e implantar rapidamente aplicativos em nuvem usando as ferramentas e recursos sem servidor no Azure, como [a Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [funções azure](../azure-functions/functions-overview.md). Este artigo mostra como começar a construir um aplicativo sem servidor, o qual usa um aplicativo lógico que chama uma função do Azure, no Visual Studio. Para saber mais sobre as soluções sem servidor no Azure, consulte [Azure sem servidor com funções e aplicativos lógicos](../logic-apps/logic-apps-serverless-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Para construir um aplicativo sem servidor no Visual Studio, você precisa:

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* As seguintes ferramentas. Se você ainda não os tiver, baixe e instale-os.

  * [Visual Studio 2019, 2017 ou 2015 (Comunidade ou outra edição)](https://aka.ms/download-visual-studio). 
  Este início rápido usa o Visual Studio Community 2017, que é gratuito.

    > [!IMPORTANT]
    > Ao instalar o Visual Studio 2019 ou 2017, selecione a carga de trabalho **Desenvolvimento do Azure**.

  * [Microsoft Azure SDK para .NET (versão 2.9.1 ou posterior)](https://azure.microsoft.com/downloads/). 
  Saiba mais sobre o [SDK do Azure para .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * Ferramentas de Aplicativos Lógicos do Azure para as versões do Visual Studio que você deseja:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Você pode baixar e instalar as Ferramentas dos Aplicativos Lógicos do Azure diretamente do Visual Studio Marketplace ou aprender como [instalar essa extensão de dentro do Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Reinicie o Visual Studio após concluir a instalação.

  * [Azure funções principais ferramentas](https://www.npmjs.com/package/azure-functions-core-tools) para depuração local de funções.

* Acesse a web enquanto usa o Logic App Designer incorporado.

  O Designer requer uma conexão de Internet para criar recursos no Azure e ler as propriedades e os dados dos conectores em seu aplicativo lógico. 
  Por exemplo, se você utilizar o conector do Dynamics CRM Online, o Designer verifica sua instância CRM para propriedades padrão e personalizadas disponíveis.

## <a name="create-a-resource-group-project"></a>Crie um projeto de grupo de recursos

Para começar, crie um [projeto do Grupo de Recursos do Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) para o aplicativo sem servidor. No Azure, você cria recursos dentro de um *grupo de recursos*, que é uma coleção lógica que você usa para organizar, gerenciar e implantar recursos para um aplicativo inteiro como um único ativo. Para um aplicativo sem servidor no Azure, o grupo de recursos contém recursos para os Aplicativos Lógicos do Azure e Azure Functions. Saiba mais sobre [Recursos e grupos de recursos do Azure](../azure-resource-manager/management/overview.md).

1. Inicie o Visual Studio e faça login usando sua conta do Azure.

1. No menu **Arquivo**, selecione **Novo** > **Projeto**.

   ![Criar um novo projeto no Visual Studio](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. Em **Instalado**, selecione **Visual C#** ou **Visual Basic**. Em seguida, selecione **Cloud** > **Azure Resource Group**.

   > [!NOTE]
   > Se a categoria **Cloud** ou o projeto **Azure Resource Group** não existirem, certifique-se de que você instalou o Azure SDK para O Visual Studio.

   Se você estiver usando o Visual Studio 2019, siga estas etapas:

   1. Na **caixa Criar uma nova** caixa de projeto, selecione o modelo de projeto do Grupo de recursos do **Azure** para visual C# ou Visual Basic e selecione **Next**.

   1. Forneça o nome e outras informações do projeto que você deseja usar para o grupo de recursos do Azure. Quando terminar, selecione **Criar**.

1. Dê ao seu projeto um nome e um local e, em seguida, selecione **OK**.

   O Visual Studio solicita que você selecione um modelo na lista de modelos. 
   Este exemplo usa um modelo Do Azure QuickStart para que você possa construir um aplicativo sem servidor que inclua um aplicativo lógico e uma chamada para uma função Azure.

   > [!TIP]
   > Em cenários em que você não deseja pré-implantar sua solução em um grupo de recursos do Azure, você pode usar o modelo **de aplicativo lógico** em branco, que apenas cria um aplicativo lógico vazio.

1. Nos **modelos Mostrar desta** lista de locais, selecione **Azure QuickStart (github.com/Azure/azure-quickstart-templates)**.

1. Na caixa de pesquisa, digite "logic-app" como seu filtro. A partir dos resultados, selecione o modelo **101-logic-app-and-function-app.**

   ![Selecione o modelo Azure QuickStart](./media/logic-apps-serverless-get-started-vs/select-template.png)

   O Visual Studio cria e abre uma solução para o projeto do grupo de recursos. 
   O modelo Azure QuickStart que você selecionou cria um modelo de implantação chamado azuredeploy.json dentro do projeto do grupo de recursos. Este modelo de implantação inclui a definição de um aplicativo lógico simples que é acionado por uma solicitação HTTP, chama uma função Azure e retorna o resultado como uma resposta HTTP.

   ![Nova solução sem servidor](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Em seguida, implante sua solução no Azure. Você deve fazer isso antes de abrir o modelo de implantação e revisar os recursos para o seu aplicativo sem servidor.

## <a name="deploy-your-solution"></a>Implantar sua solução

Antes de abrir seu aplicativo lógico no Logic App Designer no Visual Studio, você deve ter um grupo de recursos do Azure que já está implantado no Azure. O designer então pode criar conexões com recursos e serviços no aplicativo lógico. Para esta tarefa, siga estas etapas para implantar sua solução do Visual Studio para o portal Azure:

1. No Solution Explorer, no menu de atalho do projeto de recursos, selecione **Implantar** > **novo**.

   ![Criar nova implantação para o grupo de recursos](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Se eles ainda não estiverem selecionados, selecione sua assinatura do Azure e o grupo de recursos para o qual você deseja implantar. Em seguida, **selecione Implantar**.

   ![Configurações de implantação](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Se a caixa **Editar parâmetros** aparecer, forneça os nomes de recursos para usar para seu aplicativo lógico e seu aplicativo de função Azure na implantação e, em seguida, salve suas configurações. Use um nome globalmente exclusivo para o aplicativo de funções.

   ![Fornecer nomes para o aplicativo lógico e o aplicativo de funções](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Quando o Visual Studio inicia a implantação para o grupo de recursos especificado, o status da implantação da solução aparece na janela **Saída** do Visual Studio. 
   Depois que a implantação for concluída, o aplicativo lógico ficará ativo no portal do Azure.

## <a name="edit-your-logic-app-in-visual-studio"></a>Edite seu aplicativo lógico no Visual Studio

Para editar seu aplicativo lógico após a implantação, abra seu aplicativo lógico usando o Logic App Designer no Visual Studio.

1. No Solution Explorer, no menu de atalho do arquivo azuredeploy.json, selecione **Abrir com logic app designer**.

   ![Abra azuredeploy.json no Logic App Designer](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Caso não tenha esse comando no Visual Studio 2019, verifique se você tem as atualizações mais recentes do Visual Studio.

1. Depois **que** a caixa Logic App Properties aparecer, em **Assinatura,** selecione sua assinatura do Azure se ela ainda não estiver selecionada. Em **Grupo de recursos,** selecione o grupo de recursos e o local onde você implantou sua solução e, em seguida, selecione **OK**.

   ![Propriedades do aplicativo lógico](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Quando o Designer do Aplicativo Lógico for aberto, é possível continuar a adicionar etapas ou alterar o fluxo de trabalho e salvar as atualizações.

   ![Aplicativo lógico aberto no Designer do Aplicativo Lógico](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Crie seu projeto Funções Do Azure

Para criar seu projeto e função de funções usando JavaScript, Python, F#, PowerShell, Batch ou Bash, siga as etapas em [Trabalho com ferramentas principais de funções do Azure](../azure-functions/functions-run-local.md). Para desenvolver sua função Azure usando C# dentro de sua solução, use uma biblioteca de classes C# seguindo as etapas em [Publicar uma biblioteca de classe .NET como um aplicativo de função](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/).

## <a name="deploy-functions-from-visual-studio"></a>Implantar funções a partir do Visual Studio

Seu modelo de implantação implanta quaisquer funções do Azure que você tenha em sua solução a partir do repo git especificado por variáveis no arquivo azuredeploy.json. Se você criar e criar seu projeto Functions em sua solução, você poderá verificar esse projeto no controle de origem do `repo` Git (por exemplo, GitHub ou Azure DevOps) e, em seguida, atualizar a variável para que o modelo implante sua função Azure.

## <a name="manage-logic-apps-and-view-run-history"></a>Gerenciar os aplicativos lógicos exibir o histórico de execuções

Para aplicativos lógicos já implantados no Azure, você ainda pode editar, gerenciar, visualizar o histórico de execução e desativar esses aplicativos do Visual Studio.

1. No menu **Exibir** no Visual Studio, abra **Cloud Explorer**.

1. Em **Todas as assinaturas,** selecione a assinatura do Azure associada aos aplicativos lógicos que deseja gerenciar e, em seguida, **selecione Aplicar**.

1. Em **Aplicativos Lógicos**, selecione o aplicativo lógico. No menu de atalho desse aplicativo, selecione **Abrir com o Editor do Aplicativo Lógico**.

   > [!TIP]
   > Caso não tenha esse comando no Visual Studio 2019, verifique se você tem as atualizações mais recentes do Visual Studio.

Agora você pode baixar o aplicativo lógico já publicado no projeto do grupo de recursos. Assim, embora você possa ter iniciado um aplicativo lógico no portal Azure, você ainda pode importar e gerenciar esse aplicativo no Visual Studio. Para obter mais informações, consulte [Gerenciar aplicativos lógicos com o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar aplicativos lógicos no Visual Studio](manage-logic-apps-with-visual-studio.md)
