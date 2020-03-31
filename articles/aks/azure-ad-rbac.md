---
title: Controle os recursos do cluster com RBAC e Azure AD no Azure Kubernetes Service
description: Saiba como usar a adesão ao grupo Azure Active Directory para restringir o acesso a recursos de cluster usando o RBAC (Role-Based Access Control, controle de acesso baseado em função) no Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: 456b6dcdd590b48e06c830db85b726d4bebb69e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596514"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Controle o acesso aos recursos de cluster usando o controle de acesso baseado em função e as identidades do Azure Active Directory no Azure Kubernetes Service

O AKS (Serviço de Kubernetes do Azure) pode ser configurado para usar o Azure Active Directory (AD) para autenticação do usuário. Nesta configuração, você faz login em um cluster AKS usando um token de autenticação Azure AD. Você também pode configurar o RBAC (Role-Based Access Control, controle de acesso baseado em função kubernetes) para limitar o acesso a recursos de cluster com base na identidade ou membro de grupo de um usuário.

Este artigo mostra como usar a adesão ao grupo Azure AD para controlar o acesso a namespaces e recursos de cluster usando o Kubernetes RBAC em um cluster AKS. Grupos de exemplos e usuários são criados no Azure AD, em seguida, Funções e Funções vinculações são criadas no cluster AKS para conceder as permissões apropriadas para criar e exibir recursos.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha um cluster AKS existente habilitado com a integração Azure AD. Se você precisar de um cluster AKS, consulte [Integrar o Diretório Ativo do Azure com AKS][azure-ad-aks-cli].

