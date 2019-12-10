---
title: Migrar usuários com identidades sociais
titleSuffix: Azure AD B2C
description: Análise dos principais conceitos relacionados à migração de usuários com identidades sociais em Azure AD B2C usando API do Graph.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2018
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: c34535454f80b424fc0500363313a799efbdc001
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950113"
---
# <a name="azure-active-directory-b2c-migrate-users-with-social-identities"></a>Microsoft Azure Active Directory B2C: migrar usuários com identidades sociais
Quando você planeja migrar seu provedor de identidade para o Microsoft Azure AD B2C, talvez seja necessário migrar usuários com identidades sociais. Este artigo explica como migrar contas de identidades sociais existentes, como: Facebook, LinkedIn, Microsoft e Contas do Google para o Microsoft Azure AD B2C. Este artigo também aplica-se a identidades federadas, no entanto, essas migrações são menos comuns. Para o restante deste artigo, considere qualquer coisa que se aplique a contas sociais também se aplique a outros tipos de contas federadas.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo é uma continuação do artigo de migração de usuário e concentra-se na migração de identidade social. Antes de começar, leia [migração de usuário](active-directory-b2c-user-migration.md).

## <a name="social-identities-migration-introduction"></a>Introdução de migração de identidades sociais

* No Microsoft Azure Active Directory B2C, **contas locais**, os nomes de entrada (nome de usuário ou endereço de email) são armazenados na coleção `signInNames` no registro do usuário. O `signInNames` contém um ou mais `signInName` registros que especificam os nomes de entrada para o usuário. Cada nome de entrada deve ser exclusivo no locatário.

* As identidades das **Contas sociais** são armazenadas na coleção`userIdentities`. A entrada especifica o `issuer` (nome do provedor de identidade) como facebook.com e `issuerUserId`, que é um identificador de usuário exclusivo para o emissor. O atributo `userIdentities` contém um ou mais registros UserIdentity que especificam o tipo de conta social e o identificador de usuário exclusivo do provedor de identidade social.

* **Combine conta local com identidade social**. Como mencionado, os nomes de entrada da conta local e as identidades da conta social são armazenados em atributos diferentes. `signInNames` é usado para a conta local, enquanto `userIdentities` é usado para contas sociais. Uma única conta de Azure AD B2C pode ser apenas uma conta local, somente conta social ou combinar uma conta local com uma ou mais identidades sociais em um registro de usuário. Esse comportamento permite gerenciar uma única conta, enquanto um usuário pode entrar com as credenciais da conta local ou com as identidades sociais.

* `UserIdentity` Tipo - Contém informações sobre a identidade de um usuário da conta social em um locatário do Microsoft Azure Active Directory B2C:
  * `issuer` A representação da cadeia de caracteres do provedor de identidade que emitiu o identificador de usuário, como facebook.com.
  * `issuerUserId` o identificador de usuário exclusivo usado pelo provedor de identidade social no formato codificado em base64.

    ```JSON
    "userIdentities": [{
          "issuer": "Facebook.com",
          "issuerUserId": "MTIzNDU2Nzg5MA=="
      }
    ]
    ```

* Dependendo do provedor de identidade, a **ID de usuário do emissor** é um valor exclusivo para um determinado usuário por aplicativo ou conta de desenvolvimento. Configure a política de Azure AD B2C com a mesma ID de aplicativo que foi atribuída anteriormente pelo provedor social ou outro aplicativo dentro da mesma conta de desenvolvimento.

## <a name="use-graph-api-to-migrate-users"></a>Utilizar API do Graph para migrar usuários
Você cria a conta de usuário Azure AD B2C por meio do [API do Graph](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet).
Para se comunicar com a API do Graph, primeiro, você precisa ter uma conta de serviço com privilégios administrativos. No Azure AD, você registra um aplicativo e a autenticação para o Azure AD. As credenciais do aplicativo são ID do Aplicativo e Segredo do Aplicativo. O aplicativo atua em nome próprio e não como usuário, para chamar a API do Graph. Siga as instruções na etapa 1 no artigo [migração de usuário](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-user-migration) .

