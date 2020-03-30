---
title: Integrar o Azure Active Directory ao Serviço de Kubernetes do Azure
description: Saiba como usar o azure CLI para criar e o cluster Azure Active Directory (AKS Service, serviço Azure Kubernetes Service) habilitado para Azure (AKS)
services: container-service
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: d17ae12beecf9d83ef6d688af799787c5ccf322b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253046"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>Integre o Azure Active Directory com o Azure Kubernetes Service usando o Azure CLI

O AKS (Serviço de Kubernetes do Azure) pode ser configurado para usar o Azure Active Directory (AD) para autenticação do usuário. Nesta configuração, você pode fazer login em um cluster AKS usando um token de autenticação Azure AD. Os operadores de cluster também podem configurar o RBAC (Role-Based Role-Based Access Control, controle de acesso baseado em função do Kubernetes) com base na identidade do usuário ou na associação de um grupo de diretórios.

Este artigo mostra como criar os componentes Ad do Azure necessários, em seguida, implantar um cluster habilitado para Azure AD e criar uma função RBAC básica no cluster AKS. Você também pode [completar essas etapas usando o portal Azure][azure-ad-portal].

Para obter o script completo de amostra usado neste artigo, consulte [amostras azure CLI - integração AKS com Azure AD][complete-script].

As seguintes limitações se aplicam:

- O Azure AD só pode ser habilitado quando você cria um cluster novo habilitado para RBAC. Não é possível habilitar o Azure AD em um cluster existente do AKS.

## <a name="before-you-begin"></a>Antes de começar

Você precisa da versão 2.0.61 do Azure CLI ou posteriormente instalada e configurada. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, consulte [Install Azure CLI][install-azure-cli].

