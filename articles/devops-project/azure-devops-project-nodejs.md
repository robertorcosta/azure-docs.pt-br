---
title: Criar um pipeline de CI/CD para um PWA com o GatsbyJS e o iniciador DevOps do Azure
description: Com o DevOps Starter, é mais fácil começar a usar o Azure. Ele ajuda você a iniciar um aplicativo em um serviço do Azure de sua escolha em algumas etapas rápidas.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: arob98
manager: angrobe
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: angrobe
ms.custom: mvc
ms.openlocfilehash: 7db4fa2a780a3a1f53ecd73a40c247583cb6a79a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82233800"
---
# <a name="create-a-cicd-pipeline-in-azure-pipelines-for-nodejs-with-azure-devops-starter"></a>Criar um pipeline de CI/CD no Azure Pipelines para Node.js com o iniciador DevOps do Azure

Neste guia de início rápido, você cria um aplicativo Web progressivo NodeJS (PWA) usando o [GatsbyJS](https://www.gatsbyjs.org/) e a experiência de criação do Azure DevOps Starter simplificada. Quando terminar, você terá um pipeline de CI/CD (integração contínua e entrega contínua) para o PWA no Azure Pipelines. O iniciador DevOps do Azure configura o que você precisa para desenvolver, implantar e monitorar.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Uma organização do [Azure DevOps](https://azure.microsoft.com/services/devops/).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

O DevOps Starter cria um pipeline de CI/CD no Azure Pipelines. É possível criar uma nova organização do Azure DevOps ou usar uma existente. O DevOps Starter também cria recursos do Azure na assinatura do Azure de sua escolha.

1. Entre no [portal do Azure](https://portal.azure.com) e, no painel esquerdo, selecione **Criar um recurso**. 

   ![Criar um recurso do Azure no portal do Azure](_img/azure-devops-project-nodejs/create-azure-resource.png)

1. Na caixa de pesquisa, digite **DevOps Starter** e selecione. Clique em **Adicionar** para criar um.

    ![O painel do DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Selecionar um aplicativo de exemplo e o serviço do Azure

1. Selecione o aplicativo de exemplo do Node.js.   

    ![Selecione o exemplo de Node.js](_img/azure-devops-project-nodejs/select-nodejs.png) 

1. A estrutura de exemplo padrão é **Express.js**. Altere a seleção para **simples Node.js aplicativo** e, em seguida, selecione **Avançar**. 

    ![Selecione o aplicativo Node.js simples](_img/azure-devops-project-nodejs/select-nodejs-framework.png) 

1. Os destinos de implantação disponíveis nesta etapa são determinados pela estrutura de aplicativo selecionada na etapa 2. Neste exemplo, o **aplicativo Web do Windows** é o destino de implantação padrão. Deixe **aplicativo Web para contêineres** definido e selecione **Avançar**.

    ![Selecionar o destino de implantação](_img/azure-devops-project-nodejs/select-web-server.png)

## <a name="configure-a-project-name-and-an-azure-subscription"></a>Configurar um nome de projeto e uma assinatura do Azure

1. Na etapa final do fluxo de trabalho de criação do DevOps Starter, você atribui um nome de projeto, seleciona uma assinatura do Azure e seleciona **concluído**.  

    ![Atribuir um nome de projeto e selecionar uma assinatura](_img/azure-devops-project-nodejs/assign-project-name.png)

1. Uma página de resumo é exibida enquanto o projeto é compilado e seu aplicativo é implantado no Azure. Após um breve período, um projeto é criado em sua [organização DevOps do Azure](https://dev.azure.com/) que inclui um repositório git, um quadro Kanban, um pipeline de implantação, planos de teste e os artefatos exigidos pelo seu aplicativo.  

## <a name="managing-your-project"></a>Gerenciando seu projeto

1. Navegue até **todos os recursos** e encontre o iniciador do DevOps. Selecione o **iniciador do DevOps**.

    ![Painel do DevOps do Azure na lista de recursos](_img/azure-devops-project-nodejs/devops-starter-list.png)

1. Você é direcionado a um painel que fornece visibilidade da home page do projeto, do repositório de código, do pipeline de CI/CD e de um link para seu aplicativo em execução. Selecione a **página inicial do projeto** para exibir seu aplicativo no **Azure DevOps** e, em outra guia do navegador, selecione o **ponto de extremidade do aplicativo** para exibir o aplicativo de exemplo ao vivo. Alteramos este exemplo posteriormente para usar o PWA gerado pelo GatsbyJS.

    ![Painel do DevOps do Azure](_img/azure-devops-project-nodejs/devops-projects-dashboard.png) 

1. Em seu projeto DevOps do Azure, você pode convidar membros da equipe para colaborar e estabelecer um quadro Kanban para começar a controlar seu trabalho. Para saber mais, clique [aqui](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops?view=azure-devops).

![Visão geral do Azure DevOps](_img/azure-devops-project-nodejs/azure-devops-overview.png)

## <a name="clone-the-repo-and-install-your-gatsby-pwa"></a>Clonar o repositório e instalar o Gatsby PWA

O iniciador DevOps cria um repositório git no Azure Repos ou no GitHub. Este exemplo criou um repositório do Azure. A próxima etapa é clonar o repositório e fazer alterações.

1. Selecione **repositórios** em seu **projeto DevOps** e clique em **clonar**.  Há vários mecanismos para clonar o repositório Git para sua área de trabalho.  Escolha aquele que se adapte à sua experiência de desenvolvimento.  

    ![Clonar o repositório](_img/azure-devops-project-nodejs/clone-the-repo.png)

1. Depois que o repositório for clonado para a área de trabalho, faça algumas alterações no modelo inicial. Comece instalando a CLI do GatsbyJS do seu terminal.

   ```powershell
    npm install -g gatsby
   ```

1. No terminal, navegue até a raiz do seu repositório. Ele deve conter três pastas parecidas com esta:

    ```powershell
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----        2/23/2020  10:42 PM                Application
    d-----        2/23/2020   3:05 PM                ArmTemplates
    d-----        2/23/2020   3:05 PM                Tests
    ```
    
1. Não queremos todos os arquivos na pasta do aplicativo porque vamos substituí-lo por um iniciador Gatsby. Execute os comandos a seguir, em sequência, para arrumar isso.
    
    ```powershell
    cp .\Application\Dockerfile .
    rmdir Application
    ```

1. Use a CLI do Gatsby para gerar um exemplo do PWA. Execute a `gatsby new` partir do terminal para iniciar o assistente do PWA e selecione `gatsby-starter-blog` para o modelo inicial. Ele deve ser semelhante a este exemplo:

    ```powershell
    c:\myproject> gatsby new
    √ What is your project called? ... my-gatsby-project
    ? What starter would you like to use? » - Use arrow-keys. Return to submit.
        gatsby-starter-default
        gatsby-starter-hello-world
    >   gatsby-starter-blog
        (Use a different starter)
    ```
    
1. Agora você tem uma pasta chamada `my-gatsby-project` . Renomeie- `Application` o para e copie o `Dockerfile` para ele.
    
    ```powershell
    mv my-gatsby-project Application
    mv Dockerfile Application
    ```
    
1. Em seu editor favorito, abra o Dockerfile e altere a primeira linha de `FROM node:8` para `FROM node:12` . Essa alteração garante que o contêiner esteja usando Node.js versão 12. x em vez da versão 8. x. O GatsbyJS requer versões mais modernas do Node.js.

1. Em seguida, abra o package.jsno arquivo na pasta do aplicativo e edite o [campo scripts](https://docs.npmjs.com/files/package.json#scripts) para garantir que seus servidores de desenvolvimento e produção escutem em todas as interfaces de rede disponíveis (por exemplo, 0.0.0.0) e a porta 80. Sem essas configurações, o serviço de aplicativo de contêiner não é capaz de rotear o tráfego para seu aplicativo Node.js em execução dentro do contêiner. O `scripts` campo deve ser semelhante ao que está abaixo. Especificamente, você deseja alterar os `develop` destinos, `serve` e `start` de seus padrões.

    ```json
      "scripts": {
        "build": "gatsby build",
        "develop": "gatsby develop  -H 0.0.0.0 -p 80",
        "format": "prettier --write \"**/*.{js,jsx,json,md}\"",
        "start": "npm run serve",
        "serve": "npm run build && gatsby serve -H 0.0.0.0 -p 80",
        "clean": "gatsby clean",
        "test": "echo \"Write tests! -> https://gatsby.dev/unit-testing\" && exit 1"
      }
    ```
    
## <a name="edit-your-cicd-pipelines"></a>Editar seus pipelines de CI/CD

1. Antes de confirmar o código na seção anterior, faça algumas alterações em seus pipelines de compilação e versão. Edite seu ' pipeline de compilação ' e atualize a tarefa do nó para usar Node.js versão 12. x. Defina o campo **versão da tarefa** como 1. x e o campo **versão** como 12. x.

    ![Atualizar Node.js para 12. x](_img/azure-devops-project-nodejs/build-pipeline-update-node.png)

1. Neste guia de início rápido, não estamos criando testes de unidade e estamos Desabilitando essas etapas em nosso pipeline de Build. Ao escrever testes, você pode reabilitar essas etapas. Clique com o botão direito do mouse para selecionar as tarefas rotuladas **instalar dependências de teste** e **executar testes de unidade** e desabilitá-las.

    ![Desabilitar testes de compilação](_img/azure-devops-project-nodejs/disable-build-unittests.png)

1. Edite seu pipeline de liberação.

    ![Editar o pipeline de liberação](_img/azure-devops-project-nodejs/edit-release-pipeline.png)

1. Assim como no pipeline de compilação, altere a tarefa de nó para usar 12. x e desabilite as duas tarefas de teste. Sua versão deve ser semelhante a esta captura de tela.

    ![Pipeline de liberação concluído](_img/azure-devops-project-nodejs/release-pipeline-complete.png)

1. À esquerda do navegador, vá até o arquivo **views/index.pug**.

1. Selecione **Editar**, depois faça uma alteração no cabeçalho h2.  Por exemplo, digite introdução imediatamente **com o iniciador DevOps do Azure** ou faça alguma outra alteração.

1. Selecione **Confirmar**, depois salve as alterações.

1. No navegador, acesse o painel do DevOps Starter.   
Agora você deve ver uma compilação em andamento. As alterações feitas são automaticamente compiladas e implantadas por meio de um pipeline de CI/CD.

## <a name="commit-your-changes-and-examine-the-azure-cicd-pipeline"></a>Confirmar suas alterações e examinar o pipeline do Azure CI/CD

Nas duas etapas anteriores, você adicionou um PWA Gatsby gerado ao seu repositório git e editou seus pipelines para compilar e implantar o código. Podemos confirmar o código e observá-lo em andamento por meio do pipeline de Build e versão.

1. Na raiz do repositório Git do seu projeto em um terminal, execute os seguintes comandos para enviar por push seu código para o projeto DevOps do Azure:

    ```powershell
    git add .
    git commit -m "My first Gatsby PWA"
    git push
    ```
    
1. Uma compilação é iniciada assim que é `git push` concluída. Você pode seguir o progresso no **painel DevOps do Azure**.

3. Após alguns minutos, os pipelines de build e lançamento devem ser concluídos e o PWA deve ser implantado em um contêiner. Clique no link **Ponto de extremidade do aplicativo** no painel acima e você verá um projeto inicial do Gatsby para blogs.

## <a name="clean-up-resources"></a>Limpar os recursos

Exclua o Serviço de Aplicativo do Azure e outros recursos relacionados que você criou quando eles não forem mais necessários. Use a funcionalidade **Excluir** no painel do DevOps Starter.

## <a name="next-steps"></a>Próximas etapas

Quando você configura o processo de CI/CD, os pipelines de build e de lançamento são criados automaticamente. Você pode alterar esses pipelines de build e de lançamento para atender às necessidades de sua equipe. Para saber mais sobre o pipeline de CI/CD, confira:

> [!div class="nextstepaction"]
> [Personalizar o processo de CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

