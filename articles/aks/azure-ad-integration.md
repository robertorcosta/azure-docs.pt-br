---
title: Integrar o Azure Active Directory ao Serviço de Kubernetes do Azure
description: Como criar clusters Azure Active Directory (AKS Service, serviço Azure Active Directory) habilitados para Azure
services: container-service
ms.topic: article
ms.date: 02/02/2019
ms.openlocfilehash: de57a46f92fab2486aa7722daf8745a01be1f4f6
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617592"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrar o Azure Active Directory ao Serviço de Kubernetes do Azure

O Azure Kubernetes Service (AKS) pode ser configurado para usar o Azure Active Directory (Azure AD) para autenticação do usuário. Nesta configuração, você pode fazer login em um cluster AKS usando o token de autenticação Azure AD.

Os administradores de cluster podem configurar o RBAC (Role-Based Role-Based Access Control, controle de acesso baseado em função do Kubernetes) com base na identidade ou no grupo de diretórios de um usuário.

Este artigo explica como:

- Implante os pré-requisitos para AKS e Azure AD.
- Implante um cluster habilitado para Azure AD.
- Crie uma função RBAC básica no cluster AKS usando o portal Azure.

Você também pode completar essas etapas usando o [Azure CLI][azure-ad-cli].

> [!NOTE]
> O Azure AD só pode ser habilitado quando você cria um novo cluster habilitado para RBAC. Não é possível habilitar o Azure AD em um cluster existente do AKS.

## <a name="authentication-details"></a>Detalhes de autenticação

A autenticação Azure AD é fornecida para clusters AKS que possuem OpenID Connect. O OpenID Connect é uma camada de identidade compilada sobre o protocolo OAuth 2.0.

Para obter mais informações sobre o OpenID Connect, consulte [Autorizar o acesso a aplicativos web usando o OpenID Connect e o Azure AD][open-id-connect].

Dentro de um cluster Kubernetes, a autenticação do token webhook é usada para autenticar tokens. A autenticação de token do Webhook é configurada e gerenciada como parte do cluster AKS.

Para obter mais informações sobre a autenticação do token webhook, consulte a seção [Autenticação de Token do Webhook][kubernetes-webhook] na Documentação Kubernetes.

Para fornecer autenticação Azure AD para um cluster AKS, dois aplicativos Azure AD são criados. O primeiro aplicativo é um componente de servidor que fornece autenticação do usuário. O segundo aplicativo é um componente cliente que é usado quando você é solicitado pela CLI para autenticação. Este aplicativo cliente usa o aplicativo do servidor para a autenticação real das credenciais fornecidas pelo cliente.

> [!NOTE]
> Quando você configura o Azure AD para autenticação AKS, dois aplicativos Azure AD são configurados. As etapas para delegar permissões para cada aplicativo devem ser completadas por um administrador inquilino do Azure.

## <a name="create-the-server-application"></a>Criar o aplicativo do servidor

O primeiro aplicativo Azure AD é aplicado para obter a adesão do grupo Azure AD do usuário. Para criar este aplicativo no portal Azure:

1. Selecione**registros** >  **do aplicativo azure active directory** > **Novo registro**.

    a. Dê um nome ao aplicativo, como *AKSAzureADServer*.

    b. Para **tipos de conta suportados,** selecione Contas somente neste diretório **organizacional**.
    
    c. Escolha **a Web** para o tipo Uri redirecionar e, em *https://aksazureadserver*seguida, digite qualquer valor formatado por URI, como .

    d. Selecione **Registrar** quando terminar.

2. Selecione **Manifest**e edite o **grupoMembershipClaims:** value as **All**. Quando terminar as atualizações, selecione **Salvar**.

    ![Atualizar a associação de grupo para todos](media/aad-integration/edit-manifest.png)

3. No painel esquerdo do aplicativo Azure AD, selecione **Certificados & segredos**.

    a. Selecione **+ Novo cliente secreto**.

    b. Adicione uma descrição chave, como *o servidor AKS Azure AD*. Escolha um tempo de expiração e, em seguida, **selecione Adicionar**.

    c. Observe o valor-chave, que é exibido apenas neste momento. Quando você implanta um cluster AKS habilitado para Azure AD, esse valor é chamado de segredo do aplicativo do servidor.

