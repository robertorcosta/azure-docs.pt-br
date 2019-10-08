---
title: Criar um pipeline de CI/CD para Node.js com o Azure DevOps Projects
description: O DevOps Projects facilita o uso inicial do Azure. Ele ajuda você a iniciar um aplicativo em um serviço do Azure de sua escolha em algumas etapas rápidas.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc, seo-javascript-september2019
monikerRange: vsts
ms.openlocfilehash: 9fc276626d2a863f7f2e53cd7312ab8b3be513dd
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815778"
---
#  <a name="quickstart-create-a-cicd-pipeline-in-azure-pipelines-for-nodejs-with-azure-devops-projects"></a>Início Rápido: Criar um pipeline de CI/CD no Azure Pipelines para Node.js com o Azure DevOps Projects

O Azure DevOps Projects apresenta uma experiência simplificada que cria recursos do Azure e configura um pipeline de CI (integração contínua) e CD (entrega contínua) para seu aplicativo Node.js no Azure Pipelines.  

Caso não tenha uma assinatura do Azure, você pode obter uma gratuita via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

O DevOps Projects cria um pipeline de CI/CD no Azure Pipelines. É possível criar uma nova organização do Azure DevOps ou usar uma existente. O DevOps Projects também cria recursos do Azure na assinatura do Azure de sua escolha.

