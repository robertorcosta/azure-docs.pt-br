---
title: Proteja um serviço tranquilo em seu Azure AD B2C
titleSuffix: Azure AD B2C
description: Proteja suas trocas personalizadas de reclamações de API REST em seu Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37d1c181c18f69c040040da2be138eaad3a61693
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396850"
---
# <a name="secure-your-restful-services"></a>Proteja seus serviços RESTful 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ao integrar uma API REST dentro de uma jornada de usuário Azure AD B2C, você deve proteger seu ponto final da API REST com autenticação. Isso garante que apenas serviços que tenham credenciais adequadas, como o Azure AD B2C, possam fazer chamadas para o ponto final da API REST.

Aprenda a integrar uma API REST dentro da sua jornada de usuário Ad B2C do Azure na entrada do [usuário validar](custom-policy-rest-api-claims-validation.md) e [adicionar trocas de reclamações da API REST a](custom-policy-rest-api-claims-exchange.md) artigos de políticas personalizadas.

Este artigo explorará como proteger sua API REST com autenticação HTTP basic, client ou Autenticação OAuth2. 

## <a name="prerequisites"></a>Pré-requisitos

Complete as etapas em um dos seguintes guias 'Como fazer':

- [Integre as trocas de reclamações da API REST em sua jornada de usuário AD B2C do Azure para validar a entrada do usuário](custom-policy-rest-api-claims-validation.md).
- [Adicionar trocas de reclamações da API REST a políticas personalizadas](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>Autenticação básica HTTP

A autenticação básica HTTP é definida no [RFC 2617](https://tools.ietf.org/html/rfc2617). A autenticação básica funciona da seguinte forma: o Azure AD B2C envia uma solicitação HTTP com as credenciais do cliente no cabeçalho Autorização. As credenciais são formatadas como a seqüência de string codificada com base64 "nome:senha".  

### <a name="add-rest-api-username-and-password-policy-keys"></a>Adicionar as chaves de diretiva de nome de usuário e senha da API REST

Para configurar um perfil técnico da API REST com autenticação básica HTTP, crie as seguintes chaves criptográficas para armazenar o nome de usuário e a senha:

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Certifique-se de que está usando o diretório que contém seu inquilino Azure AD B2C. Selecione o filtro **de assinatura Diretório +** no menu superior e escolha o diretório Azure AD B2C.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
1. Selecione **Chaves de diretiva**e selecione **Adicionar**.
1. Em **Opções** selecione **Manual**.
1. Para **Nome,** **digite RestApiUsername**.
    O prefixo *B2C_1A_* pode ser adicionado automaticamente.
1. Na caixa **Segredo,** digite o nome de usuário da API REST.
1. Para o **Uso da chave**, selecione **Criptografia**.
1. Selecione **Criar**.
1. Selecione **Teclas de diretiva** novamente.
1. Selecione **Adicionar**.
1. Em **Opções** selecione **Manual**.
1. Para **Nome,** **digite RestApiPassword**.
    O prefixo *B2C_1A_* pode ser adicionado automaticamente.
1. Na caixa **Segredo,** digite a senha da API REST.
1. Para o **Uso da chave**, selecione **Criptografia**.
1. Selecione **Criar**.

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>Configure seu perfil técnico da API REST para usar a autenticação básica HTTP

Depois de criar as chaves necessárias, configure os metadados de perfil técnico da API REST para referenciar as credenciais.

1. No seu diretório de trabalho, abra o arquivo de política de extensão (TrustFrameworkExtensions.xml).
1. Procure o perfil técnico da API REST. Por exemplo `REST-ValidateProfile`, ou `REST-GetProfile`.
1. Localize o elemento `<Metadata>`.
1. Alterar o Tipo `Basic`de *autenticação* para .
1. Alterar o *AllowInsecureAuthInProduction* para `false`.
1. Adicione o seguinte snippet de código XML logo após fechar o elemento `</Metadata>`:
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

A seguir, um exemplo de um perfil técnico RESTful configurado com autenticação básica HTTP:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="https-client-certificate-authentication"></a>Autenticação do certificado do cliente HTTPS

A autenticação do certificado do cliente é uma autenticação baseada em certificado mútuo, onde o cliente, Azure AD B2C, fornece seu certificado de cliente ao servidor para provar sua identidade. Isso acontece como parte do aperto de mão ssl. Somente serviços que possuem certificados adequados, como o Azure AD B2C, podem acessar seu serviço de API REST. O certificado do cliente é um certificado digital X.509. Em ambientes de produção, deve ser assinado por uma autoridade certificadora.

### <a name="prepare-a-self-signed-certificate-optional"></a>Prepare um certificado auto-assinado (opcional)

Para ambientes não de produção, se você ainda não tiver um certificado, você pode usar um certificado auto-assinado. No Windows, você pode usar o cmdlet [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) da PowerShell para gerar um certificado.

1. Execute este comando PowerShell para gerar um certificado auto-assinado. Modifique `-Subject` o argumento conforme apropriado para o seu aplicativo e o nome do inquilino AZure AD B2C. Você também pode `-NotAfter` ajustar a data para especificar uma expiração diferente para o certificado.
    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```    
1. **Abrir gerencie certificados de usuário** > **Certificados** > **pessoais** > **do usuário** > atual*yourappname.yourtenant.onmicrosoft.com*.
1. Selecione o certificado > **Ação Todas** > **as Tarefas** > **Exportação**.
1. Selecione **Sim** > **A seguir** > **Sim, exporte a chave** > privada**Next**.
1. Aceitar os padrões para **formato de arquivo de exportação**.
1. Forneça uma senha para o certificado.

### <a name="add-a-client-certificate-policy-key"></a>Adicione uma chave de política de certificado suinoto

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Certifique-se de que está usando o diretório que contém seu inquilino Azure AD B2C. Selecione o filtro **de assinatura Diretório +** no menu superior e escolha o diretório Azure AD B2C.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
1. Selecione **Chaves de diretiva**e selecione **Adicionar**.
1. Na caixa **Opções**, selecione **Carregar**.
1. Na **caixa Nome,** **digite RestApiClientCertificate**.
    O prefixo *B2C_1A_* é adicionado automaticamente.
1. Na caixa **Carregar arquivo**, selecione o arquivo de certificado .pfx com uma chave privada.
1. Na caixa **Senha**, digite a senha do certificado.
1. Selecione **Criar**.

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>Configure seu perfil técnico da API REST para usar a autenticação do certificado do cliente

Depois de criar a chave necessária, configure os metadados de perfil técnico da API REST para referenciar o certificado do cliente.

1. No seu diretório de trabalho, abra o arquivo de política de extensão (TrustFrameworkExtensions.xml).
1. Procure o perfil técnico da API REST. Por exemplo `REST-ValidateProfile`, ou `REST-GetProfile`.
1. Localize o elemento `<Metadata>`.
1. Alterar o Tipo `ClientCertificate`de *autenticação* para .
1. Alterar o *AllowInsecureAuthInProduction* para `false`.
1. Adicione o seguinte snippet de código XML logo após fechar o elemento `</Metadata>`:
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

A seguir, um exemplo de um perfil técnico RESTful configurado com um certificado de cliente HTTP:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ClientCertificate</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="oauth2-bearer-authentication"></a>Autenticação do portador OAuth2 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

A autenticação do token do portador é definida no [OAuth2.0 Authorization Framework: Bearer Token Use (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). Na autenticação do token do portador, o Azure AD B2C envia uma solicitação HTTP com um token no cabeçalho de autorização.

```http
Authorization: Bearer <token>
```

Um símbolo de portador é uma corda opaca. Pode ser um token de acesso JWT ou qualquer string que a API REST espera que o Azure AD B2C envie o cabeçalho de autorização. O Azure AD B2C suporta os seguintes tipos:

- **Token do portador**. Para poder enviar o token do portador no perfil técnico Restful, sua política precisa primeiro adquirir o token do portador e, em seguida, usá-lo no perfil técnico RESTful.  
- **Token de portador estático**. Use essa abordagem quando sua API REST emitir um token de acesso de longo prazo. Para usar um token portador estático, crie uma chave de política e faça uma referência do perfil técnico RESTful à sua chave de política. 


## <a name="using-oauth2-bearer"></a>Usando o Portador OAuth2  

As etapas a seguir demonstram como usar as credenciais do cliente para obter um token portador e passá-lo para o cabeçalho de autorização das chamadas da API REST.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>Defina uma reivindicação para armazenar o token do portador

Uma reclamação fornece armazenamento temporário de dados durante a execução de uma diretiva Azure AD B2C. O [esquema de sinistros](claimsschema.md) é o lugar onde você declara suas reivindicações. O token de acesso deve ser armazenado em uma reivindicação a ser usada posteriormente. 

1. Abra o arquivo de extensões da sua apólice. Por exemplo, <em> `SocialAndLocalAccounts/` </em>.
1. Pesquise o elemento [BuildingBlocks](buildingblocks.md). Se o elemento não existir, adicione-o.
1. Localize o elemento [ClaimsSchema.](claimsschema.md) Se o elemento não existir, adicione-o.
1. Adicione o portador da cidadeToken ao elemento **ClaimsSchema.**  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>Adquirindo um token de acesso 

Você pode obter um token de acesso de várias maneiras: obtendo-o [de um provedor de identidade federado,](idp-pass-through-custom.md)ligando para uma API REST que retorna um token de acesso, usando um fluxo [ROPC](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc)ou usando o [fluxo de credenciais](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)do cliente.  

O exemplo a seguir usa um perfil técnico da API REST para fazer uma solicitação ao ponto final do token Azure AD usando as credenciais do cliente passadas como autenticação básica HTTP. Para configurar isso no Azure AD, consulte [a plataforma de identidade da Microsoft e o fluxo de credenciais do cliente OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow). Você pode precisar modificar isso para interagir com seu Provedor de Identidade. 

Para o ServiceUrl, substitua o nome do seu inquilino pelo nome do seu inquilino Azure AD. Consulte a referência de [perfil técnico RESTful](restful-technical-profile.md) para todas as opções disponíveis.

```xml
<TechnicalProfile Id="SecureREST-AccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.microsoft.com/oauth2/v2.0/token</Item>
    <Item Key="AuthenticationType">Basic</Item>
     <Item Key="SendClaimsIn">Form</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_SecureRESTClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_SecureRESTClientSecret" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="client_credentials" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://secureb2cfunction.azurewebsites.net/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>Alterar o perfil técnico REST para usar a autenticação do token do portador

Para suportar a autenticação do token do portador em sua política personalizada, modifique o perfil técnico da API REST com o seguinte:

1. No diretório de trabalho, abra o arquivo de política de extensão *TrustFrameworkExtensions.xml*.
1. Pesquise o nó `<TechnicalProfile>` que inclui `Id="REST-API-SignUp"`.
1. Localize o elemento `<Metadata>`.
1. Alterar o *Tipo de Autenticação* para *Portador,* da seguinte forma:
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. Alterar ou adicionar o *UseClaimAsBearerToken* ao *bearerToken*, da seguinte forma. O *portadorToken* é o nome da alegação de que o token `SecureREST-AccessToken`do portador será recuperado (a reivindicação de saída de ).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. Certifique-se de adicionar a reivindicação usada acima como uma reivindicação de entrada:

    ```xml
    <InputClaim ClaimTyeReferenceId="bearerToken"/>
    ```    

Depois de adicionar os trechos acima, seu perfil técnico deve parecer o seguinte código XML:

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="UseClaimAsBearerToken">bearerToken</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTyeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>Usando um portador oauth2 estático 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>Adicione a chave de diretiva do portador OAuth2

Crie uma chave de política para armazenar o valor do token do portador.

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Certifique-se de que está usando o diretório que contém seu inquilino Azure AD B2C. Selecione o filtro **de assinatura Diretório +** no menu superior e escolha o diretório Azure AD B2C.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
1. Selecione **Chaves de diretiva**e selecione **Adicionar**.
1. Para **Opções**, escolha `Manual`.
1. Insira um **Nome** para a chave de política. Por exemplo, `RestApiBearerToken`. O prefixo `B2C_1A_` será adicionado automaticamente ao nome da chave.
1. Em **Segredo**, insira o segredo do cliente que você registrou anteriormente.
1. Para **Uso de chave**, selecione `Encryption`.
1. Selecione **Criar**.

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>Configure o perfil técnico da API REST para usar a chave de diretiva do portador

Depois de criar a chave necessária, configure os metadados de perfil técnico da API REST para referenciar o token do portador.

1. No seu diretório de trabalho, abra o arquivo de política de extensão (TrustFrameworkExtensions.xml).
1. Procure o perfil técnico da API REST. Por exemplo `REST-ValidateProfile`, ou `REST-GetProfile`.
1. Localize o elemento `<Metadata>`.
1. Alterar o Tipo `Bearer`de *autenticação* para .
1. Alterar o *AllowInsecureAuthInProduction* para `false`.
1. Adicione o seguinte snippet de código XML logo após fechar o elemento `</Metadata>`:
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

A seguir, um exemplo de um perfil técnico RESTful configurado com autenticação de token do portador:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o elemento [perfil técnico Restful](restful-technical-profile.md) na referência do IEF. 
