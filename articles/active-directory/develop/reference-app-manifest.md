---
title: Entendendo o manifesto do aplicativo Azure Active Directory
description: A cobertura detalhada do manifesto do aplicativo do Azure Active Directory, que representa a configuração de identidade de um aplicativo em um locatário do Azure AD, e é usado para facilitar a autorização do OAuth, a experiência de consentimento e muito mais.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: 9f2ed6ea8cc75e2ee72f15c14f3de7bb8bf8cef6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450864"
---
# <a name="azure-active-directory-app-manifest"></a>Manifesto do aplicativo do Azure Active Directory

O manifesto do aplicativo contém uma definição de todos os atributos de um objeto de aplicativo na plataforma de identidade da Microsoft. Ele também serve como um mecanismo para atualizar o objeto do aplicativo. Para obter mais informações sobre a entidade do aplicativo e seu esquema, consulte a documentação da [entidade do aplicativo gráfico API](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity).

Você pode configurar os atributos de um aplicativo através do portal Azure ou programáticamente usando [a API REST](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) ou [powerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications). No entanto, existem alguns cenários em que você precisará editar o manifesto do aplicativo para configurar o atributo de um aplicativo. Esses cenários incluem:

* Se você registrou o aplicativo como contas azure AD multi-inquilino saqueadas e pessoais da Microsoft, você não poderá alterar as contas da Microsoft suportadas na UI. Em vez disso, você deve usar o editor de manifesto do aplicativo para alterar o tipo de conta suportado.
* Se você precisa definir permissões e funções que seu aplicativo suporta, você deve modificar o manifesto do aplicativo.

## <a name="configure-the-app-manifest"></a>Configurar o manifesto do aplicativo

Para configurar o manifesto do aplicativo:

