---
title: Credenciais de certificado da plataforma de identidade da Microsoft
titleSuffix: Microsoft identity platform
description: Este artigo discute o registro e o uso de credenciais de certificado para autenticação de aplicativo.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 47a35f70251622674205a28af9b7cc64132d0530
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690280"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Credenciais do certificado de autenticação de aplicativo da plataforma de identidade da Microsoft

A plataforma de identidade da Microsoft permite que um aplicativo use suas próprias credenciais para autenticação, por exemplo, nas [credenciais de cliente do OAuth 2,0 conceder flowv 2.0](v2-oauth2-client-creds-grant-flow.md) e o [fluxo em nome de](v2-oauth2-on-behalf-of-flow.md)).

Uma forma de credencial que um aplicativo pode usar para autenticação é uma declaração JSON Web Token (JWT) assinada com um certificado que o aplicativo possui.

## <a name="assertion-format"></a>Formato de asserção
Plataforma de identidade da Microsoft para computar a asserção, você pode usar uma das muitas bibliotecas de [token Web JSON](https://jwt.ms/) na linguagem de sua escolha. As informações transportadas pelo token são as seguintes:

### <a name="header"></a>Cabeçalho

| Parâmetro |  Comentário |
| --- | --- |
| `alg` | Deve ser **RS256** |
| `typ` | Deve ser **JWT** |
| `x5t` | Deve ser a impressão digital do certificado de x. 509 SHA-1 |

### <a name="claims-payload"></a>Declarações (carga)

| Parâmetro |  Comentários |
| --- | --- |
| `aud` | Público-alvo: deve ser ** https://login.microsoftonline.com/ *tenant_Id*/oauth2/token** |
| `exp` | Data de expiração: a data de expiração do token. A hora é representada como o número de segundos de 1º de janeiro de 1970 (1970-01-01T0:0:0Z) UTC até a hora em que a validade do token expira.|
| `iss` | Emissor: deve ser a client_id (ID do aplicativo de serviço do cliente) |
| `jti` | GUID: a ID de JWT |
| `nbf` | Não Antes de: a data anterior à qual o token não pode ser usado. A hora é representada como o número de segundos de 1º de janeiro de 1970 (1970-01-01T0:0:0Z) UTC até a hora em que o token foi emitido. |
| `sub` | Assunto: para `iss`, deve ser a client_id (ID do aplicativo de serviço do cliente) |

### <a name="signature"></a>Assinatura

A assinatura é calculada aplicando o certificado conforme descrito na [especificação Token Web JSON RFC7519](https://tools.ietf.org/html/rfc7519)

## <a name="example-of-a-decoded-jwt-assertion"></a>Exemplo de uma declaração JWT decodificada

```JSON
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="example-of-an-encoded-jwt-assertion"></a>Exemplo de uma declaração JWT codificada

A cadeia de caracteres a seguir é um exemplo de asserção codificado. Se você olhar com cuidado, você notará três seções separadas por pontos (.):
* A primeira seção codifica o cabeçalho
* A segunda seção codifica a carga útil
* A última seção é a assinatura computada com os certificados do conteúdo das duas primeiras seções

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Registrar seu certificado com a plataforma de identidade da Microsoft

Você pode associar a credencial do certificado ao aplicativo cliente na plataforma de identidade da Microsoft por meio do portal do Azure usando qualquer um dos seguintes métodos:

### <a name="uploading-the-certificate-file"></a>Fazendo upload do arquivo de certificado

No registro do aplicativo do Azure para o aplicativo cliente:
1. Selecione **Certificados e segredos**.
2. Clique em **carregar certificado** e selecione o arquivo de certificado a ser carregado.
3. Clique em **Adicionar**.
  Depois que o certificado for carregado, os valores de impressão digital, data de início e expiração serão exibidos.

### <a name="updating-the-application-manifest"></a>Atualizando o manifesto do aplicativo

Com a suspensão de um certificado, você precisa calcular:

- `$base64Thumbprint`, que é o base64 codificação de hash de certificado
- `$base64Value`, que é o base64 codificação dos dados brutos do certificado

Você também deve fornecer um GUID para identificar a chave no manifesto do aplicativo (`$keyId`).

No registro do aplicativo do Azure para o aplicativo cliente:
1. Selecione **manifesto** para abrir o manifesto do aplicativo.
2. Substitua a propriedade *keyCredentials* pelas novas informações de certificado usando o esquema a seguir.

   ```JSON
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. Salve as edições no manifesto do aplicativo e, em seguida, carregue o manifesto para a plataforma de identidade da Microsoft.

   A propriedade `keyCredentials` tem vários valores, portanto, você pode fazer upload de vários certificados para um gerenciamento de chaves mais sofisticado.

## <a name="code-sample"></a>Exemplo de código

> [!NOTE]
> Você deve calcular o cabeçalho X5T convertendo-o em uma cadeia de caracteres base 64 usando o hash do certificado. O código para executar isso em C# é `System.Convert.ToBase64String(cert.GetCertHash());`.

O exemplo de código [aplicativo de console do daemon do .NET Core usando a plataforma de identidade da Microsoft](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) mostra como um aplicativo usa suas próprias credenciais para autenticação. Também mostra como você pode [criar um certificado autoassinado](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) usando o comando `New-SelfSignedCertificate` Powershell. Você também pode aproveitar e usar o [scripts de criação do aplicativo](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) para criar os certificados, a impressão digital de computação e assim por diante.
