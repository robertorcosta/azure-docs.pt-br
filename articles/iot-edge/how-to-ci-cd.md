---
title: Integração contínua e implantação contínua – Azure IoT Edge | Microsoft Docs
description: Configurar a integração contínua e a implantação contínua – Azure IoT Edge com Azure DevOps, Azure Pipelines
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 85f77d1132af63681ee92cfd2bde82a71d8ed999
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457248"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Integração contínua e implantação contínua no Azure IoT Edge

Você pode facilmente adotar o DevOps com seus aplicativos do Azure IoT Edge com as tarefas internas do Azure IoT Edge no Azure Pipelines. Este artigo demonstra como você pode usar a integração contínua e recursos de implantação contínua de Azure Pipelines para criar, testar e implantar aplicativos de forma rápida e eficiente para o Azure IoT Edge. 

![Diagrama – ramificações de CI e CD para desenvolvimento e produção](./media/how-to-ci-cd/cd.png)

Neste artigo, você aprenderá como usar as tarefas internas do Azure IoT Edge para Azure Pipelines a fim de criar dois pipelines para sua solução do IoT Edge. There are four actions can be used in the Azure IoT Edge tasks.
   - **Azure IoT Edge - Build Module images** takes your IoT Edge solution code and builds the container images.
   - **Azure IoT Edge - Push Module images** pushes module images to the container registry you specified.
   - **Azure IoT Edge - Generate Deployment Manifest** takes a deployment.template.json file and the variables, then generates the final IoT Edge deployment manifest file.
   - **Azure IoT Edge - Deploy to IoT Edge devices** helps create IoT Edge deployments to single/multiple IoT Edge devices.

## <a name="prerequisites"></a>Pré-requisitos

* Um repositório do Azure Repos. Se você não tem um, [crie um novo repositório Git no seu projeto](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav).
* Uma solução do IoT Edge confirmada e enviada para seu repositório. Se você quiser criar uma nova solução de amostra para testar este artigo, siga as etapas em [Desenvolver e depurar módulos no Visual Studio Code](how-to-vs-code-develop-module.md) ou [Desenvolver e depurar módulos C# no Visual Studio](how-to-visual-studio-develop-csharp-module.md).
   * Para este artigo, você só precisa da pasta da solução criada pelos modelos do IoT Edge no Visual Studio Code ou no Visual Studio. Não é necessário compilar, efetuar push, implantar ou depurar esse código antes de continuar. Você configura esses processos no Azure Pipelines. 
   * Se você está criando uma nova solução, clone o repositório localmente primeiro. Depois, ao criar a solução, escolha criá-la diretamente na pasta do repositório. É possível confirmar e efetuar push dos novos arquivos facilmente por lá. 
* Um registro de contêiner em que você possa efetuar push de imagens de módulo. Você pode usar um [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) ou um registro de terceiros. 
* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) ativo com dispositivos do IoT Edge, pelo menos, para testar as fases separadas de implantação de teste e produção. Você pode seguir os artigos de início rápido para criar um dispositivo do IoT Edge no [Linux](quickstart-linux.md) ou [Windows](quickstart.md)


Para saber mais sobre como usar o Azure Repos, confira [Compartilhar seu código com o Visual Studio e o Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts).

## <a name="configure-continuous-integration"></a>Configurar a integração contínua
Nesta seção, você criará um novo pipeline de build. Configure o pipeline para ser executado automaticamente quando você fizer check-in de alterações na solução de amostra do IoT Edge e para publicar logs de build.

>[!NOTE]
>Este artigo usa o designer visual do Azure DevOps. Antes de seguir as etapas desta seção, desative versão prévia do recurso da nova experiência de criação de pipeline do YAML. 
>1. No Azure DevOps, selecione o ícone do seu perfil e a **Versão prévia do recurso**.
>2. Desative **Nova experiência de criação de pipeline YAML**. 
>
>Para saber mais, confira [Criar um pipeline de build](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline).

