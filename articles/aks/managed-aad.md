---
title: Use o Azure Active Directory no Serviço de Kubernetes do Azure
description: Saiba como usar o Azure Active Directory no Serviço de Kubernetes do Azure (AKS)
services: container-service
manager: gwallace
author: mlearned
ms.topic: article
ms.date: 06/25/2020
ms.author: mlearned
ms.openlocfilehash: bf635d37559d09e887a67be27c412bff7899127b
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86023390"
---
# <a name="integrate-aks-managed-azure-ad-preview"></a>Integrar o Azure AD gerenciado pelo AKS (versão prévia)

> [!Note]
> Os clusters existentes do AKS (serviço do kubernetes do Azure) com a integração do Azure Active Directory (Azure AD) não são afetados pela nova experiência do Azure AD gerenciada por AKS.

A integração do Azure AD com o Azure AD gerenciado pelo AKS foi projetada para simplificar a experiência de integração do Azure AD, na qual os usuários eram solicitados anteriormente a criar um aplicativo cliente, um aplicativo de servidor e exigia que o locatário do Azure AD concedesse permissões de leitura de diretório. Na nova versão, o provedor de recursos AKS gerencia os aplicativos de cliente e de servidor para você.

## <a name="limitations"></a>Limitações

* Atualmente, não é possível atualizar um cluster integrado do Azure AD AKS existente para a nova experiência do Azure AD gerenciada pelo AKS.

> [!IMPORTANT]
> As versões prévias do recurso AKS estão disponíveis em uma base de autoatendimento e aceitação. As versões prévias são fornecidas "no estado em que se encontram" e "conforme disponíveis" e são excluídas dos contratos de nível de serviço e da garantia limitada. As versões prévias do AKS são parcialmente cobertas pelo suporte ao cliente em uma base de melhor esforço. Dessa forma, esses recursos não são destinados ao uso em produção. Para obter mais informações, consulte os seguintes artigos:
>
> - [Políticas de suporte do AKS](support-policies.md)
> - [Perguntas frequentes sobre o suporte do Azure.](faq.md)

## <a name="before-you-begin"></a>Antes de começar

* Localize sua ID de locatário da conta do Azure navegando até a portal do Azure e selecione Azure Active Directory Propriedades de > > ID do diretório

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

Para instalar o kubectl, use os seguintes comandos:

```azurecli
sudo az aks install-cli
kubectl version --client
```

Use [essas instruções](https://kubernetes.io/docs/tasks/tools/install-kubectl/) para outros sistemas operacionais.

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
## <a name="azure-ad-authentication-overview"></a>Visão geral da autenticação do Azure AD

Os administradores de cluster podem configurar o RBAC (controle de acesso baseado em função) kubernetes com base na identidade de um usuário ou no grupo de diretórios. A autenticação do Azure AD é fornecida aos clusters do AKS com OpenID Connect. O OpenID Connect é uma camada de identidade compilada sobre o protocolo OAuth 2.0. Para obter mais informações sobre o OpenID Connect, consulte a [documentação do Open ID Connect][open-id-connect].

No cluster do Kubernetes, a autenticação de token do Webhook é usada para verificar os tokens de autenticação. A autenticação de token do Webhook é configurada e gerenciada como parte do cluster AKS.

## <a name="webhook-and-api-server"></a>Webhook e servidor de API

:::image type="content" source="media/aad-integration/auth-flow.png" alt-text="Fluxo de autenticação de servidor de API e webhook":::

Conforme mostrado no gráfico acima, o servidor de API chama o servidor de webhook AKS e executa as seguintes etapas:

1. O aplicativo cliente do Azure AD é usado pelo kubectl para fazer logon de usuários com o [fluxo de concessão de autorização de dispositivo OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code).
2. O Azure AD fornece um access_token, id_token e um refresh_token.
3. O usuário faz uma solicitação para kubectl com um access_token de kubeconfig.
4. Kubectl envia o access_token para APIServer.
5. O servidor de API está configurado com o servidor de webhook de autenticação para executar a validação.
6. O servidor de webhook de autenticação confirma que a assinatura do token Web JSON é válida verificando a chave de assinatura pública do Azure AD.
7. O aplicativo de servidor usa credenciais fornecidas pelo usuário para consultar associações de grupo do usuário conectado do MS API do Graph.
8. Uma resposta é enviada para o APIServer com informações de usuário, como a declaração de UPN (nome principal do usuário) do token de acesso, e a associação de grupo do usuário com base na ID de objeto.
9. A API executa uma decisão de autorização baseada na função/Rolebinding do kubernetes.
10. Depois de autorizado, o servidor de API retorna uma resposta a kubectl.
11. Kubectl fornece comentários para o usuário.


## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Criar um cluster do AKS habilitado para o Azure AD

Crie um cluster AKS usando os comandos da CLI a seguir.

Criar um grupo de recursos do Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Você pode usar um grupo existente do Azure AD ou criar um novo. Você precisa da ID de objeto para seu grupo do Azure AD.

```azurecli-interactive
# List existing groups in the directory
az ad group list
```

Para RIAR um novo grupo do Azure AD para seus administradores de cluster, use o seguinte comando:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name MyDisplay --mail-nickname MyDisplay
```

Criar um cluster AKS e habilitar o acesso de administração para seu grupo do Azure AD

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

Uma criação bem-sucedida de um cluster do Azure AD gerenciado por AKS tem a seção a seguir no corpo da resposta
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

Você precisará da função interna de [usuário do cluster do serviço kubernetes do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-cluster-user-role) para executar as etapas a seguir.

Obtenha as credenciais do usuário para acessar o cluster:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
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
Configure o [RBAC (controle de acesso baseado em função)](https://docs.microsoft.com/azure/aks/azure-ad-rbac) para configurar grupos de segurança adicionais para seus clusters.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Solucionando problemas de acesso com o Azure AD

> [!Important]
> As etapas descritas abaixo estão ignorando a autenticação de grupo do Azure AD normal. Use-os somente em uma emergência.

Se você estiver permanentemente bloqueado por não ter acesso a um grupo válido do Azure AD com acesso ao seu cluster, ainda poderá obter as credenciais de administrador para acessar o cluster diretamente.

Para executar essas etapas, você precisará ter acesso à função interna de [administrador de cluster do serviço kubernetes do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-cluster-admin-role) .

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```

## <a name="non-interactive-login-with-kubelogin"></a>Logon não interativo com kubelogin

Há alguns cenários não interativos, como pipelines de integração contínua, que atualmente não estão disponíveis com kubectl. Você pode usar o [kubelogin](https://github.com/Azure/kubelogin) para acessar o cluster com o logon da entidade de serviço não interativa.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [Controle de acesso baseado em função do Azure AD][azure-ad-rbac].
* Use o [kubelogin](https://github.com/Azure/kubelogin) para acessar recursos da autenticação do Azure que não estão disponíveis no kubectl.
* Use [modelos de Azure Resource Manager (ARM)][aks-arm-template] para criar clusters habilitados do Azure ad gerenciados por AKs.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: https://docs.microsoft.com/azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - Internal -->
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

