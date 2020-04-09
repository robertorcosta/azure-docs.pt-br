---
title: Referência de token de id da plataforma de identidade da Microsoft
description: Saiba como usar id_tokens emitidos pelos pontos finais da plataforma de identidade Azure AD v1.0 e microsoft (v2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms:custom: fasttrack-edit
ms.openlocfilehash: 3ede70719c124caebc541df8578ab2720fc68cd5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885507"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Tokens de ID da plataforma de identidade da Microsoft

`id_tokens` são enviados para o aplicativo cliente como parte de um fluxo do [OpenID Connect](v2-protocols-oidc.md). Eles podem ser enviados com um token de acesso ou em vez de um, e são usados pelo cliente para autenticar o usuário.

## <a name="using-the-id_token"></a>Usando o id_token

Os Tokens de ID devem ser usados para validar que um usuário é quem eles afirmam ser e obter informações úteis adicionais sobre eles - ele não deve ser usado para autorização no lugar de um [token](access-tokens.md)de acesso . As reivindicações que ele fornece podem ser usadas para UX dentro do seu aplicativo, como chaves em um banco de dados, e fornecendo acesso ao aplicativo do cliente.  Ao criar chaves para `idp` um banco de dados, não deve ser usado porque atrapalha os cenários dos hóspedes.  O keying deve `sub` ser feito sozinho (o que é sempre único), com `tid` o uso para roteamento, se necessário.  Se você precisar compartilhar dados `oid` + `sub` + `tid` entre os serviços, `oid`funcionará, já que vários serviços todos recebem o mesmo .

## <a name="claims-in-an-id_token"></a>Declarações em um id_token

`id_tokens` para uma identidade da Microsoft são [JWTs](https://tools.ietf.org/html/rfc7519), o que significa que são compostos pelas partes de cabeçalho, de conteúdo e de assinatura. Você pode usar o cabeçalho e a assinatura para verificar a autenticidade do token, enquanto o conteúdo tem informações sobre o usuário solicitado pelo cliente. Exceto quando observado, todas as declarações listadas aqui aparecem nos tokens de v1.0 e v2.0.

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Exiba esse token de exemplo de v1.0 em [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Exiba esse token de exemplo de v2.0 em [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw).

### <a name="header-claims"></a>Declarações de cabeçalho

|Declaração | Formatar | Descrição |
|-----|--------|-------------|
|`typ` | Cadeia de caracteres – sempre "JWT" | Indica que o token é um JWT.|
|`alg` | String | Indica o algoritmo que foi usado para assinar o token. Exemplo: "RS256" |
|`kid` | String | Impressão digital da chave pública usada para assinar esse token. Emitido em `id_tokens` v1.0 e v2.0. |
|`x5t` | String | O mesmo (em uso e valor) que `kid`. No entanto, essa é uma declaração herdada emitida somente em `id_tokens` v1.0 para fins de compatibilidade. |

### <a name="payload-claims"></a>Declarações de conteúdo

Esta lista mostra as reivindicações que estão na maioria id_tokens por padrão (exceto quando anotado).  No entanto, seu aplicativo pode usar [reivindicações opcionais](active-directory-optional-claims.md) para solicitar reclamações adicionais no id_token.  Estes podem `groups` variar desde a reivindicação até informações sobre o nome do usuário.

|Declaração | Formatar | Descrição |
|-----|--------|-------------|
|`aud` |  Cadeia de caracteres, um URI da ID do Aplicativo | Identifica o destinatário pretendido do token. Em `id_tokens`, o público-alvo é a ID de Aplicativo de seu aplicativo, atribuída a ele no portal do Azure. O aplicativo deve validar esse valor e rejeitar o token, caso o valor não seja correspondente. |
|`iss` |  Cadeia de caracteres, um URI STS | Identifica o STS (Serviço de Token de Segurança) que constrói e retorna o token e o locatário do Azure AD no qual o usuário foi autenticado. Se o token tiver sido emitido pelo ponto de extremidade v2.0, o URI terminará com `/v2.0`.  O GUID que indica que o usuário é um consumidor da conta da Microsoft é `9188040d-6c67-4c5b-b112-36a304b66dad`. O aplicativo deve usar a parte do GUID da declaração para restringir o conjunto de locatários que podem entrar no aplicativo, se aplicável. |
|`iat` |  int, um carimbo de data/hora UNIX | "Emitido em" indica quando ocorreu a autenticação desse token.  |
|`idp`|Cadeia de caracteres, normalmente um URI de STS | Registra o provedor de identidade que autenticou a entidade do token. Esse valor é idêntico ao valor da declaração Emissor, a menos que a conta de usuário não esteja no mesmo locatário que o emissor – convidados, por exemplo. Se a reivindicação não estiver presente, significa `iss` que o valor pode ser usado em vez disso.  Para as contas pessoais usadas em um contexto organizacional (por exemplo, uma conta pessoal convidada para um locatário do Azure AD), a declaração `idp`pode ser 'live.com' ou um URI de STS que contém o locatário da conta Microsoft`9188040d-6c67-4c5b-b112-36a304b66dad`. |
|`nbf` |  int, um carimbo de data/hora UNIX | A declaração "nbf" (não antes) identifica a hora antes da qual o JWT NÃO DEVE ser aceito para processamento.|
|`exp` |  int, um carimbo de data/hora UNIX | A declaração "exp" (hora de expiração) identifica a hora de expiração ou a hora após ela na qual o JWT NÃO DEVE ser aceito para processamento.  É importante notar que um recurso pode rejeitar o token antes desse tempo também - se, por exemplo, uma alteração na autenticação for necessária ou uma revogação de token for detectada. |
| `c_hash`| String |O hash de código é incluído em tokens de ID apenas quando eles são emitidos com um código de autorização OAuth 2.0. Ele pode ser usado para validar a autenticidade de um código de autorização. Para obter detalhes sobre como realizar essa validação, confira a [Especificação OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). |
|`at_hash`| String |O hash do token de acesso é incluído em tokens de ID apenas quando eles são emitidos com um token de acesso OAuth 2.0. Ele pode ser usado para validar a autenticidade de um token de acesso. Para obter detalhes sobre como realizar essa validação, confira a [Especificação OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). |
|`aio` | Cadeia de caracteres opaca | Uma declaração interna usada pelo Azure AD para registrar os dados para reutilização de token. Deve ser ignorado.|
|`preferred_username` | String | O nome de usuário principal que representa o usuário. Ele pode ser um endereço de email, número de telefone ou nome de usuário genérico sem um formato especificado. Seu valor é mutável e pode ser alterado ao longo do tempo. Uma vez que é mutável, esse valor não deve ser usado para tomar decisões de autorização. O `profile` escopo é necessário para receber esta reivindicação.|
|`email` | String | O `email` declaração está presente por padrão para contas de convidados que possuem um endereço de email.  O aplicativo pode solicitar a declaração de email para usuários gerenciados (aquelas do mesmo locatário do recurso) usando o `email` [declaração opcional](active-directory-optional-claims.md).  No ponto de extremidade v 2.0, seu aplicativo também pode solicitar o `email` escopo da OpenID Connect - você não precisa solicitar a declaração opcional e o escopo para obter a declaração.  A declaração de email só dá suporte a emails endereçável de informações de perfil do usuário. |
|`name` | String | A declaração `name` fornece um valor legível por humanos que identifica o assunto do token. O valor não é garantido para ser único, é mutável, e foi projetado para ser usado apenas para fins de exibição. O `profile` escopo é necessário para receber esta reivindicação. |
|`nonce`| String | O nonce corresponde ao parâmetro incluído na solicitação original /authorize para o IDP. Se esses itens não corresponderem, seu aplicativo deverá rejeitar o token. |
|`oid` | Cadeia de caracteres, um GUID | O identificador imutável de um objeto do sistema de identidade da Microsoft, nesse caso, uma conta de usuário. Essa ID identifica exclusivamente o usuário entre os aplicativos - dois aplicativos diferentes autenticando o mesmo usuário receberão o mesmo valor na declaração `oid`. O Microsoft Graph retornará essa ID como a propriedade `id` para uma determinada conta de usuário. Como `oid` o permite que vários aplicativos `profile` correlacionam os usuários, o escopo é necessário para receber essa reclamação. Observe que se um único usuário existir em vários inquilinos, o usuário conterá um ID de objeto diferente em cada inquilino - eles são considerados contas diferentes, mesmo que o usuário faça login em cada conta com as mesmas credenciais. A `oid` reivindicação é uma GUID e não pode ser reutilizada. |
|`roles`| Matriz de cadeias de caracteres | O conjunto de funções que foram atribuídas ao usuário que está fazendo login. |
|`rh` | Cadeia de caracteres opaca |Uma declaração interna usada pelo Azure para revalidar tokens. Deve ser ignorado. |
|`sub` | Cadeia de caracteres, um GUID | O item mais importante sobre o qual o token declara informações, como o usuário de um aplicativo. Esse valor é imutável e não pode ser reatribuído nem reutilizado. O assunto é um identificador de paridade e é exclusivo a uma ID de aplicativo específica. Se um único usuário entrar em dois aplicativos diferentes usando dois IDs de cliente diferentes, esses aplicativos receberão dois valores diferentes para a reclamação do assunto. Isso pode ou não ser desejado dependendo de sua arquitetura e requisitos de privacidade. |
|`tid` | Cadeia de caracteres, um GUID | Um GUID que representa o locatário do Azure AD do qual o usuário é proveniente. Para contas corporativas e de estudante, o GUID é a ID de locatário imutável da organização à qual o usuário pertence. Para contas pessoais, o valor é `9188040d-6c67-4c5b-b112-36a304b66dad`. O `profile` escopo é necessário para receber esta reivindicação. |
|`unique_name` | String | Fornece um valor legível que identifica a entidade do token. Esse valor é único em qualquer momento, mas como e-mails e outros identificadores podem ser reutilizados, esse valor pode reaparecer em outras contas e, portanto, deve ser usado apenas para fins de exibição. Emitido somente no `id_tokens` v1.0. |
|`uti` | Cadeia de caracteres opaca | Uma declaração interna usada pelo Azure para revalidar tokens. Deve ser ignorado. |
|`ver` | Cadeia de caracteres, 1.0 ou 2.0 | Indica a versão do id_token. |


> [!NOTE]
> Os id_token v1 e v2 têm diferenças na quantidade de informações que levarão, como visto nos exemplos acima. A versão especifica essencialmente o ponto final da plataforma Azure AD de onde foi emitido. [A implementação do Azure AD Oauth](https://docs.microsoft.com/azure/active-directory/develop/about-microsoft-identity-platform) evoluiu ao longo dos anos. Atualmente temos dois pontos finais oAuth diferentes para aplicativos AzureAD. Você pode usar qualquer um dos novos pontos finais que são categorizados como v2 ou o antigo que é dito ser v1. Os pontos finais de Oauth para ambos são diferentes. O ponto final v2 é o mais novo onde estamos tentando migrar todos os recursos do ponto final v1 e recomendar novos desenvolvedores para usar o ponto final v2. 
> - V1: Pontos finais do diretório ativo do Azure:`https://login.microsoftonline.com/common/oauth2/authorize`
> - V2: Pontos finais da plataforma de identidade da Microsoft:`https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

## <a name="validating-an-id_token"></a>Validação de um id_token

Validar um `id_token` é semelhante ao primeiro passo de validação de [um token](access-tokens.md#validating-tokens) de acesso - seu cliente deve validar que o emissor correto enviou de volta o token e que ele não foi adulterado. Como `id_tokens` sempre são um JWT, há muitas bibliotecas para validar esses tokens – recomendamos que você use uma delas em vez de fazer isso por conta própria.

Para validar o token manualmente, consulte os detalhes das etapas em [validar um token de acesso](access-tokens.md#validating-tokens). Depois de validar a assinatura no token, as seguintes declarações deverão ser validadas no id_token (elas também podem ser feitas pela sua biblioteca de validação de token):

* Carimbos de data/hora: os carimbos de data/hora `iat`, `nbf`, e `exp` devem estar todos antes ou depois da hora atual, conforme apropriado. 
* Público-alvo: a declaração `aud` deve corresponder à ID do aplicativo do seu aplicativo.
* Nonce: a declaração `nonce` no conteúdo deve corresponder ao parâmetro nonce passado para o ponto de extremidade /authorize durante a solicitação inicial.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [tokens de acesso](access-tokens.md)
* Personalize as reivindicações em seu id_token usando [reivindicações opcionais](active-directory-optional-claims.md).
