---
title: (PRETERIDO) CI/CD com Serviço de Contêiner do Azure e Swarm
description: Usar o Serviço de Contêiner do Azure com o Docker Swarm, um Registro de Contêiner do Azure e o Azure DevOps para fornecer continuamente um aplicativo .NET Core com vários contêineres
author: jcorioland
ms.service: container-service
ms.topic: conceptual
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: 11a6debe735459b617f6f93c3f67a32350dd4623
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549046"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-azure-devops-services"></a>(PRETERIDO) Pipeline de CI/CD completo para implantar um aplicativo com vários contêineres no Serviço de Contêiner do Azure com Docker Swarm usando Azure DevOps Services

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Um dos maiores desafios ao desenvolver aplicativos modernos para a nuvem é ser capaz de fornecer esses aplicativos continuamente. Neste artigo, você aprenderá a implementar uma integração contínua completa e o pipeline de implantação (CI/CD) usando o Serviço de Contêiner do Azure com Docker Swarm, Registro de Contêiner do Azure e gerenciamento do Azure Pipelines.


![Aplicativo de exemplo MyShop](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

O objetivo é fornecer esse aplicativo continuamente em um cluster Docker Swarm usando o Azure DevOps Services. A figura a seguir fornece detalhes sobre esse pipeline de entrega contínua:

![Aplicativo de exemplo MyShop](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Uma breve explicação das etapas:

1. As alterações do código são confirmadas no repositório do código-fonte (aqui, GitHub) 
1. O GitHub dispara um build no Azure DevOps Services 
1. O Azure DevOps Services obtém a versão mais recente das fontes e compila todas as imagens que compõem o aplicativo 
1. O Azure DevOps Services envia cada imagem para um registro do Docker criado usando o serviço do Registro de Contêiner do Azure 
1. O Azure DevOps Services dispara uma nova versão 
1. A versão executa alguns comandos usando o SSH no nó mestre do cluster do serviço de contêiner do Azure 
1. O Docker Swarm no cluster obtém a versão mais recente das imagens 
1. A nova versão do aplicativo é implantada usando o Docker Compose 

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, você precisa concluir as seguintes tarefas:

- [Criar um Cluster Swarm no Serviço de Contêiner do Azure](container-service-deployment.md)
- [Conectar-se ao cluster Swarm no Serviço de Contêiner do Azure](../container-service-connect.md)
- [Criar um registro de contêiner do Azure](../../container-registry/container-registry-get-started-portal.md)
- [Ter uma organização do Azure DevOps Services e o projeto criado](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Dividir o repositório GitHub para sua conta do GitHub](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Você também precisa de um computador Ubuntu (14.04 ou 16.04) com o Docker instalado. Este computador é usado pelo Azure DevOps Services durante os processos do Azure Pipelines. Uma maneira de criar essa máquina é usar a imagem disponível no Azure Marketplace. 

## <a name="step-1-configure-your-azure-devops-services-organization"></a>Etapa 1: Configurar sua organização do Azure DevOps Services 

Nesta seção, configure sua organização do Azure DevOps Services.

### <a name="configure-an-azure-devops-services-linux-build-agent"></a>Configurar um agente de build Linux do Azure DevOps Services

Para criar imagens do Docker e enviar por push essas imagens para um Registro de Contêiner do Azure de um build do Azure DevOps Services, você precisa registrar um agente Linux. Você tem estas opções de instalação:

* [Implantar um agente no Linux](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Usar o Docker para executar o agente do Azure DevOps Services](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-azure-devops-services-extension"></a>Instalar a extensão do Azure DevOps Services de integração do Docker

A Microsoft fornece uma extensão do Azure DevOps Services para trabalhar com o Docker nos processos do Azure Pipelines. Essa extensão está disponível no [Azure DevOps Services Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Clique em **Instalar** para adicionar essa extensão à sua organização do Azure DevOps Services:

![Instalar a Integração com o Docker](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

Você deverá conectar-se à sua organização do Azure DevOps Services usando suas credenciais. 

### <a name="connect-azure-devops-services-and-github"></a>Conectar-se ao Azure DevOps Services e ao GitHub

Configure uma conexão entre seu projeto do Azure DevOps Services e sua conta GitHub.

1. Em seu projeto do Azure DevOps Services, clique no ícone **Configurações** na barra de ferramentas e selecione **Serviços**.

    ![Azure DevOps Services – Conexão externa](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

1. À esquerda, clique em **Novo Ponto de Extremidade de Serviço** > **GitHub**.

    ![Azure DevOps Services – GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

1. Para autorizar o Azure DevOps Services a trabalhar com sua conta GitHub, clique em **Autorizar** e siga o procedimento na janela aberta.

    ![Azure DevOps Services – Autorizar GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-azure-devops-services-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Conectar o Azure DevOps Services ao Registro de Contêiner do Azure e ao cluster Serviço de Contêiner do Azure

As últimas etapas antes de entrar no pipeline de CI/CD são para configurar as conexões externas com seu registro do contêiner e o cluster Docker Swarm no Azure. 

1. Nas configurações **Serviços** do seu projeto do Azure DevOps Services, adicione um ponto de extremidade de serviço do tipo **Registro do Docker**. 

1. No popup aberto, digite a URL e as credenciais do registro de contêiner do Azure.

    ![Azure DevOps Services – Registro do Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

1. Para o cluster Docker Swarm, adicione um ponto de extremidade do tipo **SSH**. Então, insira as informações de conexão SSH do seu cluster Swarm.

    ![Azure DevOps Services – SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Toda a configuração é concluída agora. As próximas etapas, você criará o pipeline de CI/CD que compila e implanta o aplicativo no cluster Docker Swarm. 

## <a name="step-2-create-the-build-pipeline"></a>Etapa 2: Criar o pipeline de build

Nesta etapa, você configura um pipeline de build para seu projeto do Azure DevOps Services e define o fluxo de trabalho do build para suas imagens de contêiner

### <a name="initial-pipeline-setup"></a>Configuração inicial do pipeline

1. Para criar um pipeline de build, conecte seu projeto do Azure DevOps Services e clique em **Build e Versão**. 

1. Na seção **Definições da compilação**, clique em **+ Novo**. Selecione o modelo **Vazio**.

    ![Azure DevOps – Novo pipeline de build](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

1. Configure a nova compilação com uma fonte de repositório GitHub, marque **Integração contínua** e selecione a fila do agente na qual você registrou seu agente Linux. Clique em **Criar** para criar o pipeline de build.

    ![Azure DevOps Services – Criar pipeline de build](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

1. Na página **Definições da Compilação**, primeiro abra a guia **Repositório** e configure a compilação para usar a bifurcação do projeto MyShop que você criou nos pré-requisitos. Verifique se você selecionou *acs-docs* como o **Ramificação padrão**.

    ![Azure DevOps Services – Configuração do repositório de build](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

1. Na guia **Gatilhos**, configure a compilação a ser disparada após cada confirmação. Selecione **Integração contínua** e **Alterações de lote**.

    ![Azure DevOps Services – Configuração do gatilho de build](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Definir o fluxo de trabalho da compilação
As próximas etapas definem o fluxo de trabalho da compilação. Há cinco imagens de contêiner a compilar para o aplicativo *MyShop*. Cada imagem é compilada usando o arquivo Docker localizado nas pastas do projeto:

* ProductsApi
* Proxy
* RatingsApi
* RecommendationsApi
* ShopFront

Você precisa adicionar duas etapas do Docker para cada imagem, uma para compilar a imagem e outra para enviar a imagem no registro de contêiner do Azure. 

1. Para adicionar uma etapa no fluxo de trabalho da compilação, clique em **+ Adicionar etapa de compilação** e selecione **Docker**.

    ![Azure DevOps Services – Adicionar etapas de build](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

1. Para cada imagem, configure uma etapa que usa o comando `docker build`.

    ![Azure DevOps Services – Build do Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    Para a operação de compilação, selecione o registro de contêiner do Azure, a ação **Criar uma imagem** e o arquivo Docker que define cada imagem. Defina o **Contexto da compilação** como o diretório-raiz do arquivo Docker e defina o **Nome da Imagem**. 
    
    Como mostrado na tela anterior, inicie o nome da imagem com o URI do registro de contêiner do Azure. (Você também pode usar uma variável de compilação para parametrizar a marcação da imagem, como o identificador da compilação neste exemplo.)

1. Para cada imagem, configure uma segunda etapa que usa o comando `docker push`.

    ![Azure DevOps Services – Push do Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    Para a operação de envio, selecione o registro de contêiner do Azure, a ação **Enviar uma imagem** e insira o **Nome da Imagem** compilado na etapa anterior.

1. Depois de configurar as etapas de compilação e envio para cada uma das cinco imagens, adicione mais duas etapas do fluxo de trabalho de compilação.

    a. Uma tarefa de linha de comando que usa um script bash para substituir a ocorrência *BuildNumber* no arquivo Docker-Compose. yml pela ID de compilação atual. Consulte a tela a seguir para obter detalhes.

    ![Azure DevOps Services – Atualizar arquivo de composição](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Uma tarefa que cancela o arquivo de Composição atualizado como um artefato de compilação para que ele possa ser usado na versão. Consulte a tela a seguir para obter detalhes.

    ![Azure DevOps Services – Publicar arquivo de composição](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

1. Clique em **Salvar** e nomeie seu pipeline de build.

## <a name="step-3-create-the-release-pipeline"></a>Etapa 3: Criar o pipeline de lançamento

O Azure DevOps Services permite que você [gerencie as versões nos ambientes](https://www.visualstudio.com/team-services/release-management/). Você pode habilitar uma implantação contínua para verificar se seu aplicativo é implantado em seus ambientes diferentes (como de desenvolvimento, teste, pré-produção e produção) de forma suave. Você pode criar um novo ambiente que representa o cluster Docker Swarm do Serviço de Contêiner do Azure.

![Azure DevOps Services – Lançar para ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Configuração da versão inicial

1. Para criar um pipeline de lançamento, clique em **Versões** >  **+ Versão**

1. Para configurar a fonte do artefato, clique em **Artefatos** > **Vincular uma fonte do artefato**. Aqui, vincule esse novo pipeline de lançamento ao build definido na etapa anterior. Fazendo isso, o arquivo docker-compose.yml fica disponível no processo da versão.

    ![Azure DevOps Services – Lançar artefatos](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

1. Para configurar o gatilho da versão, clique em **Gatilhos** e selecione **Implantação Contínua**. Defina o gatilho na mesma fonte do artefato. Essa configuração garante que uma nova versão comece assim que a compilação é concluída com êxito.

    ![Azure DevOps Services – Lançar gatilhos](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Definir o fluxo de trabalho da versão

A versão do fluxo de trabalho é composta de duas tarefas que você adiciona.

1. Configure uma tarefa para copiar com segurança o arquivo de composição para uma pasta de *implantação* no nó mestre Docker Swarm usando a conexão SSH configurada anteriormente. Consulte a tela a seguir para obter detalhes.

    ![Azure DevOps Services – Lançar SCP](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

1. Configure uma segunda tarefa para executar um comando bash e os comandos `docker` e `docker-compose` no nó mestre. Consulte a tela a seguir para obter detalhes.

    ![Azure DevOps Services – Lançar Bash](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    O comando executado no mestre usa a CLI do Docker e a CLI do Docker Compose para realizar as seguintes tarefas:

   - Logon no registro de contêiner do Azure (usa três variáveis de compilação definidas na guia **Variáveis**)
   - Definir a variável **DOCKER_HOST** para trabalhar com o ponto de extremidade Swarm (:2375)
   - Navegue até a pasta de *implantação* criada pela tarefa da cópia de segurança anterior e que contém o arquivo docker-compose.yml 
   - Execute os comandos `docker-compose` que recebem as novas imagens, interrompem e removem os serviços, e criam os contêineres.

     >[!IMPORTANT]
     > Como mostrado na tela anterior, deixe a caixa de seleção **Falha no STDERR** desmarcada. Esta é uma configuração importante, porque `docker-compose` imprime várias mensagens de diagnóstico, como os contêineres estão parando ou sendo excluídos, na saída de erro padrão. Se você marcar a caixa de seleção, o Azure DevOps Services informará que ocorreram erros durante a versão, mesmo que tudo tenha corrido bem.
     >
1. Salve o novo pipeline de lançamento.


>[!NOTE]
>Essa implantação inclui um tempo de inatividade porque estamos interrompendo os antigos serviços e executando novos. É possível evitar isso fazendo uma implantação com redução da inatividade.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Etapa 4. Testar o pipeline de CI/CD

Agora que você concluiu a configuração, é hora de testar esse novo pipeline de CI/CD. A maneira mais fácil de testá-lo é atualizar o código-fonte e confirmar as alterações no repositório GitHub. Alguns segundos depois de enviar por push o código, você verá um novo build em execução no Azure DevOps Services. Depois de concluído com êxito, uma nova versão será disparada e implantará a nova versão do aplicativo no cluster Serviço de Contêiner do Azure.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre CI/CD com Azure DevOps Services, consulte o artigo [Azure pipelines documentação](/azure/devops/pipelines/?view=azure-devops) .
