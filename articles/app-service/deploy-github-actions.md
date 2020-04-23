---
title: Configure CI/CD com ações do GitHub
description: Saiba como implantar seu código no Azure App Service a partir de um pipeline de CI/CD com o GitHub Actions. Personalize as tarefas de compilação e execute implantações complexas.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 57ca5b0880d4b027e33bc0d01fc6225eb886029b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084984"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Implantar no Serviço de Aplicativos usando as ações do GitHub

[O GitHub Actions](https://help.github.com/en/articles/about-github-actions) oferece flexibilidade para construir um fluxo de trabalho automatizado do ciclo de vida do desenvolvimento de software. Com as ações de serviço do aplicativo Azure para o GitHub, você pode automatizar seu fluxo de trabalho para implantar no [Azure App Service](overview.md) usando as ações do GitHub.

> [!IMPORTANT]
> O GitHub Actions está atualmente em beta. Você deve primeiro [se inscrever para participar da pré-visualização](https://github.com/features/actions) usando sua conta do GitHub.
> 

Um fluxo de trabalho é definido por um arquivo `/.github/workflows/` YAML (.yml) no caminho em seu repositório. Esta definição contém as várias etapas e parâmetros que compõem o fluxo de trabalho.

Para um fluxo de trabalho do Azure App Service, o arquivo tem três seções:

|Seção  |Tarefas  |
|---------|---------|
|**Autenticação** | 1. Defina um diretor de serviço <br /> 2. Crie um segredo do GitHub |
|**Compilar** | 1. Configurar o ambiente <br /> 2. Construa o aplicativo web |
|**Implantar** | 1. Implantar o aplicativo web |

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

Você pode criar um [principal de serviço](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) usando o comando [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) no [Azure CLI](https://docs.microsoft.com/cli/azure/). Você pode executar este comando usando [o Azure Cloud Shell](https://shell.azure.com/) no portal Azure ou selecionando o botão **Tente.o.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> --sdk-auth
```

Neste exemplo, substitua os espaços reservados no recurso por seu ID de assinatura, nome do grupo de recursos e nome do aplicativo. A saída são as credenciais de atribuição de função que fornecem acesso ao seu aplicativo App Service. Copie este objeto JSON, que você pode usar para autenticar do GitHub.

> [!NOTE]
> Você não precisa criar um diretor de serviço se decidir usar o perfil de publicação para autenticação.

> [!IMPORTANT]
> É sempre uma boa prática conceder acesso mínimo. É por isso que o escopo no exemplo anterior está limitado ao aplicativo específico do App Service e não a todo o grupo de recursos.

## <a name="configure-the-github-secret"></a>Configure o segredo do GitHub

Você também pode usar credenciais de nível de aplicativo, ou seja, publicar perfil para implantação. Siga as etapas para configurar o segredo:

1. Baixe o perfil de publicação para o aplicativo App Service no portal usando a opção **de perfil Get Publish.**

2. No [GitHub,](https://github.com/)navegue pelo seu repositório, selecione **Configurações > Segredos > adicionar um novo segredo**

    ![segredos](media/app-service-github-actions/secrets.png)

3. Cole o conteúdo do arquivo de perfil de publicação baixado no campo de valor do segredo.

4. Agora, no arquivo de fluxo `.github/workflows/workflow.yml` de trabalho em `publish-profile` sua filial: substitua o segredo para a entrada da ação do Aplicativo Web do Azure.
    
    ```yaml
        - uses: azure/webapps-deploy@v2
          with:
            creds: ${{ secrets.azureWebAppPublishProfile }}
    ```

5. Você vê o segredo como mostrado abaixo uma vez definido.

    ![segredos](media/app-service-github-actions/app-service-secrets.png)

## <a name="set-up-the-environment"></a>Configurar o ambiente

A configuração do ambiente pode ser feita usando uma das ações de configuração.

|**Linguagem**  |**Ação de configuração**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

Os exemplos a seguir mostram a parte do fluxo de trabalho que configura o ambiente para as várias línguas suportadas:

**JavaScript**

```yaml
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```
**Python**

```yaml
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

**.NET**

```yaml
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

## <a name="build-the-web-app"></a>Construa o aplicativo web

Isso depende do idioma e para os idiomas suportados pelo Azure App Service, esta seção deve ser as etapas padrão de construção de cada idioma.

Os exemplos a seguir mostram a parte do fluxo de trabalho que constrói o aplicativo web, nos vários idiomas suportados.

**JavaScript**

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

**Python**

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

**.NET**

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

**Java**

```yaml
    - uses: actions/checkout@v1
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
```
## <a name="deploy-to-app-service"></a>Implantar no Serviço de Aplicativo

Para implantar seu código em um `azure/webapps-deploy@v2` aplicativo do App Service, use a ação. Esta ação tem quatro parâmetros:

| **Parâmetro**  | **Explicação**  |
|---------|---------|
| **nome de aplicativo** | (Obrigatório) Nome do aplicativo App Service | 
| **publicar perfil** | (Opcional) Publicar conteúdo de arquivo de perfil com segredos de Implantação da Web |
| **package** | (Opcional) Caminho para pacote ou pasta. *.zip, *.war, *.jar ou uma pasta para implantar |
| **nome de caça-níquel** | (Opcional) Digite um slot existente que não seja o slot de produção |

### <a name="deploy-using-publish-profile"></a>Implantar usando o Perfil de Publicação

Abaixo está o fluxo de trabalho de exemplo para construir e implantar um aplicativo Node.js no Azure usando o perfil de publicação.

```yaml
# File: .github/workflows/workflow.yml

on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
          uses: azure/webapps-deploy@v2
          with: 
            app-name: node-rn
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

### <a name="deploy-using-azure-service-principal"></a>Implantar usando o principal de serviço do Azure

Abaixo está o fluxo de trabalho de exemplo para construir e implantar um aplicativo Node.js no Azure usando um diretor de serviço do Azure.

```yaml
on: [push]

name: Node.js

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
   
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'node-rn'

    # Azure logout 
    - name: logout
      run: |
        az logout
```

## <a name="next-steps"></a>Próximas etapas

Você pode encontrar nosso conjunto de Ações agrupadas em diferentes repositórios no GitHub, cada um contendo documentação e exemplos para ajudá-lo a usar o GitHub para CI/CD e implantar seus aplicativos no Azure.

- [Fluxo de trabalho de ações para implantar no Azure](https://github.com/Azure/actions-workflow-samples)

- [Logon do Azure](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [WebApp do Azure para contêineres](https://github.com/Azure/webapps-container-deploy)

- [Login/logout do Docker](https://github.com/Azure/docker-login)

- [Eventos que desencadeiam fluxos de trabalho](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s implantados](https://github.com/Azure/k8s-deploy)

- [Fluxos de trabalho iniciais](https://github.com/actions/starter-workflows)