Você precisa da versão 2.0.61 do Azure CLI ou posteriormente instalada e configurada. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, consulte [Install Azure CLI][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Criar grupos de demonstração no Azure AD

Neste artigo, vamos criar duas funções de usuário que podem ser usadas para mostrar como o Kubernetes RBAC e o Azure AD controlam o acesso aos recursos de cluster. As duas funções de exemplo a seguir são usadas:

* **Desenvolvedor de aplicativos**
    * Um usuário chamado *aksdev* que faz parte do grupo *appdev.*
* **Engenheiro de confiabilidade do local**
    * Um usuário chamado *akssre* que faz parte do *grupo opssre.*

Em ambientes de produção, você pode usar usuários e grupos existentes dentro de um inquilino Azure AD.

Primeiro, obtenha o ID de recurso do seu cluster AKS usando o comando [az aks show.][az-aks-show] Atribuir o ID de recurso a uma variável chamada *AKS_ID* para que ele possa ser referenciado em comandos adicionais.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Crie o primeiro grupo de exemplo no Azure AD para os desenvolvedores de aplicativos usando o comando [az ad group create.][az-ad-group-create] O exemplo a seguir cria um grupo chamado *appdev*:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Agora, crie uma atribuição de função Azure para o grupo *appdev* usando o comando [az role assignment create.][az-role-assignment-create] Esta atribuição permite que qualquer `kubectl` membro do grupo use para interagir com um cluster AKS, concedendo-lhes a *Função de Usuário do Cluster de Serviço do Azure Kubernetes*.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Se você receber um `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.`erro como , espere alguns segundos para que o ID do objeto `az role assignment create` do grupo Azure AD se propague através do diretório, então tente o comando novamente.

Crie um segundo grupo de exemplo, este para SREs chamado *opssre*:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Novamente, crie uma atribuição de função Azure para conceder aos membros do grupo a função de usuário do cluster de *serviço saqueado do Azure Kubernetes*:

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Criar usuários de demonstração no Azure AD

Com dois grupos de exemplo criados no Azure AD para nossos desenvolvedores de aplicativos e SREs, agora vamos criar dois usuários de exemplo. Para testar a integração rbac no final do artigo, você faz login no cluster AKS com essas contas.

Crie a primeira conta de usuário no Azure AD usando o comando [az ad user create.][az-ad-user-create]

O exemplo a seguir cria um usuário com o nome de exibição `aksdev@contoso.com` *AKS Dev* e o nome principal do usuário (UPN) de . Atualize o UPN para incluir um domínio verificado para o seu inquilino Azure AD (substitua *contoso.com* por seu próprio domínio) e forneça sua própria credencial segura: `--password`

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

Agora adicione o usuário ao grupo *appdev* criado na seção anterior usando o comando add add( membro do [grupo az ad:][az-ad-group-member-add]

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Crie uma segunda conta de usuário. O exemplo a seguir cria um usuário com o nome de exibição `akssre@contoso.com` *AKS SRE* e o nome principal do usuário (UPN) de . Novamente, atualize a UPN para incluir um domínio verificado para *contoso.com* o seu inquilino Azure `--password` AD (substitua contoso.com por seu próprio domínio) e forneça sua própria credencial segura:

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name akssre@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Crie os recursos de cluster AKS para devs de aplicativos

Os grupos Azure AD e os usuários agora são criados. As atribuições de função do Azure foram criadas para que os membros do grupo se conectem a um cluster AKS como um usuário regular. Agora, vamos configurar o cluster AKS para permitir que esses diferentes grupos acessem recursos específicos.

Primeiro, obtenha as credenciais de administrador de cluster usando o comando [az aks get-credentials.][az-aks-get-credentials] Em uma das seções a seguir, você obtenha as credenciais regulares do *cluster do usuário* para ver o fluxo de autenticação Azure AD em ação.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Crie um namespace no cluster AKS usando o [comando kubectl create namespace.][kubectl-create] O exemplo a seguir cria um dev nome *namespace*:

```console
kubectl create namespace dev
```

Nos Kubernetes, *as Funções* definem as permissões para conceder e *as RoleBindings* as aplicam aos usuários ou grupos desejados. Essas atribuições podem ser aplicadas a um determinado namespace ou em todo o cluster. Para obter mais informações, consulte [Usando a autorização do RBAC][rbac-authorization].

Primeiro, crie um Role para o espaço de nome *de v.* Esta função concede permissões completas ao namespace. Em ambientes de produção, você pode especificar mais permissões granulares para diferentes usuários ou grupos.

Crie um `role-dev-namespace.yaml` arquivo nomeado e cole o seguinte manifesto YAML:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-full-access
  namespace: dev
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Crie a Função usando o comando [kubectl apply][kubectl-apply] e especifique o nome de arquivo do seu manifesto YAML:

```console
kubectl apply -f role-dev-namespace.yaml
```

Em seguida, obtenha o ID de recurso para o grupo *appdev* usando o comando [az ad group show.][az-ad-group-show] Este grupo é definido como o tema de um RoleBinding na próxima etapa.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Agora, crie um RoleBinding para o grupo *appdev* para usar a Função criada anteriormente para acesso ao namespace. Crie um arquivo chamado `rolebinding-dev-namespace.yaml` e cole o manifesto YAML a seguir. Na última linha, substitua o *groupObjectId* pela saída de ID do objeto de grupo do comando anterior:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-access
  namespace: dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: dev-user-full-access
subjects:
- kind: Group
  namespace: dev
  name: groupObjectId
```

Crie a RoleBinding usando o comando [kubectl apply][kubectl-apply] e especifique o nome de arquivo do seu manifesto YAML:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Crie os recursos de cluster AKS para SREs

Agora, repita as etapas anteriores para criar um namespace, Role e RoleBinding para os SREs.

Primeiro, crie um namespace para *sre* usando o [comando kubectl create namespace:][kubectl-create]

```console
kubectl create namespace sre
```

Crie um `role-sre-namespace.yaml` arquivo nomeado e cole o seguinte manifesto YAML:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-full-access
  namespace: sre
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Crie a Função usando o comando [kubectl apply][kubectl-apply] e especifique o nome de arquivo do seu manifesto YAML:

```console
kubectl apply -f role-sre-namespace.yaml
```

Obtenha o ID de recurso para o grupo *opssre* usando o comando [az ad group show:][az-ad-group-show]

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Crie um RoleBinding para o grupo *opssre* para usar a Função criada anteriormente para acesso ao namespace. Crie um arquivo chamado `rolebinding-sre-namespace.yaml` e cole o manifesto YAML a seguir. Na última linha, substitua o *groupObjectId* pela saída de ID do objeto de grupo do comando anterior:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-access
  namespace: sre
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: sre-user-full-access
subjects:
- kind: Group
  namespace: sre
  name: groupObjectId
```

Crie a RoleBinding usando o comando [kubectl apply][kubectl-apply] e especifique o nome de arquivo do seu manifesto YAML:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Interaja com recursos de cluster usando identidades AD do Azure

Agora, vamos testar as permissões esperadas quando você criar e gerenciar recursos em um cluster AKS. Nestes exemplos, você agenda e visualiza pods no namespace atribuído pelo usuário. Em seguida, você tenta agendar e visualizar pods fora do namespace atribuído.

Primeiro, redefinir o contexto *kubeconfig* usando o comando [az aks get-credentials.][az-aks-get-credentials] Em uma seção anterior, você define o contexto usando as credenciais de administrador de cluster. O usuário administrador ignora o sinal do Azure AD em solicitações. Sem `--admin` o parâmetro, é aplicado o contexto do usuário que requer que todas as solicitações sejam autenticadas usando o Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Agende um pod NGINX básico usando o comando [kubectl run][kubectl-run] no espaço de nome *de v:*

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

Conforme o login, digite as `appdev@contoso.com` credenciais para sua própria conta criadas no início do artigo. Uma vez que você esteja conectado com sucesso, `kubectl` o token da conta será armazenado em cache para comandos futuros. O NGINX é agendado com sucesso, como mostrado na seguinte saída de exemplo:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Agora use o comando [kubectl get pods][kubectl-get] para visualizar pods no espaço de nome *de v.*

```console
kubectl get pods --namespace dev
```

Como mostrado na saída de exemplo a seguir, o pod NGINX está *executando*com sucesso:

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Criar e visualizar recursos de cluster fora do namespace atribuído

Agora tente ver pods fora do espaço de nome *de v.* Use novamente o comando [kubectl get pods,][kubectl-get] desta vez para ver `--all-namespaces` o seguinte:

```console
kubectl get pods --all-namespaces
```

A associação de grupo do usuário não possui uma função Kubernetes que permita essa ação, como mostrado na seguinte saída de exemplo:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

Da mesma forma, tente agendar um pod em um namespace diferente, como o espaço de nome *sre.* A associação de grupo do usuário não está alinhada com uma Função Kubernetes e RoleBinding para conceder essas permissões, como mostrado na seguinte saída de exemplo:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Teste o acesso SRE aos recursos do cluster AKS

Para confirmar que nossa associação do grupo Azure AD e o Kubernetes RBAC funcionam corretamente entre diferentes usuários e grupos, tente os comandos anteriores quando estiver conectado como usuário *opssre.*

Redefinir o contexto *kubeconfig* usando o comando [az aks get-credentials][az-aks-get-credentials] que limpa o token de autenticação armazenado anteriormente para o usuário *aksdev:*

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Tente agendar e visualizar pods no espaço de nome *designado.* Quando solicitado, faça login `opssre@contoso.com` com suas próprias credenciais criadas no início do artigo:

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

Como mostrado na saída de exemplo a seguir, você pode criar e visualizar com sucesso os pods:

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Agora, tente visualizar ou agendar pods fora do namespace sre atribuído:

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

Esses `kubectl` comandos falham, como mostrado na saída de exemplo a seguir. A associação de grupo do usuário e as Alterações e As Vinculações de Papéis do Kubernetes não concedem permissões para criar ou gerenciado recursos em outros namespaces:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Limpar recursos

Neste artigo, você criou recursos no cluster AKS e usuários e grupos no Azure AD. Para limpar todos esses recursos, execute os seguintes comandos:

```azurecli-interactive
# Get the admin kubeconfig context to delete the necessary cluster resources
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin

# Delete the dev and sre namespaces. This also deletes the pods, Roles, and RoleBindings
kubectl delete namespace dev
kubectl delete namespace sre

# Delete the Azure AD user accounts for aksdev and akssre
az ad user delete --upn-or-object-id $AKSDEV_ID
az ad user delete --upn-or-object-id $AKSSRE_ID

# Delete the Azure AD groups for appdev and opssre. This also deletes the Azure role assignments.
az ad group delete --group appdev
az ad group delete --group opssre
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como proteger os clusters Kubernetes, consulte [opções de acesso e identidade para AKS)][rbac-authorization].

Para obter as melhores práticas sobre controle de identidade e recursos, consulte [As melhores práticas para autenticação e autorização em AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-ad-group-create]: /cli/azure/ad/group#az-ad-group-create
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-ad-user-create]: /cli/azure/ad/user#az-ad-user-create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az-ad-group-member-add
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
