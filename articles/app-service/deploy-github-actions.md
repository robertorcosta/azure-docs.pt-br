---
title: Configurar a CI/CD com o GitHub Actions
description: Saiba como implantar seu código no Serviço de Aplicativo do Azure por meio de um pipeline de CI/CD com o GitHub Actions. Personalize as tarefas de build e execute implantações complexas.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python
ms.openlocfilehash: 264976fdfe514a8778c60fe9242ac555f268718d
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962563"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Implantação no Serviço de Aplicativo usando o GitHub Actions

O [GitHub Actions](https://help.github.com/en/articles/about-github-actions) oferece a flexibilidade para criar um fluxo de trabalho do ciclo de vida de desenvolvimento de software automatizado. Com as Ações do Serviço de Aplicativo do Azure para o GitHub, você pode automatizar o fluxo de trabalho para implantar o [Serviço de Aplicativo do Azure](overview.md) usando o GitHub Actions.

> [!IMPORTANT]
> No momento, o GitHub Actions está na versão beta. Primeiro, [inscreva-se para ingressar na versão prévia](https://github.com/features/actions) usando sua conta do GitHub.
> 

Um fluxo de trabalho é definido por um arquivo YAML (.yml) no caminho `/.github/workflows/` no repositório. Essa definição contém as várias etapas e os parâmetros que compõem o fluxo de trabalho.

Para um fluxo de trabalho do Serviço de Aplicativo do Azure, o arquivo tem três seções:

|Seção  |Tarefas  |
|---------|---------|
|**Autenticação** | 1. definir uma entidade de serviço. <br /> 2. Crie um segredo do GitHub. |
|**Compilar** | 1. configurar o ambiente. <br /> 2. Compile o aplicativo Web. |
|**Implantar** | 1. implante o aplicativo Web. |

## <a name="generate-deployment-credentials"></a>Gerar credenciais de implantação

# <a name="user-level-credentials"></a>[Credenciais de nível de usuário](#tab/userlevel)

Crie uma [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) usando o comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) na [CLI do Azure](/cli/azure/). Execute esse comando com o [Azure Cloud Shell](https://shell.azure.com/) no portal do Azure ou selecionando o botão **Experimentar**.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

No exemplo acima, substitua os espaços reservados pela sua ID de assinatura, pelo nome do grupo de recursos e pelo nome do aplicativo. A saída é um objeto JSON com as credenciais de atribuição de função que fornecem acesso ao seu aplicativo do serviço de aplicativo semelhante ao mostrado abaixo. Copie este objeto JSON para mais tarde.

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
> É sempre uma boa prática permitir acesso mínimo. O escopo no exemplo anterior é limitado ao aplicativo do serviço de aplicativo específico e não ao grupo de recursos inteiro.

# <a name="app-level-credentials"></a>[Credenciais de nível de aplicativo](#tab/applevel)

Você pode usar credenciais de nível de aplicativo usando o perfil de publicação para seu aplicativo. Vá para a página de gerenciamento do aplicativo no Portal. Na página **visão geral** , clique na opção **obter perfil de publicação** .

Você precisará do conteúdo do arquivo mais tarde.

---

## <a name="configure-the-github-secret"></a>Configurar o segredo do GitHub

# <a name="user-level-credentials"></a>[Credenciais de nível de usuário](#tab/userlevel)

No [GitHub](https://github.com/), procure seu repositório, selecione **configurações > segredos > adicionar um novo segredo**.

Para usar [credenciais de nível de usuário](#generate-deployment-credentials), Cole toda a saída JSON do comando CLI do Azure no campo valor do segredo. Dê ao segredo o nome como `AZURE_CREDENTIALS` .

Ao configurar o arquivo de fluxo de trabalho posteriormente, você usa o segredo para a entrada `creds` da ação de logon do Azure. Por exemplo:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

# <a name="app-level-credentials"></a>[Credenciais de nível de aplicativo](#tab/applevel)

No [GitHub](https://github.com/), procure seu repositório, selecione **configurações > segredos > adicionar um novo segredo**.

Para usar [credenciais de nível de aplicativo](#generate-deployment-credentials), Cole o conteúdo do arquivo de perfil de publicação baixado no campo valor do segredo. Dê ao segredo o nome como `azureWebAppPublishProfile` .

Ao configurar o arquivo de fluxo de trabalho posteriormente, você usa o segredo para a entrada `publish-profile` da ação implantar aplicativo Web do Azure. Por exemplo:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

---

## <a name="set-up-the-environment"></a>Configurar o ambiente

A configuração do ambiente pode ser feita por meio de uma das ações de instalação.

|**Idioma**  |**Ação de instalação**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

Os seguintes exemplos mostram a parte do fluxo de trabalho que configura o ambiente para as várias linguagens compatíveis:

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

## <a name="build-the-web-app"></a>Criar o aplicativo Web

Isso depende da linguagem e das linguagens compatíveis com o Serviço de Aplicativo do Azure. Esta seção deve apresentar as etapas de build padrão de cada linguagem.

Os exemplos a seguir mostram a parte do fluxo de trabalho que cria o aplicativo Web, em várias linguagens compatíveis.

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

Para implantar seu código em um aplicativo do Serviço de Aplicativo, use a ação `azure/webapps-deploy@v2`. Essa ação tem quatro parâmetros:

| **Parâmetro**  | **Explicação**  |
|---------|---------|
| **app-name** | (Obrigatório) Nome do aplicativo do Serviço de Aplicativo | 
| **publish-profile** | (Opcional) Publique o conteúdo do arquivo de perfil com os segredos da Implantação da Web |
| **package** | (Opcional) Caminho para o pacote ou a pasta. *.zip, *.war, *.jar ou uma pasta a ser implantada |
| **slot-name** | (Opcional) Insira um slot existente que não seja o slot de produção |

# <a name="user-level-credentials"></a>[Credenciais de nível de usuário](#tab/userlevel)

Veja abaixo o exemplo de fluxo de trabalho para criar e implantar um aplicativo Node.js no Azure usando uma entidade de serviço do Azure. Observe como a `creds` entrada faz referência ao `AZURE_CREDENTIALS` segredo que você criou anteriormente.

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

# <a name="app-level-credentials"></a>[Credenciais de nível de aplicativo](#tab/applevel)

Veja abaixo o fluxo de trabalho de exemplo para criar e implantar um aplicativo Node.js no Azure usando o perfil de publicação do aplicativo. Observe como a `publish-profile` entrada faz referência ao `azureWebAppPublishProfile` segredo que você criou anteriormente.

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

---

## <a name="next-steps"></a>Próximas etapas

Encontre nosso conjunto de ações agrupadas em diferentes repositórios no GitHub, cada um contendo documentação e exemplos para ajudar você a usar o GitHub para CI/CD e implantar seus aplicativos no Azure.

- [Fluxos de trabalho de ações a serem implantados no Azure](https://github.com/Azure/actions-workflow-samples)

- [Logon do Azure](https://github.com/Azure/login)

- [Aplicativo Web do Azure](https://github.com/Azure/webapps-deploy)

- [Aplicativo Web do Azure para contêineres](https://github.com/Azure/webapps-container-deploy)

- [Logon/logoff do Docker](https://github.com/Azure/docker-login)

- [Eventos que disparam fluxos de trabalho](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Implantação do K8s](https://github.com/Azure/k8s-deploy)

- [Fluxos de trabalho iniciais](https://github.com/actions/starter-workflows)