---
title: Fluxo de código do dispositivo OAuth 2.0 | Azure
titleSuffix: Microsoft identity platform
description: Faça login usuários sem navegador. Construir fluxos de autenticação incorporados e sem navegador usando a concessão de autorização do dispositivo.
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
ms.openlocfilehash: b45ba0c0b417be9cf308fedbb7fad2f6ad5fceaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159724"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Plataforma de identidade da Microsoft e o fluxo de concessão de autorização de dispositivo OAuth 2.0

A plataforma de identidade da Microsoft suporta a concessão de autorização do [dispositivo,](https://tools.ietf.org/html/rfc8628)que permite que os usuários entrem em dispositivos com restrição de entrada, como uma smart TV, dispositivo IoT ou impressora.  Para habilitar esse fluxo, o dispositivo exige que o usuário visite uma página da Web no navegador em outro dispositivo para entrar.  Depois que o usuário entra, o dispositivo é capaz de acessar e atualizar tokens, conforme o necessário.  

Este artigo descreve como programar diretamente contra o protocolo em sua aplicação.  Quando possível, recomendamos que você use as Bibliotecas de Autenticação Microsoft (MSAL) suportadas em vez de [adquirir tokens e chamar APIs da Web protegidas](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Também dê uma olhada nos [aplicativos de exemplo que usam msal](sample-v2-code.md).

> [!NOTE]
> O ponto final da plataforma de identidade da Microsoft não suporta todos os cenários e recursos do Azure Active Directory. Para determinar se você deve usar o ponto final da plataforma de identidade da Microsoft, leia sobre [as limitações da plataforma de identidade da Microsoft](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagrama de protocolo

O fluxo de código do dispositivo inteiro é semelhante ao diagrama a seguir. Descrevemos cada uma das etapas neste artigo.

![Fluxo de código do dispositivo](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Solicitação de autorização de dispositivo

O cliente deve primeiro verificar com o servidor de autenticação um dispositivo e código de usuário que é usado para iniciar a autenticação. O cliente coleta essa solicitação do ponto de extremidade `/devicecode`. Nessa solicitação, o cliente também deve incluir as permissões que precisa adquirir do usuário. A partir do momento em que essa solicitação é enviada, o usuário tem apenas 15 minutos para entrar (o valor normal para `expires_in`), portanto, somente faça essa solicitação quando o usuário indicar que está pronto para entrar.

> [!TIP]
> Tente executar a solicitação no Postman!
> [![Tente executar este pedido no Carteiro](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| `tenant` | Obrigatório | Pode ser /comum, /consumers, ou /organizações.  Também pode ser o inquilino do diretório que você deseja solicitar permissão em GUID ou formato de nome amigável.  |
| `client_id` | Obrigatório | O **ID do aplicativo (cliente)** que o [portal Azure – Registros de aplicativos](https://go.microsoft.com/fwlink/?linkid=2083908) experimenta atribuído ao seu aplicativo. |
| `scope` | Recomendadas | Uma lista separada por espaços de [escopos](v2-permissions-and-consent.md) para os quais você deseja o consentimento do usuário.  |

### <a name="device-authorization-response"></a>Resposta de autorização de dispositivo

Uma resposta bem-sucedida será um objeto JSON contendo as informações necessárias para permitir que o usuário faça login.  

| Parâmetro | Formatar | Descrição |
| ---              | --- | --- |
|`device_code`     | String | Uma cadeia de caracteres longa usada para verificar a sessão entre o cliente e o servidor de autorização. O cliente usa esse parâmetro para solicitar o token de acesso do servidor de autorização. |
|`user_code`       | String | Uma pequena seqüência mostrada ao usuário que é usado para identificar a sessão em um dispositivo secundário.|
|`verification_uri`| URI | O URI que o usuário deve acessar com o `user_code` para entrar. |
|`expires_in`      | INT | O número de segundos antes que o `device_code` e o `user_code` expirem. |
|`interval`        | INT | O número de segundos que o cliente deve aguardar entre as solicitações de sondagem. |
| `message`        | String | Uma cadeia de caracteres legível com instruções para o usuário. Ela pode ser localizada incluindo um **parâmetro de consulta** na solicitação do formulário `?mkt=xx-XX`, preenchendo o código de cultura do idioma apropriado. |

> [!NOTE]
> O `verification_uri_complete` campo de resposta não está incluído ou suportado no momento.  Mencionamos isso porque se [standard](https://tools.ietf.org/html/rfc8628) você ler `verification_uri_complete` o padrão você vê que está listado como uma parte opcional do padrão de fluxo de código do dispositivo.

## <a name="authenticating-the-user"></a>Autenticação do usuário

Após receber `user_code` `verification_uri`o e , o cliente exibe-os para o usuário, instruindo-o a fazer login usando seu celular ou navegador pc.

Se o usuário autenticar com uma conta pessoal (em /common ou /consumers), ele será solicitado a fazer login novamente, a fim de transferir o estado de autenticação para o dispositivo.  Eles também serão solicitados a fornecer consentimento, para garantir que estejam cientes das permissões que estão sendo concedidas.  Isso não se aplica às contas de trabalho ou escola usadas para autenticar. 

Enquanto o usuário está se autenticando no `verification_uri`, o cliente deverá estar sondando o ponto de extremidade `/token` para o token solicitado usando o `device_code`.

``` 
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| Parâmetro | Obrigatório | Descrição|
| -------- | -------- | ---------- |
| `tenant`  | Obrigatório | O mesmo inquilino ou inquilino alias usado na solicitação inicial. | 
| `grant_type` | Obrigatório | Precisa ser `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Obrigatório | Precisa corresponder à `client_id` usada na solicitação inicial. |
| `device_code`| Obrigatório | O `device_code` retornado na solicitação de autorização de dispositivo.  |

### <a name="expected-errors"></a>Erros esperados

O fluxo de código do dispositivo é um protocolo de votação, então seu cliente deve esperar receber erros antes que o usuário termine de autenticar.  

| Erro | Descrição | Ação do Cliente |
| ------ | ----------- | -------------|
| `authorization_pending` | O usuário ainda não terminou a autenticação, mas não cancelou o fluxo. | Repita a solicitação depois de pelo menos `interval` segundos. |
| `authorization_declined` | O usuário final negou a solicitação de autorização.| Interrompa a sondagem e reverta para um estado não autenticado.  |
| `bad_verification_code`| O `device_code` enviado `/token` para o ponto final não foi reconhecido. | Verifique se o cliente está enviando o `device_code` correto na solicitação. |
| `expired_token` | Pelo menos `expires_in` segundos foram decorridos e a autenticação não é mais possível com este `device_code`. | Pare de votar e volte a um estado não autenticado. |   

### <a name="successful-authentication-response"></a>Resposta de autenticação bem sucedida

Uma resposta de token bem-sucedida se parecerá com esta:

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
| `token_type` | String| Sempre "Portador. |
| `scope` | Cadeia de caracteres separadas por espaço | Se um token de acesso for retornado, isso listará os escopos em que o token de acesso é válido. |
| `expires_in`| INT | Número de segundos antes que o token de acesso incluído seja válido. |
| `access_token`| Cadeia de caracteres opaca | Emitido para os [escopos](v2-permissions-and-consent.md) que foram solicitados.  |
| `id_token`   | JWT | Emitido quando o parâmetro original `scope` inclui o escopo `openid`.  |
| `refresh_token` | Cadeia de caracteres opaca | Emitido quando o parâmetro original `scope` inclui `offline_access`.  |

Você pode usar o token de atualização para adquirir novos tokens de acesso e atualizar tokens usando o mesmo fluxo documentado na documentação de [fluxo do Código OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  
