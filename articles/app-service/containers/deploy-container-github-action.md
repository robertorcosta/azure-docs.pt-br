---
title: CI/CD de contêiner personalizado das ações do GitHub
description: Aprenda a usar o GitHub Actions para implantar seu contêiner Linux personalizado no App Service a partir de um pipeline de CI/CD.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: d5f175d887cec1d5b5e567d3f716e6492f4516dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78246963"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Implantar um contêiner personalizado no App Service usando as ações do GitHub

[O GitHub Actions](https://help.github.com/en/articles/about-github-actions) oferece flexibilidade para construir um fluxo de trabalho automatizado do ciclo de vida do desenvolvimento de software. Com o [Azure App Service Action for Containers,](https://github.com/Azure/webapps-container-deploy)você pode automatizar seu fluxo de trabalho para implantar aplicativos como [contêineres personalizados para o App Service](https://azure.microsoft.com/services/app-service/containers/) usando as Ações do GitHub.

> [!IMPORTANT]
> O GitHub Actions está atualmente em beta. Você deve primeiro [se inscrever para participar da pré-visualização](https://github.com/features/actions) usando sua conta do GitHub.
> 

Um fluxo de trabalho é definido por um arquivo `/.github/workflows/` YAML (.yml) no caminho em seu repositório. Esta definição contém as várias etapas e parâmetros que compõem o fluxo de trabalho.

Para um fluxo de trabalho de contêiner do Azure App Service, o arquivo tem três seções:

|Seção  |Tarefas  |
|---------|---------|
|**Autenticação** | 1. Defina um diretor de serviço. <br /> 2. Crie um segredo do GitHub. |
|**Construir** | 1. Configure o ambiente. <br /> 2. Construa a imagem do recipiente. |
|**Implantar** | 1. Implante a imagem do contêiner. |

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

Você pode criar um [principal de serviço](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) usando o comando [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) no [Azure CLI](https://docs.microsoft.com/cli/azure/). Você pode executar este comando usando [o Azure Cloud Shell](https://shell.azure.com/) no portal Azure ou selecionando o botão **Tente.o.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
                            
# Replace {subscription-id}, {resource-group} with the subscription, resource group details of the WebApp
```

A saída é um objeto JSON com as credenciais de atribuição de função que fornecem acesso ao seu aplicativo App Service semelhante ao abaixo. Copie este objeto JSON para autenticar do GitHub.

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
> É sempre uma boa prática conceder acesso mínimo. Você pode restringir o escopo no comando Az CLI acima ao aplicativo específico do App Service e ao Registro de Contêineres do Azure, para onde as imagens do contêiner são empurradas.

## <a name="configure-the-github-secret"></a>Configure o segredo do GitHub

O exemplo abaixo usa credenciais de nível de usuário, ou seja, O Principal de Serviço do Azure para implantação. Siga as etapas para configurar o segredo:

1. No [GitHub,](https://github.com/)navegue pelo seu repositório, selecione **Configurações > Segredos > adicionar um novo segredo**

2. Cole o conteúdo do `az cli` comando abaixo como o valor da variável secreta. Por exemplo, `AZURE_CREDENTIALS`.

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. Agora, no arquivo de fluxo `.github/workflows/workflow.yml` de trabalho em sua filial: substitua o segredo na ação de login do Azure pelo seu segredo.

4. Da mesma forma, defina os seguintes segredos adicionais para as credenciais de registro de contêiner e defina-os na ação de login do Docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

5. Você vê os segredos como mostrado abaixo uma vez definido.

    ![segredos contêiner](../media/app-service-github-actions/app-service-secrets-container.png)

## <a name="build-the-container-image"></a>Construa a imagem do Container

O exemplo a seguir mostra parte do fluxo de trabalho que constrói a imagem do docker.

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
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
```

## <a name="deploy-to-an-app-service-container"></a>Implantar em um contêiner app service

Para implantar sua imagem em um contêiner `azure/webapps-container-deploy@v1` personalizado no App Service, use a ação. Esta ação tem cinco parâmetros:

| **Parâmetro**  | **Explicação**  |
|---------|---------|
| **nome de aplicativo** | (Obrigatório) Nome do aplicativo App Service | 
| **nome de caça-níquel** | (Opcional) Digite um slot existente que não seja o slot de produção |
| **Imagens** | (Obrigatório) Especifique o nome da imagem do contêiner totalmente qualificado. Por exemplo, 'myregistry.azurecr.io/nginx:latest' ou 'python:3.7.2-alpine/'. Para um aplicativo de vários contêineres, vários nomes de imagens de contêiner podem ser fornecidos (separados em várias linhas) |
| **arquivo de configuração** | (Opcional) Caminho do arquivo Docker-Compose. Deve ser um caminho totalmente qualificado ou em relação ao diretório de trabalho padrão. Necessário para aplicativos de vários contêineres. |
| **contêiner-comando** | (Opcional) Digite o comando start-up. Para ex. dotnet executar ou dotnet filename.dll |

Abaixo está o fluxo de trabalho de exemplo para construir e implantar um aplicativo Node.js em um contêiner personalizado no App Service.

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
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }} 
      
    - uses: azure/webapps-container-deploy@v1
      with:
        app-name: 'node-rnc'
        images: 'contoso.azurecr.io/nodejssampleapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

## <a name="next-steps"></a>Próximas etapas

Você pode encontrar nosso conjunto de Ações agrupadas em diferentes repositórios no GitHub, cada um contendo documentação e exemplos para ajudá-lo a usar o GitHub para CI/CD e implantar seus aplicativos no Azure.

- [Logon do Azure](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [WebApp do Azure para contêineres](https://github.com/Azure/webapps-container-deploy)

- [Login/logout do Docker](https://github.com/Azure/docker-login)

- [Eventos que desencadeiam fluxos de trabalho](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s implantados](https://github.com/Azure/k8s-deploy)

- [Fluxos de trabalho de INÍCIO CI](https://github.com/actions/starter-workflows)

- [Fluxos de trabalho iniciais para implantar no Azure](https://github.com/Azure/actions-workflow-samples)
