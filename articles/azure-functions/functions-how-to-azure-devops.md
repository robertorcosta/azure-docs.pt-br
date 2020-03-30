---
title: Atualize continuamente o código do aplicativo de função usando o Azure DevOps
description: Saiba como configurar um pipeline Azure DevOps que tem como alvo as funções do Azure.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: cshoe
ms.openlocfilehash: 5e2fc8fb06248e2cdad9067c56647da6d9626b50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255765"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Entrega contínua usando DevOps Azure

Você pode implantar automaticamente sua função em um aplicativo Azure Functions usando [a azure Pipelines](/azure/devops/pipelines/).

Você tem duas opções para definir seu pipeline:

- **Arquivo YAML**: Um arquivo YAML descreve o pipeline. O arquivo pode ter uma seção de etapas de compilação e uma seção de liberação. O arquivo YAML deve estar no mesmo repo que o aplicativo.
- **Modelo**: Os modelos são tarefas prontas que constroem ou implantam seu aplicativo.

## <a name="yaml-based-pipeline"></a>Gasoduto baseado em YAML

Para criar um pipeline baseado em YAML, primeiro crie seu aplicativo e, em seguida, implante o aplicativo.

### <a name="build-your-app"></a>Compilar seu aplicativo

A forma como você constrói seu aplicativo no Azure Pipelines depende da linguagem de programação do seu aplicativo. Cada idioma tem etapas específicas de compilação que criam um artefato de implantação. Um artefato de implantação é usado para implantar seu aplicativo de função no Azure.

# <a name="c"></a>[C\#](#tab/csharp)

Você pode usar a seguinte amostra para criar um arquivo YAML para criar um aplicativo .NET:

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: false
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Você pode usar a seguinte amostra para criar um arquivo YAML para criar um aplicativo JavaScript:

```yaml
pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' if you have Windows native +Node modules
steps:
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install 
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="python"></a>[Python](#tab/python)

Você pode usar uma das seguintes amostras para criar um arquivo YAML para criar um aplicativo para uma versão Específica do Python. Python é suportado apenas para aplicativos de função em execução no Linux.

**Versão 3.7**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.7 as required by functions"
  inputs:
    versionSpec: '3.7'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

**Versão 3.6**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/python3.6/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

Você pode usar a seguinte amostra para criar um arquivo YAML para empacotar um aplicativo PowerShell. O PowerShell é suportado apenas para funções do Windows Azure.

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

---

### <a name="deploy-your-app"></a>Implantar seu aplicativo

Você deve incluir uma das seguintes amostras de YAML em seu arquivo YAML, dependendo do sistema operacional de hospedagem.

#### <a name="windows-function-app"></a>Aplicativo de função do Windows

Você pode usar o seguinte trecho para implantar um aplicativo de função do Windows:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

#### <a name="linux-function-app"></a>Aplicativo de função Linux

Você pode usar o seguinte trecho para implantar um aplicativo de função Linux:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #Note that deployment slots is not supported for Linux Dynamic SKU
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

## <a name="template-based-pipeline"></a>Pipeline baseado em modelos

Os modelos no Azure DevOps são grupos predefinidos de tarefas que constroem ou implantam um aplicativo.

### <a name="build-your-app"></a>Compilar seu aplicativo

A forma como você constrói seu aplicativo no Azure Pipelines depende da linguagem de programação do seu aplicativo. Cada idioma tem etapas específicas de compilação que criam um artefato de implantação. Um artefato de implantação é usado para atualizar seu aplicativo de função no Azure.

Para usar modelos de compilação incorporados, ao criar um novo pipeline de compilação, selecione **Use o editor clássico** para criar um pipeline usando modelos de designer.

![Selecione o editor clássico do Azure Pipelines](media/functions-how-to-azure-devops/classic-editor.png)

Depois de configurar a fonte do seu código, procure modelos de compilação de funções do Azure. Selecione o modelo que corresponde ao idioma do aplicativo.

![Selecione um modelo de compilação de funções do Azure](media/functions-how-to-azure-devops/build-templates.png)

Em alguns casos, os artefatos de construção têm uma estrutura de pasta específica. Talvez seja necessário selecionar o nome da **pasta de raiz Prepend para arquivar a** caixa de seleção de caminhos.

![A opção de preparar o nome da pasta raiz](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>Aplicativos JavaScript

Se o aplicativo JavaScript tiver uma dependência de módulos nativos do Windows, você deve atualizar a versão do pool de agentes para **o Hosted VS2017**.

![Atualize a versão do pool de agentes](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Implantar seu aplicativo

Quando você criar um novo pipeline de versão, procure o modelo de versão Funções do Azure.

![Procure o modelo de versão Funções do Azure](media/functions-how-to-azure-devops/release-template.png)

A implantação em um slot de implantação não é suportada no modelo de versão.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Crie um pipeline de construção usando o Azure CLI

Para criar um pipeline de construção `az functionapp devops-pipeline create` no Azure, use o [comando](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create). O pipeline de compilação é criado para construir e liberar quaisquer alterações de código que forem feitas em seu repo. O comando gera um novo arquivo YAML que define o pipeline de compilação e liberação e, em seguida, o compromete com o seu repo. Os pré-requisitos para este comando dependem da localização do seu código.

- Se o seu código estiver no GitHub:

    - Você deve ter permissões de **gravação** para sua assinatura.

    - Você deve ser o administrador do projeto no Azure DevOps.

    - Você deve ter permissões para criar um Token de acesso pessoal (PAT) do GitHub que tenha permissões suficientes. Para obter mais informações, consulte [os requisitos de permissão do GitHub PAT.](https://aka.ms/azure-devops-source-repos)

    - Você deve ter permissões para se comprometer com o ramo mestre em seu repositório GitHub para que você possa comprometer o arquivo YAML gerado automaticamente.

- Se o seu código estiver nos Repos do Azure:

    - Você deve ter permissões de **gravação** para sua assinatura.

    - Você deve ser o administrador do projeto no Azure DevOps.

## <a name="next-steps"></a>Próximas etapas

- Revise a visão geral das funções do [Azure](functions-overview.md).
- Revise a visão geral do [Azure DevOps](/azure/devops/pipelines/).
