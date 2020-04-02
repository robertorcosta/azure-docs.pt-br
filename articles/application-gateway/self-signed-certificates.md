---
title: Gerar certificado auto-assinado com um CA raiz personalizado
titleSuffix: Azure Application Gateway
description: Saiba como gerar um certificado auto-assinado do Azure Application Gateway com um CA raiz personalizado
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: 0547f254a64cecc7072ee9ff79eb50204b34bc17
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548858"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>Gerar um certificado auto-assinado do Azure Application Gateway com um CA raiz personalizado

O Application Gateway v2 SKU introduz o uso de Certificados raiz confiáveis para permitir servidores back-end. Isso remove certificados de autenticação que eram exigidos no V1 SKU. O *certificado raiz* é um Base-64 codificado X.509(. CER) formato certificado raiz do servidor de certificado backend. Ele identifica a autoridade de certificado raiz (CA) que emitiu o certificado do servidor e o certificado do servidor é então usado para a comunicação SSL.

O Application Gateway confia no certificado do seu site por padrão se ele for assinado por uma CA bem conhecida (por exemplo, GoDaddy ou DigiCert). Você não precisa carregar explicitamente o certificado raiz nesse caso. Para obter mais informações, consulte [Visão geral do término do SSL e ssl de ponta a ponta com o Gateway de aplicativo](ssl-overview.md). No entanto, se você tem um ambiente de dev/teste e não deseja comprar um certificado assinado pela CA verificado, você pode criar seu próprio CA personalizado e criar um certificado auto-assinado com ele. 

> [!NOTE]
> Os certificados auto-assinados não são confiáveis por padrão e podem ser difíceis de manter. Além disso, eles podem usar suítes de hash e cifras desatualizadas que podem não ser fortes. Para uma melhor segurança, compre um certificado assinado por uma autoridade de certificados bem conhecida.

Neste artigo, você aprenderá a:

- Crie sua própria autoridade de certificado personalizada
- Crie um certificado auto-assinado assinado por seu CA personalizado
- Carregue um certificado raiz auto-assinado em um Gateway de aplicativo para autenticar o servidor backend

## <a name="prerequisites"></a>Pré-requisitos

