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

Neste artigo, você aprenderá como usar as tarefas internas do Azure IoT Edge para Azure Pipelines a fim de criar dois pipelines para sua solução do IoT Edge. Há quatro ações que podem ser usadas nas tarefas de Azure IoT Edge.
   - As **imagens de módulo Azure IOT Edge-Build** pegam o código da solução IOT Edge e compilam as imagens de contêiner.
   - **Azure IOT Edge-as imagens do módulo Push** enviam imagens de módulo para o registro de contêiner especificado.
   - **Azure IOT Edge-gerar o manifesto de implantação** Obtém um arquivo Deployment. Template. JSON e as variáveis e, em seguida, gera o arquivo de manifesto de implantação final IOT Edge.
   - **Azure IOT Edge-implantar em dispositivos IOT Edge** ajuda a criar implantações IOT Edge para dispositivos de IOT Edge único/vários.

## <a name="prerequisites"></a>pré-requisitos

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

1. Entre em sua organização do Azure DevOps (**https:\//dev.Azure.com/{Your Organization}/** ) e abra o projeto que contém o repositório da solução IOT Edge.

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

   * Se você quiser criar seus módulos na plataforma arm32v7 ou arm64 para contêineres do Linux, precisará [Configurar o agente auto-hospedado no Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).
    
     ![Configurar o pool de agentes de build](./media/how-to-ci-cd/configure-env.png)

5. O pipeline vem pré-configurado com um trabalho chamado **Trabalho do agente 1**. Selecione o sinal de adição ( **+** ) para adicionar três tarefas ao trabalho: **Azure IOT Edge** duas vezes, **Copie os arquivos** uma vez e **publique os artefatos de compilação** uma vez. Passe o mouse sobre o nome de cada tarefa para ver o botão **Adicionar**.

   ![Adicionar tarefa do Azure IoT Edge](./media/how-to-ci-cd/add-iot-edge-task.png)

   Quando todas as quatro tarefas forem adicionadas, seu trabalho do Agent será semelhante ao exemplo a seguir:
    
   ![Três tarefas no pipeline de build](./media/how-to-ci-cd/add-tasks.png)

6. Selecione a primeira tarefa **Azure IoT Edge** para editá-la. Essa tarefa compila todos os módulos na solução com a plataforma de destino que você especificar.

   * **Nome de exibição**: aceite as **imagens padrão do módulo de Azure IOT Edge compilação**.
   * **Ação**: aceite as imagens padrão do **módulo de compilação**. 
   * **arquivo. Template. JSON**: selecione as reticências ( **...** ) e navegue até o arquivo **Deployment. Template. JSON** no repositório que contém sua solução de IOT Edge. 
   * **Plataforma padrão**: selecione a plataforma apropriada para seus módulos com base em seu dispositivo de IOT Edge de destino. 
   * **Variáveis de saída**: as variáveis de saída incluem um nome de referência que você pode usar para configurar o caminho do arquivo no qual o arquivo Deployment. JSON será gerado. Defina um nome de referência fácil de lembrar, como **edge**. 

7. Selecione a segunda tarefa **Azure IoT Edge** para editá-la. Essa tarefa efetua push de todas as imagens de módulo para o registro de contêiner selecionado.

   * **Nome de exibição**: o nome de exibição é atualizado automaticamente quando o campo de ação é alterado. 
   * **Ação**: Use a lista suspensa para selecionar **imagens de módulo Push**. 
   * **Tipo de registro de contêiner**: selecione o tipo de registro de contêiner que você usa para armazenar suas imagens de módulo. Dependendo do tipo de registro escolhido, o formulário é alterado. Se você escolher **Registro de Contêiner do Azure**, use as listas suspensas para selecionar a assinatura do Azure e o nome do registro de contêiner. Se você escolher **Registro de Contêiner Genérico**, selecione **Novo** para criar uma conexão de serviço de registro. 
   * **arquivo. Template. JSON**: selecione as reticências ( **...** ) e navegue até o arquivo **Deployment. Template. JSON** no repositório que contém sua solução de IOT Edge. 
   * **Plataforma padrão**: selecione a mesma plataforma que as imagens de módulo criadas.

   Se você tiver vários registros de contêiner para hospedar suas imagens de módulo, você precisará duplicar essa tarefa, selecione o registro de contêiner diferente e usar **Ignorar módulos** nas configurações avançadas para ignorar as imagens que não são para esse registro específico.

8. Selecione a tarefa **copiar arquivos** para editá-la. Use essa tarefa para copiar arquivos para o diretório de preparo do artefato.

   * **Nome de exibição**: copiar arquivos para: remover pasta.
   * **Conteúdo**: Coloque duas linhas nesta seção, `deployment.template.json` e `**/module.json`. Esses dois tipos de arquivos são as entradas para gerar IoT Edge manifesto de implantação. Precisa ser copiado para a pasta de preparo do artefato e publicado para o pipeline de liberação.
   * **Pasta de destino**: Coloque a variável `$(Build.ArtifactStagingDirectory)`. Consulte [criar variáveis](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) para saber mais sobre a descrição.

9. Selecione a tarefa **Publicar artefatos de build** para editá-la. Forneça o caminho do diretório de preparo do artefato para a tarefa para que o caminho possa ser publicado no pipeline de liberação.
   
   * **Nome de exibição**: publicar artefato: soltar.
   * **Caminho para publicação**: Coloque a variável `$(Build.ArtifactStagingDirectory)`. Consulte [criar variáveis](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) para saber mais sobre a descrição.
   * **Nome do artefato**: Drop.
   * **Local de publicação do artefato**: Azure pipelines.


10. Abra a guia **Gatilhos** e marque a caixa **Ativar integração contínua**. Verifique se o branch que contém seu código está incluído.

    ![Ativar o gatilho de integração contínua](./media/how-to-ci-cd/configure-trigger.png)

11. Salve o novo pipeline de build com o botão **Salvar**.

Agora esse pipeline está configurado para ser executado automaticamente quando você efetuar push do novo código para seu repositório. A última tarefa, publicar os artefatos do pipeline, dispara um pipeline de lançamento. Acesse a próxima seção para compilar o pipeline de lançamento. 

## <a name="configure-continuous-deployment"></a>configurar uma implantação contínua
Nesta seção, crie um pipeline de lançamento configurado para ser executado automaticamente quando o pipeline de build deixar de ter artefatos e para mostrar logs de implantação no Azure Pipelines.

Criar um novo pipeline e adicionar um novo estágio 

1. Na guia **Versões**, escolha **+ Novo pipeline**. Ou, se você já tiver pipelines de lançamento, escolha o botão **+Novo** e clique em **+Novo pipeline de lançamento**.  

    ![Adicionar pipeline de lançamento](./media/how-to-ci-cd/add-release-pipeline.png)

2. Quando tiver que selecionar um modelo, escolha começar com um **Trabalho vazio**.

    ![Começar um trabalho vazio](./media/how-to-ci-cd/start-with-empty-job.png)

3. O novo pipeline de lançamento é iniciado com um estágio, chamado **Estágio 1**. Renomeie o estágio 1 para **desenvolver** e tratá-lo como um ambiente de teste. Normalmente, os pipelines de implantação contínua têm vários estágios, incluindo **desenvolvimento**, **preparo** e **produção**. Você pode criar mais com base em sua prática DevOps. Feche a janela de detalhes do estágio depois de renomeá-lo. 

4. Vincule a versão aos artefatos de build publicados pelo pipeline de build. Clique em **Adicionar** na área de artefatos.

   ![Adicionar artefatos](./media/how-to-ci-cd/add-artifacts.png)  
    
5. Em **Adicionar uma página de artefato**, selecione **Build** como o tipo de origem. Em seguida, selecione o projeto e o pipeline de build que você criou. Em seguida, selecione **Adicionar**.

   ![Adicionar um artefato de build](./media/how-to-ci-cd/add-an-artifact.png)

6. Abra os gatilhos de artefato e selecione o botão de alternância para habilitar o gatilho de implantação contínua. Agora, uma nova versão será criada sempre que um novo build estiver disponível.

   ![Configurar um gatilho de implantação contínua](./media/how-to-ci-cd/add-a-trigger.png)

7. O estágio de **desenvolvimento** é pré-configurado com um trabalho e zero tarefas. No menu pipeline, selecione **tarefas** e escolha o estágio **desenvolvimento** .  Selecione o trabalho e a contagem de tarefas para configurar as tarefas nesse estágio.

    ![Configurar tarefas de desenvolvimento](./media/how-to-ci-cd/view-stage-tasks.png)

8. No estágio de **desenvolvimento** , você deve ver um **trabalho de agente**padrão. É possível configurar detalhes sobre o trabalho do agente, mas a tarefa de implantação não diferencia a plataforma, de modo que você pode escolher **VS2017 hospedado** ou **Ubuntu 1604 hospedado** no **Pool de agente** (ou qualquer outro agente gerenciado por você). 

9. Selecione o sinal de adição ( **+** ) para adicionar duas tarefas. Pesquise e adicione **Azure IOT Edge** duas vezes.

    ![Adicionar tarefas para desenvolvimento](./media/how-to-ci-cd/add-task-qa.png)

10. Selecione a primeira tarefa de **Azure IOT Edge** e configure-a com os seguintes valores:

    * **Nome de exibição**: o nome de exibição é atualizado automaticamente quando o campo de ação é alterado. 
    * **Ação**: Use a lista suspensa para selecionar **gerar manifesto de implantação**. A alteração do valor de ação também atualiza o nome de exibição da tarefa para haver correspondência.
    * **arquivo. Template. JSON**: Coloque o caminho `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json`. O caminho é publicado a partir do pipeline de compilação.
    * **Plataforma padrão**: escolha o mesmo valor ao criar as imagens de módulo.
    * **Caminho de saída**: Coloque o caminho `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json`. Esse caminho é o arquivo de manifesto de implantação IoT Edge final.

    Essas configurações ajudam a substituir as URLs de imagem de módulo no arquivo `deployment.template.json`. O **manifesto de implantação de geração** também ajuda a substituir as variáveis pelo valor exato que você definiu no arquivo de `deployment.template.json`. No VS/VS Code, você está especificando o valor real em um arquivo de `.env`. Em Azure Pipelines, defina o valor na guia variáveis de pipeline de liberação. vá para a guia variáveis e configure o nome e o valor da seguinte maneira.

    * **ACR_ADDRESS**: seu endereço de registro de contêiner do Azure. 
    * **ACR_PASSWORD**: sua senha de registro de contêiner do Azure.
    * **ACR_USER**: seu nome de usuário do registro de contêiner do Azure.

    Se você tiver outras variáveis em seu projeto, poderá especificar o nome e o valor nessa guia. O **manifesto de implantação de geração** só pode reconhecer as variáveis em `${VARIABLE}` tipo, verifique se você está usando isso em seus arquivos de `*.template.json`.

    ![Configurar variáveis para o pipeline de liberação](./media/how-to-ci-cd/configure-variables.png)

10. Selecione a segunda tarefa de **Azure IOT Edge** e configure-a com os seguintes valores:

    * **Nome de exibição**: o nome de exibição é atualizado automaticamente quando o campo de ação é alterado. 
    * **Ação**: Use a lista suspensa para selecionar **implantar em dispositivos IOT Edge**. A alteração do valor de ação também atualiza o nome de exibição da tarefa para haver correspondência.
    * **Assinatura do Azure**: selecione a assinatura que contém o Hub IOT.
    * **Nome do Hub IOT**: selecione o Hub IOT. 
    * **Escolher dispositivo único/múltiplo**: escolha se deseja que o pipeline de liberação seja implantado em um dispositivo ou em vários dispositivos. 
      * Se quiser implantar em um único dispositivo, insira a **ID do dispositivo do IoT Edge**. 
      * Se estiver implantando em vários dispositivos, especifique a **condição de destino** do dispositivo. A condição de destino é um filtro para corresponder a um conjunto de dispositivos IoT Edge no Hub IoT. Se você quiser usar Marcas de Dispositivo como a condição, será necessário atualizar as Marcas de dispositivos correspondentes com o dispositivo gêmeo do Hub IoT. Atualize a **ID de implantação do IoT Edge** e a **prioridade de implantação do IoT Edge** nas configurações avançadas. Para saber mais sobre como criar uma implantação em vários dispositivos, confira [Entender as implantações automáticas do IoT Edge](module-deployment-monitoring.md).
    * Expanda Configurações avançadas, selecione **IOT Edge ID da implantação**, coloque a variável `$(System.TeamProject)-$(Release.EnvironmentName)`. Isso mapeia o projeto e o nome da versão com sua ID de implantação IoT Edge.

11. Selecione **Salvar** para salvar as alterações no novo pipeline de lançamento. Retorne para o modo de exibição do pipeline selecionando **Pipeline** no menu. 
    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Verificar o CI/CD do IoT Edge com os pipelines de lançamento e de build

Para disparar um trabalho de build, você pode efetuar push da confirmação do repositório do código-fonte ou dispará-lo manualmente. Nesta seção, dispare manualmente o pipeline de CI/CD para testar o funcionamento dele. Em seguida, verifique se a implantação é bem-sucedida.

1. Navegue até o pipeline de build criado no início deste artigo. 

2. Você pode disparar um trabalho de build em seu pipeline de build clicando no botão **Fila** como na captura de tela a seguir.

    ![Gatilho manual](./media/how-to-ci-cd/manual-trigger.png)

3. Selecione-o para acompanhar seu progresso. Se o pipeline de compilação for concluído com êxito, ele disparará uma liberação para o estágio de **desenvolvimento** . 

    ![Logs de build](./media/how-to-ci-cd/build-logs.png)

4. A versão de **desenvolvimento** bem-sucedida cria IOT Edge implantação para dispositivos de IOT Edge de destino.

    ![Versão para desenvolvimento](./media/how-to-ci-cd/pending-approval.png)

5. Clique em estágio de **desenvolvimento** para ver os logs de liberação.

    ![Logs de liberação](./media/how-to-ci-cd/release-logs.png)



## <a name="next-steps"></a>Próximas etapas
* IoT Edge exemplo de práticas recomendadas do DevOps no [projeto DevOps do Azure para IOT Edge](how-to-devops-project.md)
* Entenda a implantação do IoT Edge em [Noções básicas sobre implantações do IoT Edge para dispositivos únicos ou em escala](module-deployment-monitoring.md)
* Siga as etapas para criar, atualizar ou excluir uma implantação em [Implantar e monitorar os módulos do IoT Edge em larga escala](how-to-deploy-monitor.md).
