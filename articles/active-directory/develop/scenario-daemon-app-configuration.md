---
title: Configurar aplicativos de daemon que chamam APIs da Web-plataforma de identidade da Microsoft | Azure
description: Saiba como configurar o código para seu aplicativo daemon que chama APIs da Web (configuração de aplicativo)
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
ms.openlocfilehash: 88062c2134600d5b1460858c3799cfc8daa83744
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775223"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Aplicativo daemon que chama a configuração de código de APIs da Web

Saiba como configurar o código para seu aplicativo daemon que chama APIs da Web.

## <a name="msal-libraries-that-support-daemon-apps"></a>Bibliotecas MSAL que dão suporte a aplicativos daemon

Essas bibliotecas da Microsoft oferecem suporte a aplicativos de daemon:

  Biblioteca MSAL | Description
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | As plataformas .NET Framework e .NET Core têm suporte para a criação de aplicativos daemon. (UWP, Xamarin. iOS e Xamarin. Android não têm suporte porque essas plataformas são usadas para criar aplicativos cliente públicos.)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Suporte para aplicativos daemon em Python.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Suporte para aplicativos daemon em Java.

## <a name="configure-the-authority"></a>Configurar a autoridade

Os aplicativos daemon usam permissões de aplicativo em vez de permissões delegadas. Portanto, o tipo de conta com suporte não pode ser uma conta em nenhum diretório organizacional ou qualquer conta Microsoft pessoal (por exemplo, Skype, Xbox, Outlook.com). Não há nenhum administrador de locatários para conceder consentimento a um aplicativo daemon para uma conta pessoal da Microsoft. Você precisará escolher *contas em minha organização* ou *contas em qualquer organização*.

Portanto, a autoridade especificada na configuração do aplicativo deve ser locatário (especificando uma ID de locatário ou um nome de domínio associado à sua organização).

Se você for um ISV e quiser fornecer uma ferramenta multilocatário, poderá usar `organizations`. Mas tenha em mente que você também precisará explicar aos seus clientes como conceder o consentimento do administrador. Para obter detalhes, consulte [solicitando consentimento para um locatário inteiro](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant). Além disso, atualmente há uma limitação em MSAL: `organizations` é permitido somente quando as credenciais do cliente são um segredo do aplicativo (não um certificado).

## <a name="configure-and-instantiate-the-application"></a>Configurar e instanciar o aplicativo

Em bibliotecas MSAL, as credenciais do cliente (segredo ou certificado) são passadas como um parâmetro da construção do aplicativo cliente confidencial.

> [!IMPORTANT]
> Mesmo que seu aplicativo seja um aplicativo de console executado como um serviço, se for um aplicativo de daemon, ele precisará ser um aplicativo cliente confidencial.

### <a name="configuration-file"></a>Arquivo de configuração

O arquivo de configuração define:

- A autoridade ou a ID de locatário e a instância de nuvem.
- A ID do cliente que você obteve do registro do aplicativo.
- Um segredo do cliente ou um certificado.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[appSettings. JSON](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/appsettings.json) da amostra do [daemon do console do .NET Core](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) .

```JSon
{
  "Instance": "https://login.microsoftonline.com/{0}",
  "Tenant": "[Enter here the tenantID or domain name for your Azure AD tenant]",
  "ClientId": "[Enter here the ClientId for your application]",
  "ClientSecret": "[Enter here a client secret for your application]",
  "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]"
}
```

Você fornece um `ClientSecret` ou um `CertificateName`. Essas configurações são exclusivas.

# <a name="pythontabpython"></a>[Python](#tab/python)

