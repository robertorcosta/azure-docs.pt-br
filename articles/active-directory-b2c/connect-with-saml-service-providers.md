---
title: Configure o Azure AD B2C como um IdP SAML para seus aplicativos
title-suffix: Azure AD B2C
description: Como configurar o Azure AD B2C para fornecer afirmações de protocolo SAML aos seus aplicativos (provedores de serviços). O Azure AD B2C atuará como o provedor de identidade único (IdP) para o seu aplicativo SAML.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: a72b5b50daaae33336de9caab5202c2bf42f5c15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051615"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Registre um aplicativo SAML no Azure AD B2C

Neste artigo, você aprende como configurar o Azure Active Directory B2C (Azure AD B2C) para atuar como um provedor de identidade (SAML) (Security Assertion Markup Language) (IdP) para seus aplicativos.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="scenario-overview"></a>Visão geral do cenário

As organizações que usam o Azure AD B2C como sua solução de gerenciamento de identidade e acesso ao cliente podem exigir interação com provedores de identidade ou aplicativos configurados para autenticar usando o protocolo SAML.

O Azure AD B2C alcança a interoperabilidade SAML de duas maneiras:

* Atuando como um *provedor de identidade* (IdP) e alcançando o SSO (Single-Sign-On) com provedores de serviços baseados em SAML (seus aplicativos)
* Atuando como um *provedor de serviços* (SP) e interagindo com provedores de identidade baseados em SAML, como Salesforce e ADFS

![Diagrama com B2C como provedor de identidade à esquerda e B2C como provedor de serviços à direita](media/saml-identity-provider/saml-idp-diagram-01.jpg)

Resumindo os dois cenários centrais não exclusivos com o SAML:

| Cenário | Papel Azure AD B2C | Como fazer |
| -------- | ----------------- | ------- |
| Meu aplicativo espera que uma afirmação SAML complete uma autenticação. | **O Azure AD B2C atua como provedor de identidade (IdP)**<br />O Azure AD B2C atua como um IdP SAML para os aplicativos. | Este artigo. |
| Meus usuários precisam de um único login com um provedor de identidade compatível com SAML, como ADFS, Salesforce ou Shibboleth.  | **Azure AD B2C atua como provedor de serviços (SP)**<br />O Azure AD B2C atua como um provedor de serviços ao se conectar ao provedor de identidade SAML. É um proxy da federação entre seu aplicativo e o provedor de identidade SAML.  | <ul><li>[Configure o login com o ADFS como um IdP SAML usando políticas personalizadas](identity-provider-adfs2016-custom.md)</li><li>[Configure o login com um provedor SAML da Salesforce usando políticas personalizadas](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>Pré-requisitos

* Complete as [etapas em Iniciar com políticas personalizadas no Azure AD B2C](custom-policy-get-started.md). Você precisa da política personalizada *SocialAndLocalAccounts* do pacote de inicialização de políticas personalizadas discutida no artigo.
* Compreensão básica do protocolo SAML (Security Assertion Markup Language).
* Um aplicativo web configurado como um provedor de serviços SAML (SP). Para este tutorial, você pode usar um [aplicativo de teste SAML][samltest] que fornecemos.

## <a name="components-of-the-solution"></a>Componentes da solução

Há três componentes principais necessários para este cenário:

* O **provedor de serviços** SAML com a capacidade de enviar solicitações SAML e receber, decodificar e responder às afirmações saml do Azure AD B2C. Isso também é conhecido como o partido que depende.
* Ponto final de metadados SAML disponível **publicamente** para seu provedor de serviços.
* [Locatário do Azure AD B2C](tutorial-create-tenant.md)

Se você ainda não tem um provedor de serviços SAML e um ponto final de metadados associado, você pode usar este aplicativo SAML de amostra que disponibilizamos para testes:

[Aplicação de teste SAML][samltest]

## <a name="1-set-up-certificates"></a>1. Configurar certificados

Para construir uma relação de confiança entre seu provedor de serviços e o Azure AD B2C, você precisa fornecer os certificados X509 do aplicativo web.

* **Certificados de prestadores de serviços**
  * Certificado com uma chave privada armazenada em seu Web App. Este certificado é usado pelo seu provedor de serviços para assinar a solicitação SAML enviada ao Azure AD B2C. O Azure AD B2C lê a chave pública dos metadados do provedor de serviços para validar a assinatura.
  * (Opcional) Certificado com uma chave privada armazenada em seu Web App. O Azure AD B2C lê a chave pública dos metadados do provedor de serviços para criptografar a afirmação do SAML. Em seguida, o provedor de serviços usa a chave privada para descriptografar a afirmação.
* **Certificados Azure AD B2C**
  * Certificado com uma chave privada no Azure AD B2C. Este certificado é usado pelo Azure AD B2C para assinar a resposta SAML enviada ao seu provedor de serviços. Seu provedor de serviços lê a chave pública de metadados Ad B2C do Azure para validar a assinatura da resposta SAML.

Você pode usar um certificado emitido por uma autoridade de certificado público ou, para este tutorial, um certificado auto-assinado.

### <a name="11-prepare-a-self-signed-certificate"></a>1.1 Prepare um certificado auto-assinado

Se você ainda não tem um certificado, você pode usar um certificado auto-assinado para este tutorial. No Windows, você pode usar o cmdlet [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) da PowerShell para gerar um certificado.

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

1. **Abrir gerencie certificados de usuário** > **Certificados** > **pessoais do** > **usuário** > atual*yourappname.yourtenant.onmicrosoft.com*
1. Selecione o certificado > **Ação Todas** > **as Tarefas** > **Exportar**
1. Selecione **Sim** > **Próximo** > **Sim, exporte a chave** > privada**seguinte**
1. Aceite os padrões para **formato de arquivo de exportação**
1. Forneça uma senha para o certificado

### <a name="12-upload-the-certificate"></a>1.2 Faça upload do certificado

Em seguida, carregue o certificado de assinatura de declaração e resposta do SAML para o Azure AD B2C.

1. Faça login no [portal Azure](https://portal.azure.com) e navegue até o seu inquilino Azure AD B2C.
1. Em **Políticas,** selecione **Identity Experience Framework** e, em seguida, chaves de **diretiva**.
1. Selecione **Adicionar**e, em seguida, selecione > **'Enviar'.** **Options**
1. Digite um **Nome,** por *exemplo, SamlIdpCert*. O prefixo *B2C_1A_* será adicionado automaticamente ao nome da chave.
1. Faça upload do seu certificado usando o controle de arquivo de upload.
1. Digite a senha do certificado.
1. Selecione **Criar**.
1. Verifique se a chave aparece como esperado. Por exemplo, *B2C_1A_SamlIdpCert.*

## <a name="2-prepare-your-policy"></a>2. Prepare sua apólice

### <a name="21-create-the-saml-token-issuer"></a>2.1 Criar o emissor de token SAML

Agora, adicione o recurso para o seu inquilino emitir tokens SAML, usando [o emissor de token SAML](saml-issuer-technical-profile.md) e perfis técnicos [do provedor de sessão SAML.](custom-policy-reference-sso.md#samlssosessionprovider)

Abra `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** no pacote de partida de apólices personalizadas.

Localize `<ClaimsProviders>` a seção e adicione o seguinte trecho XML.

Você pode alterar o `IssuerUri` valor dos metadados. Este é o uri emissor que é devolvido na resposta SAML do Azure AD B2C. Seu aplicativo de parte de confiar deve ser configurado para aceitar um URI emissor durante a validação de afirmação SAML.

```XML
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="None"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <!-- The issuer contains the policy name; it should be the same name as configured in the relying party application. B2C_1A_signup_signin_SAML is used below. -->
        <!--<Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>-->
      </Metadata>
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-sp"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-sp">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="3-add-the-saml-relying-party-policy"></a>3. Adicione a política partidária que depende do SAML

Agora que seu inquilino pode emitir afirmações saml, você precisa criar a política de participação do SAML e modificar a jornada do usuário para que ele emita uma afirmação SAML em vez de um JWT.

### <a name="31-create-sign-up-or-sign-in-policy"></a>3.1 Crie política de inscrição ou de login

1. Crie uma cópia do arquivo *SignUpOrSignin.xml* no diretório de trabalho do pacote inicial e salve-o com um novo nome. Por exemplo, *SignUpOrSigninSAML.xml*. Este é o seu arquivo de política partidária.

1. Abra o arquivo *SignUpOrSigninSAML.xml* em seu editor preferido.

1. Mude `PolicyId` a `PublicPolicyUri` política para _B2C_1A_signup_signin_saml_ B2C_1A_signup_signin_saml `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml` e como visto abaixo.

    ```XML
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. Adicione o seguinte trecho XML `<RelyingParty>` pouco antes do elemento. Este XML substitui a etapa de orquestração número 7 da jornada do usuário _SignUpOrSignIn._ Se você começou a partir de uma pasta diferente no pacote inicial, ou personalizou sua jornada `order` do usuário adicionando ou removendo etapas de orquestração, certifique-se de que o número (no elemento) `LocalAccounts`esteja alinhado `SocialAccounts` com `SocialAndLocalAccountsWithMfa`o especificado na jornada do usuário para a etapa de emissor de token (por exemplo, nas outras pastas do pacote inicial é o passo número 4 para , 6 para e 9 para ).

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. Substitua `<TechnicalProfile>` todo o `<RelyingParty>` elemento no elemento pelo seguinte perfil técnico XML.

    ```XML
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
    ```

1. Atualize `tenant-name` com o nome do seu inquilino Azure AD B2C.

Seu arquivo final de política partidária deve parecer o seguinte:

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

### <a name="32-upload-and-test-your-policy-metadata"></a>3.2 Faça upload e teste seus metadados de política

Salve suas alterações e faça upload do novo arquivo de diretiva. Depois de carregar ambas as políticas (a extensão e os arquivos de parte sustiva), abra um navegador da Web e navegue até os metadados de diretiva.

Metadados de iDP de política Ad AD AD AD É uma informação usada no protocolo SAML para expor a configuração de um provedor de identidade SAML. Metadados definem o local dos serviços, como entrada e saída, certificados, método de entrada e muito mais. Os metadados da diretiva Azure AD B2C estão disponíveis na SEGUINTE URL. Substitua pelo `tenant-name` nome do seu inquilino Azure `policy-name` AD B2C e pelo nome (ID) da política:

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

Sua política personalizada e o inquilino Azure AD B2C estão agora prontos. Em seguida, crie um registro de aplicativo no Azure AD B2C.

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. Aplicativo de configuração no Diretório AD B2C do Azure

### <a name="41-register-your-application-in-azure-active-directory"></a>4.1 Registre sua aplicação no Azure Active Directory

1. Faça login no [portal Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Escolha **Registros de Aplicativo (versão prévia)** e depois selecione **Novo Registro**.
1. Insira um **Nome** para o aplicativo. Por exemplo, *SAMLApp1*.
1. Em **tipos de conta suportados,** selecione Contas neste diretório organizacional **apenas**
1. Em **Redirecionar URI,** selecione `https://localhost` **Web**e, em seguida, digite . Você modifica esse valor mais tarde no manifesto do registro do aplicativo.
1. Selecione **Registrar**.

### <a name="42-update-the-app-manifest"></a>4.2 Atualize o manifesto do aplicativo

Para aplicativos SAML, existem várias propriedades que você precisa configurar no manifesto do registro do aplicativo.

1. No [portal Azure,](https://portal.azure.com)navegue até o registro de aplicativos que você criou na seção anterior.
1. Em **Gerenciar,** **selecione Manifest** para abrir o editor do manifesto. Você modifica várias propriedades nas seções a seguir.

#### <a name="identifieruris"></a>identifierUris

A `identifierUris` coleção de strings contém URI(s) definidos pelo usuário que identificam exclusivamente um aplicativo da Web dentro de seu inquilino Azure AD B2C. Seu provedor de serviços `Issuer` deve definir esse valor no elemento de uma solicitação SAML.

#### <a name="samlmetadataurl"></a>samlMetadataUrl

Esta propriedade representa a URL de metadados disponível publicamente do provedor de serviços. A URL de metadados pode apontar para um arquivo de metadados carregado em qualquer ponto final acessível anonimamente, por exemplo, o armazenamento blob.

Os metadados são informações usadas no protocolo SAML para expor a configuração de uma parte SAML, como um provedor de serviços. Metadados define a localização dos serviços como login e login, certificados, método de login e muito mais. O Azure AD B2C lê os metadados do provedor de serviços e age de acordo. Os metadados não são necessários. Você também pode especificar alguns atributos como o URI de resposta e uri de logout diretamente no manifesto do aplicativo.

Se houver propriedades especificadas *tanto* na URL de metadados SAML quanto no manifesto do registro do aplicativo, elas serão **mescladas**. As propriedades especificadas na URL de metadados são processadas primeiro e têm precedência.

Para este tutorial, que usa o aplicativo de teste `samlMetadataUrl`SAML, use o seguinte valor para :

```JSON
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>UrlurlsWithType (Opcional)

Se você não fornecer um URI de metadados, você pode especificar explicitamente a URL de resposta. Esta propriedade opcional `AssertionConsumerServiceUrl` representa`SingleSignOnService` a URL nos metadados `BindingType` do provedor `HTTP POST`de serviços e a que se presume ser .

Se você optar por configurar a URL de resposta e a URL de logout no manifesto do aplicativo sem usar os metadados do provedor de serviços, o Azure AD B2C não validará a assinatura de solicitação SAML nem criptografará a resposta SAML.

Para este tutorial, no qual você usa o `url` aplicativo `replyUrlsWithType` de teste SAML, defina a propriedade do valor mostrado no trecho JSON a seguir.

```JSON
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>logoutUrl (opcional)

Esta propriedade opcional `Logout` representa`SingleLogoutService` a URL ( URL nos metadados de parte que dependem), e o `BindingType` para isso é assumido como sendo `Http-Redirect`.

Para este tutorial, que usa o aplicativo `logoutUrl` de `https://samltestapp2.azurewebsites.net/logout`teste SAML, deixe definido como :

```JSON
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. Atualize o código do aplicativo

O último passo é habilitar o Azure AD B2C como um IdP SAML em seu aplicativo de festa de confiar no SAML. Cada aplicação é diferente e as etapas para fazê-lo variam. Consulte a documentação do seu aplicativo para obter detalhes.

Alguns ou todos os seguintes são normalmente necessários:

* **Metadados:**`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **Emissor**: Use a entidadeID no arquivo de metadados
* **Url de login/ponto final SAML/Url SAML**: Verifique o valor no arquivo de metadados
* **Certificado**: Este é *B2C_1A_SamlIdpCert,* mas sem a chave privada. Para obter a chave pública do certificado:

    1. Vá para a URL de metadados especificada acima.
    1. Copie o valor `<X509Certificate>` no elemento.
    1. Cole-o em um arquivo de texto.
    1. Salve o arquivo de texto como um arquivo *.cer.*

### <a name="51-test-with-the-saml-test-app-optional"></a>5.1 Teste com o aplicativo de teste SAML (opcional)

Para completar este tutorial usando nosso [aplicativo de teste SAML][samltest]:

* Atualize o nome do inquilino
* Atualizar o nome da política, por *exemplo, B2C_1A_signup_signin_saml*
* Especifique este uri emissor:`https://contoso.onmicrosoft.com/app-name`

Selecione **Login** e você deve ser apresentado com uma tela de login do usuário. Ao fazer o login, uma afirmação SAML é emitida de volta ao aplicativo de amostra.

## <a name="sample-policy"></a>Política de exemplo

Fornecemos uma política de amostra completa que você pode usar para testes com o Aplicativo de Teste SAML.

1. Baixe a [política de amostra de login iniciada pelo SAML-SP](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)
1. Atualização `TenantId` para corresponder ao nome do inquilino, por *exemplo, contoso.b2clogin.com*
1. Mantenha o nome da política *de B2C_1A_SAML2_signup_signin*

## <a name="supported-and-unsupported-saml-modalities"></a>Modalidades SAML suportadas e sem suporte

Os seguintes cenários de RP (Relying Party) são suportados através do seu próprio ponto final de metadados:

* Vários URLs de logout ou vinculação POST para URL de logout no objeto principal do aplicativo/serviço.
* Especifique a chave de assinatura para verificar solicitações de RP no objeto principal do aplicativo/serviço.
* Especifique a chave de criptografia de token no objeto principal do aplicativo/serviço.
* Os logins iniciados pelo provedor de identidade não são suportados no momento na versão de pré-visualização.

## <a name="next-steps"></a>Próximas etapas

- Você pode encontrar mais informações sobre o [protocolo SAML no site do OASIS](https://www.oasis-open.org/).
- Obtenha o aplicativo web de teste SAML do [azure AD B2C GitHub repo da comunidade](https://github.com/azure-ad-b2c/saml-sp-tester).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