4. No painel esquerdo do aplicativo Azure AD, selecione **permissões de API**e selecione **+ Adicione uma permissão**.

    a. Em **APIs da Microsoft,** selecione **Microsoft Graph**.

    b. Selecione **permissões delegadas**e selecione a caixa de seleção ao lado **do diretório > Diretório.Read.All (Leia dados do diretório)**.

    c. Se não existir uma permissão delegada padrão para **usuário > usuário.Ler (Entrar e ler o perfil do usuário)** não existir, selecione a caixa de seleção ao lado.

    d. Selecione **as permissões do aplicativo**e selecione a caixa de seleção ao lado do diretório > **diretório.Read.All (Leia dados do diretório)**.

    ![Defina permissões de gráficos](media/aad-integration/graph-permissions.png)

    e. Selecione **Adicionar permissões** para salvar as atualizações.

    f. Sob **o consentimento de Grant,** **selecione o consentimento do grant.** Este botão não estará disponível a conta corrente que está sendo usada não está listada como um administração de inquilinos.

    Quando as permissões são concedidas com sucesso, a seguinte notificação é exibida no portal:

   ![Notificação de permissões concedidas com êxito](media/aad-integration/permissions-granted.png)

5. No painel esquerdo do aplicativo Azure AD, **selecione Expor uma API**e, em seguida, selecione **+ Adicione um escopo**.
    
    a. Digite um **nome de escopo,** um **nome de exibição**de consentimento de administrador e, em seguida, **uma descrição de consentimento do Administrador,** como *AKSAzureADServer*.

    b. Certifique-se de que **o estado** está definido **como Ativado**.

    ![Exponha o aplicativo do servidor como uma API para uso com outros serviços](media/aad-integration/expose-api.png)

    c. Selecione **Adicionar escopo**.

