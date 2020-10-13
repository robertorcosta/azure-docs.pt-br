---
title: Usar ações do GitHub para fazer atualizações de código no Azure Functions
description: Saiba como usar ações do GitHub para definir um fluxo de trabalho para criar e implantar Azure Functions projetos no GitHub.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python, github-actions-azure
ms.openlocfilehash: 3f35a8130c834112961f4542883704c2b8dbd08f
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999245"
---
# <a name="continuous-delivery-by-using-github-action"></a>Entrega contínua usando a ação do GitHub

Use as [ações do GitHub](https://github.com/features/actions) para definir um fluxo de trabalho para compilar e implantar código automaticamente em seu aplicativo de funções do Azure. 

Em ações do GitHub, um [fluxo de trabalho](https://help.github.com/articles/about-github-actions#workflow) é um processo automatizado que você define em seu repositório github. Esse processo informa ao GitHub como criar e implantar seu projeto de aplicativo do Functions no GitHub. 

Um fluxo de trabalho é definido por um arquivo YAML (.yml) no caminho `/.github/workflows/` no repositório. Essa definição contém as várias etapas e os parâmetros que compõem o fluxo de trabalho. 

Para um fluxo de trabalho Azure Functions, o arquivo tem três seções: 

| Seção | Tarefas |
| ------- | ----- |
| **Autenticação** | <ol><li>Baixar um perfil de publicação ou definir uma entidade de serviço.</li><li>Crie um segredo do GitHub.</li></ol>|
| **Compilar** | <ol><li>Configure o ambiente.</li><li>Compile o aplicativo de funções.</li></ol> |
| **Implantar** | <ol><li>Implante o aplicativo de funções.</li></ol>|

> [!NOTE]
> Você não precisará criar uma entidade de serviço se decidir usar o perfil de publicação para autenticação.

## <a name="downloading-and-using-a-publish-profile-as-deployment-credential-recommended"></a>Baixando e usando um perfil de publicação como uma credencial de implantação (recomendado)

Para baixar o perfil de publicação do seu aplicativo de funções:

1. Selecione a página **visão geral** do aplicativo de funções e, em seguida, selecione **obter perfil de publicação**.

   :::image type="content" source="media/functions-how-to-github-actions/get-publish-profile.png" alt-text="Baixar perfil de publicação":::

1. Salve e copie o conteúdo do arquivo de configurações de publicação.

## <a name="create-a-service-principal-deprecated"></a>Criar uma entidade de serviço (preterida)
=======
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Criar uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Uma conta do GitHub. Se você não tiver uma, Inscreva-se [gratuitamente](https://github.com/join).  
- Um aplicativo de funções de trabalho hospedado no Azure com um repositório GitHub.   
    - [Início Rápido: Criar uma função no Azure usando o Visual Studio Code](functions-create-first-function-vs-code.md)


## <a name="generate-deployment-credentials"></a>Gerar credenciais de implantação

A maneira recomendada para autenticar com Azure Functions para ações do GitHub é com um perfil de publicação. Você também pode autenticar com uma entidade de serviço, mas o processo requer mais etapas. 

## <a name="configure-the-github-secret"></a>Configurar o segredo do GitHub
= = = = = = = Salvar sua credencial de perfil de publicação ou entidade de serviço como um [segredo do GitHub](https://docs.github.com/en/actions/reference/encrypted-secrets) para autenticar com o Azure. Você acessará o segredo em seu fluxo de trabalho. 

# <a name="publish-profile"></a>[Perfil de publicação](#tab/publish-profile)

Para baixar o perfil de publicação do seu aplicativo de funções:

1. Selecione a página **visão geral** do aplicativo de funções e, em seguida, selecione **obter perfil de publicação**.

   :::image type="content" source="media/functions-how-to-github-actions/get-publish-profile.png" alt-text="Baixar perfil de publicação":::

1. Salve e copie o conteúdo do arquivo.


# <a name="service-principal"></a>[Entidade de serviço](#tab/service-principal)

Você pode criar uma [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) usando o comando [AZ ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) para [CLI do Azure](/cli/azure/). Execute este comando usando [Azure cloud Shell](https://shell.azure.com) na portal do Azure ou selecionando o botão **experimentar** .

```azurecli-interactive
az ad sp create-for-rbac --name "<MY-APP-NAME>" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

Neste exemplo, substitua os espaços reservados no recurso por sua ID de assinatura, grupo de recursos e nome do aplicativo de funções. A saída são as credenciais de atribuição de função que fornecem acesso ao seu aplicativo de funções. Copie esse objeto JSON, que você pode usar para se autenticar por meio do GitHub. 

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> É sempre uma boa prática permitir acesso mínimo. É por isso que o escopo no exemplo anterior é limitado ao aplicativo de funções específico e não ao grupo de recursos inteiro.

---

## <a name="add-the-github-secret"></a>Adicionar o segredo do GitHub

1. No [GitHub](https://github.com), navegue até o repositório, selecione **configurações**  >  **segredos**  >  **Adicionar um novo segredo**.

   :::image type="content" source="media/functions-how-to-github-actions/add-secret.png" alt-text="Baixar perfil de publicação":::

1. Adicione um novo segredo.

   * Se você estiver usando a entidade de serviço que você criou usando o CLI do Azure, use `AZURE_CREDENTIALS` para o **nome**. Em seguida, Cole a saída do objeto JSON copiado quanto a **valor**e selecione **Adicionar segredo**.
   * Se você estiver usando um perfil de publicação, use `AZURE_FUNCTIONAPP_PUBLISH_PROFILE` para o **nome**. Em seguida, use o conteúdo do arquivo do perfil de publicação para **valor**e selecione **Adicionar segredo**.

O GitHub agora pode se autenticar no seu aplicativo de funções no Azure.

## <a name="create-the-environment"></a>Criar o ambiente 

A configuração do ambiente é feita usando uma ação de instalação de publicação específica a um idioma.

|**Idioma**  |**Ação de instalação**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**ASP.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |


# <a name="net"></a>[.NET](#tab/dotnet)

O exemplo a seguir mostra a parte do fluxo de trabalho que usa a `actions/setup-dotnet` ação para configurar o ambiente:

```yaml
    - name: Setup DotNet 2.2.402 Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: 2.2.402
```

# <a name="java"></a>[Java](#tab/java)

O exemplo a seguir mostra a parte do fluxo de trabalho que usa a  `actions/setup-java` ação para configurar o ambiente:

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra a parte do fluxo de trabalho que usa a `actions/setup-node` ação para configurar o ambiente:

```yaml

    - name: Setup Node 12.x Environment
      uses: actions/setup-node@v1
      with:
        node-version: 12.x
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir mostra a parte do fluxo de trabalho que usa a `actions/setup-python` ação para configurar o ambiente:

```yaml
    - name: Setup Python 3.7 Environment
      uses: actions/setup-python@v1
      with:
        python-version: 3.7
```
---

## <a name="build-the-function-app"></a>Compilar o aplicativo de funções

Isso depende do idioma e dos idiomas com suporte pelo Azure Functions, esta seção deve ser as etapas de compilação padrão de cada idioma.

O exemplo a seguir mostra a parte do fluxo de trabalho que cria o aplicativo de funções, que é específico do idioma:

# <a name="net"></a>[.NET](#tab/dotnet)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
```

# <a name="java"></a>[Java](#tab/java)

```yaml
    env:
      POM_XML_DIRECTORY: '.'  # set this to the directory which contains pom.xml file

    - name: 'Restore Project Dependencies Using Mvn'
      shell: bash
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'  # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Npm'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

# <a name="python"></a>[Python](#tab/python)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Pip'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/site-packages"
        popd
```
---

## <a name="deploy-the-function-app"></a>Implantar o aplicativo de funções
Use a `Azure/functions-action` ação para implantar seu código em um aplicativo de funções. Esta ação tem três parâmetros:

|Parâmetro |Explicação  |
|---------|---------|
|_**nome do aplicativo**_ | Obrigatório O nome do seu aplicativo de funções. |
|_**nome do slot**_ | Adicional O nome do [slot de implantação](functions-deployment-slots.md) no qual você deseja implantar. O slot já deve estar definido em seu aplicativo de funções. |
|_**publish-profile**_ | Adicional O nome do segredo do GitHub para seu perfil de publicação. |


### <a name="publish-profile-deploy"></a>Publicar perfil implantar

Os exemplos a seguir usam a versão 1 do `functions-action` e um `publish profile` para autenticação:


# <a name="net"></a>[.NET](#tab/dotnet)

Configure um fluxo de trabalho do Linux .NET que usa um perfil de publicação.

```yaml
name: Deploy DotNet project to Azure function app with a Linux environment

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name  # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'    # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '2.2.402'              # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: Setup DotNet ${{ env.DOTNET_VERSION }} Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: ${{ env.DOTNET_VERSION }}

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: '${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}/output'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```
Configure um fluxo de trabalho do Windows .NET que usa um perfil de publicação.

```yaml
name: Deploy DotNet project to Azure function app with a Windows environment

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name  # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'    # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '2.2.402'              # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: Setup DotNet ${{ env.DOTNET_VERSION }} Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: ${{ env.DOTNET_VERSION }}

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: pwsh
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: '${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}/output'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

# <a name="java"></a>[Java](#tab/java)

Configure um fluxo de trabalho do Java Linux que usa um perfil de publicação.

```yaml
name: Deploy Java project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name      # set this to your function app name on Azure
  POM_XML_DIRECTORY: '.'                     # set this to the directory which contains pom.xml file
  POM_FUNCTIONAPP_NAME: your-app-name        # set this to the function app name in your local development environment
  JAVA_VERSION: '1.8.x'                      # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: Setup Java Sdk ${{ env.JAVA_VERSION }}
      uses: actions/setup-java@v1
      with:
        java-version: ${{ env.JAVA_VERSION }}

    - name: 'Restore Project Dependencies Using Mvn'
      shell: bash
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: './${{ env.POM_XML_DIRECTORY }}/target/azure-functions/${{ env.POM_FUNCTIONAPP_NAME }}'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

Configure um fluxo de trabalho do Windows Java que usa um perfil de publicação.

```yaml
name: Deploy Java project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name      # set this to your function app name on Azure
  POM_XML_DIRECTORY: '.'                     # set this to the directory which contains pom.xml file
  POM_FUNCTIONAPP_NAME: your-app-name        # set this to the function app name in your local development environment
  JAVA_VERSION: '1.8.x'                      # set this to the java version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: Setup Java Sdk ${{ env.JAVA_VERSION }}
      uses: actions/setup-java@v1
      with:
        java-version: ${{ env.JAVA_VERSION }}

    - name: 'Restore Project Dependencies Using Mvn'
      shell: pwsh
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: './${{ env.POM_XML_DIRECTORY }}/target/azure-functions/${{ env.POM_FUNCTIONAPP_NAME }}'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Configure um Node.JS fluxo de trabalho do Linux que usa um perfil de publicação.

```yaml
name: Deploy Node.js project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name    # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '12.x'                     # set this to the node version to use (supports 8.x, 10.x, 12.x)

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: Setup Node ${{ env.NODE_VERSION }} Environment
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}

    - name: 'Resolve Project Dependencies Using Npm'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

Configure um Node.JS fluxo de trabalho do Windows que usa um perfil de publicação.

```yaml
name: Deploy Node.js project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name    # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '10.x'                     # set this to the node version to use (supports 8.x, 10.x, 12.x)

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: Setup Node ${{ env.NODE_VERSION }} Environment
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}

    - name: 'Resolve Project Dependencies Using Npm'
      shell: pwsh
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}

```
# <a name="python"></a>[Python](#tab/python)

Configure um fluxo de trabalho do Python Linux que usa um perfil de publicação.

```yaml
name: Deploy Python project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'   # set this to the path to your web app project, defaults to the repository root
  PYTHON_VERSION: '3.7'                 # set this to the python version to use (supports 3.6, 3.7, 3.8)

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: Setup Python ${{ env.PYTHON_VERSION }} Environment
      uses: actions/setup-python@v1
      with:
        python-version: ${{ env.PYTHON_VERSION }}

    - name: 'Resolve Project Dependencies Using Pip'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/site-packages"
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

---

### <a name="service-principal-deploy"></a>Implantação da entidade de serviço

O exemplo a seguir usa a versão 1 do `functions-action` e um `service principal` para autenticação. O fluxo de trabalho configura um ambiente do Windows .NET. 

# <a name="net"></a>[.NET](#tab/dotnet)

Configure um fluxo de trabalho do Linux .NET que usa uma entidade de serviço.

```yaml
name: Deploy DotNet project to Azure function app with a Linux environment

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name  # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'    # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '2.2.402'              # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Setup DotNet ${{ env.DOTNET_VERSION }} Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: ${{ env.DOTNET_VERSION }}

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: '${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}/output'

     - name: logout
        run: |
          az logout
```

Configure um fluxo de trabalho do Windows .NET que usa uma entidade de serviço.

```yaml
name: Deploy DotNet project to Azure function app with a Windows environment

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name  # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'    # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '2.2.402'              # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Setup DotNet ${{ env.DOTNET_VERSION }} Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: ${{ env.DOTNET_VERSION }}

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: pwsh
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: '${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}/output'

     - name: logout
        run: |
          az logout
```

# <a name="java"></a>[Java](#tab/java)

Configure um fluxo de trabalho do Java Linux que usa uma entidade de serviço.

```yaml
name: Deploy Java project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name      # set this to your function app name on Azure
  POM_XML_DIRECTORY: '.'                     # set this to the directory which contains pom.xml file
  POM_FUNCTIONAPP_NAME: your-app-name        # set this to the function app name in your local development environment
  JAVA_VERSION: '1.8.x'                      # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}


    - name: Setup Java Sdk ${{ env.JAVA_VERSION }}
      uses: actions/setup-java@v1
      with:
        java-version: ${{ env.JAVA_VERSION }}

    - name: 'Restore Project Dependencies Using Mvn'
      shell: bash
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: './${{ env.POM_XML_DIRECTORY }}/target/azure-functions/${{ env.POM_FUNCTIONAPP_NAME }}'

     - name: logout
        run: |
          az logout
```

Configure um fluxo de trabalho do Windows Java que usa uma entidade de serviço.

```yaml
name: Deploy Java project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name      # set this to your function app name on Azure
  POM_XML_DIRECTORY: '.'                     # set this to the directory which contains pom.xml file
  POM_FUNCTIONAPP_NAME: your-app-name        # set this to the function app name in your local development environment
  JAVA_VERSION: '1.8.x'                      # set this to the java version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Setup Java Sdk ${{ env.JAVA_VERSION }}
      uses: actions/setup-java@v1
      with:
        java-version: ${{ env.JAVA_VERSION }}

    - name: 'Restore Project Dependencies Using Mvn'
      shell: pwsh
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: './${{ env.POM_XML_DIRECTORY }}/target/azure-functions/${{ env.POM_FUNCTIONAPP_NAME }}'

     - name: logout
        run: |
          az logout
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Configure um Node.JS fluxo de trabalho do Linux que usa uma entidade de serviço.

```yaml
name: Deploy Node.js project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name    # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '12.x'                     # set this to the node version to use (supports 8.x, 10.x, 12.x)

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Setup Node ${{ env.NODE_VERSION }} Environment
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}

    - name: 'Resolve Project Dependencies Using Npm'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}

     - name: logout
        run: |
          az logout
```

Configure um Node.JS fluxo de trabalho do Windows que usa uma entidade de serviço.

```yaml
name: Deploy Node.js project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name    # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '10.x'                     # set this to the node version to use (supports 8.x, 10.x, 12.x)

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Setup Node ${{ env.NODE_VERSION }} Environment
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}

    - name: 'Resolve Project Dependencies Using Npm'
      shell: pwsh
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}

     - name: logout
        run: |
          az logout
```

# <a name="python"></a>[Python](#tab/python)

Configure um fluxo de trabalho do Python Linux que usa uma entidade de serviço.

```yaml
name: Deploy Python project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'   # set this to the path to your web app project, defaults to the repository root
  PYTHON_VERSION: '3.7'                 # set this to the python version to use (supports 3.6, 3.7, 3.8)

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Setup Python ${{ env.PYTHON_VERSION }} Environment
      uses: actions/setup-python@v1
      with:
        python-version: ${{ env.PYTHON_VERSION }}

    - name: 'Resolve Project Dependencies Using Pip'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/site-packages"
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}

     - name: logout
        run: |
          az logout
```

---

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre a integração do Azure e do GitHub](https://docs.microsoft.com/azure/developer/github/)