1. Sign into your Azure DevOps organization (**https:\//dev.azure.com/{your organization}/** ) and open the project that contains your IoT Edge solution repository.

   Neste artigo, criamos um repositório chamado **IoTEdgeRepo**. Esse repositório contém **IoTEdgeSolution**, que tem o código para um módulo denominado **filtermodule**. 

   ![Abrir seu projeto do DevOps](./media/how-to-ci-cd/init-project.png)

2. Navegue até Azure Pipelines em seu projeto. Abra a guia **Builds** e selecione **Novo pipeline**. Ou, se você já tem um pipelines de build, selecione o botão **Novo**. Em seguida, escolha **Novo pipeline de build**.

    ![Criar um novo pipeline de build](./media/how-to-ci-cd/add-new-build.png)

3. Siga os prompts para criar o pipeline. 

   1. Forneça as informações de origem para o novo pipeline de build. Selecione **Git do Azure Repos** como a origem, depois selecione o projeto, o repositório e o branch em que se encontra o código da solução do IoT Edge. Em seguida, selecione **Continuar**. 

      ![Selecionar a fonte do pipeline](./media/how-to-ci-cd/pipeline-source.png)

   2. Selecione **Trabalho vazio** ao invés de um modelo. 

      ![Comece com um processo vazio](./media/how-to-ci-cd/start-with-empty.png)

4. Após a criação do pipeline, você será direcionado para o editor de pipeline. Na descrição do pipeline, escolha o pool de agentes correto com base em sua plataforma de destino: 
    
   * Se você quiser compilar os módulos na plataforma amd64 para contêineres do Linux, escolha **Ubuntu 1604 hospedado**

   * Se quiser criar os módulos na plataforma amd64 para contêineres do Windows 1809, você precisará [configurar o agente auto-hospedado no Windows](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

   * If you would like to build your modules in platform arm32v7 or arm64 for Linux containers, you need to [set up self-hosted agent on Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).
    
     ![Configurar o pool de agentes de build](./media/how-to-ci-cd/configure-env.png)

5. O pipeline vem pré-configurado com um trabalho chamado **Trabalho do agente 1**. Select the plus sign ( **+** ) to add three tasks to the job: **Azure IoT Edge** twice, **Copy Files** once and **Publish Build Artifacts** once. Passe o mouse sobre o nome de cada tarefa para ver o botão **Adicionar**.

   ![Adicionar tarefa do Azure IoT Edge](./media/how-to-ci-cd/add-iot-edge-task.png)

   When all four tasks are added, your Agent job looks like the following example:
    
   ![Três tarefas no pipeline de build](./media/how-to-ci-cd/add-tasks.png)

6. Selecione a primeira tarefa **Azure IoT Edge** para editá-la. This task builds all modules in the solution with the target platform that you specify.

   * **Display name**: Accept the default **Azure IoT Edge - Build module images**.
   * **Action**: Accept the default **Build module images**. 
   * **.template.json file**: Select the ellipsis ( **...** ) and navigate to the **deployment.template.json** file in the repository that contains your IoT Edge solution. 
   * **Default platform**: Select the appropriate platform for your modules based on your target IoT Edge device. 
   * **Output variables**: The output variables include a reference name that you can use to configure the file path where your deployment.json file will be generated. Defina um nome de referência fácil de lembrar, como **edge**. 

7. Selecione a segunda tarefa **Azure IoT Edge** para editá-la. Essa tarefa efetua push de todas as imagens de módulo para o registro de contêiner selecionado.

   * **Display name**: The display name is automatically updated when the action field changes. 
   * **Action**: Use the dropdown list to select **Push module images**. 
   * **Container registry type**: Select the type of container registry that you use to store your module images. Dependendo do tipo de registro escolhido, o formulário é alterado. Se você escolher **Registro de Contêiner do Azure**, use as listas suspensas para selecionar a assinatura do Azure e o nome do registro de contêiner. Se você escolher **Registro de Contêiner Genérico**, selecione **Novo** para criar uma conexão de serviço de registro. 
   * **.template.json file**: Select the ellipsis ( **...** ) and navigate to the **deployment.template.json** file in the repository that contains your IoT Edge solution. 
   * **Default platform**: Select the same platform as your built module images.

   Se você tiver vários registros de contêiner para hospedar suas imagens de módulo, você precisará duplicar essa tarefa, selecione o registro de contêiner diferente e usar **Ignorar módulos** nas configurações avançadas para ignorar as imagens que não são para esse registro específico.

8. Select the **Copy Files** task to edit it. Use this task to copy files to the artifact staging directory.

   * **Display name**: Copy Files to: Drop folder.
   * **Contents**: Put two lines in this section, `deployment.template.json` and `**/module.json`. These two types of files are the inputs to generate IoT Edge deployment manifest. Need to be copied to the artifact staging folder and published for release pipeline.
   * **Target Folder**: Put the variable `$(Build.ArtifactStagingDirectory)`. See [Build variables](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) to learn about the description.

9. Selecione a tarefa **Publicar artefatos de build** para editá-la. Provide artifact staging directory path to the task so that the path can be published to release pipeline.
   
   * **Display name**: Publish Artifact: drop.
   * **Path to publish**: Put the variable `$(Build.ArtifactStagingDirectory)`. See [Build variables](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) to learn about the description.
   * **Artifact name**: drop.
   * **Artifact publish location**: Azure Pipelines.


10. Abra a guia **Gatilhos** e marque a caixa **Ativar integração contínua**. Verifique se o branch que contém seu código está incluído.

    ![Ativar o gatilho de integração contínua](./media/how-to-ci-cd/configure-trigger.png)

11. Salve o novo pipeline de build com o botão **Salvar**.

Agora esse pipeline está configurado para ser executado automaticamente quando você efetuar push do novo código para seu repositório. A última tarefa, publicar os artefatos do pipeline, dispara um pipeline de lançamento. Acesse a próxima seção para compilar o pipeline de lançamento. 

## <a name="configure-continuous-deployment"></a>configurar uma implantação contínua
Nesta seção, crie um pipeline de lançamento configurado para ser executado automaticamente quando o pipeline de build deixar de ter artefatos e para mostrar logs de implantação no Azure Pipelines.

Create a new pipeline, and add a new stage 

1. Na guia **Versões**, escolha **+ Novo pipeline**. Ou, se você já tiver pipelines de lançamento, escolha o botão **+Novo** e clique em **+Novo pipeline de lançamento**.  

    ![Adicionar pipeline de lançamento](./media/how-to-ci-cd/add-release-pipeline.png)

2. Quando tiver que selecionar um modelo, escolha começar com um **Trabalho vazio**.

    ![Começar um trabalho vazio](./media/how-to-ci-cd/start-with-empty-job.png)

3. O novo pipeline de lançamento é iniciado com um estágio, chamado **Estágio 1**. Rename Stage 1 to **dev** and treat it as a test environment. Usually, continuous deployment pipelines have multiple stages including **dev**, **staging** and **prod**. You can create more based on your DevOps practice. Feche a janela de detalhes do estágio depois de renomeá-lo. 

4. Vincule a versão aos artefatos de build publicados pelo pipeline de build. Clique em **Adicionar** na área de artefatos.

   ![Adicionar artefatos](./media/how-to-ci-cd/add-artifacts.png)  
    
5. Em **Adicionar uma página de artefato**, selecione **Build** como o tipo de origem. Em seguida, selecione o projeto e o pipeline de build que você criou. Em seguida, selecione **Adicionar**.

   ![Adicionar um artefato de build](./media/how-to-ci-cd/add-an-artifact.png)

6. Abra os gatilhos de artefato e selecione o botão de alternância para habilitar o gatilho de implantação contínua. Agora, uma nova versão será criada sempre que um novo build estiver disponível.

   ![Configurar um gatilho de implantação contínua](./media/how-to-ci-cd/add-a-trigger.png)

7. The **dev** stage is preconfigured with one job and zero tasks. From the pipeline menu, select **Tasks** then choose the **dev** stage.  Selecione o trabalho e a contagem de tarefas para configurar as tarefas nesse estágio.

    ![Configure dev tasks](./media/how-to-ci-cd/view-stage-tasks.png)

8. In the **dev** stage, you should see a default **Agent job**. É possível configurar detalhes sobre o trabalho do agente, mas a tarefa de implantação não diferencia a plataforma, de modo que você pode escolher **VS2017 hospedado** ou **Ubuntu 1604 hospedado** no **Pool de agente** (ou qualquer outro agente gerenciado por você). 

9. Select the plus sign ( **+** ) to add two task. Search for and add **Azure IoT Edge** twice.

    ![Add tasks for dev](./media/how-to-ci-cd/add-task-qa.png)

10. Select the first **Azure IoT Edge** task and configure it with the following values:

    * **Display name**: The display name is automatically updated when the action field changes. 
    * **Action**: Use the dropdown list to select **Generate deployment manifest**. A alteração do valor de ação também atualiza o nome de exibição da tarefa para haver correspondência.
    * **.template.json file**: Put the path `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json`. The path is published from build pipeline.
    * **Default platform**: Choose the same value when building the module images.
    * **Output path**: Put the path `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json`. This path is the final IoT Edge deployment manifest file.

    These configurations helps replace the module image URLs in the `deployment.template.json` file. The **Generate deployment manifest** also helps replace the variables with the exact value you defined in the `deployment.template.json` file. In VS/VS Code, you are specifying the actual value in a `.env` file. In Azure Pipelines, you set the value in Release Pipeline Variables tab. Move to Variables tab and configure the Name and Value as following.

    * **ACR_ADDRESS**: Your Azure Container Registry address. 
    * **ACR_PASSWORD**: Your Azure Container Registry password.
    * **ACR_USER**: Your Azure Container Registry username.

    If you have other variables in your project, you can specify the name and value in this tab. The **Generate deployment manifest** can only recognize the variables are in `${VARIABLE}` flavor, make sure you are using this in your `*.template.json` files.

    ![Configure variables for release pipeline](./media/how-to-ci-cd/configure-variables.png)

10. Select the second **Azure IoT Edge** task and configure it with the following values:

    * **Display name**: The display name is automatically updated when the action field changes. 
    * **Action**: Use the dropdown list to select **Deploy to IoT Edge devices**. A alteração do valor de ação também atualiza o nome de exibição da tarefa para haver correspondência.
    * **Azure subscription**: Select the subscription that contains your IoT Hub.
    * **IoT Hub name**: Select your IoT hub. 
    * **Choose single/multiple device**: Choose whether you want the release pipeline to deploy to one device or multiple devices. 
      * Se quiser implantar em um único dispositivo, insira a **ID do dispositivo do IoT Edge**. 
      * Se estiver implantando em vários dispositivos, especifique a **condição de destino** do dispositivo. The target condition is a filter to match a set of IoT Edge devices in IoT Hub. Se você quiser usar Marcas de Dispositivo como a condição, será necessário atualizar as Marcas de dispositivos correspondentes com o dispositivo gêmeo do Hub IoT. Atualize a **ID de implantação do IoT Edge** e a **prioridade de implantação do IoT Edge** nas configurações avançadas. Para saber mais sobre como criar uma implantação em vários dispositivos, confira [Entender as implantações automáticas do IoT Edge](module-deployment-monitoring.md).
    * Expand Advanced Settings, select **IoT Edge deployment ID**, put the variable `$(System.TeamProject)-$(Release.EnvironmentName)`. This maps the project and release name with your IoT Edge deployment ID.

11. Selecione **Salvar** para salvar as alterações no novo pipeline de lançamento. Retorne para o modo de exibição do pipeline selecionando **Pipeline** no menu. 
    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Verificar o CI/CD do IoT Edge com os pipelines de lançamento e de build

Para disparar um trabalho de build, você pode efetuar push da confirmação do repositório do código-fonte ou dispará-lo manualmente. Nesta seção, dispare manualmente o pipeline de CI/CD para testar o funcionamento dele. Em seguida, verifique se a implantação é bem-sucedida.

1. Navegue até o pipeline de build criado no início deste artigo. 

2. Você pode disparar um trabalho de build em seu pipeline de build clicando no botão **Fila** como na captura de tela a seguir.

    ![Gatilho manual](./media/how-to-ci-cd/manual-trigger.png)

3. Selecione-o para acompanhar seu progresso. If the build pipeline is completed successfully, it triggers a release to **dev** stage. 

    ![Logs de build](./media/how-to-ci-cd/build-logs.png)

4. The successful **dev** release creates IoT Edge deployment to target IoT Edge devices.

    ![Release to dev](./media/how-to-ci-cd/pending-approval.png)

5. Click **dev** stage to see release logs.

    ![Release logs](./media/how-to-ci-cd/release-logs.png)



## <a name="next-steps"></a>Próximos passos
* IoT Edge DevOps best practices sample in [Azure DevOps Project for IoT Edge](how-to-devops-project.md)
* Entenda a implantação do IoT Edge em [Noções básicas sobre implantações do IoT Edge para dispositivos únicos ou em escala](module-deployment-monitoring.md)
* Siga as etapas para criar, atualizar ou excluir uma implantação em [Implantar e monitorar os módulos do IoT Edge em larga escala](how-to-deploy-monitor.md).