Vá [https://shell.azure.com](https://shell.azure.com) para abrir o Cloud Shell em seu navegador.

Para obter consistência e ajudar a executar os comandos neste artigo, crie uma variável para o nome de cluster AKS desejado. O exemplo a seguir usa o nome *myakscluster*:

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Visão geral da autenticação do Azure AD

A autenticação do Azure AD é fornecida aos clusters do AKS com OpenID Connect. O OpenID Connect é uma camada de identidade compilada sobre o protocolo OAuth 2.0. Para obter mais informações sobre o OpenID Connect, consulte a [documentação de conexão open ID][open-id-connect].

No cluster do Kubernetes, a autenticação de token do Webhook é usada para verificar os tokens de autenticação. A autenticação de token do Webhook é configurada e gerenciada como parte do cluster AKS. Mais informações sobre autenticação de token do Webhook podem ser encontradas na [documentação de autenticação do webhook][kubernetes-webhook].

> [!NOTE]
> Ao configurar o Azure AD para autenticação do AKS, dois aplicativos do Azure AD são configurados. Essa operação deve ser concluída por um administrador de locatário do Azure.

## <a name="create-azure-ad-server-component"></a>Criar o componente do servidor Azure AD

Para se integrar ao AKS, você cria e usa um aplicativo Azure AD que funciona como um ponto final para as solicitações de identidade. O primeiro aplicativo Azure AD que você precisa recebe a adesão do grupo Azure AD para um usuário.

Crie o componente do aplicativo do servidor usando o comando [az ad app create][az-ad-app-create] e atualize as reivindicações de membros do grupo usando o comando [az ad app update.][az-ad-app-update] O exemplo a seguir usa a variável *aksname* definida na seção [Antes de iniciar](#before-you-begin) e cria uma variável

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Agora crie um principal de serviço para o aplicativo do servidor usando o comando [az ad sp create.][az-ad-sp-create] Este principal de serviço é usado para autenticar-se dentro da plataforma Azure. Em seguida, obtenha o principal segredo do serviço usando o comando [de reset credencial az ad sp][az-ad-sp-credential-reset] e atribua à variável chamada *serverApplicationSecret* para uso em uma das seguintes etapas:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

O Azure AD precisa de permissões para executar as seguintes ações:

* Ler dados do diretório
* Entrar e ler o perfil do usuário

Atribuir essas permissões usando o [comando az ad app permission add:][az-ad-app-permission-add]

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Finalmente, conceda as permissões atribuídas na etapa anterior para o aplicativo do servidor usando o comando [de concessão de permissão do aplicativo az ad.][az-ad-app-permission-grant] Esta etapa falha se a conta corrente não for um administração de inquilino. Você também precisa adicionar permissões para o aplicativo Azure AD para solicitar informações que possam exigir consentimento administrativo usando o consentimento de [permissão do aplicativo az ad:][az-ad-app-permission-admin-consent]

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Criar o componente cliente Azure AD

O segundo aplicativo Azure AD é usado quando um usuário faz logon no`kubectl`cluster AKS com o Kubernetes CLI ( ). Este aplicativo cliente recebe a solicitação de autenticação do usuário e verifica suas credenciais e permissões. Crie o aplicativo Azure AD para o componente cliente usando o comando [az ad app create:][az-ad-app-create]

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Criar um princípio de serviço para o aplicativo cliente usando o comando [az ad sp create:][az-ad-sp-create]

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Obtenha o ID oAuth2 para o aplicativo do servidor para permitir o fluxo de autenticação entre os dois componentes do aplicativo usando o comando [az ad app show.][az-ad-app-show] Este iD oAuth2 é usado na próxima etapa.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Adicione as permissões para que os componentes do aplicativo cliente e do aplicativo do servidor usem o fluxo de comunicação oAuth2 usando o comando [az ad app permission add.][az-ad-app-permission-add] Em seguida, conceda permissões para que o aplicativo cliente se commente com o aplicativo do servidor usando o comando [de concessão de permissão de permissão do aplicativo az:][az-ad-app-permission-grant]

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Implantar o cluster

Com os dois aplicativos Azure AD criados, agora crie o próprio cluster AKS. Primeiro, crie um grupo de recursos usando o comando [az group create.][az-group-create] O exemplo a seguir cria o grupo de recursos na região do *EastUS:*

Criar um grupo de recursos para o cluster:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Obtenha o ID de inquilino da sua assinatura do Azure usando o comando [az account show.][az-account-show] Em seguida, crie o cluster AKS usando o comando [az aks create.][az-aks-create] O comando para criar o cluster AKS fornece os IDs de aplicativo de servidor e cliente, o principal segredo do serviço de serviço de servidor e seu ID de inquilino:

```azurecli-interactive
tenantId=$(az account show --query tenantId -o tsv)

az aks create \
    --resource-group myResourceGroup \
    --name $aksname \
    --node-count 1 \
    --generate-ssh-keys \
    --aad-server-app-id $serverApplicationId \
    --aad-server-app-secret $serverApplicationSecret \
    --aad-client-app-id $clientApplicationId \
    --aad-tenant-id $tenantId
```

Finalmente, obtenha as credenciais de administrador de cluster usando o comando [az aks get-credentials.][az-aks-get-credentials] Em uma das etapas seguintes, você obtenha as credenciais regulares do *cluster do usuário* para ver o fluxo de autenticação Do Azure AD em ação.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>Criar associação do RBAC

Antes que uma conta do Azure Active Directory possa ser usada com o cluster do AKS, uma associação de função ou uma associação de função de cluster precisa ser criada. *Funções* definem as permissões a serem concedidas e *ligações* as aplicam aos usuários desejados. Essas atribuições podem ser aplicadas a um determinado namespace ou em todo o cluster. Para obter mais informações, consulte [Usando a autorização do RBAC][rbac-authorization].

Obtenha o nome principal do usuário (UPN) para o usuário atualmente conectado usando o comando [ad ad signed-in-user show.][az-ad-signed-in-user-show] Esta conta de usuário está habilitada para a integração do Azure AD na próxima etapa.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Se o usuário para o que você concede a vinculação RBAC estiver no mesmo inquilino Azure AD, atribua permissões com base no *userPrincipalName*. Se o usuário estiver em um inquilino Azure AD diferente, consulte e use a propriedade *objectId* em vez disso.

Crie um manifesto YAML nomeado `basic-azure-ad-binding.yaml` e cole o seguinte conteúdo. Na última linha, substitua *userPrincipalName_or_objectId* pela saída UPN ou ID de objeto do comando anterior:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

Crie o ClusterRoleBinding usando o comando [kubectl apply][kubectl-apply] e especifique o nome de arquivo do seu manifesto YAML:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Acessar cluster com Azure AD

Agora vamos testar a integração da autenticação Azure AD para o cluster AKS. Defina `kubectl` o contexto de configuração para usar credenciais regulares do usuário. Este contexto repassa todas as solicitações de autenticação através do Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Agora use o comando [kubectl get pods][kubectl-get] para exibir pods em todos os namespaces:

```console
kubectl get pods --all-namespaces
```

Você recebe um aviso de login para autenticar usando credenciais Azure AD usando um navegador da Web. Depois de autenticado com sucesso, o `kubectl` comando exibe os pods no cluster AKS, conforme mostrado na saída de exemplo a seguir:

```console
kubectl get pods --all-namespaces
```

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BYMK7UXVD to authenticate.

NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE
kube-system   coredns-754f947b4-2v75r                 1/1     Running   0          23h
kube-system   coredns-754f947b4-tghwh                 1/1     Running   0          23h
kube-system   coredns-autoscaler-6fcdb7d64-4wkvp      1/1     Running   0          23h
kube-system   heapster-5fb7488d97-t5wzk               2/2     Running   0          23h
kube-system   kube-proxy-2nd5m                        1/1     Running   0          23h
kube-system   kube-svc-redirect-swp9r                 2/2     Running   0          23h
kube-system   kubernetes-dashboard-847bb4ddc6-trt7m   1/1     Running   0          23h
kube-system   metrics-server-7b97f9cd9-btxzz          1/1     Running   0          23h
kube-system   tunnelfront-6ff887cffb-xkfmq            1/1     Running   0          23h
```

O token de `kubectl` autenticação recebido para é armazenado em cache. Você só é resolicitado a entrar quando o token expirou ou o arquivo de configuração Kubernetes for recriado.

Se você vir uma mensagem de erro de autorização depois de ter feito um check-in com sucesso usando um navegador da Web como na saída de exemplo a seguir, verifique os seguintes problemas possíveis:

```output
error: You must be logged in to the server (Unauthorized)
```

* Você definiu o ID de objeto apropriado ou UPN, dependendo se a conta de usuário está no mesmo inquilino Azure AD ou não.
* O usuário não é um membro de mais de 200 grupos.
* Segredo definido no registro de aplicativo para servidor corresponde ao valor configurado usando`--aad-server-app-secret`

## <a name="next-steps"></a>Próximas etapas

Para obter o script completo que contém os comandos mostrados neste artigo, consulte o [script de integração Azure AD no repo de amostras AKS][complete-script].

Para usar usuários e grupos do Azure AD para controlar o acesso aos recursos de cluster, consulte [Controle de acesso a recursos de cluster usando controle de acesso baseado em função e identidades AD do Azure no AKS][azure-ad-rbac].

Para obter mais informações sobre como proteger os clusters Kubernetes, consulte [opções de acesso e identidade para AKS)][rbac-authorization].

Para obter as melhores práticas sobre controle de identidade e recursos, consulte [As melhores práticas para autenticação e autorização em AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-ad-app-create]: /cli/azure/ad/app#az-ad-app-create
[az-ad-app-update]: /cli/azure/ad/app#az-ad-app-update
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az-ad-app-permission-add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az-ad-app-permission-grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az-ad-app-permission-admin-consent
[az-ad-app-show]: /cli/azure/ad/app#az-ad-app-show
[az-group-create]: /cli/azure/group#az-group-create
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az-ad-signed-in-user-show
[azure-ad-portal]: azure-ad-integration.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
