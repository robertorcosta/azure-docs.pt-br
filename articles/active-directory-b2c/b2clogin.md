---
title: Migrar aplicativos e APIs para b2clogin.com
titleSuffix: Azure AD B2C
description: Saiba como usar b2clogin.com nas URLs de redirecionamento do Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 64b440054795670b99a22e37dec7188f3e1cd74c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189983"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Definir URLs de redirecionamento para b2clogin.com do Azure Active Directory B2C

Quando você configura um provedor de identidade para inscrição e login no aplicativo Azure Active Directory B2C (Azure AD B2C), você precisa especificar uma URL de redirecionamento. Você não deve mais referenciar *login.microsoftonline.com* em seus aplicativos e APIs. Em vez disso, use *b2clogin.com* para todos os novos aplicativos e migre os aplicativos existentes de *login.microsoftonline.com* para *b2clogin.com*.

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>Depreciação do login.microsoftonline.com

Em 04 de dezembro de 2019, anunciamos a aposentadoria programada de login.microsoftonline.com apoio no Azure AD B2C em **04 de dezembro de 2020**:

[Azure Active Directory B2C está depreciando login.microsoftonline.com](https://azure.microsoft.com/updates/b2c-deprecate-msol/)

A depreciação do login.microsoftonline.com entra em vigor para todos os inquilinos Azure AD B2C em 04 de dezembro de 2020, fornecendo aos inquilinos existentes um (1) ano para migrar para b2clogin.com. Novos inquilinos criados após 04 de dezembro de 2019 não aceitarão pedidos de login.microsoftonline.com. Todas as funcionalidades permanecem as mesmas no ponto final b2clogin.com.

A depreciação do login.microsoftonline.com não afeta os inquilinos do Azure Active Directory. Apenas os inquilinos B2C do Azure Active Directory são afetados por essa mudança.

## <a name="benefits-of-b2clogincom"></a>Benefícios da b2clogin.com

Quando você usa *b2clogin.com* como sua URL de redirecionamento:

* O espaço consumido no cabeçalho de cookie por serviços da Microsoft é reduzido.
* Seus URLs de redirecionamento não precisam mais incluir uma referência à Microsoft.
* O código do lado do cliente JavaScript é suportado (atualmente em [visualização)](user-flow-javascript-overview.md)em páginas personalizadas. Devido às restrições de segurança, os elementos de código JavaScript e formulário HTML são removidos de páginas personalizadas se você usar *login.microsoftonline.com*.

## <a name="overview-of-required-changes"></a>Visão geral das alterações necessárias

Existem várias modificações que você pode precisar fazer para migrar seus aplicativos para *b2clogin.com*:

* Altere a URL de redirecionamento nos aplicativos do seu provedor de identidade para fazer referência *b2clogin.com*.
* Atualize seus aplicativos Azure AD B2C para usar *b2clogin.com* em suas referências de fluxo de usuário e ponto final de token.
* Atualize todas **as Origens Permitidas** que você definiu nas configurações do CORS para [personalização da interface do usuário](custom-policy-ui-customization.md).

## <a name="change-identity-provider-redirect-urls"></a>Provedor de identidade de alteração redirecione URLs

No site de cada provedor de identidade no qual você criou um aplicativo, `your-tenant-name.b2clogin.com` altere todas as URLs confiáveis para redirecionar em vez de *login.microsoftonline.com*.

Existem dois formatos que você pode usar para o seu b2clogin.com redirecionar URLs. O primeiro oferece o benefício de não ter "Microsoft" aparecendo em qualquer lugar da URL usando o ID do inquilino (um GUID) no lugar do nome de domínio do seu inquilino:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

A segunda opção usa o nome `your-tenant-name.onmicrosoft.com`de domínio do inquilino na forma de . Por exemplo: 

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

Para ambos os formatos:

* Substitua `{your-tenant-name}` pelo nome de seu locatário do Azure AD B2C.
* Remova `/te` se ele existir na URL.

## <a name="update-your-applications-and-apis"></a>Atualize seus aplicativos e APIs

O código em seus aplicativos habilitados para Azure AD B2C e APIs pode se referir `login.microsoftonline.com` em vários lugares. Por exemplo, seu código pode ter referências a fluxos de usuário e pontos finais de token. Atualize o seguinte `your-tenant-name.b2clogin.com`para, em vez disso, referenciar:

* Ponto de extremidade de autorização
* Ponto de extremidade de token
* Emissor de token

Por exemplo, o ponto final da política de inscrição/login de Contoso seria agora:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

Para obter informações sobre a migração de aplicativos web baseados em OWIN para b2clogin.com, consulte [Migrar uma API web baseada em OWIN para b2clogin.com](multiple-token-endpoints.md).

Para migrar as APIs de gerenciamento de API do Azure protegidas pelo Azure AD B2C, consulte a seção [Migrar para b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) da API De Gerenciamento de [API do Azure com Azure AD B2C](secure-api-management.md).

## <a name="microsoft-authentication-library-msal"></a>MSAL (Biblioteca de Autenticação da Microsoft)

### <a name="validateauthority-property"></a>Validar propriedade Authority

Se você estiver usando [MSAL.NET][msal-dotnet] v2 ou anterior, `false` defina a propriedade **ValidateAuthority** para a instanciação do cliente para permitir redirecionamentos para *b2clogin.com*. Esta configuração não é necessária para MSAL.NET v3 e acima.

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

Se você estiver usando [MSAL para JavaScript:][msal-js]

```JavaScript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre a migração de aplicativos web baseados em OWIN para b2clogin.com, consulte [Migrar uma API web baseada em OWIN para b2clogin.com](multiple-token-endpoints.md).

Para migrar as APIs de gerenciamento de API do Azure protegidas pelo Azure AD B2C, consulte a seção [Migrar para b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) da API De Gerenciamento de [API do Azure com Azure AD B2C](secure-api-management.md).

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
