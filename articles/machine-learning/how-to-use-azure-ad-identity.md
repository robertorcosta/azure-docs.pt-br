---
title: Use a identidade AAD com seu serviço web
titleSuffix: Azure Machine Learning
description: Use a identidade AAD com seu serviço web no Azure Kubernetes Service para acessar recursos na nuvem durante a pontuação.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: f997aef59e91bed325b84af855a84f43cd639d83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122838"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Use a identidade Azure AD com seu serviço web de aprendizado de máquina no Azure Kubernetes Service

Neste how-to, você aprende a atribuir uma identidade AAD (AAD) do Azure Active Directory ao seu modelo de aprendizado de máquina implantado no Azure Kubernetes Service. O projeto [AAD Pod Identity](https://github.com/Azure/aad-pod-identity) permite que os aplicativos acessem recursos em nuvem com segurança com a AAD usando uma [identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) e primitivos Kubernetes. Isso permite que seu serviço web acesse com segurança seus recursos do Azure `score.py` sem ter que incorporar credenciais ou gerenciar tokens diretamente dentro do seu script. Este artigo explica as etapas para criar e instalar uma Identidade Azure no cluster do Azure Kubernetes Service e atribuir a identidade ao seu serviço web implantado.

## <a name="prerequisites"></a>Pré-requisitos

- A [extensão Azure CLI para o serviço de Aprendizado de Máquina,](reference-azure-machine-learning-cli.md)o [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou a [extensão Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

- Acesso ao seu cluster AKS usando o `kubectl` comando. Para obter mais informações, consulte [Conectar-se ao cluster](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)

- Um serviço web de aprendizado de máquina do Azure implantado no seu cluster AKS.

## <a name="create-and-install-an-azure-identity-in-your-aks-cluster"></a>Crie e instale uma Identidade Azure em seu cluster AKS

1. Para determinar se o cluster AKS está ativado pelo RBAC, use o seguinte comando:

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    Este comando retorna `true` um valor de se RBAC estiver habilitado. Este valor determina o comando a ser usado na próxima etapa.

1. Para instalar [a Identidade aad pod](https://github.com/Azure/aad-pod-identity#getting-started) no cluster AKS, use um dos seguintes comandos:

    * Se o cluster AKS tiver **o RBAC ativado,** use o seguinte comando:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
        ```
    
    * Se o cluster AKS **não tiver RBAC ativado,** use o seguinte comando:
    
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

1. [Crie uma identidade azure](https://github.com/Azure/aad-pod-identity#2-create-an-azure-identity) seguindo as etapas mostradas na página do projeto AAD Pod Identity.

1. [Instale a Identidade Azure](https://github.com/Azure/aad-pod-identity#3-install-the-azure-identity) seguindo as etapas mostradas na página do projeto AAD Pod Identity.

1. [Instale a Vinculação de Identidade do Azure](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding) seguindo as etapas mostradas na página do projeto AAD Pod Identity.

1. Se a identidade azure criada na etapa anterior não estiver no mesmo grupo de recursos do seu cluster AKS, siga [As permissões definidas para MIC](https://github.com/Azure/aad-pod-identity#6-set-permissions-for-mic) seguindo as etapas mostradas na página do projeto AAD Pod Identity.

## <a name="assign-azure-identity-to-machine-learning-web-service"></a>Atribuir identidade Azure ao serviço web de aprendizado de máquina

As etapas a seguir usam a Identidade Azure criada na seção anterior e atribuem-a ao serviço web AKS através de um **rótulo seletor**.

Primeiro, identifique o nome e o namespace de sua implantação no cluster AKS que você deseja atribuir à Identidade Azure. Você pode obter essas informações executando o seguinte comando. Os namespaces devem ser o nome do espaço de trabalho do Azure Machine Learning e seu nome de implantação deve ser o nome do ponto final, conforme mostrado no portal.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

Adicione o rótulo seletor de identidade azure à sua implantação editando a especificação de implantação. O valor do seletor deve ser o que você definiu na etapa 5 de [Instalar a Vinculação de Identidade do Azure](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding).

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

Edite a implantação para adicionar o rótulo seletor de identidade azure. Vá para a `/spec/template/metadata/labels`seção a seguir em . Você deve ver `isazuremlapp: “true”`valores como . Adicione o rótulo aad-pod-identity como mostrado abaixo.

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

Para verificar se o rótulo foi adicionado corretamente, execute o seguinte comando.

```azurecli-interactive
   kubectl get deployment <name of deployment> -n azureml-<name of workspace> --show-labels
```

Para ver todos os status do pod, execute o seguinte comando.

```azurecli-interactive
    kubectl get pods -n azureml-<name of workspace>
```

Uma vez que os pods estejam em funcionamento, os serviços web para essa implantação agora poderão acessar os recursos do Azure através de sua Identidade Azure sem ter que incorporar as credenciais em seu código. 

## <a name="assign-the-appropriate-roles-to-your-azure-identity"></a>Atribuir as funções apropriadas à sua Identidade Azure

[Atribua sua Identidade Gerenciada do Azure com funções apropriadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) para acessar outros recursos do Azure. Certifique-se de que as funções atribuídas têm as **ações de dados corretas.** Por exemplo, a [função de leitor de dados blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) terá permissões de leitura para o seu Blob de armazenamento, enquanto a função de [leitor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) genérico pode não ser.

## <a name="use-azure-identity-with-your-machine-learning-web-service"></a>Use o Azure Identity com seu serviço web de aprendizado de máquina

Implante um modelo no seu cluster AKS. O `score.py` script pode conter operações que apontam para os recursos do Azure aos os que sua Identidade Azure tem acesso. Certifique-se de que você instalou suas dependências de biblioteca de clientes necessárias para o recurso que você está tentando acessar. Abaixo estão alguns exemplos de como você pode usar sua Identidade Azure para acessar diferentes recursos do Azure a partir do seu serviço.

### <a name="access-key-vault-from-your-web-service"></a>Acesse key vault do seu serviço web

Se você deu à sua Identidade Azure acesso a `score.py` um segredo dentro de um Cofre de **Chaves,** você pode acessá-lo usando o código a seguir.

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

### <a name="access-blob-from-your-web-service"></a>Acesse blob do seu serviço web

Se você tiver dado à sua Identidade Azure acesso `score.py` aos dados dentro de um **Storage Blob,** você pode acessá-los usando o seguinte código.

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

* Para obter mais informações sobre como usar a biblioteca cliente do Cliente Identidade Python Azure, consulte o [repositório](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python) no GitHub.
* Para obter um guia detalhado sobre a implantação de modelos nos clusters do Azure Kubernetes Service, consulte [o how-to](how-to-deploy-azure-kubernetes-service.md).