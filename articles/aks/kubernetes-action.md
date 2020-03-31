---
title: Construa, teste e implante contêineres no Azure Kubernetes Service usando as ações do GitHub
description: Saiba como usar as ações do GitHub para implantar seu contêiner no Kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: 5ee8ee4d2c9e225d82e58daffeef9e5f09e43e6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595358"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>GitHub Ações para implantação no serviço Kubernetes

[O GitHub Actions](https://help.github.com/en/articles/about-github-actions) oferece flexibilidade para construir um fluxo de trabalho automatizado do ciclo de vida do desenvolvimento de software. A ação [azure/aks-set-context@v1](https://github.com/Azure/aks-set-context) do Kubernetes facilita implantações em clusters do Azure Kubernetes Service. A ação define o contexto de cluster AKS de destino, que pode ser usado por outras ações como [azure/k8s-deploy](https://github.com/Azure/k8s-deploy/tree/master), [azure/k8s-create-secret](https://github.com/Azure/k8s-create-secret/tree/master) etc. ou executar quaisquer comandos kubectl.

Um fluxo de trabalho é definido por um arquivo `/.github/workflows/` YAML (.yml) no caminho em seu repositório. Esta definição contém as várias etapas e parâmetros que compõem o fluxo de trabalho.

Para um fluxo de trabalho direcionado ao AKS, o arquivo tem três seções:

|Seção  |Tarefas  |
|---------|---------|
|**Autenticação** | Faça login em um registro de contêiner privado (ACR) |
|**Construir** | Construir & empurrar a imagem do recipiente  |
|**Implantar** | 1. Defina o cluster AKS de destino |
| |2. Crie um segredo genérico/de registro de docker no cluster Kubernetes  |
||3. Implantar no cluster Kubernetes|

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

Você pode criar um [principal de serviço](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) usando o comando [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) no [Azure CLI](https://docs.microsoft.com/cli/azure/). Você pode executar este comando usando [o Azure Cloud Shell](https://shell.azure.com/) no portal Azure ou selecionando o botão **Tente.o.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

No comando acima, substitua os espaços reservados por seu ID de assinatura e grupo de recursos. A saída são as credenciais de atribuição de função que fornecem acesso ao seu recurso. O comando deve produzir um objeto JSON semelhante a este.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
Copie este objeto JSON, que você pode usar para autenticar do GitHub.

## <a name="configure-the-github-secrets"></a>Configure os segredos do GitHub

Siga as etapas para configurar os segredos:

1. No [GitHub,](https://github.com/)navegue até o seu repositório, selecione **Configurações > Segredos > Adicione um novo segredo**.

    ![segredos](media/kubernetes-action/secrets.png)

2. Cole o conteúdo do `az cli` comando acima como o valor da variável secreta. Por exemplo, `AZURE_CREDENTIALS`.

3. Da mesma forma, defina os seguintes segredos adicionais para as credenciais de registro de contêiner e defina-os na ação de login do Docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. Você verá os segredos como mostrado abaixo uma vez definido.

    ![kubernetes-segredos](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Construa uma imagem de contêiner e implante no cluster Azure Kubernetes Service

A construção e o empurrão das `Azure/docker-login@v1` imagens do contêiner são feitos usando a ação. Para implantar uma imagem de contêiner no AKS, você precisará usar a `Azure/k8s-deploy@v1` ação. Esta ação tem cinco parâmetros:

| **Parâmetro**  | **Explicação**  |
|---------|---------|
| **namespace** | (Opcional) Escolha o espaço de nome kubernetes de destino. Se o namespace não for fornecido, os comandos serão executados no namespace padrão | 
| **Manifestos** |  (Obrigatório) Caminho para os arquivos manifestos, que serão usados para implantação |
| **Imagens** | (Opcional) URL de recurso totalmente qualificado da imagem(s) a ser usado para substituições nos arquivos manifestos |
| **imagepullsecrets** | (Opcional) Nome de um segredo de registro de docker que já foi configurado dentro do cluster. Cada um desses nomes secretos é adicionado no campo imagePullSecrets para as cargas de trabalho encontradas nos arquivos manifestos de entrada |
| **kubectl-versão** | (Opcional) Instala uma versão específica do binário kubectl |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Implantar no cluster de serviço subernetes do Azure Kubernetes

Fluxo de trabalho de ponta a ponta para a construção de imagens de contêineres e implantação em um cluster do Azure Kubernetes Service.

```yaml
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    
    - uses: Azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/k8sdemo:${{ github.sha }}
        docker push contoso.azurecr.io/k8sdemo:${{ github.sha }}
      
    # Set the target AKS cluster.
    - uses: Azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: contoso
        resource-group: contoso-rg
        
    - uses: Azure/k8s-create-secret@v1
      with:
        container-registry-url: contoso.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: demo-k8s-secret

    - uses: Azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          demo.azurecr.io/k8sdemo:${{ github.sha }}
        imagepullsecrets: |
          demo-k8s-secret
```

## <a name="next-steps"></a>Próximas etapas

Você pode encontrar nosso conjunto de Ações em diferentes repositórios no GitHub, cada um contendo documentação e exemplos para ajudá-lo a usar o GitHub para CI/CD e implantar seus aplicativos no Azure.

- [configuração-kubectl](https://github.com/Azure/setup-kubectl)

- [k8s-set-context](https://github.com/Azure/k8s-set-context)

- [aks-set-context](https://github.com/Azure/aks-set-context)

- [k8s-create-secret](https://github.com/Azure/k8s-create-secret)

- [k8s-deploy](https://github.com/Azure/k8s-deploy)

- [webapps-contêiner-implantação](https://github.com/Azure/webapps-container-deploy)

- [ações-amostras de fluxo de trabalho](https://github.com/Azure/actions-workflow-samples)
