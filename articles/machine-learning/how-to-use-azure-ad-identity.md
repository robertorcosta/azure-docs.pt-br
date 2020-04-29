---
title: Usar a identidade do AAD com seu serviço Web
titleSuffix: Azure Machine Learning
description: Use a identidade do AAD com seu serviço Web no serviço de kubernetes do Azure para acessar recursos de nuvem durante a pontuação.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: f997aef59e91bed325b84af855a84f43cd639d83
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77122838"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Usar a identidade do Azure AD com o serviço Web do Machine Learning no serviço kubernetes do Azure

Neste "como", você aprende a atribuir uma identidade de Azure Active Directory (AAD) ao seu modelo de aprendizado de máquina implantado no serviço kubernetes do Azure. O projeto de [identidade Pod do AAD](https://github.com/Azure/aad-pod-identity) permite que os aplicativos acessem recursos de nuvem com segurança com o AAD usando uma [identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) e primitivos kubernetes. Isso permite que seu serviço Web acesse seus recursos do Azure com segurança sem precisar inserir credenciais ou gerenciar tokens diretamente `score.py` dentro do script. Este artigo explica as etapas para criar e instalar uma identidade do Azure em seu cluster do serviço kubernetes do Azure e atribuir a identidade ao serviço Web implantado.

## <a name="prerequisites"></a>Pré-requisitos

- A [extensão CLI do Azure para o serviço Machine Learning](reference-azure-machine-learning-cli.md), o [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou a [extensão Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

- Acesso ao cluster AKS usando o `kubectl` comando. Para obter mais informações, consulte [conectar-se ao cluster](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)

- Um serviço Web Azure Machine Learning implantado em seu cluster AKS.

## <a name="create-and-install-an-azure-identity-in-your-aks-cluster"></a>Criar e instalar uma identidade do Azure em seu cluster AKS

1. Para determinar se o cluster AKS está habilitado para RBAC, use o seguinte comando:

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    Esse comando retornará um valor de `true` se o RBAC estiver habilitado. Esse valor determina o comando a ser usado na próxima etapa.

1. Para instalar a [identidade do Pod do AAD](https://github.com/Azure/aad-pod-identity#getting-started) em seu cluster AKs, use um dos seguintes comandos:

    * Se o cluster AKS tiver o **RBAC habilitado** , use o seguinte comando:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
        ```
    
    * Se o cluster AKS não **tiver o RBAC habilitado**, use o seguinte comando:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
        ```
    
        A saída do comando é semelhante ao seguinte texto:

        ```text
        customresourcedefinition.apiextensions.k8s.io/azureassignedidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentitybindings.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azurepodidentityexceptions.aadpodidentity.k8s.io created
        daemonset.apps/nmi created
        deployment.apps/mic created
        ```

1. [Crie uma identidade do Azure](https://github.com/Azure/aad-pod-identity#2-create-an-azure-identity) seguindo as etapas mostradas na página projeto de identidade do Pod do AAD.

1. [Instale a identidade do Azure](https://github.com/Azure/aad-pod-identity#3-install-the-azure-identity) seguindo as etapas mostradas na página projeto de identidade Pod do AAD.

1. [Instale a associação de identidade do Azure](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding) seguindo as etapas mostradas na página projeto de identidade Pod do AAD.

1. Se a identidade do Azure criada na etapa anterior não estiver no mesmo grupo de recursos que o cluster AKS, siga as [permissões definidas para o MIC](https://github.com/Azure/aad-pod-identity#6-set-permissions-for-mic) seguindo as etapas mostradas na página projeto de identidade Pod do AAD.

## <a name="assign-azure-identity-to-machine-learning-web-service"></a>Atribuir identidade do Azure ao serviço Web do Machine Learning

As etapas a seguir usam a identidade do Azure criada na seção anterior e a atribuem ao serviço Web AKS por meio de um **rótulo de seletor**.

Primeiro, identifique o nome e o namespace de sua implantação no cluster AKS para o qual você deseja atribuir a identidade do Azure. Você pode obter essas informações executando o comando a seguir. Os namespaces devem ser seu Azure Machine Learning nome do espaço de trabalho e o nome da implantação deve ser o nome do ponto de extremidade, conforme mostrado no Portal.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

Adicione o rótulo do seletor de identidade do Azure à sua implantação editando a especificação de implantação. O valor do seletor deve ser aquele que você definiu na etapa 5 de [instalar a associação de identidade do Azure](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding).

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

Edite a implantação para adicionar o rótulo do seletor de identidade do Azure. Vá para a seção a seguir `/spec/template/metadata/labels`em. Você deve ver valores como `isazuremlapp: “true”`. Adicione o rótulo AAD-Pod-Identity, como mostrado abaixo.

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
      - aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

Para verificar se o rótulo foi adicionado corretamente, execute o comando a seguir.

```azurecli-interactive
   kubectl get deployment <name of deployment> -n azureml-<name of workspace> --show-labels
```

Para ver todos os status de Pod, execute o comando a seguir.

```azurecli-interactive
    kubectl get pods -n azureml-<name of workspace>
```

Depois que os pods estiverem em funcionamento, os serviços Web para essa implantação agora poderão acessar os recursos do Azure por meio de sua identidade do Azure sem precisar inserir as credenciais em seu código. 

## <a name="assign-the-appropriate-roles-to-your-azure-identity"></a>Atribuir as funções apropriadas à sua identidade do Azure

[Atribua sua identidade gerenciada do Azure com as funções apropriadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) para acessar outros recursos do Azure. Verifique se as funções que você está atribuindo têm as **ações de dados**corretas. Por exemplo, a [função de leitor de dados de blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) terá permissões de leitura para seu blob de armazenamento, enquanto a [função leitor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) genérica pode não.

## <a name="use-azure-identity-with-your-machine-learning-web-service"></a>Usar a identidade do Azure com o serviço Web do Machine Learning

Implante um modelo no cluster AKS. O `score.py` script pode conter operações que apontam para os recursos do Azure aos quais sua identidade do Azure tem acesso. Verifique se você instalou as dependências de biblioteca de cliente necessárias para o recurso ao qual você está tentando acessar. Abaixo estão alguns exemplos de como você pode usar sua identidade do Azure para acessar diferentes recursos do Azure do seu serviço.

### <a name="access-key-vault-from-your-web-service"></a>Acesse Key Vault de seu serviço Web

Se você tiver dado ao seu acesso de leitura de identidade do Azure a **Key Vault**um segredo dentro `score.py` de um Key Vault, seu poderá acessá-lo usando o código a seguir.

```python
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

my_vault_name = "yourkeyvaultname"
my_vault_url = "https://{}.vault.azure.net/".format(my_vault_name) 
my_secret_name = "sample-secret"

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
secret_client = SecretClient(
    vault_url=my_vault_url,
    credential=credential)
secret = secret_client.get_secret(my_secret_name)
```

### <a name="access-blob-from-your-web-service"></a>Acessar o blob do serviço Web

Se você tiver dado ao seu acesso de leitura de identidade do Azure aos dados dentro de `score.py` um blob de **armazenamento**, você poderá acessá-lo usando o código a seguir.

```python
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobServiceClient

my_storage_account_name = "yourstorageaccountname"
my_storage_account_url = "https://{}.blob.core.windows.net/".format(my_storage_account_name)

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
blob_service_client = BlobServiceClient(
    account_url=my_storage_account_url,
    credential=credential
)
blob_client = blob_service_client.get_blob_client(container="some-container", blob="some_text.txt")
blob_data = blob_client.download_blob()
blob_data.readall()
```

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre como usar a biblioteca de cliente de identidade do Azure para Python, consulte o [repositório](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python) no github.
* Para obter um guia detalhado sobre como implantar modelos em clusters do serviço kubernetes do Azure, consulte a [instruções](how-to-deploy-azure-kubernetes-service.md).