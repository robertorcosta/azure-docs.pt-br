---
title: Use o Azure Active Directory no Serviço de Kubernetes do Azure
description: Saiba como usar o Azure Active Directory no Serviço de Kubernetes do Azure (AKS)
services: container-service
ms.topic: article
ms.date: 08/26/2020
ms.author: thomasge
ms.openlocfilehash: fdbef15bb7831fedd7c375d565e0cde10f9b9a9e
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380425"
---
# <a name="aks-managed-azure-active-directory-integration"></a>Integração de Azure Active Directory gerenciados pelo AKS

A integração do Azure AD gerenciada pelo AKS foi projetada para simplificar a experiência de integração do Azure AD, em que os usuários eram solicitados anteriormente a criar um aplicativo cliente, um aplicativo de servidor e exigiram que o locatário do Azure AD concedesse permissões de leitura de diretório. Na nova versão, o provedor de recursos AKS gerencia os aplicativos de cliente e de servidor para você.

## <a name="azure-ad-authentication-overview"></a>Visão geral da autenticação do Azure AD

Os administradores de cluster podem configurar o RBAC (controle de acesso baseado em função) kubernetes com base na identidade de um usuário ou no grupo de diretórios. A autenticação do Azure AD é fornecida aos clusters do AKS com OpenID Connect. O OpenID Connect é uma camada de identidade compilada sobre o protocolo OAuth 2.0. Para obter mais informações sobre o OpenID Connect, consulte a [documentação do Open ID Connect][open-id-connect].

Saiba mais sobre o fluxo de integração do Azure AD na [documentação de conceitos de integração do Azure Active Directory](concepts-identity.md#azure-active-directory-integration).

## <a name="limitations"></a>Limitações 

* A integração do Azure AD gerenciada pelo AKS não pode ser desabilitada
* Não há suporte para clusters habilitados para RBAC para integração do Azure AD gerenciada por AKS
* Não há suporte para a alteração do locatário do Azure AD associado à integração do Azure AD AKS

## <a name="prerequisites"></a>Pré-requisitos

* O CLI do Azure versão 2.11.0 ou posterior
* Kubectl com uma versão mínima de [1.18.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) ou [kubelogin](https://github.com/Azure/kubelogin)
* Se você estiver usando o [Helm](https://github.com/helm/helm), a versão mínima do Helm 3,3.

> [!Important]
> Você deve usar Kubectl com uma versão mínima de 1.18.1 ou kubelogin. Se você não usar a versão correta, observará problemas de autenticação.

Para instalar o kubectl e o kubelogin, use os seguintes comandos:

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
kubelogin --version
```

Use [essas instruções](https://kubernetes.io/docs/tasks/tools/install-kubectl/) para outros sistemas operacionais.


## <a name="before-you-begin"></a>Antes de começar

Para o cluster, você precisa de um grupo do Azure AD. Esse grupo é necessário como grupo de administradores para que o cluster conceda permissões de administrador de cluster. Você pode usar um grupo existente do Azure AD ou criar um novo. Registre a ID de objeto do seu grupo do Azure AD.

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

Para criar um novo grupo do Azure AD para seus administradores de cluster, use o seguinte comando:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Criar um cluster do AKS habilitado para o Azure AD

Crie um cluster AKS usando os comandos da CLI a seguir.

Criar um grupo de recursos do Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Criar um cluster AKS e habilitar o acesso de administração para seu grupo do Azure AD

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Uma criação bem-sucedida de um cluster do Azure AD gerenciado por AKS tem a seção a seguir no corpo da resposta
```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Depois que o cluster for criado, você poderá começar a acessá-lo.

## <a name="access-an-azure-ad-enabled-cluster"></a>Acessar um cluster habilitado do Azure AD

Você precisará da função interna de [usuário do cluster do serviço kubernetes do Azure](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) para executar as etapas a seguir.

Obtenha as credenciais do usuário para acessar o cluster:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
Siga as instruções em para entrar.

Use o comando kubectl Get Nodes para exibir os nós no cluster:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
Configure o [controle de acesso baseado em função do Azure (RBAC do Azure)](./azure-ad-rbac.md) para configurar grupos de segurança adicionais para seus clusters.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Solucionando problemas de acesso com o Azure AD

> [!Important]
> As etapas descritas abaixo estão ignorando a autenticação de grupo do Azure AD normal. Use-os somente em uma emergência.

Se você estiver permanentemente bloqueado por não ter acesso a um grupo válido do Azure AD com acesso ao seu cluster, ainda poderá obter as credenciais de administrador para acessar o cluster diretamente.

Para executar essas etapas, você precisará ter acesso à função interna de [administrador de cluster do serviço kubernetes do Azure](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) .

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="enable-aks-managed-azure-ad-integration-on-your-existing-cluster"></a>Habilitar a integração do Azure AD gerenciada pelo AKS em seu cluster existente

Você pode habilitar a integração do Azure AD gerenciada pelo AKS em seu cluster existente do RBAC habilitado. Certifique-se de definir seu grupo de administradores para manter o acesso ao cluster.

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad --aad-admin-group-object-ids <id-1> [--aad-tenant-id <id>]
```

Uma ativação bem-sucedida de um cluster do Azure AD gerenciado por AKS tem a seção a seguir no corpo da resposta

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Baixe as credenciais do usuário novamente para acessar o cluster seguindo as etapas [aqui][access-cluster].

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>Atualizando para a integração do Azure AD gerenciada pelo AKS

Se o cluster usar a integração herdada do Azure AD, você poderá atualizar para a integração do Azure AD gerenciada pelo AKS.

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Uma migração bem-sucedida de um cluster do Azure AD gerenciado por AKS tem a seção a seguir no corpo da resposta

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Se você quiser acessar o cluster, siga as etapas [aqui][access-cluster].

## <a name="non-interactive-sign-in-with-kubelogin"></a>Entrada não interativa com kubelogin

Há alguns cenários não interativos, como pipelines de integração contínua, que atualmente não estão disponíveis com kubectl. Você pode usar [`kubelogin`](https://github.com/Azure/kubelogin) o para acessar o cluster com a entrada de entidade de serviço não interativa.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [a integração do RBAC do Azure para autorização de kubernetes][azure-rbac-integration]
* Saiba mais sobre [a integração do Azure AD com o KUBERNETES RBAC][azure-ad-rbac].
* Use o [kubelogin](https://github.com/Azure/kubelogin) para acessar recursos para a autenticação do Azure que não estão disponíveis no kubectl.
* Saiba mais sobre os [conceitos de identidade AKs e kubernetes][aks-concepts-identity].
* Use [modelos de Azure Resource Manager (ARM) ][aks-arm-template] para criar clusters habilitados do Azure ad gerenciados por AKs.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - Internal -->
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
