---
title: Use um certificado TLS/SSL em código
description: Saiba como usar os certificados do cliente em seu código. Autenticar com recursos remotos com um certificado de cliente ou executar tarefas criptográficas com eles.
ms.topic: article
ms.date: 11/04/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: d76bac60bae11f0843d81de523030154af62a373
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811703"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>Use um certificado TLS/SSL em seu código no Azure App Service

No código do aplicativo, você pode acessar os [certificados públicos ou privados que você adiciona ao App Service](configure-ssl-certificate.md). Seu código de aplicativo pode atuar como um cliente e acessar um serviço externo que requer autenticação de certificado, ou pode precisar realizar tarefas criptográficas. Este guia de como fazer mostra como usar certificados públicos ou privados no código do aplicativo.

Essa abordagem de usar certificados em seu código faz uso da funcionalidade TLS no App Service, que exige que seu aplicativo esteja no nível **Básico** ou acima. Se o aplicativo estiver no **nível Gratuito** ou **Compartilhado,** você pode [incluir o arquivo de certificado no repositório do aplicativo.](#load-certificate-from-file)

Quando você permite que o App Service gerencie seus certificados TLS/SSL, você pode manter os certificados e o código do aplicativo separadamente e proteger seus dados confidenciais.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia de instruções, é necessário ter:

- [Crie um aplicativo de serviço de aplicativos](/azure/app-service/)
- [Adicione um certificado ao seu aplicativo](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Encontre a impressão digital

No <a href="https://portal.azure.com" target="_blank">portal Azure</a>, no menu esquerdo, selecione**\<app-name do ** **App Services** > >.

Na navegação à esquerda do aplicativo, selecione **configurações TLS/SSL**e selecione **Certificados de chave privada (.pfx)** ou **Certificados de Chave Pública (.cer).**

Encontre o certificado que deseja usar e copie a impressão digital.

![Copie a impressão digital do certificado](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>Tornar o certificado acessível

Para acessar um certificado no código do aplicativo, adicione sua impressão digital à configuração do `WEBSITE_LOAD_CERTIFICATES` aplicativo, executando o seguinte comando no Cloud <a target="_blank" href="https://shell.azure.com" >Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Para tornar todos os seus certificados `*`acessíveis, defina o valor para .

## <a name="load-certificate-in-windows-apps"></a>Certificado de carga em aplicativos Windows

A `WEBSITE_LOAD_CERTIFICATES` configuração do aplicativo torna os certificados especificados acessíveis ao seu aplicativo hospedado no Windows na loja de certificados do Windows, e a localização depende do [nível de preços:](overview-hosting-plans.md)

- **Nível isolado** - em [Local Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores). 
- Todos os outros níveis - em [Current User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).

Em código C#, você acessa o certificado pela impressão digital do certificado. O código a seguir carrega um certificado com a impressão digital `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

```csharp
using System;
using System.Linq;
using System.Security.Cryptography.X509Certificates;

string certThumbprint = "E661583E8FABEF4C0BEF694CBC41C28FB81CD870";
bool validOnly = false;

using (X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser))
{
  certStore.Open(OpenFlags.ReadOnly);

  X509Certificate2Collection certCollection = certStore.Certificates.Find(
                              X509FindType.FindByThumbprint,
                              // Replace below with your certificate's thumbprint
                              certThumbprint,
                              validOnly);
  // Get the first cert with the thumbprint
  X509Certificate2 cert = certCollection.OfType<X509Certificate>().FirstOrDefault();

  if (cert is null)
      throw new Exception($"Certificate with thumbprint {certThumbprint} was not found");

  // Use certificate
  Console.WriteLine(cert.FriendlyName);
  
  // Consider to call Dispose() on the certificate after it's being used, avaliable in .NET 4.6 and later
}
```

No código Java, você acessa o certificado da loja "Windows-MY" usando o campo Nome Comum sujeito (consulte [Certificado de chave pública](https://en.wikipedia.org/wiki/Public_key_certificate)). O código a seguir mostra como carregar um certificado de chave privada:

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.PrivateKey;

...
KeyStore ks = KeyStore.getInstance("Windows-MY");
ks.load(null, null); 
Certificate cert = ks.getCertificate("<subject-cn>");
PrivateKey privKey = (PrivateKey) ks.getKey("<subject-cn>", ("<password>").toCharArray());

// Use the certificate and key
...
```

Para idiomas que não suportam ou oferecem suporte insuficiente para a loja de certificados do Windows, consulte [Certificado de carga do arquivo](#load-certificate-from-file).

## <a name="load-certificate-in-linux-apps"></a>Certificado de carga em aplicativos Linux

As `WEBSITE_LOAD_CERTIFICATES` configurações do aplicativo tornam os certificados especificados acessíveis aos seus aplicativos hospedados no Linux (incluindo aplicativos de contêiner personalizados) como arquivos. Os arquivos são encontrados sob os seguintes diretórios:

- Certificados privados - `/var/ssl/private` (arquivos) `.p12`
- Certificados públicos `/var/ssl/certs` `.der` - (arquivos)

Os nomes dos arquivos do certificado são as impressões digitais do certificado. O código C# a seguir mostra como carregar um certificado público em um aplicativo Linux.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Para ver como carregar um certificado TLS/SSL de um arquivo em Node.js, PHP, Python, Java ou Ruby, consulte a documentação para a respectiva linguagem ou plataforma web.

## <a name="load-certificate-from-file"></a>Certificado de carga do arquivo

Se você precisar carregar um arquivo de certificado que você carrega manualmente, é melhor carregar o certificado usando [FTPS](deploy-ftp.md) em vez de [Git,](deploy-local-git.md)por exemplo. Você deve manter dados confidenciais como um certificado privado fora do controle de origem.

> [!NOTE]
> ASP.NET e ASP.NET Core no Windows devem acessar a loja de certificados mesmo se você carregar um certificado de um arquivo. Para carregar um arquivo de certificado em um aplicativo Windows .NET, carregue o perfil do usuário atual com o seguinte comando no <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Essa abordagem de usar certificados em seu código faz uso da funcionalidade TLS no App Service, que exige que seu aplicativo esteja no nível **Básico** ou acima.

O exemplo C# a seguir carrega um certificado público de um caminho relativo em seu aplicativo:

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Para ver como carregar um certificado TLS/SSL de um arquivo em Node.js, PHP, Python, Java ou Ruby, consulte a documentação para a respectiva linguagem ou plataforma web.

## <a name="more-resources"></a>Mais recursos

* [Proteja um nome DNS personalizado com uma vinculação TLS/SSL no Serviço de Aplicativos Do Azure](configure-ssl-bindings.md)
* [Impor HTTPS](configure-ssl-bindings.md#enforce-https)
* [Impor o TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [FAQ: Certificados de Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
