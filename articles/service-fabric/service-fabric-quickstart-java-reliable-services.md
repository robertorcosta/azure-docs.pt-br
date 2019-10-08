---
title: 'Início Rápido: Criar um aplicativo Java no Azure Service Fabric'
description: Neste guia de início rápido, você cria um aplicativo Java para o Azure usando um aplicativo de exemplo dos serviços confiáveis do Service Fabric.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/29/2019
ms.author: suhuruli
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 4ef0db5f72f5849942bb043261f1166cf7c046b1
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703290"
---
# <a name="quickstart--deploy-a-java-app-to-azure-service-fabric-on-linux"></a>Início Rápido:  Implantar um aplicativo Java no Azure Service Fabric no Linux

Este guia de início rápido mostra como implantar seu primeiro aplicativo Java para o Azure Service Fabric usando o IDE do Eclipse em um computador de desenvolvedor Linux. Ao terminar, você terá um aplicativo de votação com um front-end da Web em Java que salva os resultados da votação em um serviço de back-end com estado do cluster.

O Azure Service Fabric é uma plataforma de sistemas distribuídos para implantação e gerenciamento de contêineres e microsserviços.

![Amostra de votação do Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-voting-sample.png)

Neste guia de início rápido, você aprende a:

* Usar o Eclipse como uma ferramenta para seus aplicativos Java do Service Fabric
* Implantar o aplicativo no cluster local
* Expandir o aplicativo para vários nós

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

1. [Instalar o SDK do Service Fabric e a CLI (interface de linha de comando) do Service Fabric (CLI)](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [Instalar o Git](https://git-scm.com/)
3. [Instalar o Eclipse](https://www.eclipse.org/downloads/)
4. [Configurar o Ambiente Java](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development), seguindo as etapas opcionais para instalar o plug-in do Eclipse

## <a name="download-the-sample"></a>Baixar o exemplo

Em uma janela de comando, execute o comando a seguir para clonar o repositório de aplicativos de exemplo no computador local.

```git
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>Executar o aplicativo localmente

1. Inicie seu cluster local executando o seguinte comando:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    A inicialização do cluster local leva algum tempo. Para confirmar que o cluster está totalmente funcional, acesse o Service Fabric Explorer em **http://localhost:19080** . Os cinco nós íntegros indicam que o cluster local está em execução.

    ![O Azure Service Fabric Explorer mostra nós íntegros](./media/service-fabric-quickstart-java/service-fabric-explorer-healthy-nodes.png)

2. Abra o Eclipse.
3. Selecione **Arquivo** > **Importar** > **Gradle** > **Projeto Gradle Existente** e siga o assistente.
4. Selecione **Diretório** e escolha o diretório `Voting` na pasta `service-fabric-java-quickstart` clonada do GitHub. Selecione **Concluir**.

    ![Importar o projeto do Gradle para o Eclipse](./media/service-fabric-quickstart-java/eclipse-import-gradle-project.png)

5. Agora você tem o projeto `Voting` no Explorador de Pacote para o Eclipse.
6. Clique com o botão direito do mouse no projeto e selecione **Publicar Aplicativo** na lista suspensa **Service Fabric**. Escolha **PublishProfiles/Local.json** como o Perfil de Destino e selecione **Publicar**.

    ![Publicação de JSON local do Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-publish-local-json.png)

7. Abra seu navegador da Web favorito e acesse o aplicativo em `http://localhost:8080`.

    ![Host local do Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-local-host.png)

Agora, você pode adicionar um conjunto de opções de votação e começar a votar. O aplicativo é executado e armazena todos os dados no cluster do Service Fabric, sem a necessidade de um banco de dados separado.

## <a name="scale-applications-and-services-in-a-cluster"></a>Dimensionar aplicativos e serviços em um cluster

Os serviços do Service Fabric podem ser facilmente colocados em escala em um cluster para acomodar uma alteração na carga dos serviços. Dimensione um serviço alterando o número de instâncias em execução no cluster. Existem várias maneiras de colocar seus serviços em escala, por exemplo, usando scripts ou comandos da CLI do Service Fabric (sfctl). As etapas a seguir usam o Service Fabric Explorer.

O Service Fabric Explorer é executado em todos os clusters do Service Fabric e pode ser acessado em um navegador, navegando para a porta de gerenciamento HTTP do cluster (19080), por exemplo, `http://localhost:19080`.

Para dimensionar o serviço de front-end da Web, faça o seguinte:

1. Abra o Service Fabric Explorer no cluster – por exemplo, `https://localhost:19080`.
2. Selecione as reticências ( **...** ) ao lado do nó **fabric:/Voting/VotingWeb** no modo de exibição de árvore e selecione **Dimensionar Serviço**.

    ![Dimensionar um serviço no Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-scale-service.png)

    Agora, você pode optar por dimensionar o número de instâncias do serviço de front-end da Web.

3. Altere o número para **2** e selecione **Dimensionar Serviço**.
4. Selecione o nó **fabric:/Voting/VotingWeb** do modo de exibição de árvore e expanda o nó de partição (representado por um GUID).

    ![Serviço dimensionado no Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-explorer-service-scaled.png)

    Agora, você pode ver que o serviço tem duas instâncias e no modo de exibição de árvore, você vê em quais nós as instâncias são executadas.

Com essa tarefa de gerenciamento simples, você dobrou o número de recursos disponíveis para o serviço de front-end processar a carga do usuário. É importante entender que você não precisa de várias instâncias de um serviço para que ele seja executado de forma confiável. Se um serviço falhar, o Service Fabric fará com que uma nova instância de serviço seja executada no cluster.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

* Usar o Eclipse como uma ferramenta para seus aplicativos Java do Service Fabric
* Implantar aplicativos Java no cluster local
* Expandir o aplicativo para vários nós

Para saber mais sobre como trabalhar com aplicativos Java no Service Fabric, continue o tutorial para os aplicativos Java.

> [!div class="nextstepaction"]
> [Implantar um aplicativo Java](./service-fabric-tutorial-create-java-app.md)
