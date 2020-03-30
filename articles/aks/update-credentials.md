---
title: Redefinir as credenciais para um cluster do AKS (Serviço de Kubernetes do Azure)
description: Saiba como atualizar ou redefinir as credenciais do serviço principal ou do aplicativo AAD para um cluster Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: b7d652be3733cb130a3973909de59489047efe0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475537"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>Atualize ou gire as credenciais para o Azure Kubernetes Service (AKS)

Por padrão, os clusters do AKS são criados com uma entidade de serviço que tem um tempo de término de um ano. Conforme você se aproxima da data do término, pode redefinir as credenciais para estender a entidade de serviço por um período adicional. Talvez você também deseje atualizar ou girar as credenciais como parte de uma política de segurança definida. Este artigo fornece detalhes de como atualizar essas credenciais para um cluster do AKS.

Você também pode ter [integrado seu cluster AKS com o Azure Active Directory][aad-integration]e usá-lo como um provedor de autenticação para o seu cluster. Nesse caso, você terá mais 2 identidades criadas para o seu cluster, o AAD Server App e o AAD Client App, você também pode redefinir essas credenciais. 

## <a name="before-you-begin"></a>Antes de começar

Você precisa da versão 2.0.65 do Azure CLI ou posteriormente instalada e configurada. Execute  `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, confira  [Instalar a CLI do Azure][install-azure-cli].

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>Atualize ou crie um novo Diretor de Serviço para o seu cluster AKS

Quando desejar atualizar as credenciais para um cluster do AKS, você poderá optar por:

* atualizar as credenciais para a entidade de serviço existente usadas pelo cluster; ou
* criar uma entidade de serviço e atualizar o cluster para usar essas novas credenciais.

### <a name="reset-existing-service-principal-credential"></a>Redefinir credencial principal do serviço existente

Para atualizar as credenciais para a entidade de serviço existente, obtenha a ID da entidade de serviço do cluster usando o comando [az aks show][az-aks-show]. O exemplo a seguir obtém a ID do cluster chamado *myAKSCluster* no grupo de recursos *myResourceGroup*. O ID principal do serviço é definido como uma variável chamada *SP_ID* para uso em comando adicional.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

Com um conjunto de variáveis que contém a ID da entidade de serviço, redefina agora as credenciais usando [az ad sp credential reset][az-ad-sp-credential-reset]. O exemplo a seguir permite que a plataforma Azure gere um novo segredo seguro para a entidade de serviço. Esse novo segredo seguro também é armazenado como uma variável.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Agora continue [atualizando o cluster AKS com novas credenciais principais de serviço](#update-aks-cluster-with-new-service-principal-credentials). Esta etapa é necessária para que as alterações do Service Principal reflitam sobre o cluster AKS.

### <a name="create-a-new-service-principal"></a>Criar um novo diretor de serviço

Se você optar por atualizar as credenciais da entidade de serviço existente na seção anterior, ignore esta etapa. Continue [atualizando o cluster AKS com novas credenciais principais de serviço](#update-aks-cluster-with-new-service-principal-credentials).

Para criar uma entidade de serviço e, em seguida, atualizar o cluster do AKS para usar essas novas credenciais, use o comando [az ad sp create-for-rbac][az-ad-sp-create]. No exemplo abaixo, o parâmetro `--skip-assignment` impede que outras atribuições padrão sejam atribuídas:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

A saída deverá ser semelhante ao seguinte exemplo: Anote seu próprio `appId` e `password`. Esses valores serão usados na próxima etapa.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

Agora defina as variáveis para a ID da entidade de serviço e o segredo do cliente usando a saída do próprio comando [az ad sp create-for-rbac][az-ad-sp-create], conforme mostrado no exemplo a seguir. A *SP_ID* é sua *appId* e o *SP_SECRET* é sua *password*:

```console
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Agora continue [atualizando o cluster AKS com novas credenciais principais de serviço](#update-aks-cluster-with-new-service-principal-credentials). Esta etapa é necessária para que as alterações do Service Principal reflitam sobre o cluster AKS.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>Atualize o cluster AKS com novas credenciais do Service Principal

Independentemente se você optou por atualizar as credenciais da entidade de serviço existente ou criar uma entidade de serviço, agora você atualizará o cluster do AKS com suas novas credenciais usando o comando [az aks update-credentials][az-aks-update-credentials]. As variáveis para *--service-principal* e *--client-secret* são usadas:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Serão necessários alguns instantes para que as credenciais da entidade de serviço sejam atualizadas no AKS.

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>Atualize o Cluster AKS com novas credenciais de aplicativo AAD

Você pode criar novos aplicativos AAD Server e Client seguindo as [etapas de integração AAD][create-aad-app]. Ou redefinir seus aplicativos AAD existentes seguindo o [mesmo método de reset principal do serviço](#reset-existing-service-principal-credential). Depois disso, você só precisa atualizar as credenciais do aplicativo AAD do cluster usando o mesmo comando [az aks update-credentials,][az-aks-update-credentials] mas usando as variáveis *--reset-aad.*

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-aad \
    --aad-server-app-id <SERVER APPLICATION ID> \
    --aad-server-app-secret <SERVER APPLICATION SECRET> \
    --aad-client-app-id <CLIENT APPLICATION ID>
```


## <a name="next-steps"></a>Próximas etapas

Neste artigo, o principal de serviço do próprio cluster AKS e dos Aplicativos de Integração AAD foram atualizados. Para obter mais informações sobre como gerenciar a identidade para cargas de trabalho em um cluster, confira [Melhores práticas de autenticação e autorização no AKS][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: azure-ad-integration.md
[create-aad-app]: azure-ad-integration.md#create-the-server-application
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
