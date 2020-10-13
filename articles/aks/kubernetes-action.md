---
title: Compilar, testar e implantar contêineres no serviço kubernetes do Azure usando ações do GitHub
description: Saiba como usar as ações do GitHub para implantar seu contêiner no kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: 7743a3a8d6e77affd6229b648ab79b5b2f07a0af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90564093"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>Ações do GitHub para implantação no serviço kubernetes

O [GitHub Actions](https://help.github.com/en/articles/about-github-actions) oferece a flexibilidade para criar um fluxo de trabalho do ciclo de vida de desenvolvimento de software automatizado. A ação kubernetes [azure/aks-set-context@v1](https://github.com/Azure/aks-set-context) facilita implantações em clusters do serviço kubernetes do Azure. A ação define o contexto de cluster AKS de destino, que pode ser usado por outras ações, como [Azure/K8S-Deploy](https://github.com/Azure/k8s-deploy/tree/master), [Azure/K8S-Create-Secret,](https://github.com/Azure/k8s-create-secret/tree/master) etc. ou executar qualquer comando kubectl.

Um fluxo de trabalho é definido por um arquivo YAML (.yml) no caminho `/.github/workflows/` no repositório. Essa definição contém as várias etapas e os parâmetros que compõem o fluxo de trabalho.

Para um fluxo de trabalho direcionado a AKS, o arquivo tem três seções:

|Seção  |Tarefas  |
|---------|---------|
|**Autenticação** | Fazer logon em um ACR (registro de contêiner privado) |
|**Compilar** | Compilar & enviar por push a imagem de contêiner  |
|**Implantar** | 1. definir o cluster AKS de destino |
| |2. criar um segredo de registro genérico/Docker no cluster kubernetes  |
||3. implantar no cluster kubernetes|

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

Crie uma [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) usando o comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) na [CLI do Azure](/cli/azure/). Execute esse comando com o [Azure Cloud Shell](https://shell.azure.com/) no portal do Azure ou selecionando o botão **Experimentar**.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

No comando acima, substitua os espaços reservados por sua ID de assinatura e grupo de recursos. A saída são as credenciais de atribuição de função que fornecem acesso ao recurso. O comando deve gerar um objeto JSON semelhante a este.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
Copie esse objeto JSON, que você pode usar para se autenticar por meio do GitHub.

## <a name="configure-the-github-secrets"></a>Configurar os segredos do GitHub

Siga as etapas para configurar os segredos:

1. No [GitHub](https://github.com/), navegue até o repositório, selecione **configurações > segredos > adicionar um novo segredo**.

    ![Captura de tela mostra o link adicionar um novo segredo para um repositório.](media/kubernetes-action/secrets.png)

2. Cole o conteúdo do comando acima `az cli` como o valor da variável secreta. Por exemplo, `AZURE_CREDENTIALS`.

3. Da mesma forma, defina os seguintes segredos adicionais para as credenciais de registro de contêiner e defina-as na ação de logon do Docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. Você verá os segredos conforme mostrado abaixo, uma vez definidos.

    ![Captura de tela mostra os segredos existentes para um repositório.](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Criar uma imagem de contêiner e implantar no cluster do serviço kubernetes do Azure

A compilação e o push das imagens de contêiner são feitas usando a `Azure/docker-login@v1` ação. Para implantar uma imagem de contêiner no AKS, você precisará usar a `Azure/k8s-deploy@v1` ação. Esta ação tem cinco parâmetros:

| **Parâmetro**  | **Explicação**  |
|---------|---------|
| **namespace** | Adicional Escolha o namespace kubernetes de destino. Se o namespace não for fornecido, os comandos serão executados no namespace padrão | 
| **manifestos** |  Necessária Caminho para os arquivos de manifesto que serão usados para implantação |
| **images** | Adicional URL de recurso totalmente qualificado das imagens a serem usadas para substituições nos arquivos de manifesto |
| **imagepullsecrets** | Adicional Nome de um segredo do registro do Docker que já foi configurado no cluster. Cada um desses nomes de segredo é adicionado no campo imagePullSecrets para as cargas de trabalho encontradas nos arquivos de manifesto de entrada |
| **kubectl-versão** | Adicional Instala uma versão específica do binário kubectl |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Implantar no cluster do serviço kubernetes do Azure

Fluxo de trabalho de ponta a ponta para a criação de imagens de contêiner e implantação em um cluster do serviço kubernetes do Azure.

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

Você pode encontrar nosso conjunto de ações em repositórios diferentes no GitHub, cada um contendo documentação e exemplos para ajudá-lo a usar o GitHub para CI/CD e implantar seus aplicativos no Azure.

- [instalação-kubectl](https://github.com/Azure/setup-kubectl)

- [K8S-Set-Context](https://github.com/Azure/k8s-set-context)

- [AKs-Set-Context](https://github.com/Azure/aks-set-context)

- [K8S-criar-segredo](https://github.com/Azure/k8s-create-secret)

- [K8S-implantar](https://github.com/Azure/k8s-deploy)

- [webapps-contêiner-implantar](https://github.com/Azure/webapps-container-deploy)

- [ações-fluxo de trabalho-amostras](https://github.com/Azure/actions-workflow-samples)
