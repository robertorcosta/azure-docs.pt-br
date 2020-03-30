---
title: Autenticação do cluster Kubernetes
description: Aprenda a fornecer a um cluster Kubernetes acesso a imagens em seu registro de contêineres do Azure criando um segredo de tração usando um principal de serviço
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 02/10/2020
ms.openlocfilehash: 0608ca0e0e53acf2f19910a7f1107dacf67d4e61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77154888"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster"></a>Puxe imagens de um registro de contêiner azure para um cluster Kubernetes

Você pode usar um registro de contêiner do Azure como fonte de imagens de contêineres com qualquer cluster Kubernetes, incluindo clusters Kubernetes "locais", como [minikube](https://minikube.sigs.k8s.io/) e [tipo](https://kind.sigs.k8s.io/). Este artigo mostra como criar um segredo de atração kubernetes baseado em um diretor de serviço do Azure Active Directory. Em seguida, use o segredo para retirar imagens de um registro de contêiner do Azure em uma implantação do Kubernetes.

> [!TIP]
> Se você estiver usando o [Serviço Azure Kubernetes](../aks/intro-kubernetes.md)gerenciado, você também pode [integrar seu cluster](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) com um registro de contêiner Azure direcionado para retiradas de imagens. 

Este artigo pressupõe que você já criou um registro privado de contêineres do Azure. Você também precisa ter um cluster Kubernetes `kubectl` em execução e acessível através da ferramenta de linha de comando.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

Se você não salvar ou lembrar a senha principal do serviço, você pode redefini-la com o comando [az ad sp credencial reset:][az-ad-sp-credential-reset]

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

Este comando retorna uma nova senha válida para o seu diretor de serviço.

## <a name="create-an-image-pull-secret"></a>Crie um segredo de atração de imagem

Kubernetes usa um *segredo de atração de imagens* para armazenar informações necessárias para autenticar em seu registro. Para criar o segredo de tração para um registro de contêiner do Azure, você fornece o ID principal do serviço, a senha e o URL do registro. 

Crie um segredo de `kubectl` atração de imagem com o seguinte comando:

```console
kubectl create secret docker-registry <secret-name> \
  --namespace <namespace> \
  --docker-server=https://<container-registry-name>.azurecr.io \
  --docker-username=<service-principal-ID> \
  --docker-password=<service-principal-password>
```
onde:

| Valor | Descrição |
| :--- | :--- |
| `secret-name` | Nome do segredo de atração de imagem, por exemplo, *acr-secret* |
| `namespace` | Kubernetes namespace para colocar o segredo em <br/> Só é necessário se você quiser colocar o segredo em um namespace diferente do namespace padrão |
| `container-registry-name` | Nome do seu registro de contêiner Azure |
| `service-principal-ID` | ID do principal de serviço que será usado pela Kubernetes para acessar seu registro |
| `service-principal-password` | Senha principal do serviço |

## <a name="use-the-image-pull-secret"></a>Use o segredo de puxar a imagem

Depois de criar o segredo de puxar a imagem, você pode usá-la para criar pods e implantações kubernetes. Forneça o nome do `imagePullSecrets` segredo no arquivo de implantação. Por exemplo: 

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: your-awesome-app-pod
  namespace: awesomeapps
spec:
  containers:
    - name: main-app-container
      image: your-awesome-app:v1
      imagePullPolicy: IfNotPresent
  imagePullSecrets:
    - name: acr-secret
```

No exemplo anterior, `your-awesome-app:v1` está o nome da imagem a ser retirada `acr-secret` do registro de contêiner do Azure, e é o nome do segredo de tração que você criou para acessar o registro. Quando você implanta o pod, o Kubernetes retira automaticamente a imagem do seu registro, se ela ainda não estiver presente no cluster.


## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre como trabalhar com os diretores de serviços e o Registro de Contêineres do Azure, consulte [a autenticação do Registro de Contêineres do Azure com os diretores de serviços](container-registry-auth-service-principal.md)
* Saiba mais sobre segredos de atração de imagens na documentação do [Kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset