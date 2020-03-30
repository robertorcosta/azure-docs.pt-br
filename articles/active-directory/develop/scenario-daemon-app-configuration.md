---
title: Configure aplicativos daemon que chamam de APIs web - plataforma de identidade da Microsoft | Azure
description: Saiba como configurar o código para o seu aplicativo daemon que chama APIs web (configuração do aplicativo)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: fc441ef64f98ace04b7b847c03d575215656f9db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77611843"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Aplicativo Daemon que chama APIs da Web - configuração de código

Saiba como configurar o código para o seu aplicativo daemon que chama APIs da Web.

## <a name="msal-libraries-that-support-daemon-apps"></a>Bibliotecas MSAL que suportam aplicativos daemon

Essas bibliotecas da Microsoft suportam aplicativos daemon:

  Biblioteca MSAL | Descrição
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | As plataformas .NET Framework e .NET Core são suportadas para criar aplicativos daemon. (UWP, Xamarin.iOS e Xamarin.Android não são suportados porque essas plataformas são usadas para criar aplicativos públicos de clientes.)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Suporte para aplicações daemon em Python.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Suporte para aplicações daemon em Java.

## <a name="configure-the-authority"></a>Configure a autoridade

Os aplicativos Daemon usam permissões de aplicativos em vez de permissões delegadas. Assim, seu tipo de conta suportada não pode ser uma conta em qualquer diretório organizacional ou qualquer conta pessoal da Microsoft (por exemplo, Skype, Xbox, Outlook.com). Não há nenhum inquilino para conceder consentimento a um aplicativo daemon para uma conta pessoal da Microsoft. Você precisará escolher *contas na minha organização* ou *contas em qualquer organização.*

Assim, a autoridade especificada na configuração do aplicativo deve ser inquilina (especificando um ID de inquilino ou um nome de domínio associado à sua organização).

Se você é um ISV e deseja fornecer uma `organizations`ferramenta multi-inquilino, você pode usar . Mas tenha em mente que você também precisará explicar aos seus clientes como conceder o consentimento do admin. Para obter detalhes, [consulte Solicitar o consentimento para um inquilino inteiro](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant). Além disso, há atualmente uma limitação no MSAL: `organizations` é permitido apenas quando as credenciais do cliente são um segredo de aplicativo (não um certificado).

## <a name="configure-and-instantiate-the-application"></a>Configure e instanciar o aplicativo

Nas bibliotecas MSAL, as credenciais do cliente (segredo ou certificado) são passadas como parâmetro da construção de aplicativos confidenciais do cliente.

> [!IMPORTANT]
> Mesmo que seu aplicativo seja um aplicativo de console que seja executado como um serviço, se for um aplicativo daemon, ele precisa ser um aplicativo cliente confidencial.

### <a name="configuration-file"></a>Arquivo de configuração

O arquivo de configuração define:

- A autoridade ou a instância da nuvem e o iD do inquilino.
- A iD do cliente que você tem do registro do aplicativo.
- Ou um segredo de cliente ou um certificado.

# <a name="net"></a>[.NET](#tab/dotnet)

[appsettings.json](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/appsettings.json) da amostra [de daemon do console .NET Core.](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)

```JSon
{
  "Instance": "https://login.microsoftonline.com/{0}",
  "Tenant": "[Enter here the tenantID or domain name for your Azure AD tenant]",
  "ClientId": "[Enter here the ClientId for your application]",
  "ClientSecret": "[Enter here a client secret for your application]",
  "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]"
}
```

Você fornece `ClientSecret` um `CertificateName`ou um . Essas configurações são exclusivas.

# <a name="python"></a>[Python](#tab/python)

Quando você constrói um cliente confidencial com segredos do cliente, o arquivo de configuração [parameters.json](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/1-Call-MsGraph-WithSecret/parameters.json) na amostra [de daemon Python](https://github.com/Azure-Samples/ms-identity-python-daemon) é o seguinte:

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "secret": "The secret generated by AAD during your confidential app registration",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

Quando você constrói um cliente confidencial com certificados, o arquivo de configuração [parameters.json](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/parameters.json) na amostra [de daemon Python](https://github.com/Azure-Samples/ms-identity-python-daemon) é o seguinte:

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "thumbprint": "790E... The thumbprint generated by AAD when you upload your public cert",
  "private_key_file": "server.pem",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
 private final static String CLIENT_ID = "";
 private final static String AUTHORITY = "https://login.microsoftonline.com/<tenant>/";
 private final static String CLIENT_SECRET = "";
 private final static Set<String> SCOPE = Collections.singleton("https://graph.microsoft.com/.default");
```

---

### <a name="instantiate-the-msal-application"></a>Instanciar o aplicativo MSAL

Para instanciar o aplicativo MSAL, você precisa adicionar, referenciar ou importar o pacote MSAL (dependendo do idioma).

A construção é diferente, dependendo se você está usando segredos ou certificados do cliente (ou, como um cenário avançado, afirmações assinadas).

#### <a name="reference-the-package"></a>Referenciar o pacote

Consulte o pacote MSAL no código do aplicativo.

# <a name="net"></a>[.NET](#tab/dotnet)

Adicione o pacote [Microsoft.IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet ao seu aplicativo.
Em MSAL.NET, o aplicativo cliente `IConfidentialClientApplication` confidencial é representado pela interface.
Use o espaço de nome MSAL.NET no código fonte.

```csharp
using Microsoft.Identity.Client;
IConfidentialClientApplication app;
```

# <a name="python"></a>[Python](#tab/python)

```python
import msal
```

# <a name="java"></a>[Java](#tab/java)

```java
import com.microsoft.aad.msal4j.ClientCredentialFactory;
import com.microsoft.aad.msal4j.ClientCredentialParameters;
import com.microsoft.aad.msal4j.ConfidentialClientApplication;
import com.microsoft.aad.msal4j.IAuthenticationResult;
import com.microsoft.aad.msal4j.IClientCredential;
import com.microsoft.aad.msal4j.MsalException;
import com.microsoft.aad.msal4j.SilentParameters;
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-secret"></a>Instanciar o aplicativo de cliente confidencial com um segredo do cliente

Aqui está o código para instanciar o aplicativo de cliente confidencial com um segredo do cliente:

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-certificate"></a>Instanciar o aplicativo de cliente confidencial com um certificado de cliente

Aqui está o código para construir um aplicativo com um certificado:

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="java"></a>[Java](#tab/java)

No MSAL Java, existem dois construtores para instanciar o aplicativo cliente confidencial com certificados:

```Java

InputStream pkcs12Certificate = ... ; /* Containing PCKS12-formatted certificate*/
string certificatePassword = ... ;    /* Contains the password to access the certificate */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(pkcs12Certificate, certificatePassword);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

ou

```Java
PrivateKey key = getPrivateKey(); /* RSA private key to sign the assertion */
X509Certificate publicCertificate = getPublicCertificate(); /* x509 public certificate used as a thumbprint */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(key, publicCertificate);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

#### <a name="advanced-scenario-instantiate-the-confidential-client-application-with-client-assertions"></a>Cenário avançado: Instanciar o aplicativo de cliente confidencial com afirmações do cliente

# <a name="net"></a>[.NET](#tab/dotnet)

Em vez de um segredo de cliente ou um certificado, o aplicativo de cliente confidencial também pode provar sua identidade usando afirmações do cliente.

MSAL.NET tem dois métodos para fornecer afirmações assinadas ao aplicativo cliente confidencial:

- `.WithClientAssertion()`
- `.WithClientClaims()`

Quando você `WithClientAssertion`usa, você precisa fornecer um JWT assinado. Este cenário avançado é detalhado nas [afirmações do Cliente](msal-net-client-assertions.md).

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Quando você `WithClientClaims`usar , MSAL.NET produzirá uma afirmação assinada que contém as reivindicações esperadas pelo Azure AD, além de reclamações adicionais do cliente que você deseja enviar.
Este código mostra como fazer isso:

```csharp
string ipAddress = "192.168.1.2";
var claims = new Dictionary<string, string> { { "client_ip", ipAddress } };
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, claims)
                                          .Build();```
```

Novamente, para obter [detalhes,](msal-net-client-assertions.md)consulte as afirmações do cliente .

# <a name="python"></a>[Python](#tab/python)

No MSAL Python, você pode fornecer reivindicações de clientes usando as reivindicações que serão assinadas por essa `ConfidentialClientApplication`chave privada.

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    client_claims = {"client_ip": "x.x.x.x"}
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

Para obter detalhes, consulte a documentação de referência do MSAL Python para [ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__).

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromClientAssertion(assertion);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

## <a name="next-steps"></a>Próximas etapas

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Aplicativo Daemon - aquisição de tokens para o aplicativo](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Aplicativo Daemon - aquisição de tokens para o aplicativo](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Aplicativo Daemon - aquisição de tokens para o aplicativo](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=java)

---
