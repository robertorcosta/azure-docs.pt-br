---
title: Use as APIs do Microsoft Graph para configurar o logon único baseado em SAML
titleSuffix: Azure Active Directory
description: Precisa configurar o logon único baseado em SAML para várias instâncias de um aplicativo? Saiba como economizar tempo usando as APIs do Microsoft Graph para automatizar a configuração do logon único baseado em SAML.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: luleon
ms.openlocfilehash: 5b5de26afceb1127b42c937f1cb1005a660881d4
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87273415"
---
# <a name="automate-saml-based-sso-app-configuration-with-microsoft-graph-api"></a>Automatizar a configuração do aplicativo SSO baseado em SAML com a API do Microsoft Graph

Neste artigo, você aprenderá a criar e configurar um aplicativo na galeria do Azure Active Directory (AAD). Este artigo usa a AWS como exemplo, mas é possível usar as etapas neste artigo para qualquer aplicativo baseado em SAML na galeria do Azure Active Directory.

**Etapas para usar as APIs do Microsoft Graph para automatizar a configuração do logon único baseado em SAML**

| Etapa  | Detalhes  |
|---------|---------|
| [1. Criar o aplicativo de galeria](#step-1-create-the-gallery-application) | Entrar no cliente da API <br> Recuperar o aplicativo de galeria <br> Criar o aplicativo de galeria|
| [2. Configurar o logon único](#step-2-configure-single-sign-on) | Recuperar a ID de objeto do aplicativo e a ID de objeto da entidade de serviço <br> Configurar o modo de logon único <br> Defina as URLs básicas do SAML, como o identificador, a URL de resposta e a URL de logon <br> Adicionar funções de aplicativo (opcional)|
| [3. Configurar mapeamento de declarações](#step-3-configure-claims-mapping) | Criar uma política de mapeamento de declarações <br> Atribuir a política de mapeamento de declarações à entidade de serviço|
| [4. Configurar o certificado de assinatura](#step-4-configure-signing-certificate) | Criar um certificado <BR> Adicionar uma chave de assinatura personalizada <br> Ativar a chave de assinatura personalizada|
| [5. Atribuir usuários](#step-5-assign-users) | Atribuir usuários e grupos ao aplicativo
| [6. Configurar o lado do aplicativo](#step-6-configure-the-application-side)| Obter metadados do SAML do Azure Active Directory

**Lista de todas as APIs usadas na documentação**

Verifique se você tem as permissões correspondentes para chamar as APIs a seguir.

|Tipo de recurso |Método |
|---------|---------|
|[applicationTemplate](https://docs.microsoft.com/graph/api/resources/applicationtemplate?view=graph-rest-beta)|[Listar applicationTemplate](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) <br>[Instanciar applicationTemplate](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http)|
|[servicePrincipals](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-1.0)|[Atualizar servicePrincipal](https://docs.microsoft.com/graph/api/serviceprincipal-update?view=graph-rest-1.0&tabs=http) <br> [Criar appRoleAssignments](https://docs.microsoft.com/graph/api/serviceprincipal-post-approleassignments?view=graph-rest-1.0&tabs=http) <br> [Atribuir claimsMappingPolicies](https://docs.microsoft.com/graph/api/serviceprincipal-post-claimsmappingpolicies?view=graph-rest-beta&tabs=http)|
|[aplicativos](https://docs.microsoft.com/graph/api/resources/application?view=graph-rest-1.0)|[Atualizar o aplicativo](https://docs.microsoft.com/graph/api/application-update?view=graph-rest-1.0&tabs=http)|
|[claimsMappingPolicy](https://docs.microsoft.com/graph/api/resources/claimsmappingpolicy?view=graph-rest-beta)| [Criar claimsMappingPolicy](https://docs.microsoft.com/graph/api/claimsmappingpolicy-post-claimsmappingpolicies?view=graph-rest-beta&tabs=http)

>[!NOTE]
>Os objetos de resposta mostrados neste artigo podem ser reduzidos para facilitar a leitura. Todas as propriedades serão retornadas de uma chamada real.

## <a name="step-1-create-the-gallery-application"></a>Etapa 1: Criar o aplicativo de galeria

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Entre no Microsoft Graph Explorer (recomendado), no Postman ou em qualquer outro cliente de API que você usar

1. Inicie o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
2. Escolha **Entrar com a Microsoft** e entre usando um administrador global do Azure Active Directory ou credenciais de administrador de aplicativo.
3. Depois de entrar, você verá os detalhes da conta de usuário no painel esquerdo.

### <a name="retrieve-the-gallery-application-template-identifier"></a>Recuperar o identificador do modelo de aplicativo da galeria

Os aplicativos na galeria de aplicativos do Azure Active Directory têm um [modelo de aplicativo](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) que descreve os metadados para esse aplicativo. Usando esse modelo, é possível criar uma instância do aplicativo e a entidade de serviço em seu locatário para gerenciamento.

#### <a name="request"></a>Solicitação

<!-- {
  "blockType": "request",
  "name": "get_applicationtemplates"
}-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```

#### <a name="response"></a>Resposta

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "Amazon Web Services (AWS)",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": null    
  
}
```

### <a name="create-the-gallery-application"></a>Criar o aplicativo de galeria

Usando a ID de modelo que você recuperou para seu aplicativo na última etapa, [crie uma instância](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http) do aplicativo e da entidade de serviço em seu locatário.

> [!NOTE] 
> Você pode usar a API do applicationtemplate para instanciar [aplicativos que não são da Galeria](add-non-gallery-app.md). Use applicationTemplateId `8adf8e6e-67b2-4cf2-a259-e3dc5476c621` .
#### <a name="request"></a>Solicitação

<!-- {
  "blockType": "request",
  "name": "applicationtemplate_instantiate"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/8b1025e4-1dd2-430b-a150-2ef79cd700f5/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>Resposta


<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationServicePrincipal"
} -->

```http
HTTP/1.1 201 OK
Content-type: application/json


{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-configure-single-sign-on"></a>Etapa 2: Configurar o logon único

### <a name="retrieve-app-object-id-and-service-principal-object-id"></a>Recuperar a ID de objeto do aplicativo e a ID de objeto da entidade de serviço

Use a resposta da chamada anterior para recuperar e salvar a ID de objeto do aplicativo e a ID de objeto da entidade de serviço.

```
"application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63"
}
"servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e"
}
```
### <a name="set-single-sign-on-mode"></a>Configurar o modo de logon único

Neste exemplo, você definirá `saml` como o modo de logon único no [tipo de recurso servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-1.0). Outras propriedades de logon único do SAML que podem ser configuradas são: `notificationEmailAddresses``loginUrl` e `samlSingleSignOnSettings.relayState`

#### <a name="request"></a>Solicitação

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e
Content-type: servicePrincipal/json

{
    "preferredSingleSignOnMode": "saml"
}
```

#### <a name="response"></a>Resposta

<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 204
```

### <a name="set-basic-saml-urls-such-as-identifier-reply-url-sign-on-url"></a>Defina as URLs básicas do SAML, como o identificador, a URL de resposta e a URL de logon

Defina o identificador e URLs de resposta para a AWS no objeto do aplicativo.

#### <a name="request"></a>Solicitação

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/applications/cbc071a6-0fa5-4859-8g55-e983ef63df63
Content-type: applications/json

{
    "web": {
        "redirectUris": [
            "https://signin.aws.amazon.com/saml"
        ] 
    },
    "identifierUris": [
        "https://signin.aws.amazon.com/saml"
    ]    
}
```
#### <a name="response"></a>Resposta

<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 204
```
### <a name="add-app-roles-optional"></a>Adicionar funções de aplicativo (opcional)

Se o aplicativo exigir as informações de função no token, adicione a definição das funções no objeto do aplicativo. Para a AWS, é possível [habilitar o provisionamento do usuário](../app-provisioning/application-provisioning-configure-api.md) para buscar todas as funções dessa conta da AWS. 

Para obter mais informações, leia [Configurar a declaração de função emitida no token SAML](../develop/active-directory-enterprise-app-role-management.md)

> [!NOTE] 
> Ao adicionar funções de aplicativo, não mude as funções de aplicativo padrão msiam_access. 

#### <a name="request"></a>Solicitação

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/serviceprincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e
Content-type: serviceprincipals/json

{
"appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "454dc4c2-8176-498e-99df-8c4efcde41ef",
            "isEnabled": true,
            "value": "arn:aws:iam::212743507312:role/accountname-aws-admin,arn:aws:iam::212743507312:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Finance,WAAD",
            "displayName": "Finance,WAAD",
            "id": "8642d5fa-18a3-4245-ab8c-a96000c1a217",
            "isEnabled": true,
            "value": "arn:aws:iam::212743507312:role/accountname-aws-finance,arn:aws:iam::212743507312:saml-provider/WAAD"
        }
    ]

}
```

#### <a name="response"></a>Resposta

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

## <a name="step-3-configure-claims-mapping"></a>Etapa 3: Configurar mapeamento de declarações

### <a name="create-claims-mapping-policy"></a>Criar uma política de mapeamento de declarações

Além das declarações básicas, configure as seguintes declarações para que o Azure Active Directory emita no token SAML:

| Nome da declaração | Fonte  |
|---------|---------|
| `https://aws.amazon.com/SAML/Attributes/Role` | assignedroles| 
| `https://aws.amazon.com/SAML/Attributes/RoleSessionName` | userprincipalname |
| `https://aws.amazon.com/SAML/Attributes/SessionDuration` | "900" |
| roles | assignedroles |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` | userprincipalname |

Para obter mais informações, consulte [Personalizar declarações emitidas no token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).

#### <a name="request"></a>Solicitação

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/v1.0/policies/claimsMappingPolicies
Content-type: claimsMappingPolicies/json

{
    "definition":[
            "{\"ClaimsMappingPolicy\": {
                \"Version\": 1,
                \"IncludeBasicClaimSet\": \"true\",
                \"ClaimsSchema\": [
                    {
                        \"Source\": \"user\",
                        \"ID\": \"assignedroles\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/Role\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"userprincipalname\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/RoleSessionName\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"900\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/SessionDuration\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"assignedroles\",
                        \"SamlClaimType\": \"appRoles\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"userprincipalname\",
                        \"SamlClaimType\": \"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\"
                    }
                ]
            }
        }"

    ],
    "displayName": "AWS Claims policy",
    "isOrganizationDefault": false
}
```

#### <a name="response"></a>Resposta

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.claimsMappingPolicies",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: claimsMappingPolicies/json

{
    "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#policies/claimsMappingPolicies/$entity",
    "id": "6b33aa8e-51f3-41a6-a0fd-d660d276197a",
    "definition": [
        "{\"ClaimsMappingPolicy\": {\"Version\": 1,\"IncludeBasicClaimSet\": \"true\",\"ClaimsSchema\": [{\"Source\": \"user\",\"ID\": \"assignedroles\",\"SamlClaimType\":\"https://aws.amazon.com/SAML/Attributes/Role\"\r\n                    },{\"Source\": \"user\",\"ID\": \"userprincipalname\",\"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/RoleSessionName\"},{\"Source\": \"user\",\"ID\": \"900\",\"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/SessionDuration\"},{\"Source\": \"user\",\"ID\": \"assignedroles\",\"SamlClaimType\":\"appRoles\"},{\"Source\": \"user\",\"ID\": \"userprincipalname\",\"SamlClaimType\": \"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\"}]}}"
    ],
    "displayName": "AWS Claims policy",
    "isOrganizationDefault": false
}
```

### <a name="assign-claims-mapping-policy-to-service-principal"></a>Atribuir a política de mapeamento de declarações à entidade de serviço

#### <a name="request"></a>Solicitação

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e/claimsMappingPolicies/$ref

Content-type: claimsMappingPolicies/json

{
  "@odata.id":"https://graph.microsoft.com/v1.0/policies/claimsMappingPolicies/6b33aa8e-51f3-41a6-a0fd-d660d276197a"
}
```

#### <a name="response"></a>Resposta

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

## <a name="step-4-configure-signing-certificate"></a>Etapa 4: Configurar o certificado de assinatura

O uso da API [applicationTemplate](https://docs.microsoft.com/graph/api/resources/applicationtemplate?view=graph-rest-beta) não cria um certificado de autenticação por padrão. Crie seu certificado de assinatura personalizado e atribua ele ao aplicativo. 

### <a name="create-a-custom-signing-certificate"></a>Criar um certificado de autenticação personalizado

Para testar, é possível usar o seguinte comando do PowerShell para obter um certificado autoassinado. Use a prática de segurança recomendada da sua empresa para criar um certificado de autenticação para produção.

```powershell
Param(
    [Parameter(Mandatory=$true)]
    [string]$fqdn,
    [Parameter(Mandatory=$true)]
    [string]$pwd,
    [Parameter(Mandatory=$true)]
    [string]$location
) 

if (!$PSBoundParameters.ContainsKey('location'))
{
    $location = "."
} 

$cert = New-SelfSignedCertificate -certstorelocation cert:\currentuser\my -DnsName $fqdn
$pwdSecure = ConvertTo-SecureString -String $pwd -Force -AsPlainText
$path = 'cert:\currentuser\my\' + $cert.Thumbprint
$cerFile = $location + "\\" + $fqdn + ".cer"
$pfxFile = $location + "\\" + $fqdn + ".pfx" 

Export-PfxCertificate -cert $path -FilePath $pfxFile -Password $pwdSecure
Export-Certificate -cert $path -FilePath $cerFile
```

### <a name="add-a-custom-signing-key"></a>Adicionar uma chave de assinatura personalizada

Adicione as seguintes informações à entidade de serviço:

* Chave privada
* Senha
* Chave pública 

Extraia a codificação Base64 da chave pública e privada do arquivo PFX. Para saber mais sobre as propriedades, leia [tipo de recurso keyCredential](https://docs.microsoft.com/graph/api/resources/keycredential?view=graph-rest-1.0).

Certifique-se de que o keyId para a keyCredential usada para "assinar" corresponda à keyId de passwordCredential.

É possível gerar o `customkeyIdentifier` obtendo o hash da impressão digital do certificado.

```csharp
  public string GetSha256FromThumbprint(string thumbprint)
  {
      var message = Encoding.ASCII.GetBytes(thumbprint);
      SHA256Managed hashString = new SHA256Managed();
      return Convert.ToBase64String(hashString.ComputeHash(message));
  }
```

#### <a name="request"></a>Solicitação

> [!NOTE]
> O valor de "chave" na propriedade keyCredentials é reduzido para facilitar a leitura. O valor é codificado de base 64. Para a chave privada, a propriedade `usage` é "Sign". Para a chave pública, a propriedade `usage` é "Verify".

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json

{
    "keyCredentials":[
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Sign",
            "key":"MIIKIAIBAz.....HBgUrDgMCERE20nuTptI9MEFCh2Ih2jaaLZBZGeZBRFVNXeZmAAgIH0A==",
            "displayName": "CN=awsAPI"
        },
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "e35a7d11-fef0-49ad-9f3e-aacbe0a42c42",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "key": "MIIDJzCCAg+gAw......CTxQvJ/zN3bafeesMSueR83hlCSyg==",
            "displayName": "CN=awsAPI"
        }

    ],
    "passwordCredentials": [
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "endDateTime": "2022-01-27T19:40:33Z",
            "startDateTime": "2020-04-20T19:40:33Z",
            "secretText": "61891f4ee44d"
        }
    ]
}
```

#### <a name="response"></a>Resposta

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

### <a name="activate-the-custom-signing-key"></a>Ativar a chave de assinatura personalizada

É preciso definir a propriedade `preferredTokenSigningKeyThumbprint` como a impressão digital do certificado que você deseja que o Azure Active Directory use para assinar a resposta SAML. 

#### <a name="request"></a>Solicitação

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json

{
    "preferredTokenSigningKeyThumbprint": "AC09FEF18DDE6983EE2A164FBA3C4DD7518BD787"
}
```

#### <a name="response"></a>Resposta

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```
## <a name="step-5-assign-users"></a>Etapa 5: Atribuir usuários

### <a name="assign-users-and-groups-to-the-application"></a>Atribuir usuários e grupos ao aplicativo

Atribua o usuário a seguir à entidade de serviço e atribua o AWS_Role1. 

| Nome  | ID  |
|---------|---------|
| ID de usuário (principalId) | 6cad4079-4e79-4a3f-9efb-ea30a14bdb26 |
| Tipo (principalType) | Usuário |
| ID da função de aplicativo (appRoleId) | 454dc4c2-8176-498e-99df-8c4efcde41ef |
| servicePrincipalID (resourceId) | f47a6776-bca7-4f2e-bc6c-eec59d058e3e |

#### <a name="request"></a>Solicitação

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e/appRoleAssignments

Content-type: appRoleAssignments/json

{
  "principalId": "6cad4079-4e79-4a3f-9efb-ea30a14bdb26",
  "principalType": "User",
  "appRoleId":"454dc4c2-8176-498e-99df-8c4efcde41ef",
  "resourceId":"f47a6776-bca7-4f2e-bc6c-eec59d058e3e"
}
```
#### <a name="response"></a>Resposta

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 201 
Content-type: appRoleAssignments/json

{
    "id": "rq7hyzl4yECaNZleMrTpDV-OCe5TEl5Ao_o76XMrRFU",
    "creationTimestamp": "2020-04-23T17:38:13.2508567Z",
    "appRoleId": "454dc4c2-8176-498e-99df-8c4efcde41ef",
    "principalDisplayName": "User 1",
    "principalId": "6cad4079-4e79-4a3f-9efb-ea30a14bdb26",
    "principalType": "User",
    "resourceDisplayName": "AWS API Created",
    "resourceId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e"
}
```

Para obter mais informações, consulte o tipo de recurso [appRoleAssignment](https://docs.microsoft.com/graph/api/resources/approleassignment?view=graph-rest-1.0).

## <a name="step-6-configure-the-application-side"></a>Etapa 6: Configurar o lado do aplicativo

### <a name="get-azure-ad-saml-metadata"></a>Obter metadados do SAML do Azure Active Directory

Use a URL a seguir para obter os metadados do SAML do Azure Active Directory para o aplicativo específico configurado. Os metadados contêm informações como o certificado de autenticação, o entityID do Azure Active Directory e o SingleSignOnService do Azure Active Directory, entre outros.

https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id}

## <a name="next-steps"></a>Próximas etapas
- [Usar APIs do Microsoft Graph para configurar o provisionamento de usuário](../app-provisioning/application-provisioning-configure-api.md)
- [Usar o relatório de atividade do aplicativo do AD FS para migrar aplicativos para o Azure Active Directory](migrate-adfs-application-activity.md)