6. Retorne à página **de visão geral** do aplicativo e observe o **ID do aplicativo (cliente)**. Quando você implanta um cluster AKS habilitado para Azure AD, esse valor é chamado de ID do aplicativo do servidor.

    ![Obter ID do aplicativo](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>Crie o aplicativo cliente

O segundo aplicativo Azure AD é usado quando você faz login com o Kubernetes CLI (kubectl).

1. Selecione**registros** >  **do aplicativo azure active directory** > **Novo registro**.

    a. Dê um nome ao aplicativo, como *AKSAzureADClient*.

    b. Para **tipos de conta suportados,** selecione Contas somente neste diretório **organizacional**.

    c. Selecione **A Web** para o tipo Uri redirecionar e, *https://aksazureadclient*em seguida, digite qualquer valor formatado por URI, como .

    >[!NOTE]
    >Se você estiver criando um novo cluster habilitado para RBAC para suportar o Azure Monitor para contêineres, adicione as duas URLs adicionais adicionais a esta lista como tipos de aplicativos **da Web.** O valor da URL `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` de primeira base deve ser e o valor da URL da segunda base deve ser`https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`
    >
    >Se você estiver usando esse recurso no Azure China, `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` o valor da URL de primeira base deve ser e o valor da URL de segunda base deve ser`https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`
    >
    >Para obter mais informações, consulte [Como configurar o recurso Dados vivos (visualização)](../azure-monitor/insights/container-insights-livedata-setup.md) para o Monitor Azure para contêineres e as etapas para configurar a autenticação sob a seção Configurar a [autenticação integrada do AD.](../azure-monitor/insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication)

    d. Selecione **Registrar** quando terminar.

2. No painel esquerdo do aplicativo Azure AD, selecione **permissões de API**e selecione **+ Adicione uma permissão**.

    a. Selecione **Minhas APIs**e escolha o aplicativo de servidor Azure AD criado na etapa anterior, como *AKSAzureADServer*.

    b. Selecione **permissões delegadas**e selecione a caixa de seleção ao lado do aplicativo do servidor Azure AD.

    ![Configurar permissões de aplicativo](media/aad-integration/select-api.png)

    c. Selecione **Adicionar Permissões**.

    d. Sob **o consentimento de Grant,** **selecione o consentimento do grant.** Este botão não está disponível se a conta corrente não for um administração de inquilinos. Quando as permissões são concedidas, a seguinte notificação é exibida no portal:

    ![Notificação de permissões concedidas com êxito](media/aad-integration/permissions-granted.png)

3. No painel esquerdo do aplicativo Azure AD, selecione **Autenticação**.

    - No **tipo cliente Padrão,** selecione **Sim** para tratar o cliente como **cliente público**.

5. No painel esquerdo do aplicativo Azure AD, observe o ID do aplicativo. Quando você implanta um cluster AKS habilitado para Azure AD, esse valor é chamado de ID de aplicativo cliente.

   ![Obtenha a ID do aplicativo](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>Obter a id inquilino

Em seguida, pegue a id do seu inquilino Azure. Esse valor é usado quando você cria o cluster AKS.

No portal Azure, selecione > **Propriedades** de diretório ativo do **Azure**e observe o **ID do diretório**. Quando você cria um cluster AKS habilitado para Azure AD, esse valor é chamado de ID de inquilino.

![Obter a ID do locatário do Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>Implantar o cluster AKS

Utilize o comando [az group create][az-group-create] para criar um grupo de recursos para o cluster do AKS.

```azurecli
az group create --name myResourceGroup --location eastus
```

Use o comando [az aks create][az-aks-create] para implantar o cluster AKS. Em seguida, substitua os valores no seguinte comando de amostra. Use os valores coletados quando criou os aplicativos Azure AD para o ID do aplicativo do servidor, o aplicativo secreto, o ID do aplicativo cliente e o ID do inquilino.

```azurecli
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --generate-ssh-keys \
  --aad-server-app-id b1536b67-29ab-4b63-b60f-9444d0c15df1 \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 8aaf8bd5-1bdd-4822-99ad-02bfaa63eea7 \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

Um cluster AKS leva alguns minutos para criar.

## <a name="create-an-rbac-binding"></a>Criar uma vinculação RBAC

> [!NOTE]
> O nome de vinculação da função de cluster é sensível ao caso.

Antes de usar uma conta do Azure Active Directory com um cluster AKS, você deve criar vinculação de função ou vinculação de função. Funções definem as permissões a serem concedidas e ligações as aplicam aos usuários desejados. Essas atribuições podem ser aplicadas a um determinado namespace ou em todo o cluster. Para obter mais informações, consulte [Usando a autorização do RBAC][rbac-authorization].

Primeiro, use o comando [az aks get-credentials][az-aks-get-credentials] com o `--admin` argumento de entrar no cluster com acesso a administrador.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Em seguida, crie clusterRoleBinding para uma conta AD do Azure que você deseja conceder acesso ao cluster AKS. O exemplo a seguir dá à conta acesso total a todos os namespaces no cluster:

- Se o usuário para o que você concede a vinculação RBAC estiver no mesmo inquilino azure AD, atribua permissões com base no nome principal do usuário (UPN). Siga para a etapa para criar o manifesto YAML para ClusterRoleBinding.

- Se o usuário estiver em um inquilino Azure AD diferente, consulte e use a propriedade **objectId** em vez disso. Se necessário, obtenha o objectId da conta de usuário necessária usando o comando [az ad user show.][az-ad-user-show] Forneça o nome principal do usuário (UPN) da conta requerida:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Crie um arquivo, como *rbac-aad-user.yaml*e cole o seguinte conteúdo. Na última linha, substitua **userPrincipalName_or_objectId** pelo UPN ou pelo ID do objeto. A escolha depende se o usuário é o mesmo inquilino do Azure AD ou não.

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

Aplique a vinculação usando o comando [kubectl apply][kubectl-apply] como mostrado no exemplo a seguir:

```console
kubectl apply -f rbac-aad-user.yaml
```

Uma associação de função também pode ser criada para todos os membros de um grupo do Azure AD. Os grupos Azure AD são especificados usando o ID de objeto de grupo, conforme mostrado no exemplo a seguir.

Crie um arquivo, como *rbac-aad-group.yaml*e cole o seguinte conteúdo. Atualize a ID do objeto do grupo com um do seu locatário do Azure AD:

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
   kind: Group
   name: "894656e1-39f8-4bfe-b16a-510f61af6f41"
```

Aplique a vinculação usando o comando [kubectl apply][kubectl-apply] como mostrado no exemplo a seguir:

```console
kubectl apply -f rbac-aad-group.yaml
```

Para obter mais informações sobre como proteger um cluster do Kubernetes com RBAC, consulte [Usar autorização de RBAC][rbac-authorization].

## <a name="access-the-cluster-with-azure-ad"></a>Acesse o cluster com o Azure AD

Puxe o contexto para o usuário não-admin usando o comando [az aks get-credentials.][az-aks-get-credentials]

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Depois de `kubectl` executar o comando, você será solicitado a autenticar usando o Azure. Siga as instruções na tela para concluir o processo, conforme mostrado no exemplo a seguir:

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

Quando o processo é concluído, o token de autenticação é armazenado em cache. Você só é solicitado a fazer login novamente quando o token expirar, ou o arquivo de configuração Kubernetes é recriado.

Se você vir uma mensagem de erro de autorização depois de fazer login com sucesso, verifique os seguintes critérios:

```console
error: You must be logged in to the server (Unauthorized)
```


- Você definiu o ID de objeto apropriado ou UPN, dependendo se a conta de usuário está no mesmo inquilino Azure AD ou não.
- O usuário não é membro de mais de 200 grupos.
- O segredo definido no registro do aplicativo para `--aad-server-app-secret`servidor corresponde ao valor configurado pelo uso .

## <a name="next-steps"></a>Próximas etapas

Para usar usuários e grupos do Azure AD para controlar o acesso aos recursos de cluster, consulte [Controle de acesso a recursos de cluster usando controle de acesso baseado em função e identidades AD do Azure no AKS][azure-ad-rbac].

Para obter mais informações sobre como proteger os clusters Kubernetes, consulte [opções de acesso e identidade para AKS][rbac-authorization].

Para saber mais sobre controle de identidade e recursos, consulte [As melhores práticas para autenticação e autorização em AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