1. Entre no [Portal do Microsoft Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **Criar um recurso**, depois procure o **DevOps Projects**. 

    ![Iniciando a configuração de Entrega Contínua](_img/azure-devops-project-nodejs/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Selecionar um aplicativo de exemplo e o serviço do Azure

1. Selecione o aplicativo de exemplo do Node.js.  
    Os exemplos do Node.js incluem uma opção de várias estruturas de aplicativo.

1. A estrutura de exemplo padrão é Express.js. Mantenha a configuração padrão e selecione **Avançar**.  
    **Aplicativo Web no Windows** é o destino de implantação padrão.  A estrutura do aplicativo escolhida anteriormente determina o tipo de destino de implantação do serviço do Azure disponível aqui.  

2. Mantenha o serviço padrão e selecione **Avançar**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma assinatura do Azure 

1. Crie uma nova organização do Azure DevOps ou escolha uma organização existente. 

    a. Insira um nome para seu projeto.

    b. Selecione a assinatura e o local do Azure, insira um nome para o aplicativo e selecione **Concluído**.  
    Após alguns minutos, o painel do DevOps Projects é exibido no portal do Azure. Um aplicativo de exemplo é configurado em um repositório em sua organização do Azure DevOps, é executado um build, e seu aplicativo é implantado no Azure. Esse painel oferece visibilidade ao seu repositório de código, pipeline de CI/CD e seu aplicativo no Azure.
     
3. Selecione **Procurar** para exibir o aplicativo em execução.

    ![Exibição Painel](_img/azure-devops-project-nodejs/dashboardnopreview.png) 
    
O DevOps Projects configura automaticamente um build de CI e gatilho de liberação.  Agora você está pronto para colaborar com uma equipe em um aplicativo Node.js com um processo de CI/CD que implanta automaticamente o trabalho mais recente em seu site da Web.

## <a name="commit-code-changes-and-execute-cicd"></a>Confirmar as alterações de código e executar CI/CD

O DevOps Projects cria um repositório Git no Azure Repos ou GitHub. Siga as etapas a seguir para exibir o repositório e fazer alterações de código no seu aplicativo.

1. À esquerda do painel do DevOps Projects, selecione o link para seu branch mestre.  
Esse link abre uma exibição do repositório de Git recém-criado.

1. Para exibir a URL de clone do repositório, selecione **Clone** na parte superior direita do navegador.   
    Você pode clonar seu repositório Git no seu IDE favorito. Nas próximas etapas, você pode usar o navegador da Web para criar e confirmar as alterações de código diretamente no branch mestre.

1. À esquerda do navegador, vá até o arquivo **views/index.pug**.

1. Selecione **Editar**, depois faça uma alteração no cabeçalho h2.  
    Por exemplo, insira **Começar agora mesmo com o Azure DevOps Projects** ou faça outra alteração.

1. Selecione **Confirmar**, depois salve as alterações.

1. No navegador, vá até o painel do DevOps Projects.   
Agora você deve ver uma compilação em andamento. As alterações que você acabou de criar são compiladas e implantadas automaticamente por meio de um pipeline de CI/CD.

## <a name="examine-the-azure-cicd-pipeline"></a>Examinar o pipeline de CI/CD do Azure

Na etapa anterior, o DevOps Projects configurou automaticamente um pipeline de CI/CD completo. Explore e personalize o pipeline conforme necessário. Siga as etapas a seguir para se familiarizar com os pipelines de build e de lançamento.

1. Na parte superior do painel do DevOps Projects, selecione **Pipelines de Build**.  
Esse link abre uma guia de navegador e o pipeline de build do seu novo projeto.

1. Aponte para o campo **Status**, depois selecione as reticências (...).  
    Essa ação abre um menu no qual você pode iniciar várias atividades, como enfileirar um novo build, pausar um build e editar o pipeline de build.

1. Selecione **Editar**.

1. Nesse painel, é possível examinar as diversas tarefas do pipeline de build.  
O build realiza várias tarefas, como efetuar fetch das fontes do repositório Git, restaurar dependências e publicar saídas usadas para implantações.

1. Na parte superior do pipeline de build, selecione o nome desse pipeline.

1. Altere o nome do pipeline de build para algo mais descritivo, selecione **Salvar e enfileirar** e selecione **Salvar**.

1. Embaixo do nome do pipeline de build, selecione **Histórico**.   
No painel **Histórico**, é exibida uma trilha de auditoria das alterações recentes do build.  O Azure Pipelines controla quaisquer alterações feitas no pipeline de build e permite que você compare versões.

1. Selecione **Gatilhos**.   
 O DevOps Projects criou automaticamente um gatilho de CI e cada confirmação no repositório inicia um novo build.  Você pode optar por incluir ou excluir os branches do processo de CI.

1. Selecione **Retenção**.   
Dependendo do seu cenário, você pode especificar políticas para manter ou remover um determinado número de compilações.

1. Selecione **Build e lançamento**, depois selecione **Lançamentos**.  
 O DevOps Projects cria um pipeline de lançamento para gerenciar implantações no Azure.

1. À esquerda, selecione as reticências (...) ao lado do pipeline de lançamento, depois selecione **Editar**.  
O pipeline de lançamento define o processo de lançamento.

12. Em **Artefatos**, selecione **Soltar**.  
    O pipeline de build examinado nas etapas anteriores produz a saída usada para o artefato. 

1. Ao lado do ícone **Soltar**, selecione o **Gatilho de implantação contínua**.  
Esse pipeline de lançamento tem um gatilho de CD habilitado, o qual executa uma implantação sempre que houver um novo artefato de build disponível. Outra opção é desabilitar o gatilho para que suas implantações exijam uma execução manual. 


1. À esquerda, selecione **Tarefas**.   
As tarefas são as atividades realizadas pelo seu processo de implantação. Neste exemplo, uma tarefa foi criada para ser implantada no Serviço de Aplicativo do Azure.


1. À direita, selecione **Exibir versões**.  
Essa exibição mostra um histórico de versões.

1. Selecione as reticências (...) ao lado de uma das versões, depois selecione **Abrir**.  
Há vários menus para explorar, como um resumo da versão, itens de trabalho associados e testes.

1. Selecione **Confirmações**.   
Essa exibição mostra as confirmações de código associadas à implantação específica.

1. Selecione **Logs**.  
Os logs contêm informações úteis sobre o processo de implantação. Eles podem ser exibidos durante e após as implantações.

## <a name="clean-up-resources"></a>Limpar recursos

É possível excluir o Serviço de Aplicativo do Azure e outros recursos criados quando eles não forem mais precisos. Use a funcionalidade **Excluir** no painel do DevOps Projects.


## <a name="next-steps"></a>Próximas etapas

Quando você configurou seu processo de CI/CD, os pipelines de build e de lançamento foram criados automaticamente. Você pode modificar esses pipelines de build e de lançamento para atender às necessidades de sua equipe. Para saber mais sobre o pipeline de CI/CD, confira:

> [!div class="nextstepaction"]
> [Personalizar o processo de CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>vídeos

> [!VIDEO https://www.youtube.com/embed/3etwjubReJs]
