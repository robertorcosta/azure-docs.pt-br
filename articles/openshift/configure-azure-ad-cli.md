---
title: Azure Red Hat OpenShift executando OpenShift 4-configurar a autenticação Azure Active Directory usando a linha de comando
description: Saiba como configurar a autenticação Azure Active Directory para um cluster do Azure Red Hat OpenShift executando o OpenShift 4 usando a linha de comando
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc
ms.openlocfilehash: 45da3034891e5a82fb8423adb6bcd5e867f9d4e2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82204995"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-cli"></a>Configurar a autenticação de Azure Active Directory para um cluster do Azure Red Hat OpenShift 4 (CLI)

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que você esteja executando o CLI do Azure versão 2.0.75 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Recupere as URLs específicas do cluster que serão usadas para configurar o aplicativo Azure Active Directory.

Construa a URL de retorno de chamada OAuth do cluster e armazene-a em uma variável **oauthCallbackURL**. Certifique-se de substituir **toa-RG** pelo nome do grupo de recursos e pela **toa-cluster** pelo nome do cluster.

> [!NOTE]
> A `AAD` seção na URL de retorno de chamada OAuth deve corresponder ao nome do provedor de identidade OAuth que você irá configurar mais tarde.

```azurecli-interactive
domain=$(az aro show -g aro-rg -n aro-cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g aro-rg -n aro-cluster --query location -o tsv)
apiServer=$(az aro show -g aro-rg -n aro-cluster --query apiserverProfile.url -o tsv)
webConsole=$(az aro show -g aro-rg -n aro-cluster --query consoleProfile.url -o tsv)
oauthCallbackURL=https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD
```

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Criar um aplicativo Azure Active Directory para autenticação

Crie um aplicativo Azure Active Directory e recupere o identificador de aplicativo criado. Substituir **\<ClientSecret>** por uma senha segura.

```azurecli-interactive
az ad app create \
  --query appId -o tsv \
  --display-name aro-auth \
  --reply-urls $oauthCallbackURL \
  --password '<ClientSecret>'
```

Você deve receber algo assim. Anote-a, pois essa é a **AppID** que você precisará em etapas posteriores.

```output
6a4cb4b2-f102-4125-b5f5-9ad6689f7224
```

Recupere a ID de locatário da assinatura proprietária do aplicativo.

```azure
az account show --query tenantId -o tsv
```

Você deve receber algo assim. Anote isso, pois essa é a **tenantid** que você precisará em etapas posteriores.

```output
72f999sx-8sk1-8snc-js82-2d7cj902db47
```

## <a name="create-a-manifest-file-to-define-the-optional-claims-to-include-in-the-id-token"></a>Criar um arquivo de manifesto para definir as declarações opcionais a serem incluídas no token de ID

Os desenvolvedores de aplicativos podem usar [declarações opcionais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims) em seus aplicativos do Azure ad para especificar quais declarações eles desejam em tokens enviados para seu aplicativo.

Você pode usar declarações opcionais para:

- Selecione declarações adicionais para incluir nos tokens para o aplicativo.
- Altere o comportamento de determinadas declarações que o Azure AD retorna em tokens.
- Adicione e acesse as declarações personalizadas para o aplicativo.

Vamos configurar OpenShift para usar a `email` declaração e voltar para `upn` para definir o nome de usuário preferencial adicionando o `upn` como parte do token de ID retornado por Azure Active Directory.

Crie um **manifest.jsno** arquivo para configurar o aplicativo Azure Active Directory.

```bash
cat > manifest.json<< EOF
[{
  "name": "upn",
  "source": null,
  "essential": false,
  "additionalProperties": []
},
{
"name": "email",
  "source": null,
  "essential": false,
  "additionalProperties": []
}]
EOF
```

## <a name="update-the-azure-active-directory-applications-optionalclaims-with-a-manifest"></a>Atualizar o optionalClaims do aplicativo de Azure Active Directory com um manifesto

