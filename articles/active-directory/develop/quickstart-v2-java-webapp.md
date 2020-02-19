---
title: Início rápido de aplicativo Web Java na plataforma de identidade da Microsoft | Azure
description: Saiba como implementar a opção Entrar com a conta da Microsoft em um aplicativo Web Java usando o OpenID Connect
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java
ms.openlocfilehash: 3e1369901e259af6722d9e5a14fababac80f1d02
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160552"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Início Rápido: Adicionar uma entrada com a Microsoft a um aplicativo Web Java

Neste início rápido, você aprenderá a integrar um aplicativo Web Java à plataforma de identidade da Microsoft. Seu aplicativo conectará um usuário, obterá um token de acesso para chamar a API do Microsoft Graph e fará uma solicitação para a API do Microsoft Graph.

Após concluir este início rápido, seu aplicativo aceitará credenciais de contas Microsoft pessoais (incluindo outlook.com, live.com e outras) e contas corporativas ou de estudante de qualquer empresa ou organização que utilize o Azure Active Directory.

![Mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-java-webapp/java-quickstart.svg)

## <a name="prerequisites"></a>Pré-requisitos

Para executar esta amostra, você precisará do seguinte:

- [JDK (Java Development Kit)](https://openjdk.java.net/) 8 ou superior e o [Maven](https://maven.apache.org/).
- Um locatário do Azure AD (Azure Active Directory). Para saber mais sobre como obter um locatário do Azure AD, confira [Como obter um locatário do Azure AD.](https://azure.microsoft.com/documentation/articles/active-directory-howto-tenant/)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrar e baixar o aplicativo de início rápido
> Você tem duas opções para iniciar o aplicativo de início rápido: expressa (opção 1) ou manual (opção 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código
>
> 1. Acesse o [portal do Azure – Registros de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Insira um nome para seu aplicativo e selecione **Registrar**.
> 1. Siga as instruções para baixar e configurar automaticamente o novo aplicativo.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registrar e configurar manualmente o aplicativo e o exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo
>
> Para registrar seu aplicativo e adicionar manualmente as informações de registro do aplicativo à solução, execute estas etapas:
>
> 1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
> 1. Se sua conta fornecer acesso a mais de um locatário, selecione sua conta no canto superior direito e defina sua sessão do portal para o locatário desejado do Azure AD.
>
> 1. Navegue até a página [Registros de aplicativo](/azure/active-directory/develop/) da plataforma de identidade da Microsoft para desenvolvedores.
> 1. Selecione **Novo registro**.
> 1. Quando a página **Registrar um aplicativo** for exibida, insira as informações de registro do aplicativo:
>    - Na seção **Nome**, insira um nome de aplicativo relevante que será exibido aos usuários do aplicativo, por exemplo, `java-webapp`.
>    - Deixe **URI de Redirecionamento** em branco por enquanto e selecione **Registrar**.
> 1. Na página **Visão Geral**, localize os valores **ID do Aplicativo (cliente)** e **ID do Diretório (locatário)** do aplicativo. Copie esses valores para uso posterior.
> 1. Selecione **Autenticação** no menu e, em seguida, adicione as seguintes informações:
>    - Em **URIs de Redirecionamento**, adicione `https://localhost:8080/msal4jsample/secure/aad` e `https://localhost:8080/msal4jsample/graph/me`.
>    - Clique em **Salvar**.
> 1. Selecione **Certificados e segredos** no menu e, na seção **Segredos do cliente**, clique em **Novo segredo do cliente**:
>
>    - Digite uma descrição de chave (para o segredo do aplicativo da instância).
>    - Selecione uma duração de chave igual a **Em 1 ano**.
>    - O valor da chave será exibido quando você selecionar **Adicionar**.
>    - Copie o valor da chave para uso posterior. Esse valor de chave não será exibido novamente nem poderá ser recuperado por outros meios, portanto, registre-o assim que estiver visível na portal do Azure.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Etapa 1: Configurar seu aplicativo no portal do Azure
>
> Para o exemplo de código deste início rápido funcionar, você precisará:
>
> 1. Adicionar URLs de resposta como `https://localhost:8080/msal4jsamples/secure/aad` e `https://localhost:8080/msal4jsamples/graph/me`.
> 1. Criar um segredo do cliente.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-aspnet-webapp/green-check.png) Seu aplicativo já está configurado com esses atributos.

#### <a name="step-2-download-the-code-sample"></a>Etapa 2: Baixar o exemplo de código

 [Baixe o exemplo de código](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

#### <a name="step-3-configure-the-code-sample"></a>Etapa 3: Configurar o exemplo de código

 1. Extraia o arquivo zip para uma pasta local.
 1. Se você usar um ambiente de desenvolvimento integrado, abra a amostra em seu IDE favorito (opcional).
 1. Abra o arquivo application.properties, que pode ser encontrado na pasta src/main/resources/, e substitua o valor dos campos *aad.clientId*, *aad.authority* e *aad.secretKey* pelos respectivos valores de **Id do Aplicativo**, **Id do Locatário** e **Segredo do Cliente** como se segue:

    ```file
    aad.clientId=Enter_the_Application_Id_here
    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
    aad.secretKey=Enter_the_Client_Secret_Here
    aad.redirectUriSignin=https://localhost:8080/msal4jsample/secure/aad
    aad.redirectUriGraph=https://localhost:8080/msal4jsample/graph/me
    ```

    > [!div renderon="docs"]
    > Em que:
    >
    > - `Enter_the_Application_Id_here`: é a ID do aplicativo que você registrou.
    > - `Enter_the_Client_Secret_Here` – é o **Segredo do Cliente** criado em **Certificados e Segredos** para o aplicativo registrado.
    > - `Enter_the_Tenant_Info_Here` – é o valor da **ID do Diretório (locatário)** do aplicativo que você registrou.

 1. Para usar https com localhost, preencha as propriedades server.ssl.key. Para gerar um certificado autoassinado, use o utilitário keytool (incluído no JRE).

   ```
   Example: 
   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password

   server.ssl.key-store-type=PKCS12  
   server.ssl.key-store=classpath:keystore.p12  
   server.ssl.key-store-password=password  
   server.ssl.key-alias=testCert 
   ```

   Coloque o arquivo de repositório de chaves gerado na pasta "recursos".

#### <a name="step-4-run-the-code-sample"></a>Etapa 4: Executar o exemplo de código

Para executar o projeto, você pode:

Executá-lo diretamente do seu IDE usando o servidor Spring Boot inserido ou empacotá-lo em um arquivo WAR usando [maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) e implantá-lo em uma solução de contêiner J2EE, como [Apache Tomcat](http://tomcat.apache.org/).

##### <a name="running-from-ide"></a>Executando no IDE

Se você estiver executando o aplicativo Web em um IDE, clique em Executar e navegue até a home page do projeto. Nesta amostra, a URL da home page padrão é https://localhost:8080.

1. Na página frontal, selecione o botão **Logon** a fim de redirecionar para o Azure Active Directory e solicite ao usuário suas credenciais.

1. Depois que o usuário é autenticado, ele é redirecionado para *https://localhost:8080/msal4jsample/secure/aad* . Agora ele está conectado e a página mostrará informações sobre a conta credenciada. A interface do usuário da amostra tem os seguintes botões:
    - *Sair*: desconecta o usuário atual do aplicativo e o redireciona para a home page.
    - *Mostrar Informações do Usuário*: adquire um token para o Microsoft Graph e chama o Microsoft Graph com uma solicitação que contém o token, que retorna informações básicas sobre o usuário conectado.

> [!IMPORTANT]
> Este aplicativo de início rápido usa um segredo do cliente para se identificar como cliente confidencial. Como o segredo do cliente é adicionado como texto sem formatação a seus arquivos de projeto, por motivos de segurança, é recomendável usar um certificado, em vez de um segredo do cliente, antes de considerar o aplicativo como aplicativo de produção. Para saber mais sobre como usar um certificado, confira [Credenciais de certificado para autenticação de aplicativo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials).

## <a name="more-information"></a>Mais informações

### <a name="getting-msal"></a>Como obter o MSAL

MSAL4J (MSAL para Java) é a biblioteca Java usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pela plataforma de identidade da Microsoft.

Adicione a MSAL4J ao seu aplicativo usando o Maven ou o Gradle para gerenciar as dependências fazendo as alterações a seguir no arquivo pom.xml (Maven) ou build.gradle (Gradle) do aplicativo.

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>Inicialização da MSAL

Adicione uma referência à MSAL para Java incluindo o seguinte código ao início do arquivo no qual você usará a MSAL4J:

```Java
import com.microsoft.aad.msal4j.*;
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre permissões e consentimento:

> [!div class="nextstepaction"]
> [Permissões e consentimento](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

Para saber mais sobre o fluxo de autenticação para este cenário, confira o fluxo do código de autorização OAuth 2.0:

> [!div class="nextstepaction"]
> [Fluxo do código de autorização OAuth](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)

Ajude-nos a melhorar a plataforma de identidade da Microsoft. Deixe sua opinião respondendo a uma breve pesquisa de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa da plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