## <a name="required-properties"></a>Propriedades obrigatórias
A lista a seguir mostra as propriedades que são obrigatórias ao criar um usuário.
* **accountEnabled** - verdadeiro
* **displayName** - O nome a ser exibido no catálogo de endereços do usuário.
* **passwordProfile** - O perfil de senha do usuário.

> [!NOTE]
> Para contas sociais somente (sem credenciais de conta local), você ainda deve especificar a senha. O Microsoft Azure Active Directory B2C ignora a senha que você especifica para contas sociais.

* **userPrincipalName** - O nome UPN (someuser@contoso.com). O nome UPN deve conter um dos domínios verificados para o locatário. Para especificar o UPN, gere o novo valor de GUID, concatenar com `@` e o nome do seu locatário.
* **mailNickname** - O alias de email do usuário. Esse valor pode ser a mesmo ID utilizada para o userPrincipalName.
* **signInNames** - Um ou mais registros SignInName que especificam os nomes de entrada do usuário. Cada nome de entrada deve ser exclusivo na empresa/locatário. Apenas para conta social, essa propriedade pode ser deixada vazia.
* **userIdentities** - Um ou mais registros UserIdentity que especificam o tipo de conta social e o identificador de usuário exclusivo do provedor de identidade social.
* [opcional] **otherMails** - Apenas para conta social, os endereços de email do usuário

