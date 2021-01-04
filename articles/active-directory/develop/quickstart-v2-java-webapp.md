---
title: 'Início Rápido: Adicionar a opção Entrar com a conta da Microsoft a um aplicativo Web Java | Azure'
titleSuffix: Microsoft identity platform
description: Neste guia de início rápido, saiba como implementar a opção Entrar com a conta da Microsoft em um aplicativo Web Java usando o OpenID Connect.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java, devx-track-java
ms.openlocfilehash: e188c00840a4d043e94f94f9db565e2d4e06aaba
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031055"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Início Rápido: Adicionar uma entrada com a Microsoft a um aplicativo Web Java

Neste guia de início rápido, você baixará e executará um exemplo de código que demonstra como um aplicativo Web Java pode conectar usuários e chamar a API do Microsoft Graph. Os usuários de qualquer organização do Azure AD (Azure Active Directory) podem entrar no aplicativo.

 Confira [Como o exemplo funciona](#how-the-sample-works) para ver uma ilustração.

## <a name="prerequisites"></a>Pré-requisitos

Para executar esta amostra, você precisará do seguinte:

- [JDK (Java Development Kit)](https://openjdk.java.net/) 8 ou superior e o [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrar e baixar o aplicativo de início rápido
> Você tem duas opções para iniciar o aplicativo de início rápido: expressa (opção 1) ou manual (opção 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código
>
> 1. Acesse a experiência de início rápido do [portal do Azure – Registros de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaQuickstartPage/sourceType/docs).
> 1. Insira um nome para seu aplicativo e selecione **Registrar**.
> 1. Siga as instruções na experiência de início rápido do portal para baixar o código do aplicativo configurado automaticamente.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registrar e configurar manualmente o aplicativo e o exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo
>
> Para registrar seu aplicativo e adicionar manualmente as informações de registro do aplicativo, execute estas etapas:
>
> 1. Entre no [portal do Azure](https://portal.azure.com).
> 1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar um aplicativo.
> 1. Pesquise **Azure Active Directory** e selecione-o.
> 1. Em **Gerenciar**, selecione **Registros de aplicativo** > **Novo registro**.
> 1. Insira um **Nome** para seu aplicativo, por exemplo, `java-webapp`. Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde.
> 1. Selecione **Registrar**.
> 1. Na página **Visão geral**, anote a **ID do Aplicativo (cliente)** e a **ID do Diretório (locatário)** para uso posterior.
> 1. Em **Gerenciar**, selecione **Autenticação**.
> 1. Selecione **Adicionar uma plataforma** > **Web**.
> 1. Na seção **URIs de Redirecionamento**, adicione `https://localhost:8443/msal4jsample/secure/aad`.
> 1. Selecione **Configurar**.
> 1. Na seção **Web**, adicione `https://localhost:8443/msal4jsample/graph/me` como um segundo **URI de Redirecionamento**.
> 1. Em **Gerenciar**, selecione **Certificados e segredos**. Na seção **Segredos do cliente**, selecione **Novo segredo do cliente**.
> 1. Digite uma descrição de chave (por exemplo, segredo do aplicativo); deixe a expiração padrão e selecione **Adicionar**.
> 1. Observe o **Valor** do **Segredo do cliente** para uso posterior.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Etapa 1: Configurar seu aplicativo no portal do Azure
>
> Para o exemplo de código deste início rápido funcionar, você precisará:
>
> 1. Adicionar URLs de resposta como `https://localhost:8443/msal4jsample/secure/aad` e `https://localhost:8443/msal4jsample/graph/me`
> 1. Criar um segredo do cliente.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-aspnet-webapp/green-check.png) Seu aplicativo já está configurado com esses atributos.

#### <a name="step-2-download-the-code-sample"></a>Etapa 2: Baixar o exemplo de código
> [!div renderon="docs"]
> [Baixe o exemplo de código](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Baixe o projeto e extraia o arquivo zip para uma pasta local mais próxima da pasta raiz, por exemplo, **C:\Azure-Samples**
>
> Para usar https com localhost, preencha as propriedades server.ssl.key. Para gerar um certificado autoassinado, use o utilitário keytool (incluído no JRE).
>
>  ```
>   Example:
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   Coloque o arquivo de repositório de chaves gerado na pasta "recursos".

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Baixe o exemplo de código](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>Etapa 3: Configurar o exemplo de código
> 1. Extraia o arquivo zip para uma pasta local.
> 1. Se você usar um ambiente de desenvolvimento integrado, abra a amostra em seu IDE favorito (opcional).
> 1. Abra o arquivo application.properties, que pode ser encontrado na pasta src/main/resources/, e substitua o valor dos campos *aad.clientId*, *aad.authority* e *aad.secretKey* pelos respectivos valores de **Id do Aplicativo**, **Id do Locatário** e **Segredo do Cliente** como se segue:
>
>    ```file
>    aad.clientId=Enter_the_Application_Id_here
>    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
>    aad.secretKey=Enter_the_Client_Secret_Here
>    aad.redirectUriSignin=https://localhost:8443/msal4jsample/secure/aad
>    aad.redirectUriGraph=https://localhost:8443/msal4jsample/graph/me
>    aad.msGraphEndpointHost="https://graph.microsoft.com/"
>    ```
> Em que:
>
> - `Enter_the_Application_Id_here`: é a ID do aplicativo que você registrou.
> - `Enter_the_Client_Secret_Here` – é o **Segredo do Cliente** criado em **Certificados e Segredos** para o aplicativo registrado.
> - `Enter_the_Tenant_Info_Here` – é o valor da **ID do Diretório (locatário)** do aplicativo que você registrou.
> 1. Para usar https com localhost, preencha as propriedades server.ssl.key. Para gerar um certificado autoassinado, use o utilitário keytool (incluído no JRE).
>
>  ```
>   Example:
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   Coloque o arquivo de repositório de chaves gerado na pasta "recursos".


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Etapa 3: Executar o exemplo de código
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Etapa 4: Executar o exemplo de código

Para executar o projeto, você pode:

Executá-lo diretamente do seu IDE usando o servidor Spring Boot inserido ou empacotá-lo em um arquivo WAR usando [maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) e implantá-lo em uma solução de contêiner J2EE, como [Apache Tomcat](http://tomcat.apache.org/).

##### <a name="running-from-ide"></a>Executando no IDE

Se você estiver executando o aplicativo Web em um IDE, selecione Executar e navegue até a home page do projeto. Nesta amostra, a URL da home page padrão é https://localhost:8443.

1. Na página frontal, selecione o botão **Logon** a fim de redirecionar para o Azure Active Directory e solicite ao usuário suas credenciais.

1. Depois que o usuário é autenticado, ele é redirecionado para *https://localhost:8443/msal4jsample/secure/aad* . Agora ele está conectado e a página mostrará informações sobre a conta credenciada. A interface do usuário da amostra tem os seguintes botões:
    - *Sair*: desconecta o usuário atual do aplicativo e o redireciona para a home page.
    - *Mostrar Informações do Usuário*: adquire um token para o Microsoft Graph e chama o Microsoft Graph com uma solicitação que contém o token, que retorna informações básicas sobre o usuário conectado.

##### <a name="running-from-tomcat"></a>Executando no Tomcat

Se você quiser implantar o exemplo da Web no Tomcat, será necessário fazer algumas alterações no código-fonte.

1. Abra ms-identity-java-webapp/pom.xml
    - Em `<name>msal-web-sample</name>` adicione `<packaging>war</packaging>`

2. Abra ms-identity-java-webapp/src/main/java/com.microsoft.azure.msalwebsample/MsalWebSampleApplication

    - Exclua todo o código-fonte e substitua pelo seguinte:

   ```Java
    package com.microsoft.azure.msalwebsample;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.boot.builder.SpringApplicationBuilder;
    import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;

    @SpringBootApplication
    public class MsalWebSampleApplication extends SpringBootServletInitializer {

     public static void main(String[] args) {
      SpringApplication.run(MsalWebSampleApplication.class, args);
     }

     @Override
     protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
      return builder.sources(MsalWebSampleApplication.class);
     }
    }
   ```

3.   A porta HTTP padrão do Tomcat é 8080, embora seja necessária uma conexão HTTPS pela porta 8443. Para configurar isso:
        - Vá para tomcat/conf/server.xml
        - Pesquise a marca `<connector>` e substitua o conector existente por:

        ```xml
        <Connector
                   protocol="org.apache.coyote.http11.Http11NioProtocol"
                   port="8443" maxThreads="200"
                   scheme="https" secure="true" SSLEnabled="true"
                   keystoreFile="C:/Path/To/Keystore/File/keystore.p12" keystorePass="KeystorePassword"
                   clientAuth="false" sslProtocol="TLS"/>
        ```

4. Abra um prompt de comando, vá para a pasta raiz deste exemplo (em que o arquivo pom.xml está localizado) e execute `mvn package` para compilar o projeto
    - Isso vai gerar um arquivo `msal-web-sample-0.1.0.war` no /diretório de destino.
    - Renomeie este arquivo como `msal4jsample.war`
    - Implante esse arquivo WAR usando o Tomcat ou qualquer outra solução de contêiner J2EE.
        - Para implantar, copie o arquivo msal4jsample.war para o diretório `/webapps/` na instalação do Tomcat e inicie o servidor Tomcat.

5. Depois de implantado, vá para https://localhost:8443/msal4jsample em seu navegador


> [!IMPORTANT]
> Este aplicativo de início rápido usa um segredo do cliente para se identificar como cliente confidencial. Como o segredo do cliente é adicionado como texto sem formatação a seus arquivos de projeto, por motivos de segurança, é recomendável usar um certificado, em vez de um segredo do cliente, antes de considerar o aplicativo como aplicativo de produção. Para saber mais sobre como usar um certificado, confira [Credenciais de certificado para autenticação de aplicativo](./active-directory-certificate-credentials.md).

## <a name="more-information"></a>Mais informações

### <a name="how-the-sample-works"></a>Como o exemplo funciona
![Mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="getting-msal"></a>Como obter o MSAL

MSAL4J (MSAL para Java) é a biblioteca Java usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pela plataforma de identidade da Microsoft.

Adicione a MSAL4J ao seu aplicativo usando o Maven ou o Gradle para gerenciar as dependências fazendo as alterações a seguir no arquivo pom.xml (Maven) ou build.gradle (Gradle) do aplicativo.

No pom.xml:

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

No build.gradle:

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>Inicialização da MSAL

Adicione uma referência à MSAL para Java incluindo o seguinte código ao início do arquivo no qual você usará a MSAL4J:

```Java
import com.microsoft.aad.msal4j.*;
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximas etapas

Para ver uma discussão mais detalhada sobre a criação de aplicativos Web que conectam usuários na plataforma de identidade da Microsoft, prossiga para nossa série de cenários com várias partes:

> [!div class="nextstepaction"]
> [Cenário: Aplicativo Web que conecta usuários](scenario-web-app-sign-user-overview.md?tabs=java)
