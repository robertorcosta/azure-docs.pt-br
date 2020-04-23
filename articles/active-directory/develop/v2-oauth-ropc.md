---
title: Faça login com a concessão de credenciais de senha do proprietário de recursos | Azure
titleSuffix: Microsoft identity platform
description: Suporte aos fluxos de autenticação sem navegador usando a concessão de credencial de senha do proprietário de recursos (ROPC).
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
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868644"
---
# <a name="microsoft-identity-platform-and-oauth-20-resource-owner-password-credentials"></a>Plataforma de identidade Microsoft e Credenciais de senha do proprietário de recursos OAuth 2.0

A plataforma de identidade da Microsoft suporta a [concessão ROPC (Resource Owner Password Credentials, credenciais de proprietário de recursos) oAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.3), que permite que um aplicativo faça login no usuário manuseando diretamente sua senha.  Este artigo descreve como programar diretamente contra o protocolo em sua aplicação.  Quando possível, recomendamos que você use as Bibliotecas de Autenticação Microsoft (MSAL) suportadas em vez de [adquirir tokens e chamar APIs da Web protegidas](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Também dê uma olhada nos [aplicativos de exemplo que usam msal](sample-v2-code.md).

> [!WARNING]
> A Microsoft recomenda que você _não use_ o fluxo ROPC. Na maioria dos cenários, alternativas mais seguras estão disponíveis e recomendadas. Esse fluxo requer um grau muito alto de confiança na aplicação, e traz riscos que não estão presentes em outros fluxos. Você só deve usar este fluxo quando outros fluxos mais seguros não podem ser usados.

> [!IMPORTANT]
>
> * O ponto final da plataforma de identidade da Microsoft só suporta ROPC para inquilinos Azure AD, não contas pessoais. Isso significa que você deve usar um terminal específico do locatário (`https://login.microsoftonline.com/{TenantId_or_Name}`) ou o terminal `organizations`.
> * Contas pessoais são convidadas a um locatário Azure AD não é possível usar ROPC.
> * Contas que não têm senhas não podem entrar por meio de ROPC. Para este cenário, recomendamos que você use um fluxo diferente para seu aplicativo em vez disso.
> * Se os usuários precisam usar a autenticação multifator (MFA) para fazer logon no aplicativo, eles serão bloqueados em vez disso.
> * O ROPC não é suportado em cenários [de federação de identidade híbrida](/azure/active-directory/hybrid/whatis-fed) (por exemplo, Azure AD e ADFS usados para autenticar contas no local). Se os usuários forem redirecionados de página inteira para um provedor de identidade local, o Azure AD não poderá testar o nome de usuário e a senha contra esse provedor de identidade. No entanto, [a autenticação de passagem](/azure/active-directory/hybrid/how-to-connect-pta) é suportada com ROPC.

## <a name="protocol-diagram"></a>Diagrama de protocolo

O diagrama a seguir mostra o fluxo do ROPC.

![Diagrama mostrando o fluxo de credencial de senha do proprietário do recurso](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Solicitação de autorização

O fluxo DE ROPC é uma única solicitação: ele envia a identificação do cliente e as credenciais do usuário para o IDP e, em seguida, recebe tokens em troca. O cliente deve solicitar o endereço de e-mail (UPN) e a senha do usuário antes de fazer isso. Imediatamente após uma solicitação bem-sucedida, o cliente deve liberar com segurança as credenciais do usuário da memória. Ele nunca deve salvá-los.

> [!TIP]
> Tente executar a solicitação no Postman!
> [![Tente executar este pedido no Carteiro](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


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
| `client_id` | Obrigatório | O ID do aplicativo (cliente) que o [portal Azure - Página de registros do aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) atribuído ao seu aplicativo. |
| `grant_type` | Obrigatório | Deve ser definido como `password`. |
| `username` | Obrigatório | Endereço de email do usuário. |
| `password` | Obrigatório | A senha do usuário. |
| `scope` | Recomendadas | Uma lista separada por espaço de [escopos](v2-permissions-and-consent.md) ou permissões que o aplicativo exige. Em um fluxo interativo, o admin ou o usuário devem consentir com esses escopos antes do tempo. |
| `client_secret`| Às vezes necessário | Se o seu aplicativo é `client_secret` um `client_assertion` cliente público, então o ou não pode ser incluído.  Se o aplicativo é um cliente confidencial, então ele deve ser incluído. |
| `client_assertion` | Às vezes necessário | Uma forma `client_secret`diferente de, gerada usando um certificado.  Consulte [as credenciais de certificado](active-directory-certificate-credentials.md) para obter mais detalhes. |

### <a name="successful-authentication-response"></a>Resposta de autenticação bem sucedida

O exemplo a seguir mostra uma resposta de token bem sucedida:

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
| `token_type` | String | Sempre defina como `Bearer`. |
| `scope` | Cadeia de caracteres separadas por espaço | Se um token de acesso foi retornado, esse parâmetro lista os escopos para os quais o token de acesso é válido. |
| `expires_in`| INT | Número de segundos para o qual o token de acesso incluído é válido. |
| `access_token`| Cadeia de caracteres opaca | Emitido para os [escopos](v2-permissions-and-consent.md) que foram solicitados. |
| `id_token` | JWT | Emitido quando o parâmetro original `scope` inclui o escopo `openid`. |
| `refresh_token` | Cadeia de caracteres opaca | Emitido quando o parâmetro original `scope` inclui `offline_access`. |

Você pode usar o token de atualização para adquirir novos tokens de acesso e atualizar os tokens usando o mesmo fluxo descrito na documentação do [fluxo de código do OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Resposta de erro

Se o usuário não tiver fornecido o nome de usuário ou a senha corretos, ou se o cliente não tiver recebido o consentimento solicitado, a autenticação falhará.

| Erro | Descrição | Ação do cliente |
|------ | ----------- | -------------|
| `invalid_grant` | A autenticação falhou | As credenciais estavam incorretas ou o cliente não tem consentimento para os escopos solicitados. Se os escopos não forem concedidos, um `consent_required` erro será devolvido. Se isso ocorrer, o cliente deve enviar o usuário para um prompt interativo usando uma visualização da Web ou um navegador. |
| `invalid_request` | A solicitação foi mal construída | O tipo de subvenção não `/common` `/consumers` é suportado nos contextos ou autenticação.  Use `/organizations` uma id de inquilino em vez disso. |

## <a name="learn-more"></a>Saiba mais

* Experimente ROPC por conta própria usando o [aplicativo de console de exemplo](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Para determinar se você deve usar o ponto final v2.0, leia sobre [as limitações](active-directory-v2-limitations.md)da plataforma de identidade microsoft .