Para obter mais informações, consulte: [referência de API do Graph API](/previous-versions/azure/ad/graph/api/users-operations#CreateLocalAccountUser)

## <a name="migrate-social-account-only"></a>Migrar conta social (somente)
Para criar apenas uma conta social, sem credenciais de conta local, envie uma solicitação HTTPS POST para API do Graph. O corpo da solicitação contém as propriedades do usuário da conta social para criar. No mínimo, é necessário especificar as propriedades necessárias.


**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Envie os dados de formulário a seguir:

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8",
    "signInNames": [],
    "creationType": null,
    "displayName": "Sara Bell",
    "givenName": "Sara",
    "surname": "Bell",
    "passwordProfile": {
        "password": "Test1234",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": null,
    "userIdentities": [{
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ],
    "otherMails": ["sara@live.com"],
    "userPrincipalName": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8@tenant-name.onmicrosoft.com"
}
```
## <a name="migrate-social-account-with-local-account"></a>Migrar conta social com conta local
Para criar uma conta local combinada com identidades sociais, envie uma solicitação HTTPS POST para a API do Graph. O corpo da solicitação contém as propriedades do usuário da conta social para criar, incluindo o nome de entrada da conta local. No mínimo, é necessário especificar as propriedades necessárias.

**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Envie os dados de formulário a seguir:

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "5164db16-3eee-4629-bfda-dcc3326790e9",
    "signInNames": [{
            "type": "emailAddress",
            "value": "david@contoso.com"
        }
    ],
    "creationType": "LocalAccount",
    "displayName": "David Hor",
    "givenName": "David",
    "surname": "Hor",
    "passwordProfile": {
        "password": "1234567",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": "DisablePasswordExpiration,DisableStrongPassword",
    "userIdentities": [{
            "issuer": "contoso.com",
            "issuerUserId": "ZGF2aWRAY29udG9zby5jb20="
        }
    ],
    "otherMails": [],
    "userPrincipalName": "5164db16-3eee-4629-bfda-dcc3326790e9@tenant-name.onmicrosoft.com"
}
```

## <a name="frequently-asked-questions"></a>Perguntas frequentes
### <a name="how-can-i-know-the-issuer-name"></a>Como é possível saber o nome do emissor?
O nome do emissor ou o nome do provedor de identidade é configurado na política. Se você não souber o valor a ser especificado em `issuer`, siga este procedimento:
1. Entre com uma das contas sociais
2. Do token JWT, copie o valor `sub`. O `sub` geralmente contém a ID de objeto do usuário no Microsoft Azure Active Directory B2C. Ou pelo Portal do Azure, abra as propriedades do usuário e copie a ID de objeto.
3. Abrir o [Explorador do Graph do Microsoft Azure AD](https://graphexplorer.azurewebsites.net)
4. Entre com seu administrador.
5. Execute a seguinte solicitação GET. Substitua o userObjectId pela ID de usuário que você copiou. **GET** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId
6. Localize o elemento `userIdentities` dentro do retorno JSON do Microsoft Azure Active Directory B2C.
7. [Opcional] Também é possível decodificar o valor `issuerUserId`.

> [!NOTE]
> Utilize uma conta de administrador de locatários B2C que seja local para o locatário B2C. A sintaxe do nome da conta é admin@tenant-name.onmicrosoft.com.

### <a name="is-it-possible-to-add-a-social-identity-to-an-existing-user"></a>É possível adicionar uma identidade social a um usuário existente?
Sim. Você pode adicionar a identidade social depois que a conta de Azure AD B2C tiver sido criada (independentemente de ser uma conta local ou social ou uma combinação delas). Execute uma solicitação de PATCH HTTPS. Substitua o userObjectId pela ID de usuário que você deseja atualizar.

**PATCH** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Envie os dados de formulário a seguir:

```JSON
{
    "userIdentities": [
        {
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

### <a name="is-it-possible-to-add-multiple-social-identities"></a>É possível adicionar várias identidades sociais?
Sim. Você pode adicionar várias identidades sociais a uma única conta do Microsoft Azure Active Directory B2C. Execute a solicitação HTTPS PATCH. Substitua o userObjectId pela ID de usuário.

**PATCH** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Envie os dados de formulário a seguir:

```JSON
{
    "userIdentities": [
        {
            "issuer": "google.com",
            "issuerUserId": "MjQzMjE2NTc4NTQ="
        },
        {
            "issuer": "facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

## <a name="optional-user-migration-application-sample"></a>[Opcional] Amostra de aplicativo de migração de usuário
[Baixar e executar o aplicativo de exemplo V2](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). O aplicativo de exemplo V2 usa um arquivo JSON que contém dados de usuário fictícios, incluindo: conta local, conta social e identidades locais e sociais em uma única conta.  Para editar o arquivo JSON, abra a solução do Visual Studio `AADB2C.UserMigration.sln`. No projeto `AADB2C.UserMigration`, abra o arquivo `UsersData.json`. O arquivo contém uma lista de entidades do usuário. Cada entidade de usuário tem as seguintes propriedades:
* **signInName** - Para conta local, endereço de email para entrar
* **displayName** - Nome de exibição do usuário
* **firstName** - Nome do usuário
* **lastName** - Sobrenome do usuário
* **password** Para conta local, a senha do usuário (pode estar vazia)
* **issuer** - Para conta social, o nome do provedor de identidade
* **issuerUserId** - Para conta social, o identificador de usuário exclusivo usado pelo provedor de identidade social. O valor deve estar com texto não criptografado. O aplicativo de exemplo codifica esse valor para Base64.
* **email** Apenas para conta social (não combinada), o endereço de email do usuário

```JSON
{
  "userType": "emailAddress",
  "Users": [
    {
      // Local account only
      "signInName": "James@contoso.com",
      "displayName": "James Martin",
      "firstName": "James",
      "lastName": "Martin",
      "password": "Pass!w0rd"
    },
    {
      // Social account only
      "issuer": "Facebook.com",
      "issuerUserId": "1234567890",
      "email": "sara@contoso.com",
      "displayName": "Sara Bell",
      "firstName": "Sara",
      "lastName": "Bell"
    },
    {
      // Combine local account with social identity
      "signInName": "david@contoso.com",
      "issuer": "Facebook.com",
      "issuerUserId": "0987654321",
      "displayName": "David Hor",
      "firstName": "David",
      "lastName": "Hor",
      "password": "Pass!w0rd"
    }
  ]
}
```

> [!NOTE]
> Se você não atualizar o arquivo UsersData.json no exemplo com seus dados, poderá entrar com as credenciais da conta local de exemplo, mas não com os exemplos de conta social. Para migrar suas contas sociais, forneça dados reais.

Para obter mais informações sobre como utilizar o aplicativo de exemplo, consulte [Azure Active Directory B2C: migração de usuário](active-directory-b2c-user-migration.md)
