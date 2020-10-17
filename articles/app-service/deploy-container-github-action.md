---
title: CI/CD personalizado de ações do GitHub
description: Saiba como usar as ações do GitHub para implantar seu contêiner personalizado do Linux no serviço de aplicativo de um pipeline de CI/CD.
ms.devlang: na
ms.topic: article
ms.date: 10/03/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: github-actions-azure
ms.openlocfilehash: d6f66993b0fb7f97c551f4fbcb305111cfb2097e
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150275"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Implantar um contêiner personalizado no serviço de aplicativo usando as ações do GitHub

As [ações do GitHub](https://help.github.com/en/articles/about-github-actions) oferecem a flexibilidade para criar um fluxo de trabalho de desenvolvimento de software automatizado. Com a [ação de implantação da Web do Azure](https://github.com/Azure/webapps-deploy), você pode automatizar o fluxo de trabalho para implantar contêineres personalizados no [serviço de aplicativo](overview.md) usando ações do github.

Um fluxo de trabalho é definido por um arquivo YAML (.yml) no caminho `/.github/workflows/` no repositório. Essa definição contém as várias etapas e parâmetros que estão no fluxo de trabalho.

Para um fluxo de trabalho de contêiner de serviço Azure App, o arquivo tem três seções:

|Seção  |Tarefas  |
|---------|---------|
|**Autenticação** | 1. recupere uma entidade de serviço ou um perfil de publicação. <br /> 2. Crie um segredo do GitHub. |
|**Compilar** | 1. Crie o ambiente. <br /> 2. Crie a imagem de contêiner. |
|**Implantar** | 1. implante a imagem de contêiner. |

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Criar uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Uma conta do GitHub. Se você não tiver uma, Inscreva-se [gratuitamente](https://github.com/join).  
- Um registro de contêiner em funcionamento e um aplicativo de serviço de Azure App para contêineres. Este exemplo usa o registro de contêiner do Azure. 
    - [Saiba como criar um aplicativo de Node.js em contêiner usando o Docker, enviar por push a imagem de contêiner para um registro e, em seguida, implantar a imagem no serviço Azure App](/azure/developer/javascript/tutorial-vscode-docker-node-01)

## <a name="generate-deployment-credentials"></a>Gerar credenciais de implantação

A maneira recomendada de autenticar com os serviços Azure App para ações do GitHub é com um perfil de publicação. Você também pode autenticar com uma entidade de serviço, mas o processo requer mais etapas. 

Salve a credencial de perfil de publicação ou a entidade de serviço como um [segredo do GitHub](https://docs.github.com/en/actions/reference/encrypted-secrets) para autenticar com o Azure. Você acessará o segredo em seu fluxo de trabalho. 

# <a name="publish-profile"></a>[Perfil de publicação](#tab/publish-profile)

Um perfil de publicação é uma credencial no nível do aplicativo. Configure seu perfil de publicação como um segredo do GitHub. 

1. Vá para o serviço de aplicativo no portal do Azure. 

1. Na página **visão geral** , selecione **obter perfil de publicação**.

1. Salve o arquivo baixado. Você usará o conteúdo do arquivo para criar um segredo do GitHub.

# <a name="service-principal"></a>[Entidade de serviço](#tab/service-principal)

Você pode criar uma [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) com o comando [AZ ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) na [CLI do Azure](/cli/azure/). Execute este comando com [Azure cloud Shell](https://shell.azure.com/) na portal do Azure ou selecionando o botão **experimentar** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

No exemplo, substitua os espaços reservados por sua ID de assinatura, nome do grupo de recursos e nome do aplicativo. A saída é um objeto JSON com as credenciais de atribuição de função que fornecem acesso ao seu aplicativo do serviço de aplicativo. Copie este objeto JSON para mais tarde.

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

---

## <a name="configure-the-github-secret"></a>Configurar o segredo do GitHub

No [GitHub](https://github.com/), procure seu repositório, selecione **configurações > segredos > adicionar um novo segredo**.

Cole o conteúdo da saída JSON como o valor da variável secreta. Dê ao segredo o nome como `AZURE_CREDENTIALS` .

Ao configurar o arquivo de fluxo de trabalho posteriormente, você usa o segredo para a entrada `creds` da ação de logon do Azure. Por exemplo:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="configure-the-github-secret-for-authentication"></a>Configurar o segredo do GitHub para autenticação

# <a name="publish-profile"></a>[Perfil de publicação](#tab/publish-profile)

No [GitHub](https://github.com/), procure seu repositório, selecione **configurações > segredos > adicionar um novo segredo**.

Para usar [credenciais de nível de aplicativo](#generate-deployment-credentials), Cole o conteúdo do arquivo de perfil de publicação baixado no campo valor do segredo. Nomeie o segredo `AZURE_WEBAPP_PUBLISH_PROFILE` .

Ao configurar o fluxo de trabalho do GitHub, você usa o `AZURE_WEBAPP_PUBLISH_PROFILE` na ação implantar aplicativo Web do Azure. Por exemplo:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Entidade de serviço](#tab/service-principal)

No [GitHub](https://github.com/), procure seu repositório, selecione **configurações > segredos > adicionar um novo segredo**.

Para usar [credenciais de nível de usuário](#generate-deployment-credentials), Cole toda a saída JSON do comando CLI do Azure no campo valor do segredo. Dê ao segredo o nome como `AZURE_CREDENTIALS` .

Ao configurar o arquivo de fluxo de trabalho posteriormente, você usa o segredo para a entrada `creds` da ação de logon do Azure. Por exemplo:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="configure-github-secrets-for-your-registry"></a>Configurar segredos do GitHub para o registro

Defina os segredos a serem usados com a ação de logon do Docker. 

1. Vá para o contêiner no portal do Azure ou no Docker e copie o nome de usuário e a senha. 

2. Defina um novo segredo para o nome de usuário do Registro chamado `REGISTRY_USERNAME` . 

3. Defina um novo segredo para a senha de registro chamada `REGISTRY_PASSWORD` . 

## <a name="build-the-container-image"></a>Criar a imagem de contêiner

O exemplo a seguir mostra parte do fluxo de trabalho que cria um Node.JS imagem do Docker. Use o [logon do Docker](https://github.com/azure/docker-login) para fazer logon em um registro de contêiner privado. Este exemplo usa o registro de contêiner do Azure, mas a mesma ação funciona para outros registros. 


```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

Você também pode usar o [logon do Docker](https://github.com/azure/docker-login) para fazer logon em vários registros de contêiner ao mesmo tempo. Este exemplo inclui dois novos segredos do GitHub para autenticação com docker.io.

```yml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - uses: azure/docker-login@v1
      with:
        login-server: index.docker.io
        username: ${{ secrets.DOCKERIO_USERNAME }}
        password: ${{ secrets.DOCKERIO_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

## <a name="deploy-to-an-app-service-container"></a>Implantar em um contêiner do serviço de aplicativo

Para implantar a imagem em um contêiner personalizado no serviço de aplicativo, use a `azure/webapps-deploy@v2` ação. Esta ação tem cinco parâmetros:

| **Parâmetro**  | **Explicação**  |
|---------|---------|
| **app-name** | (Obrigatório) Nome do aplicativo do Serviço de Aplicativo | 
| **publish-profile** | (Opcional) Publique o conteúdo do arquivo de perfil com os segredos da Implantação da Web |
| **images** | Nome da (s) imagem (ns) do contêiner totalmente qualificado. Por exemplo, ' myregistry.azurecr.io/nginx:latest ' ou ' Python: 3.7.2-Alpine/'. Para cenários de vários contêineres, vários nomes de imagem de contêiner podem ser fornecidos (separados por várias linhas) |
| **slot-name** | (Opcional) Insira um slot existente que não seja o slot de produção |
| **arquivo de configuração** | Adicional Caminho do arquivo de Docker-Compose |

# <a name="publish-profile"></a>[Perfil de publicação](#tab/publish-profile)

```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2

    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     

    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
```
# <a name="service-principal"></a>[Entidade de serviço](#tab/service-principal)

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
      
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

---

## <a name="next-steps"></a>Próximas etapas

Encontre nosso conjunto de ações agrupadas em diferentes repositórios no GitHub, cada um contendo documentação e exemplos para ajudar você a usar o GitHub para CI/CD e implantar seus aplicativos no Azure.

- [Fluxos de trabalho de ações a serem implantados no Azure](https://github.com/Azure/actions-workflow-samples)

- [Logon do Azure](https://github.com/Azure/login)

- [Aplicativo Web do Azure](https://github.com/Azure/webapps-deploy)

- [Logon/logoff do Docker](https://github.com/Azure/docker-login)

- [Eventos que disparam fluxos de trabalho](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Implantação do K8s](https://github.com/Azure/k8s-deploy)

- [Fluxos de trabalho iniciais](https://github.com/actions/starter-workflows)