Substitua **\<AppID>** pela ID que você obteve anteriormente.

```azurecli-interactive
az ad app update \
  --set optionalClaims.idToken=@manifest.json \
  --id <AppId>
```

## <a name="update-the-azure-active-directory-application-scope-permissions"></a>Atualizar as permissões de escopo do aplicativo Azure Active Directory

Para poder ler as informações do usuário de Azure Active Directory, precisamos definir os escopos adequados.

Substitua **\<AppID>** pela ID que você obteve anteriormente.

Adicione permissão para o escopo **Azure Active Directory Graph. User. Read** para habilitar a entrada e ler o perfil do usuário.

```azurecli-interactive
az ad app permission add \
 --api 00000002-0000-0000-c000-000000000000 \
 --api-permissions 311a71cc-e848-46a1-bdf8-97ff7156d8e6=Scope \
 --id <AppId>
```

> [!NOTE]
> A menos que você esteja autenticado como um administrador global desse Azure Active Directory, você pode ignorar a mensagem para conceder o consentimento, pois será solicitado que você faça isso depois de fazer logon em sua própria conta.

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>Atribuir usuários e grupos ao cluster (opcional)

Aplicativos registrados em um locatário do Azure Active Directory (Azure AD) estão, por padrão, disponíveis para todos os usuários do locatário que se autenticar com êxito. O Azure AD permite que os desenvolvedores e administradores de locatários restrinjam um aplicativo a um conjunto específico de usuários ou grupos de segurança no locatário.

Siga as instruções na documentação do Azure Active Directory para [atribuir usuários e grupos ao aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users#app-registration).

## <a name="configure-openshift-openid-authentication"></a>Configurar a Autenticação OpenID OpenShift

Recupere as `kubeadmin` credenciais. Execute o comando a seguir para localizar a senha para o usuário `kubeadmin`.

```azurecli-interactive
az aro list-credentials \
  --name aro-cluster \
  --resource-group aro-rg
```

A saída de exemplo a seguir mostra que a senha estará em `kubeadminPassword`.

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

Faça logon no servidor de API do cluster OpenShift usando o comando a seguir. A `$apiServer` variável foi definida [anteriormente](). Substitua **\<kubeadmin password>** pela senha que você recuperou.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

Crie um segredo OpenShift para armazenar o segredo do aplicativo Azure Active Directory, substituindo **\<ClientSecret>** pelo segredo que você recuperou anteriormente.

```azurecli-interactive
oc create secret generic openid-client-secret-azuread \
  --namespace openshift-config \
  --from-literal=clientSecret=<ClientSecret>
```    

Crie um arquivo **oidc. YAML** para configurar a autenticação do OpenShift OpenID em relação ao Azure Active Directory. Substitua **\<AppID>** e **\<TenantId>** pelos valores recuperados anteriormente.

```bash
cat > oidc.yaml<< EOF
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: AAD
    mappingMethod: claim
    type: OpenID
    openID:
      clientID: <AppId>
      clientSecret: 
        name: openid-client-secret-azuread
      extraScopes: 
      - email
      - profile
      extraAuthorizeParameters: 
        include_granted_scopes: "true"
      claims:
        preferredUsername: 
        - email
        - upn
        name: 
        - name
        email: 
        - email
      issuer: https://login.microsoftonline.com/<TenantId>
EOF
```

Aplique a configuração ao cluster.

```azurecli-interactive
oc apply -f oidc.yaml
```

Você receberá uma resposta semelhante à seguinte.

```output
oauth.config.openshift.io/cluster configured
```

## <a name="verify-login-through-azure-active-directory"></a>Verificar o logon por meio de Azure Active Directory

Se agora fizer logoff do console Web do OpenShift e tentar fazer logon novamente, você verá uma nova opção para fazer logon com o **AAD**. Talvez seja necessário aguardar alguns minutos.

![Tela de logon com a opção Azure Active Directory](media/aro4-login-2.png)
