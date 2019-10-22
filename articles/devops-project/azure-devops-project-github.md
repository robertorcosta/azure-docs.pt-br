---
title: 'Tutorial: Criar um pipeline de CI/CD para seu código existente usando o Azure DevOps Projects'
description: O Azure DevOps Projects facilita o uso inicial do Azure. O DevOps Projects ajuda você a usar seu próprio código e o repositório do GitHub para iniciar um aplicativo em um serviço do Azure de sua escolha em algumas etapas rápidas.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/27/2019
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 4a48b5c413f6dfb58a1ba3f896ded49250cde306
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286235"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Tutorial: Criar um pipeline de CI/CD para seu código existente usando o Azure DevOps Projects

O Azure DevOps Projects apresenta uma experiência simplificada na qual é possível trazer o seu código e repositório Git existentes ou escolher um aplicativo de exemplo para criar um pipeline de CI (integração contínua) e CD (entrega contínua) para o Azure.

Você vai:

> [!div class="checklist"]
> * Use o DevOps Projects para criar um pipeline de CI/CD
> * Configurar o acesso ao seu repositório do GitHub e escolher uma estrutura
> * Configurar o Azure DevOps e uma assinatura do Azure 
> * Confirmar alterações no GitHub e implantá-las automaticamente no Azure
> * Examinar o pipeline de CI/CD do Azure Pipelines
> * Limpar recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Você pode obter uma gratuita por meio do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Acesso ao GitHub ou repositório Git externo que contém o .NET, Java, PHP, Node, Python ou código da Web estático.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

O Azure DevOps Projects cria um pipeline de CI/CD no Azure Pipelines. É possível criar uma nova organização do Azure DevOps ou usar uma existente. O Azure DevOps Projects também cria recursos do Azure na assinatura do Azure de sua escolha.

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No painel esquerdo, selecione **+Criar um recurso** na barra de navegação à esquerda, depois procure o **DevOps Projects** e selecione **Criar**.

   ![O painel do DevOps Projects](_img/azure-devops-project-github/azuredashboard.png)

3. Selecione **Traga seu próprio código**, depois selecione **Avançar**.

## <a name="configure-access-to-your-github-repo-and-choose-a-framework"></a>Configurar o acesso ao seu repositório do GitHub e escolher uma estrutura

1. Selecione o **GitHub** ou um repositório de código **Git** externo. Para este tutorial, selecione **GitHub**. Talvez seja necessário autenticar-se no GitHub pela primeira vez para permitir que o Azure acesse seu repositório GitHub.

2. Conclua selecionando o **Repositório** e o **Branch** e selecione **Avançar**.

3. Se você estiver usando contêineres do Docker, altere **É um aplicativo Docker** para **SIM**; para este tutorial, deixe **NÃO** selecionado e selecione **Avançar**. Para obter mais informações sobre como usar contêineres do Docker, passe o mouse sobre o ícone **i**.

   ![.NET Framework](_img/azure-devops-project-github/appframework.png)

4. Nos menus suspensos, selecione o **runtime do aplicativo** e **estrutura** e, em seguida, selecione **Avançar**. A estrutura do aplicativo que você escolheu anteriormente determinará o tipo de destino de implantação do serviço do Azure disponível.

5. Selecionar o **Serviço do Azure** no qual implantar o aplicativo e, em seguida, selecione **Avançar**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma assinatura do Azure

1. Insira um **Nome de projeto**.

2. Crie uma nova **Organização do Azure DevOps** gratuita ou escolha uma existente no menu suspenso.

3. Selecione sua **Assinatura do Azure**, insira um nome para o **aplicativo Web** ou utilize o padrão. Selecione um **Localização** e, em seguida, selecione **Concluído**. Após alguns minutos, a Visão geral de implantação de projeto de DevOps é exibida no portal do Azure.

4. Selecione **Ir para o recurso** para exibir o painel do DevOps Project. No canto superior direito, fixe o **Projeto** ao seu painel para ter acesso rápido. O Azure DevOps Projects configura automaticamente um build de CI e gatilho de liberação. Seu código permanece no seu repositório GitHub ou em outro repositório externo. Um aplicativo de exemplo é configurado em um repositório em sua **Organização do Azure DevOps**. Uma compilação é executada e seu aplicativo é implantado no Azure.

   ![Exibição do painel do DevOps Projects](_img/azure-devops-project-github/projectsdashboard.png)

