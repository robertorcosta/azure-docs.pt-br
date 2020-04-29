---
title: Entrar com concessão de credenciais de senha do proprietário do recurso | Azure
titleSuffix: Microsoft identity platform
description: Suporte a fluxos de autenticação sem navegador usando a concessão de credencial de senha do proprietário do recurso (ROPC).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: a282264ed3e9539bcc96babfc41376d2c6c35628
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81868644"
---
# <a name="microsoft-identity-platform-and-oauth-20-resource-owner-password-credentials"></a>Plataforma de identidade da Microsoft e credenciais de senha do proprietário do recurso OAuth 2,0

A plataforma de identidade da Microsoft dá suporte à [concessão de credenciais de senha (ROPC) do proprietário do recurso OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-4.3), que permite que um aplicativo entre no usuário manipulando sua senha diretamente.  Este artigo descreve como programar diretamente em relação ao protocolo em seu aplicativo.  Quando possível, recomendamos que você use as MSAL (bibliotecas de autenticação da Microsoft) com suporte em vez de [adquirir tokens e chamar APIs da Web protegidas](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Veja também os [aplicativos de exemplo que usam MSAL](sample-v2-code.md).

> [!WARNING]
> A Microsoft recomenda que você _não_ use o fluxo ROPC. Na maioria dos cenários, as alternativas mais seguras estão disponíveis e recomendadas. Esse fluxo requer um grau muito alto de confiança no aplicativo e traz riscos que não estão presentes em outros fluxos. Você só deve usar esse fluxo quando outros fluxos mais seguros não puderem ser usados.

> [!IMPORTANT]
>
> * O ponto de extremidade da plataforma Microsoft Identity suporta apenas ROPC para locatários do Azure AD, não contas pessoais. Isso significa que você deve usar um terminal específico do locatário (`https://login.microsoftonline.com/{TenantId_or_Name}`) ou o terminal `organizations`.
> * Contas pessoais são convidadas a um locatário Azure AD não é possível usar ROPC.
> * Contas que não têm senhas não podem entrar por meio de ROPC. Para este cenário, recomendamos que você use um fluxo diferente para seu aplicativo em vez disso.
> * Se os usuários precisam usar a autenticação multifator (MFA) para fazer logon no aplicativo, eles serão bloqueados em vez disso.
> * Não há suporte para ROPC em cenários de [Federação de identidade híbrida](/azure/active-directory/hybrid/whatis-fed) (por exemplo, AD do Azure e ADFS usados para autenticar contas locais). Se os usuários forem redirecionados de página inteira para provedores de identidade locais, o Azure AD não poderá testar o nome de usuário e a senha nesse provedor de identidade. No entanto, há suporte para a [autenticação de passagem](/azure/active-directory/hybrid/how-to-connect-pta) com ROPC.

## <a name="protocol-diagram"></a>Diagrama do protocolo

O diagrama a seguir mostra o fluxo de ROPC.

![Diagrama mostrando o fluxo de credenciais de senha do proprietário do recurso](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Solicitação de autorização

O fluxo ROPC é uma única solicitação: ele envia a identificação do cliente e as credenciais do usuário para o IDP e, em seguida, recebe tokens em retorno. O cliente deve solicitar o endereço de email (UPN) do usuário e a senha antes de fazer isso. Imediatamente após uma solicitação bem-sucedida, o cliente deve liberar com segurança as credenciais do usuário da memória. Ele nunca deve salvá-los.

> [!TIP]
> Tente executar a solicitação no Postman!
> [![Tente executar esta solicitação no postmaster](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```HTTP
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

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| `tenant` | Obrigatório | O locatário do diretório no qual você deseja fazer o login. Pode estar no formato de nome amigável ou de GUID. Este parâmetro não pode ser definido como `common` ou `consumers`, mas pode ser definido como `organizations`. |
| `client_id` | Obrigatório | A ID do aplicativo (cliente) que a página de [portal do Azure registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) atribuída ao seu aplicativo. |
| `grant_type` | Obrigatório | Deve ser definido como `password`. |
| `username` | Obrigatório | O endereço de email do usuário. |
| `password` | Obrigatório | A senha do usuário. |
| `scope` | Recomendadas | Uma lista separada por espaço de [escopos](v2-permissions-and-consent.md) ou permissões que o aplicativo exige. Em um fluxo interativo, o administrador ou o usuário deve consentir esses escopos antes do tempo. |
| `client_secret`| Às vezes é necessário | Se seu aplicativo for um cliente público, o ou `client_secret` `client_assertion` o não poderá ser incluído.  Se o aplicativo for um cliente confidencial, ele deverá ser incluído. |
| `client_assertion` | Às vezes é necessário | Uma forma diferente de `client_secret`, gerada usando um certificado.  Consulte [credenciais de certificado](active-directory-certificate-credentials.md) para obter mais detalhes. |

### <a name="successful-authentication-response"></a>Resposta de autenticação bem-sucedida

O exemplo abaixo mostra uma resposta de token bem-sucedida:

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

| Parâmetro | Formatar | Descrição |
| --------- | ------ | ----------- |
| `token_type` | Cadeia de caracteres | Sempre defina como `Bearer`. |
| `scope` | Cadeia de caracteres separadas por espaço | Se um token de acesso for retornado, esse parâmetro listará os escopos para os quais o token de acesso é válido. |
| `expires_in`| INT | Número de segundos pelos quais o token de acesso incluído é válido. |
| `access_token`| Cadeia de caracteres opaca | Emitido para os [escopos](v2-permissions-and-consent.md) que foram solicitados. |
| `id_token` | JWT | Emitido quando o parâmetro original `scope` inclui o escopo `openid`. |
| `refresh_token` | Cadeia de caracteres opaca | Emitido quando o parâmetro original `scope` inclui `offline_access`. |

Você pode usar o token de atualização para adquirir novos tokens de acesso e atualizar os tokens usando o mesmo fluxo descrito na documentação do [fluxo de código do OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Resposta de erro

Se o usuário não tiver fornecido o nome de usuário ou a senha corretos, ou se o cliente não tiver recebido o consentimento solicitado, a autenticação falhará.

| Erro | Descrição | Ação do cliente |
|------ | ----------- | -------------|
| `invalid_grant` | A autenticação falhou | As credenciais estavam incorretas ou o cliente não tem consentimento para os escopos solicitados. Se os escopos não forem concedidos, um `consent_required` erro será retornado. Se isso ocorrer, o cliente deve enviar o usuário para um prompt interativo usando uma visualização da Web ou um navegador. |
| `invalid_request` | A solicitação foi mal construída | Não há suporte para o tipo Grant `/common` nos `/consumers` contextos de autenticação ou.  Use `/organizations` ou uma ID de locatário em vez disso. |

## <a name="learn-more"></a>Saiba mais

* Experimente ROPC por conta própria usando o [aplicativo de console de exemplo](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Para determinar se você deve usar o ponto de extremidade v 2.0, leia sobre as [limitações da plataforma de identidade da Microsoft](active-directory-v2-limitations.md).