- **[OpenSSL](https://www.openssl.org/) em um computador executando Windows ou Linux** 

   Embora possa haver outras ferramentas disponíveis para o gerenciamento de certificados, este tutorial usa OpenSSL. Você pode encontrar openssl empacotado com muitas distribuições Linux, como ubuntu.
- **Um servidor web**

   Por exemplo, Apache, IIS ou NGINX para testar os certificados.

- **Um Gateway de aplicativo v2 SKU**
   
  Se você não tiver um gateway de aplicativo existente, consulte [Quickstart: Tráfego direto da Web com o Gateway de aplicativos Do Azure - portal Azure](quick-create-portal.md).

## <a name="create-a-root-ca-certificate"></a>Crie um certificado de CA raiz

Crie seu certificado de CA raiz usando openssl.

### <a name="create-the-root-key"></a>Crie a chave raiz

1. Faça login no computador onde o OpenSSL está instalado e execute o seguinte comando. Isso cria uma chave protegida por senha.

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. No prompt, digite uma senha forte. Por exemplo, pelo menos nove caracteres, usando maiúsculas, minúsculas, números e símbolos.

### <a name="create-a-root-certificate-and-self-sign-it"></a>Crie um Certificado Raiz e assine-o por si mesmo

1. Use os seguintes comandos para gerar o csr e o certificado.

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   Os comandos anteriores criam o certificado raiz. Você usará isso para assinar seu certificado de servidor.

1. Quando solicitado, digite a senha para a chave raiz e as informações organizacionais para a CA personalizada, como País, Estado, Org, OU e o nome de domínio totalmente qualificado (este é o domínio do emissor).

   ![criar certificado raiz](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>Crie um certificado de servidor

Em seguida, você criará um certificado de servidor usando openssl.

### <a name="create-the-certificates-key"></a>Crie a chave do certificado

Use o seguinte comando para gerar a chave para o certificado do servidor.

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>Crie o CSR (Solicitação de Assinatura de Certificado)

A RSE é uma chave pública que é dada a uma CA ao solicitar um certificado. A CA emite o certificado para esta solicitação específica.

> [!NOTE]
> O CN (Nome Comum) para o certificado de servidor deve ser diferente do domínio do emissor. Por exemplo, neste caso, a CN `www.contoso.com` para o emissor é `www.fabrikam.com`e a CN do certificado do servidor é .


1. Use o seguinte comando para gerar o CSR:

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. Quando solicitado, digite a senha para a chave raiz e as informações organizacionais para o CA personalizado: País, Estado, Org, OU e o nome de domínio totalmente qualificado. Este é o domínio do site e deve ser diferente do emissor.

   ![Certificado de servidor](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>Gere o certificado com o CSR e a chave e assine-o com a chave raiz da CA

1. Use o seguinte comando para criar o certificado:

   ```
   openssl x509 -req -in fabrikam.csr -CA  contoso.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>Verifique o certificado recém-criado

1. Use o seguinte comando para imprimir a saída do arquivo CRT e verificar seu conteúdo:

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![Verificação de certificado](media/self-signed-certificates/verify-cert.png)

1. Verifique os arquivos em seu diretório e certifique-se de ter os seguintes arquivos:

   - contoso.crt
   - contoso.key
   - fabrikam.crt
   - fabrikam.key

## <a name="configure-the-certificate-in-your-web-servers-ssl-settings"></a>Configure o certificado nas configurações ssl do servidor web

Em seu servidor web, configure o SSL usando os arquivos fabrikam.crt e fabrikam.key. Se o servidor web não conseguir pegar dois arquivos, você pode combiná-los a um único arquivo .pem ou .pfx usando comandos OpenSSL.

### <a name="iis"></a>IIS

Para obter instruções sobre como importar certificado e carregá-los como certificado de servidor no IIS, consulte [COMO instalar certificados importados em um servidor web no Windows Server 2003](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server).

Para obter instruções de encadernação SSL, [consulte Como configurar o SSL no IIS 7](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1).

### <a name="apache"></a>Apache

A configuração a seguir é um exemplo [de host virtual configurado para SSL](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts) no Apache:

```
<VirtualHost www.fabrikam:443>
      DocumentRoot /var/www/fabrikam
      ServerName www.fabrikam.com
      SSLEngine on
      SSLCertificateFile /home/user/fabrikam.crt
      SSLCertificateKeyFile /home/user/fabrikam.key
</VirtualHost>
```

### <a name="nginx"></a>NGINX

A configuração a seguir é um [exemplo de bloqueio de servidor NGINX](https://nginx.org/docs/http/configuring_https_servers.html) com configuração SSL:

![NGINX com SSL](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>Acesse o servidor para verificar a configuração

1. Adicione o certificado raiz à loja raiz confiável da sua máquina. Ao acessar o site, certifique-se de que toda a cadeia de certificados seja vista no navegador.

   ![Certificados raiz confiáveis](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > Presume-se que o DNS foi configurado para apontar o nome do servidor web (neste exemplo, www.fabrikam.com) para o endereço IP do servidor web. Caso não, você pode editar o [arquivo hosts](https://answers.microsoft.com/en-us/windows/forum/all/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d) para resolver o nome.
1. Navegue pelo seu site e clique no ícone de bloqueio na caixa de endereços do seu navegador para verificar as informações do site e do certificado.

## <a name="verify-the-configuration-with-openssl"></a>Verifique a configuração com openSSL

Ou, você pode usar openssl para verificar o certificado.

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![Verificação de certificado OpenSSL](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>Carregue o certificado raiz para as configurações HTTP do Application Gateway

Para carregar o certificado no Application Gateway, você deve exportar o certificado .crt para um formato .cer Base-64 codificado. Como o .crt já contém a chave pública no formato codificado base-64, basta renomear a extensão de arquivo de .crt para .cer. 

### <a name="azure-portal"></a>Portal do Azure

Para carregar o certificado raiz confiável do portal, selecione as **Configurações HTTP** e escolha o protocolo **HTTPS.**

![Adicionar um certificado usando o portal](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

Ou, você pode usar a Cli ou o Azure PowerShell para carregar o certificado raiz. O código a seguir é uma amostra do Azure PowerShell.

> [!NOTE]
> A amostra a seguir adiciona um certificado raiz confiável ao gateway de aplicativo, cria uma nova configuração HTTP e adiciona uma nova regra, assumindo que o pool de backend e o ouvinte já existem.

```azurepowershell
## Add the trusted root certificate to the Application Gateway

$gw=Get-AzApplicationGateway -Name appgwv2 -ResourceGroupName rgOne

Add-AzApplicationGatewayTrustedRootCertificate `
   -ApplicationGateway $gw `
   -Name CustomCARoot `
   -CertificateFile "C:\Users\surmb\Downloads\contoso.cer"

$trustedroot = Get-AzApplicationGatewayTrustedRootCertificate `
   -Name CustomCARoot `
   -ApplicationGateway $gw

## Get the listener, backend pool and probe

$listener = Get-AzApplicationGatewayHttpListener `
   -Name basichttps `
   -ApplicationGateway $gw

$bepool = Get-AzApplicationGatewayBackendAddressPool `
  -Name testbackendpool `
  -ApplicationGateway $gw

Add-AzApplicationGatewayProbeConfig `
  -ApplicationGateway $gw `
  -Name testprobe `
  -Protocol Https `
  -HostName "www.fabrikam.com" `
  -Path "/" `
  -Interval 15 `
  -Timeout 20 `
  -UnhealthyThreshold 3

$probe = Get-AzApplicationGatewayProbeConfig `
  -Name testprobe `
  -ApplicationGateway $gw

## Add the configuration to the HTTP Setting and don't forget to set the "hostname" field
## to the domain name of the server certificate as this will be set as the SNI header and
## will be used to verify the backend server's certificate. Note that SSL handshake will
## fail otherwise and might lead to backend servers being deemed as Unhealthy by the probes

Add-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $gw `
  -Name testbackend `
  -Port 443 `
  -Protocol Https `
  -Probe $probe `
  -TrustedRootCertificate $trustedroot `
  -CookieBasedAffinity Disabled `
  -RequestTimeout 20 `
  -HostName www.fabrikam.com

## Get the configuration and update the Application Gateway

$backendhttp = Get-AzApplicationGatewayBackendHttpSettings `
  -Name testbackend `
  -ApplicationGateway $gw

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $gw `
  -Name testrule `
  -RuleType Basic `
  -BackendHttpSettings $backendhttp `
  -HttpListener $listener `
  -BackendAddressPool $bepool

Set-AzApplicationGateway -ApplicationGateway $gw 
```

### <a name="verify-the-application-gateway-backend-health"></a>Verifique o gateway de entrada de aplicativo backend health

1. Clique na exibição **Backend Health** do gateway do aplicativo para verificar se a sonda está saudável.
1. Você deve ver que o Status é **saudável** para o teste HTTPS.

![Sonda HTTPS](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o SSL\TLS no Gateway de aplicativo, consulte [Visão geral da terminação SSL e saque a fim do SSL com o Application Gateway](ssl-overview.md).