Quando você cria um cliente confidencial com segredos do cliente, o arquivo de configuração [Parameters. JSON](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/1-Call-MsGraph-WithSecret/parameters.json) no exemplo do [daemon Python](https://github.com/Azure-Samples/ms-identity-python-daemon) é o seguinte:

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "secret": "The secret generated by AAD during your confidential app registration",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

Quando você cria um cliente confidencial com certificados, o arquivo de configuração [Parameters. JSON](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/parameters.json) no exemplo de [daemon do Python](https://github.com/Azure-Samples/ms-identity-python-daemon) é o seguinte:

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

# <a name="javatabjava"></a>[Java](#tab/java)

[TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java) é a classe usada para configurar exemplos de desenvolvimento do MSAL Java:

```Java
public class TestData {

    final static String TENANT_SPECIFIC_AUTHORITY = "https://login.microsoftonline.com/<TenantId>/";
    final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
    final static String CONFIDENTIAL_CLIENT_ID = "";
    final static String CONFIDENTIAL_CLIENT_SECRET = "";
}
```

---

### <a name="instantiate-the-msal-application"></a>Instanciar o aplicativo MSAL

Para instanciar o aplicativo MSAL, você precisa adicionar, referenciar ou importar o pacote MSAL (dependendo do idioma).

A construção é diferente, dependendo se você estiver usando os segredos ou certificados do cliente (ou, como um cenário avançado, asserções assinadas).

#### <a name="reference-the-package"></a>Referenciar o pacote

Referencie o pacote MSAL no código do aplicativo.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Adicione o pacote NuGet [Microsoft. IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) ao seu aplicativo.
No MSAL.NET, o aplicativo cliente confidencial é representado pela interface `IConfidentialClientApplication`.
Use o namespace MSAL.NET no código-fonte.

```csharp
using Microsoft.Identity.Client;
IConfidentialClientApplication app;
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```python
import msal
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
import com.microsoft.aad.msal4j.ClientCredentialFactory;
import com.microsoft.aad.msal4j.ClientCredentialParameters;
import com.microsoft.aad.msal4j.ConfidentialClientApplication;
import com.microsoft.aad.msal4j.IAuthenticationResult;
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-secret"></a>Criar uma instância do aplicativo cliente confidencial com um segredo do cliente

Este é o código para instanciar o aplicativo cliente confidencial com um segredo do cliente:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```csharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

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

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(TestData.CONFIDENTIAL_CLIENT_SECRET))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-certificate"></a>Criar uma instância do aplicativo cliente confidencial com um certificado de cliente

Este é o código para criar um aplicativo com um certificado:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```csharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

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

# <a name="javatabjava"></a>[Java](#tab/java)

No MSAL Java, há dois construtores para instanciar o aplicativo cliente confidencial com certificados:

```Java

InputStream pkcs12Certificate = ... ; /* Containing PCKS12-formatted certificate*/
string certificatePassword = ... ;    /* Contains the password to access the certificate */

ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(pkcs12Certificate, certificatePassword))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

ou

```Java
PrivateKey key = getPrivateKey(); /* RSA private key to sign the assertion */
X509Certificate publicCertificate = getPublicCertificate(); /* x509 public certificate used as a thumbprint */

ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(rsaPrivateKey, publicKeyCertificate))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

---

#### <a name="advanced-scenario-instantiate-the-confidential-client-application-with-client-assertions"></a>Cenário avançado: instanciar o aplicativo cliente confidencial com declarações de cliente

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Em vez de um segredo do cliente ou um certificado, o aplicativo cliente confidencial também pode provar sua identidade usando as declarações do cliente.

MSAL.NET tem dois métodos para fornecer asserções assinadas ao aplicativo cliente confidencial:

- `.WithClientAssertion()`
- `.WithClientClaims()`

Ao usar `WithClientAssertion`, você precisa fornecer um JWT assinado. Esse cenário avançado é detalhado em [declarações de cliente](msal-net-client-assertions.md).

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Quando você usa `WithClientClaims`, o MSAL.NET produzirá uma asserção assinada que contém as declarações esperadas pelo Azure AD, além de declarações de cliente adicionais que você deseja enviar.
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

Novamente, para obter detalhes, consulte [declarações do cliente](msal-net-client-assertions.md).

# <a name="pythontabpython"></a>[Python](#tab/python)

No MSAL Python, você pode fornecer declarações de cliente usando as declarações que serão assinadas por essa chave privada de `ConfidentialClientApplication`.

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

Para obter detalhes, consulte a documentação de referência do Python do MSAL para [ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__).

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java está em visualização pública. As declarações assinadas ainda não têm suporte.

---

## <a name="next-steps"></a>Próximos passos

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Aplicativo de daemon-adquirindo tokens para o aplicativo](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Aplicativo de daemon-adquirindo tokens para o aplicativo](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Aplicativo de daemon-adquirindo tokens para o aplicativo](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=java)

---
