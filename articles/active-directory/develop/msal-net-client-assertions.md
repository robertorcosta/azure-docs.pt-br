---
title: Afirmações do cliente (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre o suporte a afirmações de clientes assinados para aplicativos clientes confidenciais na Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/18/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8c97387bfd2a362d3bf5a6b8a3252242f061da31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050282"
---
# <a name="confidential-client-assertions"></a>Afirmações confidenciais do cliente

Para provar sua identidade, aplicativos confidenciais de clientes trocam um segredo com o Azure AD. O segredo pode ser:
- Um segredo do cliente (senha do aplicativo).
- Um certificado, que é usado para construir uma afirmação assinada contendo reivindicações padrão.

Esse segredo também pode ser uma afirmação assinada diretamente.

MSAL.NET tem quatro métodos para fornecer credenciais ou afirmações ao aplicativo cliente confidencial:
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> Embora seja possível usar `WithClientAssertion()` a API para adquirir tokens para o cliente confidencial, não recomendamos usá-lo por padrão, pois é mais avançado e foi projetado para lidar com cenários muito específicos que não são comuns. O `.WithCertificate()` uso da API permitirá que MSAL.NET lide com isso para você. Esta api oferece a você a capacidade de personalizar sua solicitação `.WithCertificate()` de autenticação, se necessário, mas a afirmação padrão criada por será suficiente para a maioria dos cenários de autenticação. Esta API também pode ser usada como solução em alguns cenários em que MSAL.NET não executa a operação de assinatura internamente.

### <a name="signed-assertions"></a>Afirmações assinadas

Uma afirmação de cliente assinado toma a forma de um JWT assinado com a carga contendo as reivindicações de autenticação exigidas pelo Azure AD, Base64 codificado. Para usá-lo:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

As reivindicações esperadas pelo Azure AD são:

Tipo de declaração | Valor | Descrição
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | A reivindicação "aud" (audiência) identifica os destinatários para os quais o JWT se destina (aqui Azure AD) Ver [RFC 7519, Seção 4.1.3]
exp | Qui Jun 27 2019 15:04:17 GMT+0200 (Horário de Verão de Romance) | A declaração "exp" (hora de expiração) identifica a hora de expiração ou a hora após ela na qual o JWT NÃO DEVE ser aceito para processamento. Consulte [RFC 7519, Seção 4.1.4]
iss | {ClientID} | A alegação "iss" (emissor) identifica o principal que emitiu o JWT. O processamento desta reclamação é específico do aplicativo. O valor "iss" é uma seqüência sensível a maiúsculas e minúsculas que contém um valor StringOrURI. [RFC 7519, Seção 4.1.1]
jti | (a Guid) | A reivindicação "jti" (JWT ID) fornece um identificador exclusivo para o JWT. O valor do identificador DEVE ser atribuído de forma a garantir que haja uma probabilidade insignificante de que o mesmo valor seja acidentalmente atribuído a um objeto de dados diferente; se o aplicativo utilizar vários emissores, as colisões devem ser evitadas entre os valores produzidos por diferentes emissores também. A alegação "jti" pode ser usada para evitar que o JWT seja repetido. O valor "jti" é uma seqüência sensível ao caso. [RFC 7519, Seção 4.1.7]
nbf | Qui Jun 27 2019 14:54:17 GMT+0200 (Horário de Verão do Romance) | A declaração "nbf" (não antes) identifica a hora antes da qual o JWT NÃO DEVE ser aceito para processamento. [RFC 7519, Seção 4.1.5]
sub | {ClientID} | A alegação "sub" (assunto) identifica o sujeito do JWT. As alegações em um JWT são normalmente declarações sobre o assunto. O valor do assunto deve ser escopo para ser localmente único no contexto do emissor ou ser globalmente único. O See [RFC 7519, Seção 4.1.2]

Aqui está um exemplo de como criar essas reivindicações:

```csharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = $"https://login.microsoftonline.com/{tenantId}/v2.0";

      string ConfidentialClientID = "00000000-0000-0000-0000-000000000000" //client id
      const uint JwtToAadLifetimeInSeconds = 60 * 10; // Ten minutes
      DateTime validFrom = DateTime.UtcNow;
      var nbf = ConvertToTimeT(validFrom);
      var exp = ConvertToTimeT(validFrom + TimeSpan.FromSeconds(JwtToAadLifetimeInSeconds));

      return new Dictionary<string, string>()
           {
                { "aud", aud },
                { "exp", exp.ToString() },
                { "iss", ConfidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "nbf", nbf.ToString() },
                { "sub", ConfidentialClientID }
            };
}
```

Veja como criar uma afirmação de cliente assinado:

```csharp
string Encode(byte[] arg)
{
    char Base64PadCharacter = '=';
    char Base64Character62 = '+';
    char Base64Character63 = '/';
    char Base64UrlCharacter62 = '-';
    char Base64UrlCharacter63 = '_';

    string s = Convert.ToBase64String(arg);
    s = s.Split(Base64PadCharacter)[0]; // RemoveAccount any trailing padding
    s = s.Replace(Base64Character62, Base64UrlCharacter62); // 62nd char of encoding
    s = s.Replace(Base64Character63, Base64UrlCharacter63); // 63rd char of encoding

    return s;
}

string GetSignedClientAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
     X509Certificate2 certificate = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

    //Create RSACryptoServiceProvider
    var x509Key = new X509AsymmetricSecurityKey(certificate);
    var privateKeyXmlParams = certificate.PrivateKey.ToXmlString(true);
    var rsa = new RSACryptoServiceProvider();
    rsa.FromXmlString(privateKeyXmlParams);

    //alg represents the desired signing algorithm, which is SHA-256 in this case
    //kid represents the certificate thumbprint
    var header = new Dictionary<string, string>()
         {
              { "alg", "RS256"},
              { "kid", Encode(Certificate.GetCertHash()) }
         };

    //Please see the previous code snippet on how to craft claims for the GetClaims() method
    string token = Encode(Encoding.UTF8.GetBytes(JObject.FromObject(header).ToString())) + "." + Encode(Encoding.UTF8.GetBytes(JObject.FromObject(GetClaims())));

    string signature = Encode(rsa.SignData(Encoding.UTF8.GetBytes(token), new SHA256Cng()));
    string signedClientAssertion = string.Concat(token, ".", signature);
    return signedClientAssertion;
}
```

### <a name="alternative-method"></a>Método alternativo

Você também tem a opção de usar o [Microsoft.IdentityModel.JsonWebTokens](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/) para criar a afirmação para você. O código será mais elegante, como mostrado no exemplo abaixo:

```csharp
        string GetSignedClientAssertion()
        {
            var cert = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

            //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
            string aud = $"https://login.microsoftonline.com/{tenantID}/v2.0";

            // client_id
            string confidentialClientID = "00000000-0000-0000-0000-000000000000";

            // no need to add exp, nbf as JsonWebTokenHandler will add them by default.
            var claims = new Dictionary<string, object>()
            {
                { "aud", aud },
                { "iss", confidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "sub", confidentialClientID }
            };

            var securityTokenDescriptor = new SecurityTokenDescriptor
            {
                Claims = claims,
                SigningCredentials = new X509SigningCredentials(cert)
            };

            var handler = new JsonWebTokenHandler();
            var signedClientAssertion = handler.CreateToken(securityTokenDescriptor);
        }
```

Uma vez que você tenha a sua afirmação de cliente assinado, você pode usá-lo com o apis MSAL como mostrado abaixo.

```csharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>Comclientes de clientes

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)`por padrão produzirá uma afirmação assinada contendo as reivindicações esperadas pelo Azure AD mais reclamações adicionais de clientes que você deseja enviar. Aqui está um trecho de código sobre como fazer isso.

```csharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

Se uma das alegações no dicionário que você passar é a mesma de uma das reivindicações obrigatórias, o valor da reivindicação adicional será levado em conta. Ele vai anular as reivindicações computadas por MSAL.NET.

Se você quiser fornecer suas próprias reivindicações, incluindo as reivindicações `false` obrigatórias `mergeWithDefaultClaims` esperadas pelo Azure AD, passe para o parâmetro.
