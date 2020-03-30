---
title: Use a Azure AD no Serviço Azure Kubernetes
description: Saiba como usar o Azure AD no Azure Kubernetes Service (AKS)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 03/24/2020
ms.openlocfilehash: 1ca4b70139ed5e0a136f6f5f2b0382b8c1688983
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389402"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>Integre o Azure AD no Azure Kubernetes Service (Preview)

> [!Note]
> Os clusters AKS v1 existentes com integração AD não são afetados pela nova experiência AKS v2.

A integração do Azure AD com o AKS v2 foi projetada para simplificar a integração do Azure AD com a experiência AKS v1, onde os usuários eram obrigados a criar um aplicativo cliente, um aplicativo de servidor e exigiam que o inquilino do Azure AD concedesse permissões de Leitura do Diretório. Na nova versão, o provedor de recursos AKS gerencia os aplicativos de cliente e servidor para você.

## <a name="limitations"></a>Limitações

* No momento, não é possível atualizar um azure AD habilitado para a aks v1 para a experiência v2.

> [!IMPORTANT]
> Os recursos de visualização do AKS estão disponíveis em uma base de autoatendimento, opt-in. As visualizações são fornecidas "como está" e "conforme disponível", e são excluídas dos Contratos de Nível de Serviço e garantia limitada. As visualizações aks são parcialmente cobertas pelo suporte ao cliente com o melhor esforço. Como tal, esses recursos não são destinados ao uso da produção. Para obter mais informações, consulte os seguintes artigos de suporte:
>
> - [Políticas de suporte da AKS](support-policies.md)
> - [Perguntas frequentes sobre o suporte do Azure.](faq.md)

## <a name="before-you-begin"></a>Antes de começar

Você deve ter os seguintes recursos instalados:

- O Azure CLI, versão 2.2.0 ou posterior
- A extensão aks-preview 0.4.38
- Kubectl com uma versão mínima de [1.18 beta](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#client-binaries)

Para instalar/atualizar a extensão aks-preview ou posterior, use os seguintes comandos Azure CLI:

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

Para instalar kubectl, use o seguinte
```azurecli
curl -LO "https://storage.googleapis.com/kubernetes-release/release/v1.18.0-beta.2/bin/linux/amd64/kubectl"
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
kubectl version --client
```

Use [estas instruções](https://kubernetes.io/docs/tasks/tools/install-kubectl/) para outros sistemas operacionais.

> [!CAUTION]
> Depois de registrar um recurso em uma assinatura, você não pode atualmente cancelar o registro desse recurso. Quando você habilita alguns recursos de visualização, os padrões podem ser usados para todos os clusters AKS criados posteriormente na assinatura. Não habilite recursos de visualização em assinaturas de produção. Em vez disso, use uma assinatura separada para testar recursos de visualização e coletar feedback.

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

Pode levar vários minutos para o status aparecer como **Registrado**. Você pode verificar o status do registro usando o comando [az feature list:](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

Quando o status aparecer como registrado, `Microsoft.ContainerService` atualize o registro do provedor de recursos usando o comando [de registro do provedor az:](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Crie um cluster AKS com o Azure AD ativado

Agora você pode criar um cluster AKS usando os seguintes comandos CLI.

Primeiro, crie um grupo de recursos do Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Em seguida, crie um cluster AKS:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad
```
O comando acima cria um cluster AKS de três nós, mas o usuário, que criou o cluster, por padrão, não é membro de um grupo que tem acesso a esse cluster. Esse usuário precisa criar um grupo Ad do Azure, adicionar-se como membro do grupo e, em seguida, atualizar o cluster como mostrado abaixo. Siga as instruções [aqui](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)

Depois de criar um grupo e adicionar a si mesmo (e outros) como membro, você pode atualizar o cluster com o grupo Azure AD usando o seguinte comando

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id1,id2>] [--aad-tenant-id <id>]
```
Alternativamente, se você primeiro criar um grupo e adicionar membros, você poderá habilitar o grupo Azure AD no momento de criar usando o seguinte comando,

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id1,id2>] [--aad-tenant-id <id>]
```

Uma criação bem-sucedida de um cluster Azure AD v2 tem a seguinte seção no corpo de resposta
```
"Azure ADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

O cluster é criado em poucos minutos.

## <a name="access-an-azure-ad-enabled-cluster"></a>Acesse um cluster habilitado para Azure AD
Para obter as credenciais de administrador para acessar o cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```
Agora use o comando kubectl get nodes para exibir nós no cluster:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```

Para obter as credenciais do usuário para acessar o cluster:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
Siga as instruções para fazer login.

Você recebe: **Você deve estar logado no servidor (Não Autorizado)**

O usuário acima recebe um erro porque o usuário não faz parte de um grupo que tem acesso ao cluster.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [o controle de acesso baseado em função ad do Azure AD][azure-ad-rbac].

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