1. Vá para o [Portal do Azure](https://portal.azure.com). Procure e selecione o serviço diretório ativo do **Azure.**
1. Selecione **inscrições do Aplicativo**.
1. Selecione o aplicativo que você deseja configurar.
1. Na página **Visão Geral** do aplicativo, selecione a seção **Manifesto**. Um editor de manifesto baseado na Web é aberto, permitindo que você edite o manifesto no portal. Opcionalmente, você pode selecionar **Download** para editar o manifesto localmente e usar **Upload** para reaplicá-lo ao seu aplicativo.

## <a name="manifest-reference"></a>Referência do manifesto

Esta seção descreve os atributos encontrados no manifesto de aplicação.

### <a name="id-attribute"></a>atributo id

| Chave | Tipo de valor |
| :--- | :--- |
| id | String |

O identificador exclusivo do aplicativo no diretório. Essa ID não é o identificador usado para identificar o aplicativo em qualquer transação de protocolo. Ele é usado para referenciar o objeto em consultas de diretório.

Exemplo:

```json
    "id": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="accesstokenacceptedversion-attribute"></a>atributo accessTokenAcceptedVersion

| Chave | Tipo de valor |
| :--- | :--- |
| accessTokenAcceptedVersion | Int32 anulável |

Especifica a versão do token de acesso esperada pelo recurso. Este parâmetro altera a versão e o formato do JWT produzido independente mente do ponto final ou cliente usado para solicitar o token de acesso.

O ponto de extremidade usado, v1.0 ou v2.0, é escolhido pelo cliente e só afeta a versão do id_tokens. Os recursos precisam configurar explicitamente `accesstokenAcceptedVersion` para indicar o formato do token de acesso com suporte.

Os valores possíveis para `accesstokenAcceptedVersion` são 1, 2 ou nulo. Se o valor for nulo, esse parâmetro será padrão para 1, o que corresponde ao ponto final v1.0.

Se `signInAudience` `AzureADandPersonalMicrosoftAccount`for, o `2`valor deve ser.

Exemplo:

```json
    "accessTokenAcceptedVersion": 2,
```

### <a name="addins-attribute"></a>atributo addIns

| Chave | Tipo de valor |
| :--- | :--- |
| addIns | Coleção |

Define o comportamento personalizado que um serviço consumidor pode usar para chamar um aplicativo em contextos específicos. Por exemplo, aplicativos que podem renderizar `addIns` fluxos de arquivos podem definir a propriedade para sua funcionalidade "FileHandler". Esse parâmetro permitirá que serviços como o Office 365 chamem o aplicativo no contexto de um documento em que o usuário está trabalhando.

Exemplo:

```json
    "addIns": [
       {
        "id": "968A844F-7A47-430C-9163-07AE7C31D407",
        "type":" FileHandler",
        "properties": [
           {
              "key": "version",
              "value": "2"
           }
        ]
       }
    ],
```

### <a name="allowpublicclient-attribute"></a>permitiraaaaaaaatribuiçãodoPublicClient

| Chave | Tipo de valor |
| :--- | :--- |
| permitirPublicClient | Boolean |

Especifica o tipo de aplicativo de fallback. O Azure AD infere o tipo de aplicativo do replyUrlsWithType por padrão. Existem certos cenários em que o Azure AD não pode determinar o tipo de aplicativo cliente. Por exemplo, um desses cenários é o fluxo [DE ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) onde a solicitação HTTP acontece sem um redirecionamento de URL). Nesses casos, o Azure AD interpretará o tipo de aplicação com base no valor desta propriedade. Se esse valor for definido como true, o tipo de aplicativo de fallback será definido como cliente público, como um aplicativo instalado em execução em um dispositivo móvel. O valor padrão é false, que significa que o tipo de aplicativo de fallback é cliente confidencial, como o aplicativo Web.

Exemplo:

```json
    "allowPublicClient": false,
```

### <a name="availabletoothertenants-attribute"></a>disponívelAtribuiçãoToOtherTenants

| Chave | Tipo de valor |
| :--- | :--- |
| availableToOtherTenants | Boolean |

Definido como verdadeiro se o aplicativo for compartilhado com outros inquilinos; caso contrário, falso.

> [!NOTE]
> Este atributo está disponível apenas na experiência de registros do **App (Legacy).** Substituído pela `signInAudience` experiência de registro de [aplicativos.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="appid-attribute"></a>atributo appId

| Chave | Tipo de valor |
| :--- | :--- |
| appId | String |

Especifica o identificador exclusivo do aplicativo que é atribuído a um aplicativo pelo Azure AD.

Exemplo:

```json
    "appId": "601790de-b632-4f57-9523-ee7cb6ceba95",
```

### <a name="approles-attribute"></a>appRoles atributo

| Chave | Tipo de valor |
| :--- | :--- |
| appRoles | Coleção |

Especifica a coleção de funções que um aplicativo pode declarar. Essas funções podem ser atribuídas a usuários, grupos ou entidades de serviço. Para obter mais exemplos e informações, consulte [Adicionar funções de aplicativo em seu aplicativo e recebê-las no token](howto-add-app-roles-in-azure-ad-apps.md).

Exemplo:

```json
    "appRoles": [
        {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "Read-only access to device information",
           "displayName": "Read Only",
           "id": "601790de-b632-4f57-9523-ee7cb6ceba95",
           "isEnabled": true,
           "value": "ReadOnly"
        }
    ],
```

### <a name="displayname-attribute"></a>atributo displayName

| Chave | Tipo de valor |
| :--- | :--- |
| displayName | String |

O nome de exibição do aplicativo.

> [!NOTE]
> Este atributo está disponível apenas na experiência de registros do **App (Legacy).** Substituído pela `name` experiência de registro de [aplicativos.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="errorurl-attribute"></a>atributo de url de erro

| Chave | Tipo de valor |
| :--- | :--- |
| errorUrl | String |

Sem suporte.

### <a name="groupmembershipclaims-attribute"></a>atributo groupMembershipClaims

| Chave | Tipo de valor |
| :--- | :--- |
|GroupMembershipClaims | String |

Configura a declaração `groups` emitida em um usuário ou o token de acesso OAuth 2.0 que o aplicativo espera. Para definir esse atributo, use um dos seguintes valores válidos da cadeia de caracteres:

- `"None"`
- `"SecurityGroup"` (para grupos de segurança e funções do Azure AD)
- `"All"` (isso obterá todos os grupos de segurança, grupos de distribuição e funções do diretório do Azure AD dos quais o usuário conectado é membro.

Exemplo:

```json
    "groupMembershipClaims": "SecurityGroup",
```

### <a name="homepage-attribute"></a>atributo página inicial

| Chave | Tipo de valor |
| :--- | :--- |
| homepage |String |

A URL para a home page do aplicativo.

> [!NOTE]
> Este atributo está disponível apenas na experiência de registros do **App (Legacy).** Substituído pela `signInUrl` experiência de registro de [aplicativos.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="objectid-attribute"></a>atributo objectId

| Chave | Tipo de valor |
| :--- | :--- |
|objectId | String |

O identificador exclusivo do aplicativo no diretório.

Isso está disponível apenas na experiência de registros do **App (Legacy).** Substituído pela `id` experiência de registro de [aplicativos.](https://go.microsoft.com/fwlink/?linkid=2083908)

Exemplo:

```json
    "objectId": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="optionalclaims-attribute"></a>Atribuição optionalClaims

| Chave | Tipo de valor |
| :--- | :--- |
| optionalClaims | String |

As declarações opcionais retornadas no token pelo serviço de token de segurança para este aplicativo específico.

No momento, os aplicativos que oferecem suporte a contas pessoais e ao Azure AD (registrados por meio do portal de registro de aplicativos) não podem usar declarações opcionais. No entanto, os aplicativos registrados apenas para o Azure AD usando o ponto de extremidade v2.0 podem obter as declarações opcionais solicitadas no manifesto. Para obter mais informações, consulte [Reivindicações opcionais](active-directory-optional-claims.md).

Exemplo:

```json
    "optionalClaims": null,
```



### <a name="identifieruris-attribute"></a>identificadorAaUris

| Chave | Tipo de valor |
| :--- | :--- |
| identifierUris | Matriz de cadeia de caracteres |

Os URIs definidos pelo usuário que identificam exclusivamente um aplicativo Web em seu locatário do Azure AD ou em um domínio personalizado verificado, quando o aplicativo é multilocatário.

Exemplo:

```json
    "identifierUris": "https://MyRegisteredApp",
```

### <a name="informationalurls-attribute"></a>atributo informationalUrls

| Chave | Tipo de valor |
| :--- | :--- |
| Urls informativos | String |

Especifica os links para os termos de serviço e a política de privacidade do aplicativo. Os termos de serviço e a declaração de privacidade são revelados aos usuários por meio da experiência de consentimento do usuário. Para obter mais informações, confira [Como adicionar termos de serviço e política de privacidade para aplicativos do Azure AD registrados](howto-add-terms-of-service-privacy-statement.md).

Exemplo:

```json
    "informationalUrls": {
        "termsOfService": "https://MyRegisteredApp/termsofservice",
        "support": "https://MyRegisteredApp/support",
        "privacy": "https://MyRegisteredApp/privacystatement",
        "marketing": "https://MyRegisteredApp/marketing"
    },
```

### <a name="keycredentials-attribute"></a>keyCredentials atributo

| Chave | Tipo de valor |
| :--- | :--- |
| keyCredentials | Coleção |

Contém referências a credenciais, segredos compartilhados com base em cadeia de caracteres e certificados X.509 atribuídos ao aplicativo. Essas credenciais são usadas ao solicitar tokens de acesso (quando o aplicativo está agindo como um cliente, em vez disso, como um recurso).

Exemplo:

```json
    "keyCredentials": [
        {
           "customKeyIdentifier":null,
           "endDate":"2018-09-13T00:00:00Z",
           "keyId":"<guid>",
           "startDate":"2017-09-12T00:00:00Z",
           "type":"AsymmetricX509Cert",
           "usage":"Verify",
           "value":null
        }
    ],
```

### <a name="knownclientapplications-attribute"></a>atributo clientes conhecido

| Chave | Tipo de valor |
| :--- | :--- |
| knownClientApplications | Matriz de cadeia de caracteres |

Usado para agrupamento de consentimento no caso de uma solução que contenha duas partes: um aplicativo cliente e um aplicativo de API Web personalizado. Se você inserir a appID do aplicativo cliente nesse valor, o usuário precisará consentir somente uma vez no aplicativo cliente. O Azure AD saberá que consentir com o cliente significa consentir implicitamente com a API web. Ele irá prover automaticamente os diretores de serviço para o cliente e a API web ao mesmo tempo. O cliente e o aplicativo de API Web precisam ser registrados no mesmo locatário.

Exemplo:

```json
    "knownClientApplications": ["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"],
```

### <a name="logourl-attribute"></a>logotipoAurl

| Chave | Tipo de valor |
| :--- | :--- |
| logourl | String |

Leia apenas o valor que aponta para a URL da CDN para o logotipo que foi carregado no portal.

Exemplo:

```json
    "logoUrl": "https://MyRegisteredAppLogo",
```

### <a name="logouturl-attribute"></a>logoutAurl

| Chave | Tipo de valor |
| :--- | :--- |
| logoutUrl | String |

A URL para fazer logoff do aplicativo.

Exemplo:

```json
    "logoutUrl": "https://MyRegisteredAppLogout",
```

### <a name="name-attribute"></a>atributo nome

| Chave | Tipo de valor |
| :--- | :--- |
| name | String |

O nome de exibição do aplicativo.

Exemplo:

```json
    "name": "MyRegisteredApp",
```

### <a name="oauth2allowimplicitflow-attribute"></a>oauth2AllowImplicitFlow atributo

| Chave | Tipo de valor |
| :--- | :--- |
| oauth2AllowImplicitFlow | Boolean |

Especifica se este aplicativo Web pode solicitar tokens de acesso de fluxo implícitos OAuth 2.0. O padrão é false. Este sinalizador é usado para aplicativos baseados em navegador, como aplicativos de página única JavaScript. Para saber mais, digite `OAuth 2.0 implicit grant flow` no sumário e consulte os tópicos sobre fluxo implícito.

Exemplo:

```json
    "oauth2AllowImplicitFlow": false,
```

### <a name="oauth2allowidtokenimplicitflow-attribute"></a>oauth2AllowIdTokenatributoImplicitFlow

| Chave | Tipo de valor |
| :--- | :--- |
| oauth2AllowIdTokenImplicitFlow | Boolean |

Especifica se este aplicativo Web pode solicitar os tokens de ID de fluxo implícitos OAuth 2.0. O padrão é false. Este sinalizador é usado para aplicativos baseados em navegador, como aplicativos de página única JavaScript.

Exemplo:

```json
    "oauth2AllowIdTokenImplicitFlow": false,
```

### <a name="oauth2permissions-attribute"></a>oauth2AtributoPermissões

| Chave | Tipo de valor |
| :--- | :--- |
| oauth2Permissions | Coleção |

Especifica a coleção de escopos de permissão do OAuth 2.0 que o aplicativo de API Web (recurso) expõe aos aplicativos clientes. Esses escopos de permissões podem ser concedidos a aplicativos clientes durante o consentimento.

Exemplo:

```json
    "oauth2Permissions": [
       {
          "adminConsentDescription": "Allow the app to access resources on behalf of the signed-in user.",
          "adminConsentDisplayName": "Access resource1",
          "id": "<guid>",
          "isEnabled": true,
          "type": "User",
          "userConsentDescription": "Allow the app to access resource1 on your behalf.",
          "userConsentDisplayName": "Access resources",
          "value": "user_impersonation"
        }
    ],
```

### <a name="oauth2requiredpostresponse-attribute"></a>oauth2RequiredPostResponse atributo

| Chave | Tipo de valor |
| :--- | :--- |
| oauth2RequiredPostResponse | Boolean |

Especifica se, como parte das solicitações de token OAuth 2.0, o Azure AD permitirá solicitações POST, em vez de solicitações GET. O padrão é false, que especifica que somente as solicitações GET serão permitidas.

Exemplo:

```json
    "oauth2RequirePostResponse": false,
```

### <a name="parentalcontrolsettings-attribute"></a>parentalControlConfigurações atributo

| Chave | Tipo de valor |
| :--- | :--- |
| parentalControlSettings | String |

- `countriesBlockedForMinors` especifica os países em que o aplicativo está bloqueado para menores.
- `legalAgeGroupRule` especifica a regra de grupo de faixa etária que se aplica a usuários do aplicativo. Pode ser definido como `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids` ou `BlockMinors`.  

Exemplo:

```json
    "parentalControlSettings": {
        "countriesBlockedForMinors": [],
        "legalAgeGroupRule": "Allow"
    },
```

### <a name="passwordcredentials-attribute"></a>Atribuição de senhaS

| Chave | Tipo de valor |
| :--- | :--- |
| passwordCredentials | Coleção |

Confira a descrição da propriedade `keyCredentials`.

Exemplo:

```json
    "passwordCredentials": [
      {
        "customKeyIdentifier": null,
        "endDate": "2018-10-19T17:59:59.6521653Z",
        "keyId": "<guid>",
        "startDate":"2016-10-19T17:59:59.6521653Z",
        "value":null
      }
    ],
```

### <a name="preauthorizedapplications-attribute"></a>atributo pré-Aplicativos Autorizados

| Chave | Tipo de valor |
| :--- | :--- |
| pré-AutorizadosAplicativos | Coleção |

Lista os aplicativos e as permissões solicitadas para consentimento implícito. Exige que um administrador tenha fornecido o consentimento para o aplicativo. preAuthorizedApplications não exigem que o usuário consinta com as permissões solicitadas. As permissões listadas em preAuthorizedApplications não exigem o consentimento do usuário. No entanto, quaisquer permissões solicitadas adicionais não listadas no preAuthorizedApplications exigem o consentimento do usuário.

Exemplo:

```json
    "preAuthorizedApplications": [
       {
          "appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",
          "permissionIds": [
             "8748f7db-21fe-4c83-8ab5-53033933c8f1"
            ]
        }
    ],
```

### <a name="publicclient-attribute"></a>atributo publicClient

| Chave | Tipo de valor |
| :--- | :--- |
| publicClient | Boolean|

Especifica se esse aplicativo é um cliente público (por exemplo, um aplicativo instalado em execução em um dispositivo móvel). 

Esta propriedade está disponível apenas na experiência de registros do **App (Legacy).** Substituído pela `allowPublicClient` experiência de registro de [aplicativos.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="publisherdomain-attribute"></a>atributo de domínio do publisher

| Chave | Tipo de valor |
| :--- | :--- |
| editorDomínio | String |

O domínio do editor verificado para o aplicativo. Somente leitura.

Exemplo:

```json
    "publisherDomain": "https://www.contoso.com",
````

### <a name="replyurls-attribute"></a>atributo replyUrls

| Chave | Tipo de valor |
| :--- | :--- |
| replyUrls | Matriz de cadeia de caracteres |

Essa propriedade de vários valores contém a lista de valores redirect_uri registrados que o Azure AD aceitará como destinos quando retornar tokens.

Esta propriedade está disponível apenas na experiência de registros do **App (Legacy).** Substituído pela `replyUrlsWithType` experiência de registro de [aplicativos.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="replyurlswithtype-attribute"></a>answerurlsComType atributo

| Chave | Tipo de valor |
| :--- | :--- |
| respostasUrlsComType | Coleção |

Essa propriedade de vários valores contém a lista de valores redirect_uri registrados que o Azure AD aceitará como destinos quando retornar tokens. Cada valor URI deve conter um valor de tipo de aplicativo associado. Os valores de tipo suportados são:

- `Web`
- `InstalledClient`

Para saber mais, consulte [as restrições e limitações da Url de resposta](https://docs.microsoft.com/azure/active-directory/develop/reply-url).

Exemplo:

```json
    "replyUrlsWithType": [
       {
          "url": "https://localhost:4400/services/office365/redirectTarget.html",
          "type": "InstalledClient"
       }
    ],
```

### <a name="requiredresourceaccess-attribute"></a>atribuição requiredResourceAccess

| Chave | Tipo de valor |
| :--- | :--- |
| requiredResourceAccess | Coleção |

Com o consentimento dinâmico, o `requiredResourceAccess` gera a experiência de consentimento do administrador e a experiência de consentimento do usuário para usuários que estão usando o consentimento estático. No entanto, este parâmetro não impulsiona a experiência de consentimento do usuário para o caso geral.

- `resourceAppId` é o identificador exclusivo do recurso ao qual o aplicativo requer acesso. Esse valor deve ser igual à appId declarada no aplicativo do recurso de destino.
- `resourceAccess` é uma matriz que lista os escopos de permissão do OAuth2.0 e as funções de aplicativo que o aplicativo exige do recurso especificado. Contém os valores `id` e `type` dos recursos especificados.

Exemplo:

```json
    "requiredResourceAccess": [
        {
            "resourceAppId": "00000002-0000-0000-c000-000000000000",
            "resourceAccess": [
                {
                    "id": "311a71cc-e848-46a1-bdf8-97ff7156d8e6",
                    "type": "Scope"
                }
            ]
        }
    ],
```

### <a name="samlmetadataurl-attribute"></a>samlMetadataUrl atributo

| Chave | Tipo de valor |
| :--- | :--- |
| samlMetadataUrl | String |

A URL dos metadados SAML do aplicativo.

Exemplo:

```json
    "samlMetadataUrl": "https://MyRegisteredAppSAMLMetadata",
```

### <a name="signinurl-attribute"></a>atributo signInUrl

| Chave | Tipo de valor |
| :--- | :--- |
| signInUrl | String |

Especifica a URL da home page do aplicativo.

Exemplo:

```json
    "signInUrl": "https://MyRegisteredApp",
```

### <a name="signinaudience-attribute"></a>signInAudience atributo

| Chave | Tipo de valor |
| :--- | :--- |
| signInAudience | String |

Especifica quais contas da Microsoft são suportadas para o aplicativo atual. Os valores com suporte são:
- `AzureADMyOrg`- Usuários com uma conta de trabalho ou escola da Microsoft no inquilino Azure AD da minha organização (por exemplo, inquilino único)
- `AzureADMultipleOrgs`- Usuários com uma conta de trabalho ou escola da Microsoft no inquilino Azure AD de qualquer organização (por exemplo, multi-inquilino)
- `AzureADandPersonalMicrosoftAccount`- Usuários com uma conta microsoft pessoal, ou uma conta de trabalho ou escola em qualquer inquilino Azure AD de qualquer organização
- `PersonalMicrosoftAccount`- Contas pessoais que são usadas para fazer login em serviços como Xbox e Skype.

Exemplo:

```json
    "signInAudience": "AzureADandPersonalMicrosoftAccount",
```

### <a name="tags-attribute"></a>tags atributo

| Chave | Tipo de valor |
| :--- | :--- |
| marcas | Matriz de cadeia de caracteres  |

Cadeias de caracteres personalizadas que podem ser usadas para categorizar e identificar o aplicativo.

Exemplo:

```json
    "tags": [
       "ProductionApp"
    ],
```

## <a name="common-issues"></a>Problemas comuns

### <a name="manifest-limits"></a>Limites manifestos

Um manifesto de aplicação tem vários atributos que são chamados de coleções; por exemplo, appRoles, keyCredentials, knownClientApplications, identifierUris, redirectUris, requiredResourceAccess e oauth2Permissions. Dentro do manifesto de inscrição completo para qualquer aplicação, o número total de entradas em todas as coleções combinadas foi limitado a 1200. Se você especificar anteriormente 100 URIs de redirecionamento no manifesto do aplicativo, então você só fica com 1100 entradas restantes para usar em todas as outras coleções combinadas que compõem o manifesto.

> [!NOTE]
> No caso de você tentar adicionar mais de 1200 entradas no manifesto do aplicativo, você pode ver um erro **"Falha ao atualizar o aplicativo xxxxxx. Detalhes de erro: O tamanho do manifesto excedeu seu limite. Por favor, reduza o número de valores e tente novamente sua solicitação."**

### <a name="unsupported-attributes"></a>Atributos não suportados

O manifesto de aplicação representa o esquema do modelo de aplicação subjacente no Azure AD. À medida que o esquema subjacente evolui, o editor do manifesto será atualizado para refletir o novo esquema de tempos em tempos. Como resultado, você pode notar novos atributos aparecendo no manifesto de aplicação. Em raras ocasiões, você pode notar uma mudança sintática ou semântica nos atributos existentes ou você pode encontrar um atributo que existia anteriormente não são suportados mais. Por exemplo, você verá novos atributos nos registros do App, que são [conhecidos](https://go.microsoft.com/fwlink/?linkid=2083908)com um nome diferente na experiência de registros do App (Legacy).

| Registros de aplicativos (Legado)| Registros de aplicativo           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Para obter descrições desses atributos, consulte a seção [de referência manifesto.](#manifest-reference)

Quando você tenta carregar um manifesto baixado anteriormente, você pode ver um dos seguintes erros. Esse erro é provável porque o editor do manifesto agora suporta uma versão mais recente do esquema, que não corresponde à que você está tentando carregar.

* "Falha ao atualizar o aplicativo xxxxxx. Detalhe do erro: identificador de objeto inválido 'indefinido'. []."
* "Falha ao atualizar o aplicativo xxxxxx. Detalhe do erro: Um ou mais valores de propriedade especificados são inválidos. []."
* "Falha ao atualizar o aplicativo xxxxxx. Detalhe de erro: Não é permitido definir disponívelToOtherTenants nesta versão api para atualização. []."
* "Falha ao atualizar o aplicativo xxxxxx. Detalhe de erro: Atualizações para a propriedade 'replyUrls' não são permitidas para este aplicativo. Use a propriedade 'replyUrlsWithType'. []."
* "Falha ao atualizar o aplicativo xxxxxx. Detalhe de erro: Um valor sem nome de tipo foi encontrado e nenhum tipo esperado está disponível. Quando o modelo é especificado, cada valor na carga deve ter um tipo que pode ser especificado na carga útil, explicitamente pelo chamador ou implicitamente inferido a partir do valor pai. []"

Quando você vê um desses erros, recomendamos as seguintes ações:

1. Edite os atributos individualmente no editor manifesto em vez de carregar um manifesto baixado anteriormente. Use a tabela [de referência manifesto](#manifest-reference) para entender a sintaxe e semântica de atributos antigos e novos para que você possa editar com sucesso os atributos que você está interessado. 
1. Se o seu fluxo de trabalho exigir que você salve os manifestos em seu repositório de origem para uso posteriormente, sugerimos reabastecar os manifestos salvos em seu repositório com o que você vê na experiência de registros do **App.**

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a relação entre o aplicativo de um aplicativo e o objeto principal do serviço, consulte [os principais objetos de aplicação e serviço no Azure AD](app-objects-and-service-principals.md).
* Consulte o [glossário](developer-glossary.md) de desenvolvedor da plataforma de identidade da Microsoft para obter definições de alguns conceitos principais de desenvolvedores de plataformas de identidade da Microsoft.

Use a seção de comentários a seguir para dar sua opinião e nos ajudar a aprimorar e adaptar nosso conteúdo.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
