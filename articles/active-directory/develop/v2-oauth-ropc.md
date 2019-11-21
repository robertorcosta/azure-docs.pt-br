---
title: Use Microsoft identity platform to sign in users using resource owner password credential (ROPC) grant | Azure
description: Suporta fluxos de autenticação sem navegador usando a concessão de credencial de senha do proprietário do recurso.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4504a1ae60aaac790ca15c120433159c2ff78fa
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207779"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-resource-owner-password-credentials"></a>Microsoft identity platform and the OAuth 2.0 Resource Owner Password Credentials

Microsoft identity platform supports the [OAuth 2.0 Resource Owner Password Credentials (ROPC) grant](https://tools.ietf.org/html/rfc6749#section-4.3), which allows an application to sign in the user by directly handling their password.  This article describes how to program directly against the protocol in your application.  When possible, we recommend you use the supported Microsoft Authentication Libraries (MSAL) instead to [acquire tokens and call secured web APIs](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Also take a look at the [sample apps that use MSAL](sample-v2-code.md).

> [!WARNING]
> Microsoft recommends you do _not_ use the ROPC flow. In most scenarios, more secure alternatives are available and recommended. This flow requires a very high degree of trust in the application, and carries risks which are not present in other flows. You should only use this flow when other more secure flows can't be used.

> [!IMPORTANT]
>
> * The Microsoft identity platform endpoint only supports ROPC for Azure AD tenants, not personal accounts. Isso significa que você deve usar um terminal específico do locatário (`https://login.microsoftonline.com/{TenantId_or_Name}`) ou o terminal `organizations`.
> * Contas pessoais são convidadas a um locatário Azure AD não é possível usar ROPC.
> * Contas que não têm senhas não podem entrar por meio de ROPC. Para este cenário, recomendamos que você use um fluxo diferente para seu aplicativo em vez disso.
> * Se os usuários precisam usar a autenticação multifator (MFA) para fazer logon no aplicativo, eles serão bloqueados em vez disso.
> * ROPC is not supported in [hybrid identity federation](/azure/active-directory/hybrid/whatis-fed) scenarios (for example, Azure AD and ADFS used to authenticate on-premises accounts). If users are full-page redirected to an on-premises identity providers, Azure AD is not able to test the username and password against that identity provider. [Pass-through authentication](/azure/active-directory/hybrid/how-to-connect-pta) is supported with ROPC, however.

## <a name="protocol-diagram"></a>Diagrama de protocolo

O diagrama a seguir mostra o fluxo do ROPC.

![Diagram showing the resource owner password credential flow](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Solicitação de autorização

The ROPC flow is a single request: it sends the client identification and user's credentials to the IDP, and then receives tokens in return. O cliente deve solicitar o endereço de e-mail (UPN) e a senha do usuário antes de fazer isso. Imediatamente após uma solicitação bem-sucedida, o cliente deve liberar com segurança as credenciais do usuário da memória. Ele nunca deve salvá-los.

> [!TIP]
> Tente executar a solicitação no Postman!
> [![Try running this request in Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```
// Line breaks and spaces are for legibility only.  This is a public client, so no secret is required. 

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| . | Condição | Descrição |
| --- | --- | --- |
| `tenant` | obrigatórios | O locatário do diretório no qual você deseja fazer o login. Pode estar no formato de nome amigável ou de GUID. Este parâmetro não pode ser definido como `common` ou `consumers`, mas pode ser definido como `organizations`. |
| `client_id` | obrigatórios | The Application (client) ID that the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page assigned to your app. | 
| `grant_type` | obrigatórios | Deve ser definido como `password`. |
| `username` | obrigatórios | Endereço de email do usuário. |
| `password` | obrigatórios | A senha do usuário. |
| `scope` | Recomendado | Uma lista separada por espaço de [escopos](v2-permissions-and-consent.md) ou permissões que o aplicativo exige. In an interactive flow, the admin or the user must consent to these scopes ahead of time. |
| `client_secret`| Sometimes required | If your app is a public client, then the `client_secret` or `client_assertion` cannot be included.  If the app is a confidential client, then it must be included. | 
| `client_assertion` | Sometimes required | A different form of `client_secret`, generated using a certificate.  See [certificate credentials](active-directory-certificate-credentials.md) for more details. | 

### <a name="successful-authentication-response"></a>Resposta de autenticação bem sucedida

The following example shows a successful token response:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| . | Formatar | Descrição |
| --------- | ------ | ----------- |
| `token_type` | string | Sempre defina como `Bearer`. |
| `scope` | Cadeia de caracteres separadas por espaço | Se um token de acesso foi retornado, esse parâmetro lista os escopos para os quais o token de acesso é válido. |
| `expires_in`| int | Número de segundos para o qual o token de acesso incluído é válido. |
| `access_token`| Cadeia de caracteres opaca | Emitido para os [escopos](v2-permissions-and-consent.md) que foram solicitados. |
| `id_token` | JWT | Emitido quando o parâmetro original `scope` inclui o escopo `openid`. |
| `refresh_token` | Cadeia de caracteres opaca | Emitido quando o parâmetro original `scope` inclui `offline_access`. |

Você pode usar o token de atualização para adquirir novos tokens de acesso e atualizar os tokens usando o mesmo fluxo descrito na documentação do [fluxo de código do OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Resposta de erro

Se o usuário não tiver fornecido o nome de usuário ou a senha corretos, ou se o cliente não tiver recebido o consentimento solicitado, a autenticação falhará.

| Erro | Descrição | Ação do cliente |
|------ | ----------- | -------------|
| `invalid_grant` | A autenticação falhou | As credenciais estavam incorretas ou o cliente não tem consentimento para os escopos solicitados. If the scopes aren't granted, a `consent_required` error will be returned. Se isso ocorrer, o cliente deve enviar o usuário para um prompt interativo usando uma visualização da Web ou um navegador. |
| `invalid_request` | A solicitação foi mal construída | The grant type isn't supported on the `/common` or `/consumers` authentication contexts.  Use `/organizations` or a tenant ID instead. |

## <a name="learn-more"></a>Saiba mais

* Experimente ROPC por conta própria usando o [aplicativo de console de exemplo](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* To determine whether you should use the v2.0 endpoint, read about [Microsoft identity platform limitations](active-directory-v2-limitations.md).