5. O painel oferece visibilidade no seu repositório de código, pipeline de CI/CD e aplicativo no Azure. À direita, em recursos do Azure, selecione **Procurar** para exibir o aplicativo em execução.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Confirmar alterações no GitHub e implantá-las automaticamente no Azure

Agora você está pronto para colaborar com uma equipe em seu aplicativo usando um processo de CI/CD que implanta automaticamente o trabalho mais recente em seu site. Cada alteração do repositório GitHub inicia um build no Azure DevOps, e um pipeline de CD executa uma implantação no Azure.

1. No painel do Projeto do DevOps, selecione **Repositórios**. O repositório do GitHub é aberto em uma nova guia do navegador. Faça uma alteração em seu aplicativo e, em seguida, clique em **Confirmar alterações**.

2. Depois de um tempo, um build inicia no Azure Pipelines. Você pode monitorar o status do build no painel do DevOps Projects ou monitorá-lo em sua organização do Azure DevOps selecionando a guia **Pipelines de Build** no painel do projeto.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Examinar o pipeline de CI/CD do Azure Pipelines

O Azure DevOps Projects configura automaticamente um pipeline de CI/CD no Azure Pipelines. Explore e personalize o pipeline conforme necessário. Para se familiarizar com os pipelines de build e de lançamento, faça o seguinte:

1. No painel do DevOps Projects, selecione **Pipelines de Build**.

2. Quando a página **Azure Pipelines** for aberta, você verá um histórico dos builds mais recentes e o status de cada build.

   ![Builds do pipeline do Azure DevOps](_img/azure-devops-project-github/pipelinesbuildpage.png)

3. No canto superior direito da página **Builds**, você verá opções para **Editar** o build atual, **Fila** para enfileirar um novo build e as reticências ( **&#8942;** ) para abrir um menu com mais opções; selecione **Editar**.

4. O build realiza várias tarefas, como efetuar buscas das fontes no repositório, restaurar dependências e publicar as saídas das implantações. À direita em **Nome**, altere o nome do pipeline de build para algo mais descritivo. Selecione **Salvar e enfileirar** e, em seguida, **Salvar**; deixe um comentário e, em seguida, selecione **Salvar** novamente.

   ![Página de builds do Azure DevOps](_img/azure-devops-project-github/buildpage.png)

5. Para ver uma trilha de auditoria das alterações recentes do build, selecione a guia **Histórico**. O Azure DevOps rastreia as alterações feitas no pipeline de build e permite que você compare as versões.

6. Selecione a guia **Gatilhos**. O projeto do Azure DevOps cria automaticamente um gatilho de CI com algumas configurações padrão. Gatilhos como **Habilitar integração contínua** podem ser definidos para executar um build cada vez que uma alteração de código tiver sido confirmada ou agendar builds para serem executados em horários específicos.

## <a name="clean-up-resources"></a>Limpar recursos

Quando eles não forem mais necessários, será possível excluir o serviço do Aplicativo Azure e os recursos relacionados criados neste tutorial. Para isso, use a funcionalidade **Excluir** no painel do DevOps Projects.

## <a name="next-steps"></a>Próximas etapas

Depois de ter configurado seu processo de CI/CD neste tutorial, um pipeline de build e um pipeline de lançamento foram criados automaticamente no Azure DevOps Projects. Você pode modificar esses pipelines de build e de lançamento para atender às necessidades de sua equipe. Você aprendeu como:

> [!div class="checklist"]
>  * Use o DevOps Projects para criar um pipeline de CI/CD
> * Configurar o acesso ao seu repositório do GitHub e escolher uma estrutura
> * Configurar o Azure DevOps e uma assinatura do Azure
> * Confirmar alterações no GitHub e implantá-las automaticamente no Azure
> * Examinar o pipeline de CI/CD do Azure Pipelines
> * Limpar recursos

Para saber mais sobre o pipeline de CI/CD, consulte:

> [!div class="nextstepaction"]
> [Definir seu pipeline de CD (implantação contínua) de vários estágios](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

Para saber mais sobre o monitoramento de aplicativos, confira:
  
 > [!div class="nextstepaction"]
 > [O que é o monitor do Azure](https://docs.microsoft.com/azure/azure-monitor/overview)
