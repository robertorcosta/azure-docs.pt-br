---
title: 'Início rápido: criar um pipeline de CI/CD para a linguagem de programação go usando o iniciador DevOps do Azure'
description: O iniciante do DevOps facilita a introdução ao Azure. Ele ajuda a iniciar um aplicativo Web de linguagem de programação Go em um serviço do Azure em algumas etapas rápidas.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 02304758ef89ff7c94d1c3d8f98f6931ab7fd8dc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82233118"
---
# <a name="create-a-cicd-pipeline-for-go-using-azure-devops-starter"></a>Criar um pipeline de CI/CD para usar usando o Azure DevOps Starter

Configure a CI (integração contínua) e o CD (entrega contínua) para seu aplicativo go usando o Azure DevOps Starter. O iniciante do DevOps simplifica a configuração inicial de um pipeline de versão e compilação DevOps do Azure.

Caso não tenha uma assinatura do Azure, você pode obter uma gratuita via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

O iniciador DevOps cria um pipeline de CI/CD no Azure Pipelines. É possível criar uma nova organização do Azure DevOps ou usar uma existente. O iniciador DevOps também cria recursos do Azure na assinatura do Azure de sua escolha.

1. Entre no [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa, digite **DevOps Starter**e, em seguida, selecione. Clique em **Adicionar** para criar um novo.

    ![O painel inicial do DevOps](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-app-and-azure-service"></a>Selecionar um aplicativo de exemplo e o serviço do Azure

1. Selecione o aplicativo de exemplo **Go**, depois selecione **Avançar**.  
    
1. **Aplicativo Go simples** é a estrutura padrão. Selecione **Avançar**.  A estrutura do aplicativo, escolhida anteriormente, determina o tipo dos destinos de implantação do serviço do Azure disponível para implantação. 
    
1. Mantenha o serviço padrão do Azure e selecione **Avançar**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma assinatura do Azure 

1. Crie uma nova organização do Azure DevOps gratuita ou escolha uma existente. 

1. Insira um nome para o projeto do Azure DevOps. 

1. Selecione a assinatura e o local do Azure, insira um nome para o aplicativo e selecione **Concluído**. Após alguns minutos, o painel inicial do DevOps é exibido no portal do Azure. Um aplicativo de exemplo é configurado em um repositório em sua organização do Azure DevOps, é executado um build, e seu aplicativo é implantado no Azure. 

    O painel oferece visibilidade no seu repositório de código, pipeline de CI/CD e aplicativo no Azure. À direita, selecione **Procurar** para exibir o aplicativo em execução.

    ![Exibição Painel](_img/azure-devops-project-go/dashboardnopreview.png) 

## <a name="commit-your-code-changes-and-execute-the-cicd"></a>Confirmar as alterações de código e executar a CI/CD

O iniciador DevOps cria um repositório git no Azure Repos ou no GitHub. Para exibir o repositório e fazer alterações de código no seu aplicativo, faça o seguinte:

1. No início DevOps, à esquerda, selecione o link para sua ramificação mestre. O link abre uma exibição do repositório de Git recém-criado.

1. Para exibir a URL do clone do repositório, selecione **Clone** no canto superior direito. Você pode clonar seu repositório Git no seu IDE favorito. Nas próximas etapas, você pode usar o navegador da Web para criar e confirmar as alterações de código diretamente no branch mestre.

1. À esquerda, vá até o arquivo *views/index.html*, depois selecione **Editar**.

1. Faça uma alteração no arquivo. Por exemplo, modifique o texto dentro de uma das marcas div.

1. Selecione **Confirmar**, depois salve as alterações.

1. No navegador, vá até o painel do DevOps Projects. Um build deve estar em andamento. As alterações feitas são automaticamente compiladas e implantadas via pipeline de CI/CD.

## <a name="examine-the-cicd-pipeline"></a>Examinar o pipeline de CI/CD

O DevOps Starter configura automaticamente um pipeline de CI/CD completo no Azure Repos. Explore e personalize o pipeline conforme necessário. Para se familiarizar com os pipelines de build e de lançamento do Azure DevOps, faça o seguinte:

1. Vá para o painel inicial do DevOps.

1. Na parte superior, selecione **Pipeline de build**. Uma guia do navegador exibe o pipeline de build do seu novo projeto.

1. Aponte para o campo **status** e, em seguida, selecione as reticências (...). Um menu exibe várias opções, como enfileirar uma nova compilação, pausar uma compilação e editar o pipeline de compilação.

1. Selecione **Editar**.

1. Nesse painel, é possível examinar as diversas tarefas do pipeline de build. A compilação realiza várias tarefas, como efetuar buscas das fontes no repositório Git, restaurar dependências e publicar as saídas usadas para as implantações.

1. Na parte superior do pipeline de build, selecione o nome desse pipeline.

1. Altere o nome do pipeline de compilação para algo mais descritivo, selecione **salvar & fila**e, em seguida, selecione **salvar**.

1. Embaixo do nome do pipeline de build, selecione **Histórico**. Esse painel exibe uma trilha de auditoria das alterações recentes do build. O Azure DevOps controla quaisquer alterações feitas no pipeline de build e permite que você compare versões.

1. Selecione **Gatilhos**. O DevOps Starter cria automaticamente um gatilho de CI e cada confirmação para o repositório inicia uma nova compilação. Como opção, é possível incluir ou excluir os branches do processo de CI.

1. Selecione **Retenção**. Dependendo do seu cenário, você pode especificar políticas para manter ou remover um determinado número de compilações.

1. Selecione **Build e Lançamento** e selecione **Lançamentos**.  O iniciador DevOps cria um pipeline de liberação para gerenciar implantações no Azure.

1. Selecione as reticências (...) ao lado do pipeline de lançamento e selecione **Editar**. O pipeline de lançamento contém um *pipeline* que define o processo de lançamento.

1. Em **Artefatos**, selecione **Soltar**. O pipeline de build examinado anteriormente produz a saída usada para o artefato. 

1. À direita do ícone **Soltar**, selecione **Gatilho de implantação contínua**. Esse pipeline de lançamento tem um gatilho de CD habilitado, que executa uma implantação sempre que houver um novo artefato de build disponível. Outra opção é desabilitar o gatilho para que suas implantações exijam uma execução manual. 

1. À esquerda, selecione **Tarefas**. Tarefas são as atividades que seu processo de implantação realiza. Neste exemplo, uma tarefa foi criada para ser implantada no Serviço de Aplicativo do Azure.

1. À direita, selecione **Exibir versões** para exibir um histórico de versões.

1. Selecione as reticências (...) ao lado de uma versão e selecione **Abrir**. É possível explorar vários menus, como um resumo da versão, itens de trabalho associados e testes.

1. Selecione **Confirmações**. Essa exibição mostra as confirmações de código associadas a essa implantação. 

1. Selecione **logs**. Os logs contêm informações úteis sobre o processo de implantação. É possível exibi-los durante e após as implantações.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando eles não forem mais necessários, é possível excluir a instância de Serviço de Aplicativo do Azure e recursos relacionados criados neste início rápido. Para fazer isso, use a funcionalidade **excluir** no painel inicial do DevOps.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como modificar os pipelines de build e de lançamento para atender às necessidades de sua equipe, confira:

> [!div class="nextstepaction"]
> [Definir seu pipeline de CD (implantação contínua) de vários estágios](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
