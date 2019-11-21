---
title: Microsoft identity platform and OAuth2.0 On-Behalf-Of flow | Azure
description: Este artigo descreve como usar mensagens HTTP para implementar a autenticação de serviço para serviço usando o fluxo em nome de do OAuth2.0.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
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
ms.openlocfilehash: 09d851572731ad9c83093b7076279df112585703
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207503"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Microsoft identity platform and OAuth 2.0 On-Behalf-Of flow

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

O fluxo On-Behalf-Of (OBO) do OAuth 2.0 serve para o caso em que um aplicativo chama um serviço/uma API Web que, por sua vez, precisa chamar outro serviço/outra API Web. A ideia é propagar as permissões e identidade de usuário delegado por meio da cadeia de solicitações. For the middle-tier service to make authenticated requests to the downstream service, it needs to secure an access token from the Microsoft identity platform, on behalf of the user.

This article describes how to program directly against the protocol in your application.  When possible, we recommend you use the supported Microsoft Authentication Libraries (MSAL) instead to [acquire tokens and call secured web APIs](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Also take a look at the [sample apps that use MSAL](sample-v2-code.md).

> [!NOTE]
>
> - The Microsoft identity platform endpoint doesn't support all scenarios and features. To determine whether you should use the Microsoft identity platform endpoint, read about [Microsoft identity platform limitations](active-directory-v2-limitations.md). Specifically, known client applications aren't supported for apps with Microsoft account (MSA) and Azure AD audiences. Portanto, um padrão comum de consentimento para o OBO não funcionará em clientes que entrem com contas pessoais e com contas corporativas ou de estudante. Para saber mais sobre como lidar com esta etapa do fluxo, confira o artigo sobre como [obter consentimento para o aplicativo de camada intermediária](#gaining-consent-for-the-middle-tier-application).
> - Desde maio de 2018, alguns `id_token` derivados de fluxo implícito não podem ser usados para o fluxo OBO. Os aplicativos de página única (SPAs) devem aprovar um token de **acesso** para um cliente confidencial de camada intermediária a fim de executar fluxos OBO. Para saber mais sobre quais clientes podem fazer chamadas OBO, confira as [limitações](#client-limitations).

## <a name="protocol-diagram"></a>Diagrama de protocolo

Suponha que o usuário tenha sido autenticado em um aplicativo usando o [fluxo de concessão de código de autorização OAuth 2.0](v2-oauth2-auth-code-flow.md). Nesse ponto, o aplicativo tem um token de acesso *para a API A* (token A) com as declarações do usuário e o consentimento para acessar a API da web de camada intermediária (API A). Agora, a API A precisa fazer uma solicitação autenticada para a API Web downstream (API B).

As etapas abaixo constituem o fluxo OBO e são explicadas com a ajuda do diagrama a seguir.

![Shows the OAuth2.0 On-Behalf-Of flow](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. O aplicativo cliente faz uma solicitação para API A com o token A (com uma declaração `aud` da API A).
1. API A authenticates to the Microsoft identity platform token issuance endpoint and requests a token to access API B.
1. The Microsoft identity platform token issuance endpoint validates API A's credentials with token A and issues the access token for API B (token B).
1. O token B é definido no cabeçalho de autorização da solicitação para a API B.
1. Os dados do recurso protegido são retornados pela API B.

> [!NOTE]
> Nesse cenário, o serviço de camada intermediária não tem nenhuma interação do usuário para obter o consentimento do usuário para acessar a API downstream. Portanto, a opção de conceder acesso à API downstream é apresentada antecipadamente como parte da etapa de consentimento durante a autenticação. Para saber como realizar essa configuração em seu aplicativo, confira o artigo sobre como [obter consentimento para o aplicativo de camada intermediária](#gaining-consent-for-the-middle-tier-application).

## <a name="service-to-service-access-token-request"></a>Solicitação de token de acesso de serviço para serviço

To request an access token, make an HTTP POST to the tenant-specific Microsoft identity platform token endpoint with the following parameters.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Há dois casos que dependem se o aplicativo cliente escolhe ser protegido por um segredo compartilhado ou por um certificado.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Na primeira ocorrência: solicitação de token de acesso com um segredo compartilhado

Ao usar um segredo compartilhado, uma solicitação de token de acesso de serviço para serviço contém estes parâmetros:

| . |  | Descrição |
| --- | --- | --- |
| `grant_type` | obrigatórios | O tipo da solicitação do token. Para uma solicitação usando um JWT, o valor deve ser `urn:ietf:params:oauth:grant-type:jwt-bearer`. |
| `client_id` | obrigatórios | The application (client) ID that [the Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page has assigned to your app. |
| `client_secret` | obrigatórios | The client secret that you generated for your app in the Azure portal - App registrations page. |
| `assertion` | obrigatórios | O valor do token usado na solicitação. |
| `scope` | obrigatórios | Lista de escopos separados por espaço para a solicitação de token. Para obter mais informações, consulte [escopos](v2-permissions-and-consent.md). |
| `requested_token_use` | obrigatórios | Especifica como a solicitação deve ser processada. No fluxo OBO, o valor precisa ser definido como `on_behalf_of`. |

#### <a name="example"></a>Exemplo

O seguinte HTTP POST solicita um token de acesso e um token de atualização com o escopo `user.read` para a API da Web https://graph.microsoft.com.

```
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE0OTM5MjA5MTYsIm5iZiI6MTQ5MzkyMDkxNiwiZXhwIjoxNDkzOTI0ODE2LCJhaW8iOiJBU1FBMi84REFBQUFnZm8vNk9CR0NaaFV2NjJ6MFFYSEZKR0VVYUIwRUlIV3NhcGducndMMnVrPSIsIm5hbWUiOiJOYXZ5YSBDYW51bWFsbGEiLCJvaWQiOiJkNWU5NzljNy0zZDJkLTQyYWYtOGYzMC03MjdkZDRjMmQzODMiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJuYWNhbnVtYUBtaWNyb3NvZnQuY29tIiwic3ViIjoiZ1Q5a1FMN2hXRUpUUGg1OWJlX1l5dVZNRDFOTEdiREJFWFRhbEQzU3FZYyIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjN5U3F4UHJweUVPd0ZsTWFFMU1PQUEiLCJ2ZXIiOiIyLjAifQ.TPPJSvpNCSCyUeIiKQoLMixN1-M-Y5U0QxtxVkpepjyoWNG0i49YFAJC6ADdCs5nJXr6f-ozIRuaiPzy29yRUOdSz_8KqG42luCyC1c951HyeDgqUJSz91Ku150D9kP5B9-2R-jgCerD_VVuxXUdkuPFEl3VEADC_1qkGBiIg0AyLLbz7DTMp5DvmbC09DhrQQiouHQGFSk2TPmksqHm3-b3RgeNM1rJmpLThis2ZWBEIPx662pjxL6NJDmV08cPVIcGX4KkFo54Z3rfwiYg4YssiUc4w-w3NJUBQhnzfTl4_Mtq2d7cVlul9uDzras091vFy32tWkrpa970UvdVfQ
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Segundo caso: solicitação de token de acesso com um certificado

Uma solicitação de token de acesso de serviço para serviço com certificado contém estes parâmetros:

| . |  | Descrição |
| --- | --- | --- |
| `grant_type` | obrigatórios | O tipo da solicitação de token. Para uma solicitação usando um JWT, o valor deve ser `urn:ietf:params:oauth:grant-type:jwt-bearer`. |
| `client_id` | obrigatórios |  The application (client) ID that [the Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page has assigned to your app. |
| `client_assertion_type` | obrigatórios | O valor deve ser `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| `client_assertion` | obrigatórios | Uma asserção (um token web JSON) que você precisa para criar e assinar com o certificado registrado como credenciais do seu aplicativo. Para saber mais sobre como registrar seu certificado e o formato da asserção, confira [credenciais de certificado](active-directory-certificate-credentials.md). |
| `assertion` | obrigatórios | O valor do token usado na solicitação. |
| `requested_token_use` | obrigatórios | Especifica como a solicitação deve ser processada. No fluxo OBO, o valor precisa ser definido como `on_behalf_of`. |
| `scope` | obrigatórios | Lista de escopos separados por espaço para a solicitação de token. Para obter mais informações, consulte [escopos](v2-permissions-and-consent.md).|

Observe que os parâmetros são praticamente os mesmos como no caso da solicitação pelo segredo compartilhado, exceto pelo fato de o parâmetro `client_secret` ser substituído por dois parâmetros: `client_assertion_type` e `client_assertion`.

#### <a name="example"></a>Exemplo

O seguinte HTTP POST solicita um token de acesso com o escopo `user.read` para a API da Web https://graph.microsoft.com com um certificado.

```
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="service-to-service-access-token-response"></a>Resposta de token de acesso de serviço para serviço

Uma resposta bem-sucedida é uma resposta JSON do OAuth 2.0 com os parâmetros a seguir.

| . | Descrição |
| --- | --- |
| `token_type` | Indica o valor do tipo de token. The only type that Microsoft identity platform supports is `Bearer`. For more info about bearer tokens, see the [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| `scope` | O escopo do acesso concedido no token. |
| `expires_in` | O tempo, em segundos, pelo qual o token de acesso é válido. |
| `access_token` | O token de acesso solicitado. O serviço de chamada pode usar esse token para se autenticar no serviço de recebimento. |
| `refresh_token` | O token de atualização para o token de acesso solicitado. O serviço de chamada poderá usar esse token para solicitar outro token de acesso depois que o token de acesso atual expirar. O token de atualização é fornecido apenas se o `offline_access` escopo foi solicitado. |

### <a name="success-response-example"></a>Exemplo de resposta de êxito

O exemplo a seguir mostra uma resposta bem-sucedida a uma solicitação para um token de acesso para a API Web https://graph.microsoft.com.

```
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4AnqPnvFqnzS3GiikHr5wBM1bV1YyjH3nUeIhKhqJWGwqJFRqs2sE_rqUfz7__3J92JDpi6gDdCZNNaXgreQsH89kLCVNYZeN6kGuFGZrjwxp1wS2JYc97E_3reXBxkHrA09K5aR-WsSKCEjf6WI23FhZMTLhk_ZKOe_nWvcvLj13FyvSrTMZV2cmzyCZDqEHtPVLJgSoASuQlD2NXrfmtcmgWfc3uJSrWLIDSn4FEmVDA63X6EikNp9cllH3Gp7Vzapjlnws1NQ1_Ff5QrmBHp_LKEIwfzVKnLLrQXN0EzP8f6AX6fdVTaeKzm7iw6nH0vkPRpUeLc3q_aNsPzqcTOnFfgng7t2CXUsMAGH5wclAyFCAwL_Cds7KnyDLL7kzOS5AVZ3Mqk2tsPlqopAiHijZaJumdTILDudwKYCFAMpUeUwEf9JmyFjl2eIWPmlbwU7cHKWNvuRCOYVqbsTTpJthwh4PvsL5ov5CawH_TaV8omG_tV6RkziHG9urk9yp2PH9gl7Cv9ATa3Vt3PJWUS8LszjRIAJmyw_EhgHBfYCvEZ8U9PYarvgqrtweLcnlO7BfnnXYEC18z_u5wemAzNBFUje2ttpGtRmRic4AzZ708tBHva2ePJWGX6pgQbiWF8esOrvWjfrrlfOvEn1h6YiBW291M022undMdXzum6t1Y1huwxHPHjCAA"
}
```

> [!NOTE]
> O token de acesso acima é um token formatado para v1.0. Isso ocorre porque o token é fornecido com base no recurso que está sendo acessado. The Microsoft Graph requests v1.0 tokens, so Microsoft identity platform produces v1.0 access tokens when a client requests tokens for Microsoft Graph. Somente os aplicativos devem examinar os tokens de acesso. Os clientes não devem precisar inspecioná-los.

### <a name="error-response-example"></a>Exemplo de resposta de erro

An error response is returned by the token endpoint when trying to acquire an access token for the downstream API, if the downstream API has a Conditional Access policy (such as multi-factor authentication) set on it. The middle-tier service should surface this error to the client application so that the client application can provide the user interaction to satisfy the Conditional Access policy.

```
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Usar o token de acesso para acessar o recurso protegido

Agora, o serviço de camada intermediária pode usar o token adquirido acima para fazer solicitações autenticadas para a API Web downstream, definindo o token no cabeçalho `Authorization`.

### <a name="example"></a>Exemplo

```
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkSzdNN0RyNXlvUUdLNmFEc19vdDF3cEQyZjNqRkxiNlVrcm9PcXA2cXBJclAxZVV0QktzMHEza29HN3RzXzJpSkYtQjY1UV8zVGgzSnktUHZsMjkxaFNBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMDE2LCJuYmYiOjE0OTM5MzAwMTYsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQUlzQjN5ZUljNkZ1aEhkd1YxckoxS1dlbzJPckZOUUQwN2FENTVjUVRtems9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJzUVlVekYxdUVVS0NQS0dRTVFVRkFBIiwidmVyIjoiMS4wIn0.Hrn__RGi-HMAzYRyCqX3kBGb6OS7z7y49XPVPpwK_7rJ6nik9E4s6PNY4XkIamJYn7tphpmsHdfM9lQ1gqeeFvFGhweIACsNBWhJ9Nx4dvQnGRkqZ17KnF_wf_QLcyOrOWpUxdSD_oPKcPS-Qr5AFkjw0t7GOKLY-Xw3QLJhzeKmYuuOkmMDJDAl0eNDbH0HiCh3g189a176BfyaR0MgK8wrXI_6MTnFSVfBePqklQeLhcr50YTBfWg3Svgl6MuK_g1hOuaO-XpjUxpdv5dZ0SvI47fAuVDdpCE48igCX5VMj4KUVytDIf6T78aIXMkYHGgW3-xAmuSyYH_Fr0yVAQ
```

## <a name="gaining-consent-for-the-middle-tier-application"></a>Obter consentimento para o aplicativo de camada intermediária

Depending on the audience for your application, you may consider different strategies for ensuring that the OBO flow is successful. Em todos os casos, o objetivo final é garantir que o consentimento adequado seja fornecido. Como isso ocorre, no entanto, dependerá dos usuários aos quais seu aplicativo dá suporte.

### <a name="consent-for-azure-ad-only-applications"></a>Consentimento para aplicativos somente do Azure AD

#### <a name="default-and-combined-consent"></a>/.default e consentimento combinado

Para aplicativos que só precisam entrar nas contas corporativas ou de estudante, a abordagem tradicional "Aplicativos clientes conhecidos" é suficiente. O aplicativo de camada intermediária adiciona o cliente à lista de aplicativos cliente conhecidos em seu manifesto e, em seguida, o cliente pode disparar um fluxo de consentimento combinado para ele mesmo e para o aplicativo de camada intermediária. On the Microsoft identity platform endpoint, this is done using the [`/.default` scope](v2-permissions-and-consent.md#the-default-scope). Ao disparar uma tela de consentimento usando aplicativos clientes conhecidos e `/.default`, a tela de consentimento mostrará as permissões para o cliente da API de camada intermediária e também solicitará todas as permissões necessárias para a API de camada intermediária. O usuário fornece o consentimento para ambos os aplicativos e, em seguida, o fluxo OBO funciona.

Atualmente, o sistema de conta pessoal da Microsoft não oferece suporte ao consentimento combinado e, portanto, essa abordagem não funciona em aplicativos que em que seja preciso entrar especificamente com contas pessoais. As contas pessoais da Microsoft que estão sendo usadas como contas de convidado em um locatário são manipuladas utilizando o sistema do Azure AD e podem passar pelo consentimento combinado.

#### <a name="pre-authorized-applications"></a>Aplicativos pré-autorizados

A feature of the application portal is "pre-authorized applications". Dessa forma, um recurso pode indicar que um determinado aplicativo sempre terá permissão para receber determinados escopos. Isso é útil principalmente para estabelecer conexões entre um cliente de front-end e um recurso de back-end de forma mais contínua. Um recurso pode declarar vários aplicativos pré-autorizados: qualquer aplicativo desse tipo pode solicitar essas permissões em um fluxo OBO e recebê-las sem que o usuário forneça o consentimento.

#### <a name="admin-consent"></a>Consentimento do administrador

Um administrador de locatários pode garantir que os aplicativos tenham permissão para chamar as APIs necessárias, fornecendo consentimento do administrador para o aplicativo de camada intermediária. Para fazer isso, o administrador pode localizar o aplicativo de camada intermediária em seu locatário, abrir a página de permissões necessárias e optar por dar permissão ao aplicativo. Para saber mais sobre o consentimento do administrador, confira a [documentação de permissões e consentimento](v2-permissions-and-consent.md).

### <a name="consent-for-azure-ad--microsoft-account-applications"></a>Consentimento para o Azure AD + aplicativos da conta da Microsoft

Because of restrictions in the permissions model for personal accounts and the lack of a governing tenant, the consent requirements for personal accounts are a bit different from Azure AD. Não há nenhum locatário para fornecer o consentimento amplo de locatários, e também não existe a possibilidade de fazer o consentimento combinado. Dessa forma, há outras estratégias presentes: tenha em mente que elas só funcionam para aplicativos que precisam dar suporte a contas do Azure AD também.

#### <a name="use-of-a-single-application"></a>Uso de um único aplicativo

Em alguns cenários, você pode ter apenas um emparelhamento único de cliente de camada intermediária e front-end. Nesse caso, talvez seja mais fácil fazer isso em um único aplicativo, eliminando a necessidade de um aplicativo de camada intermediária completamente. Para fazer a autenticação entre o front-end e a API web, você pode usar cookies, um id_token ou um token de acesso solicitado para o aplicativo em si. Em seguida, solicite o consentimento desse aplicativo único para o recurso de back-end.

## <a name="client-limitations"></a>Limitações do cliente

If a client uses the implicit flow to get an id_token, and that client also has wildcards in a reply URL, the id_token can't be used for an OBO flow.  No entanto, os tokens de acesso adquiridos por meio de fluxo de concessão implícito ainda podem ser resgatados por um cliente confidencial mesmo se o cliente que inicia o processo tiver uma URL de resposta curinga registrada.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre o protocolo OAuth 2.0 e outra maneira de executar autenticação de serviço para serviço usando as credenciais do cliente.

* [OAuth 2.0 client credentials grant in Microsoft identity platform](v2-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0 code flow in Microsoft identity platform](v2-oauth2-auth-code-flow.md)
* [Como usar o escopo `/.default`](v2-permissions-and-consent.md#the-default-scope)
