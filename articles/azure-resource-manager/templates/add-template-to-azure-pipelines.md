---
title: CI/CD com Pipelines e modelos azure
description: Descreve como configurar a integração contínua no Azure Pipelines usando projetos de implantação do Azure Resource Group no Visual Studio para implantar modelos de Gerenciador de recursos.
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: d8eff1c7efae319106eb8a85af7823a820a0da39
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084644"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>Integre modelos ARM com pipelines azure

O Visual Studio fornece o projeto do Azure Resource Group para criar modelos ARM (Resource Manager) do Azure e implantá-los na assinatura do Azure. Você pode integrar este projeto com o Azure Pipelines para integração contínua e implantação contínua (CI/CD).

Existem duas maneiras de implantar modelos com o Azure Pipelines:

* **Adicionar tarefa que executa um script Azure PowerShell**. Essa opção tem a vantagem de fornecer consistência durante todo o ciclo de vida do desenvolvimento porque você usa o mesmo script que está incluído no projeto do Visual Studio (Deploy-AzureResourceGroup.ps1). O script encena artefatos do seu projeto para uma conta de armazenamento que o Resource Manager pode acessar. Artefatos são itens do seu projeto, como modelos vinculados, scripts e binários de aplicativos. Em seguida, o script implanta o modelo.

* **Adicione tarefas para copiar e implantar tarefas**. Esta opção oferece uma alternativa conveniente ao script do projeto. Você configura duas tarefas no pipeline. Uma tarefa encena os artefatos e a outra tarefa implanta o modelo.

Este artigo mostra as duas abordagens.

## <a name="prepare-your-project"></a>Preparar seu projeto

Este artigo pressupõe que seu projeto Visual Studio e a organização Azure DevOps estão prontos para criar o pipeline. As etapas a seguir mostram como ter certeza de que você está pronto:

* Você tem uma organização Azure DevOps. Se você não tem um, [crie um de graça.](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops) Se sua equipe já possui uma organização Azure DevOps, certifique-se de que você é um administrador do projeto Azure DevOps que você deseja usar.

