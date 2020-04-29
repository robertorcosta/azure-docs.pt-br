---
title: Usar o Azure AD no serviço kubernetes do Azure
description: Saiba como usar o Azure AD no AKS (serviço kubernetes do Azure)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 03/24/2020
ms.openlocfilehash: b121830192a2b88185bbbbc9a92934e51b32a61c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81114655"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>Integrar o Azure AD no serviço kubernetes do Azure (versão prévia)

> [!Note]
> Os clusters AKS v1 existentes com integração com o AD não são afetados pela nova experiência do AKS v2.

A integração do Azure AD com o AKS v2 foi projetada para simplificar a integração do Azure AD com a experiência do AKS v1, em que os usuários eram solicitados a criar um aplicativo cliente, um aplicativo de servidor e exigiram que o locatário do Azure AD concedesse permissões de leitura de diretório. Na nova versão, o provedor de recursos AKS gerencia os aplicativos de cliente e servidor para você.

## <a name="limitations"></a>Limitações

* Atualmente, não é possível atualizar um cluster do AKS v1 habilitado para o Azure AD para a experiência v2.

> [!IMPORTANT]
> Os recursos de visualização do AKS estão disponíveis em uma base de autoatendimento e aceitação. As visualizações são fornecidas "no estado em que se encontram" e "como disponíveis" e são excluídas dos contratos de nível de serviço e da garantia limitada. As visualizações do AKS são parcialmente cobertas pelo suporte ao cliente em uma base de melhor esforço. Dessa forma, esses recursos não são destinados ao uso em produção. Para obter mais informações, consulte os seguintes artigos de suporte:
>
> - [Políticas de suporte do AKS](support-policies.md)
> - [Perguntas frequentes sobre o suporte do Azure.](faq.md)

## <a name="before-you-begin"></a>Antes de começar

Você deve ter os seguintes recursos instalados:

- O CLI do Azure, versão 2.2.0 ou posterior
- A extensão 0.4.38 AKs-Preview
- Kubectl com uma versão mínima de [1,18 beta](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#client-binaries)

Para instalar/atualizar a extensão AKs-Preview ou posterior, use os seguintes comandos de CLI do Azure:

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

Use [estas instruções](https://kubernetes.io/docs/tasks/tools/install-kubectl/) para outros sistemas operacionais.

> [!CAUTION]
> Depois de registrar um recurso em uma assinatura, não é possível cancelar o registro desse recurso no momento. Quando você habilita alguns recursos de visualização, os padrões podem ser usados para todos os clusters AKS criados posteriormente na assinatura. Não habilite os recursos de visualização em assinaturas de produção. Em vez disso, use uma assinatura separada para testar recursos de visualização e coletar comentários.

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

Pode levar vários minutos para que o status seja exibido como **registrado**. Você pode verificar o status do registro usando o comando [AZ Feature List](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

Quando o status for exibido como registrado, atualize o registro do `Microsoft.ContainerService` provedor de recursos usando o comando [AZ Provider Register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Criar um cluster AKS com o Azure AD habilitado

Agora você pode criar um cluster AKS usando os comandos da CLI a seguir.

Primeiro, crie um grupo de recursos do Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Em seguida, crie um cluster AKS:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad
```
O comando acima cria um cluster AKS de três nós, mas o usuário, que criou o cluster, por padrão, não é um membro de um grupo que tem acesso a esse cluster. Esse usuário precisa criar um grupo do Azure AD, adicioná-lo como um membro do grupo e, em seguida, atualizar o cluster, conforme mostrado abaixo. Siga as instruções [aqui](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)

Depois de criar um grupo e adicioná-lo (e outros) como um membro, você pode atualizar o cluster com o grupo do Azure AD usando o comando a seguir

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```
Como alternativa, se você criar primeiro um grupo e adicionar membros, poderá habilitar o grupo do Azure AD no momento da criação usando o comando a seguir,

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

Uma criação bem-sucedida de um cluster do Azure AD V2 tem a seguinte seção no corpo da resposta
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
Agora, use o comando kubectl Get Nodes para exibir os nós no cluster:

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
Siga as instruções para entrar.

Você recebe: **você deve estar conectado ao servidor (não autorizado)**

O usuário acima Obtém um erro porque o usuário não faz parte de um grupo que tem acesso ao cluster.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [controle de acesso baseado em função do Azure ad][azure-ad-rbac].

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
