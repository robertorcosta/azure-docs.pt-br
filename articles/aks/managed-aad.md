---
title: Use o Azure Active Directory no Serviço de Kubernetes do Azure
description: Saiba como usar o Azure Active Directory no Serviço de Kubernetes do Azure (AKS)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 05/11/2020
ms.openlocfilehash: 67f5f707ad2971551e3c9623dd5c07ad880afcf2
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211139"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>Integre o Azure Active Directory no Serviço de Kubernetes do Azure (versão prévia)

> [!Note]
> Os clusters AKS existentes com a integração do Azure Active Directory (AAD) não são afetados pela nova experiência do AAD gerenciada pelo AKS.

A integração do Azure AD com o AAD gerenciado pelo AKS foi projetada para simplificar a experiência de integração do Azure AD, na qual os usuários eram solicitados anteriormente a criar um aplicativo cliente, um aplicativo de servidor e exigia que o locatário do Azure AD concedesse permissões de leitura de diretório. Na nova versão, o provedor de recursos AKS gerencia os aplicativos de cliente e de servidor para você.

## <a name="limitations"></a>Limitações

* Atualmente, não é possível atualizar um cluster AKS do AAD existente para a nova experiência do AAD gerenciada pelo AKS.

> [!IMPORTANT]
> As versões prévias do recurso AKS estão disponíveis em uma base de autoatendimento e aceitação. As versões prévias são fornecidas "no estado em que se encontram" e "conforme disponíveis" e são excluídas dos contratos de nível de serviço e da garantia limitada. As versões prévias do AKS são parcialmente cobertas pelo suporte ao cliente em uma base de melhor esforço. Dessa forma, esses recursos não são destinados ao uso em produção. Para obter mais informações, consulte os seguintes artigos:
>
> - [Políticas de suporte do AKS](support-policies.md)
> - [Perguntas frequentes sobre o suporte do Azure.](faq.md)

## <a name="before-you-begin"></a>Antes de começar

> [!Important]
> Você deve usar o Kubectl com uma versão mínima de 1,18

Você deve ter os seguintes recursos instalados:

- A CLI do Azure versão 2.5.1 ou posterior
- A extensão da versão prévia do AKS 0.4.38
- Kubectl com uma versão mínima de [1.18](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1180)

Para instalar/atualizar a extensão da versão prévia do AKS ou posterior, use os seguintes comandos da CLI do Azure:

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

Para instalar o kubectl, use o seguinte:

```azurecli
sudo az aks install-cli
kubectl version --client
```

Use [essas instruções](https://kubernetes.io/docs/tasks/tools/install-kubectl/) para outros sistemas operacionais.

> [!CAUTION]
> Depois de registrar um recurso em uma assinatura, não é possível cancelar o registro desse recurso no momento. Quando você habilita algumas versões prévias do recurso, os padrões podem ser usados para todos os clusters do AKS criados posteriormente na assinatura. Não habilite as versões prévias do recurso em assinaturas de produção. Em vez disso, use uma assinatura separada para testar versões prévias do recurso e coletar comentários.

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

Pode levar vários minutos para que o status seja exibido como **Registrado**. Você pode verificar o status de registro usando o comando [az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list):

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

Quando o status aparecer como registrado, atualize o registro do provedor de recursos `Microsoft.ContainerService` usando o comando [az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register):

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Criar um cluster do AKS habilitado para o Azure AD

Agora você pode criar um cluster do AKS usando os comandos da CLI a seguir.

Primeiro, crie um grupo de recursos do Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Em seguida, crie um cluster do AKS:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad
```
O comando acima cria um cluster do AKS de três nós, mas o usuário que criou o cluster por padrão não é um membro de um grupo que tem acesso a esse cluster. Esse usuário precisa criar um grupo do Azure AD, adicioná-lo como um membro do grupo e, em seguida, atualizar o cluster conforme mostrado abaixo. Siga as instruções [aqui](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)

Depois de criar um grupo e adicioná-lo (e outros) como um membro, você pode atualizar o cluster com o grupo do Azure AD usando o comando a seguir

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```
Como alternativa, se você criar primeiro um grupo e adicionar membros, poderá habilitar o grupo do Azure AD no momento da criação usando o comando a seguir.

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

Uma criação bem-sucedida de um cluster AAD gerenciado pelo AKS tem a seção a seguir no corpo da resposta
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

O cluster é criado dentro de alguns minutos.

## <a name="access-an-azure-ad-enabled-cluster"></a>Acessar um cluster habilitado do Azure AD
Para obter as credenciais de administrador para acessar o cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```
Agora, use o comando de obtenção dos nós kubectl para exibir os nós no cluster:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```

Para obter as credenciais de usuário para acessar o cluster:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
Siga as instruções em para entrar.

Você receberá: **Você deve estar conectado ao servidor (não autorizado)**

O usuário acima obtém um erro porque o usuário não faz parte de um grupo que tem acesso ao cluster.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [Controle de acesso baseado em função do Azure AD][azure-ad-rbac].
* Use o [kubelogin](https://github.com/Azure/kubelogin) para acessar recursos da autenticação do Azure que não estão disponíveis no kubectl.

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