* Você configurou uma [conexão de serviço](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) para sua assinatura do Azure. As tarefas no pipeline são executadas sob a identidade do diretor de serviço. Para obter etapas para criar a conexão, consulte [Criar um projeto DevOps](deployment-tutorial-pipeline.md#create-a-devops-project).

* Você tem um projeto do Visual Studio que foi criado a partir do modelo inicial do **Azure Resource Group.** Para obter informações sobre como criar esse tipo de projeto, consulte [Criar e implantar grupos de recursos do Azure através do Visual Studio](create-visual-studio-deployment-project.md).

* Seu projeto Visual Studio está [conectado a um projeto Azure DevOps](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops).

## <a name="create-pipeline"></a>Criar um pipeline

1. Se você não adicionou um pipeline anteriormente, você precisa criar um novo pipeline. A partir de sua organização Azure DevOps, selecione **Pipelines** e **New pipeline**.

   ![Adicionar novo pipeline](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. Especifique onde seu código está armazenado. A imagem a seguir mostra a seleção **do Azure Repos Git**.

   ![Selecione a fonte de código](./media/add-template-to-azure-pipelines/select-source.png)

1. A partir dessa fonte, selecione o repositório que tem o código para o seu projeto.

   ![Selecione o repositório](./media/add-template-to-azure-pipelines/select-repo.png)

1. Selecione o tipo de pipeline a ser criado. Você pode selecionar **o pipeline Starter**.

   ![Selecionar pipeline](./media/add-template-to-azure-pipelines/select-pipeline.png)

Você está pronto para adicionar uma tarefa do Azure PowerShell ou o arquivo de cópia e implantar tarefas.

## <a name="azure-powershell-task"></a>Tarefa Do Azure PowerShell

Esta seção mostra como configurar a implantação contínua usando uma única tarefa que executa o script PowerShell em seu projeto. O seguinte arquivo YAML cria [uma tarefa do Azure PowerShell:](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops)

```yaml
pool:
  name: Hosted Windows 2019 with VS2019
  demands: azureps

steps:
- task: AzurePowerShell@3
  inputs:
    azureSubscription: 'demo-deploy-sp'
    ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
    ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus'
    azurePowerShellVersion: LatestVersion
```

Quando você define `AzurePowerShell@3`a tarefa para , o pipeline usa comandos do módulo AzureRM para autenticar a conexão. Por padrão, o script PowerShell no projeto Visual Studio usa o módulo AzureRM. Se você atualizou seu script para usar o módulo `AzurePowerShell@4` [Az,](/powershell/azure/new-azureps-module-az)defina a tarefa para .

```yaml
steps:
- task: AzurePowerShell@4
```

Para `azureSubscription`, fornecer o nome da conexão de serviço que você criou.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

Para `scriptPath`, fornecer o caminho relativo do arquivo pipeline para o seu script. Você pode olhar em seu repositório para ver o caminho.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Se você não precisar encenar artefatos, basta passar o nome e a localização de um grupo de recursos para usar para implantação. O script do Visual Studio cria o grupo de recursos se ele ainda não existir.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Se você precisar encenar artefatos para uma conta de armazenamento existente, use:

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

Agora, que você entenda como criar a tarefa, vamos passar pelas etapas para editar o pipeline.

1. Abra seu pipeline e substitua o conteúdo pelo seu YAML:

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019
     demands: azureps

   steps:
   - task: AzurePowerShell@3
     inputs:
       azureSubscription: 'demo-deploy-sp'
       ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
       ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName 'stage3a4176e058d34bb88cc'
       azurePowerShellVersion: LatestVersion
   ```

1. Clique em **Salvar**.

   ![Salve o pipeline](./media/add-template-to-azure-pipelines/save-pipeline.png)

1. Forneça uma mensagem para o compromisso e comprometa-se diretamente com o **mestre**.

1. Quando você **seleciona Salvar,** o pipeline de compilação é executado automaticamente. Volte para o resumo do seu pipeline de construção, e observe o status.

   ![Exibir os resultados](./media/add-template-to-azure-pipelines/view-results.png)

Você pode selecionar o pipeline em execução no momento para ver detalhes sobre as tarefas. Quando termina, você vê os resultados de cada etapa.

## <a name="copy-and-deploy-tasks"></a>Copiar e implantar tarefas

Esta seção mostra como configurar a implantação contínua usando duas tarefas para encenar os artefatos e implantar o modelo.

O YAML a seguir mostra a [tarefa de cópia de arquivo Do Azure:](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)

```yaml
- task: AzureFileCopy@3
  displayName: 'Stage files'
  inputs:
    SourcePath: 'AzureResourceGroup1'
    azureSubscription: 'demo-deploy-sp'
    Destination: 'AzureBlob'
    storage: 'stage3a4176e058d34bb88cc'
    ContainerName: 'democontainer'
    outputStorageUri: 'artifactsLocation'
    outputStorageContainerSasToken: 'artifactsLocationSasToken'
    sasTokenTimeOutInMinutes: '240'
```

Existem várias partes desta tarefa para revisar para o seu ambiente. O `SourcePath` indica a localização dos artefatos relativos ao arquivo do oleoduto. Neste exemplo, os arquivos existem `AzureResourceGroup1` em uma pasta nomeada que era o nome do projeto.

```yaml
SourcePath: '<path-to-artifacts>'
```

Para `azureSubscription`, fornecer o nome da conexão de serviço que você criou.

```yaml
azureSubscription: '<your-connection-name>'
```

Para armazenamento e nome do contêiner, forneça os nomes da conta de armazenamento e do contêiner que você deseja usar para armazenar os artefatos. A conta de armazenamento deve existir.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

O YAML a seguir mostra a [tarefa de implantação do modelo do Azure Resource Manager:](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    deploymentScope: 'Resource Group'
    ConnectedServiceName: 'demo-deploy-sp'
    subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'Central US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
    deploymentMode: 'Incremental'
```

Existem várias partes desta tarefa para revisar para o seu ambiente.

- `deploymentScope`: Selecione o escopo de `Management Group` `Subscription` implantação `Resource Group`das opções: e . Use **o Resource Group** nesta caminhada. Para saber mais sobre os escopos, confira [Escopos de implantação](deploy-rest.md#deployment-scope).

- `ConnectedServiceName`: Forneça o nome da conexão de serviço que você criou.

    ```yaml
    ConnectedServiceName: '<your-connection-name>'
    ```

- `subscriptionName`: Forneça o ID de assinatura de destino. Essa propriedade só se aplica ao escopo de implantação do Grupo de Recursos e ao escopo de implantação da assinatura.

- `resourceGroupName`e: `location`fornecer o nome e a localização do grupo de recursos para o que deseja implantar. A tarefa cria o grupo de recursos se ele não existir.

    ```yaml
    resourceGroupName: '<resource-group-name>'
    location: '<location>'
    ```

A tarefa de implantação `WebSite.json` é vinculado a um modelo nomeado e a um arquivo de parâmetros chamado WebSite.parameters.json. Use os nomes do seu modelo e arquivos de parâmetros.

Agora, que você entenda como criar as tarefas, vamos passar pelas etapas para editar o pipeline.

1. Abra seu pipeline e substitua o conteúdo pelo seu YAML:

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019

   steps:
   - task: AzureFileCopy@3
     displayName: 'Stage files'
     inputs:
       SourcePath: 'AzureResourceGroup1'
       azureSubscription: 'demo-deploy-sp'
       Destination: 'AzureBlob'
       storage: 'stage3a4176e058d34bb88cc'
       ContainerName: 'democontainer'
       outputStorageUri: 'artifactsLocation'
       outputStorageContainerSasToken: 'artifactsLocationSasToken'
       sasTokenTimeOutInMinutes: '240'
    - task: AzureResourceGroupDeployment@2
      displayName: 'Deploy template'
      inputs:
        deploymentScope: 'Resource Group'
        ConnectedServiceName: 'demo-deploy-sp'
        subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
        action: 'Create Or Update Resource Group'
        resourceGroupName: 'demogroup'
        location: 'Central US'
        templateLocation: 'URL of the file'
        csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
        csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
        overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
        deploymentMode: 'Incremental'
   ```

1. Clique em **Salvar**.

1. Forneça uma mensagem para o compromisso e comprometa-se diretamente com o **mestre**.

1. Quando você **seleciona Salvar,** o pipeline de compilação é executado automaticamente. Volte para o resumo do seu pipeline de construção, e observe o status.

   ![Exibir os resultados](./media/add-template-to-azure-pipelines/view-results.png)

Você pode selecionar o pipeline em execução no momento para ver detalhes sobre as tarefas. Quando termina, você vê os resultados de cada etapa.

## <a name="next-steps"></a>Próximas etapas

Para obter o passo-a-passo do processo sobre o uso do Azure Pipelines com modelos ARM, consulte [Tutorial: Integração contínua dos modelos do Azure Resource Manager com o Azure Pipelines](deployment-tutorial-pipeline.md).
