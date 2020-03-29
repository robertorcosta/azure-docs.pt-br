---
title: Verificações personalizadas de e-mail
titleSuffix: Azure AD B2C
description: Saiba como personalizar o e-mail de verificação enviado aos seus clientes quando eles se inscreverem para usar seus aplicativos habilitados para Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6cc0508a63f26b955ac5e0ebf3ef58a184a35997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671640"
---
# <a name="custom-email-verification-in-azure-active-directory-b2c"></a>Verificação personalizada de e-mail no Azure Active Directory B2C

Use e-mails personalizados no Azure Active Directory B2C (Azure AD B2C) para enviar e-mails personalizados aos usuários que se cadasitem para usar seus aplicativos. Usando [DisplayControls](display-controls.md) (atualmente em visualização) e um provedor de e-mail de terceiros, você pode usar seu próprio modelo de e-mail e *De:* endereço e assunto, bem como suporte às configurações de localização e senha única personalizada (OTP).

A verificação de e-mail personalizada requer o uso de um provedor de e-mail de terceiros como [sendgrid](https://sendgrid.com) ou [SparkPost,](https://sparkpost.com)uma API rest personalizada ou qualquer provedor de e-mail baseado em HTTP (incluindo o seu próprio). Este artigo descreve a configuração de uma solução que usa sendgrid.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-sendgrid-account"></a>Criar uma conta do SendGrid

Se você ainda não tiver um, comece configurando uma conta sendgrid (os clientes do Azure podem desbloquear 25.000 e-mails gratuitos por mês). Para obter instruções de configuração, consulte a [seção Criar uma conta do SendGrid](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account) de [Como enviar e-mails usando o SendGrid com o Azure](../sendgrid-dotnet-how-to-send-email.md).

Certifique-se de completar a seção na qual você [cria uma chave API SendGrid](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key). Grave a tecla API para uso posteriormente.

## <a name="create-azure-ad-b2c-policy-key"></a>Criar a chave de política Ad B2C do Azure

Em seguida, armazene a chave API do SendGrid em uma chave de diretiva Azure AD B2C para que suas políticas sejam referenciadas.

1. Faça login no [portal Azure](https://portal.azure.com/).
1. Certifique-se de que está usando o diretório que contém seu inquilino Azure AD B2C. Selecione o filtro **de assinatura Diretório +** no menu superior e escolha o diretório Azure AD B2C.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
1. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
1. Para **Opções**, escolha `Manual`.
1. Insira um **Nome** para a chave de política. Por exemplo, `SendGridSecret`. O prefixo `B2C_1A_` será adicionado automaticamente ao nome da chave.
1. Em **Segredo**, insira o segredo do cliente que você registrou anteriormente.
1. Para **Uso de chave**, selecione `Signature`.
1. Selecione **Criar**.

## <a name="create-sendgrid-template"></a>Criar modelo SendGrid

Com uma conta SendGrid criada e uma chave API do SendGrid armazenada em uma chave de diretiva Azure AD B2C, crie um [modelo de transação dinâmico sendGrid](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/).

1. No site sendgrid, abra a página [de modelos transacionais](https://sendgrid.com/dynamic_templates) e selecione **Criar modelo**.
1. Digite um nome `Verification email` de modelo exclusivo como e, em seguida, **selecione Salvar**.
1. Para começar a editar seu novo modelo, **selecione Adicionar versão**.
1. Selecione **Editor de Código** e, em seguida, **Continue**.
1. No editor HTML, cole seguindo o modelo HTML ou use o seu próprio. Os `{{otp}}` `{{email}}` parâmetros e parâmetros serão substituídos dinamicamente pelo valor de senha única e pelo endereço de e-mail do usuário.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <!-- Template B O365 -->
       <style>
           table td {border-collapse:collapse;margin:0;padding:0;}
       </style>
    </head>
    <body dir="ltr" lang="en">
       <table width="100%" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
            <tr>
               <td valign="top" width="50%"></td>
               <td valign="top">
                  <!-- Email Header -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en" style="border-left:1px solid #e3e3e3;border-right: 1px solid #e3e3e3;">
                   <tr style="background-color: #0072C6;">
                       <td width="1" style="background:#0072C6; border-top:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-top:1px solid #e3e3e3;border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="310" valign="middle" style="border-top:1px solid #e3e3e3; border-bottom:1px solid #e3e3e3;padding:12px 0;">
                           <h1 style="line-height:20pt;font-family:Segoe UI Light; font-size:18pt; color:#ffffff; font-weight:normal;">
                            <span id="HeaderPlaceholder_UserVerificationEmailHeader"><font color="#FFFFFF">Verify your email address</font></span>
                           </h1>
                       </td>
                       <td width="24" style="border-top: 1px solid #e3e3e3;border-bottom: 1px solid #e3e3e3;">&nbsp;</td>
                   </tr>
                  </table>
                  <!-- Email Content -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
                   <tr>
                       <td width="1" style="background:#e3e3e3;"></td>
                       <td width="24">&nbsp;</td>
                       <td id="PageBody" width="640" valign="top" colspan="2" style="border-bottom:1px solid #e3e3e3;padding:10px 0 20px;border-bottom-style:hidden;">
                           <table cellpadding="0" cellspacing="0" border="0">
                               <tr>
                                   <td width="630" style="font-size:10pt; line-height:13pt; color:#000;">
                                       <table cellpadding="0" cellspacing="0" border="0" width="100%" style="" dir="ltr" lang="en">
                                           <tr>
                                               <td>

       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{email}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{otp}}</span>
       </div>
       <br>
       <br>

                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
                                                   Sincerely,
                                                   </div>
                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; font-style:italic; color:#333;">
                                                       Contoso
                                                   </div>
                                               </td>
                                           </tr>
                                       </table>
                                   </td>
                               </tr>
                           </table>

                       </td>

                       <td width="1">&nbsp;</td>
                       <td width="1"></td>
                       <td width="1">&nbsp;</td>
                       <td width="1" valign="top"></td>
                       <td width="29">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3;"></td>
                   </tr>
                   <tr>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td id="PageFooterContainer" width="585" valign="top" colspan="6" style="border-bottom:1px solid #e3e3e3;padding:0px;">


                       </td>

                       <td width="29" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                   </tr>
                  </table>

               </td>
               <td valign="top" width="50%"></td>
           </tr>
       </table>
    <img src="https://mucp.api.account.microsoft.com/m/v2/v?d=AIAACWEPFYXYIUTJIJVV4ST7XLBHVI5MLLYBKJAVXHBDTBHUM5VBSVVPTTVRWDFIXJ5JQTHYOH5TUYIPO4ZAFRFK52UAMIS3UNIPPI7ZJNDZPRXD5VEJBN4H6RO3SPTBS6AJEEAJOUYL4APQX5RJUJOWGPKUABY&amp;i=AIAACL23GD2PFRFEY5YVM2XQLM5YYWMHFDZOCDXUI2B4LM7ETZQO473CVF22PT6WPGR5IIE6TCS6VGEKO5OZIONJWCDMRKWQQVNP5VBYAINF3S7STKYOVDJ4JF2XEW4QQVNHMAPQNHFV3KMR3V3BA4I36B6BO7L4VQUHQOI64EOWPLMG5RB3SIMEDEHPILXTF73ZYD3JT6MYOLAZJG7PJJCAXCZCQOEFVH5VCW2KBQOKRYISWQLRWAT7IINZ3EFGQI2CY2EMK3FQOXM7UI3R7CZ6D73IKDI" width="1" height="1"></body>
    </html>
    ```

1. Expanda **as configurações** à esquerda e `{{subject}}`para o assunto de **e-mail,** digite .
1. Selecione **'Salvar modelo'.**
1. Retorne à página **Modelos transacionais** selecionando a seta traseira.
1. Registre o **ID** do modelo que você criou para uso em uma etapa posterior. Por exemplo, `d-989077fbba9746e89f3f6411f596fb96`. Você especifica este ID ao [adicionar a transformação de sinistros](#add-the-claims-transformation).

## <a name="add-azure-ad-b2c-claim-types"></a>Adicionar tipos de reivindicação Azure AD B2C

Em sua política, adicione os `<ClaimsSchema>` seguintes `<BuildingBlocks>`tipos de reivindicação ao elemento dentro .

Esses tipos de sinistros são necessários para gerar e verificar o endereço de e-mail usando um código de senha única (OTP).

```XML
<ClaimType Id="Otp">
  <DisplayName>Secondary One-time password</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="sendGridReqBody">
  <DisplayName>SendGrid request body</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="VerificationCode">
  <DisplayName>Secondary Verification Code</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter your email verification code</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

## <a name="add-the-claims-transformation"></a>Adicione a transformação de sinistros

Em seguida, você precisa de uma transformação de reivindicações para produzir uma reclamação de string JSON que será o corpo da solicitação enviada ao SendGrid.

A estrutura do objeto JSON é definida pelos IDs na notação de nota dos Parâmetros de Entrada e dos Tipos de Declaração de Transformação das Alegações de Entrada. Os números na notação de dot implicam matrizes. Os valores vêm dos valores do InputClaims e das propriedades "Valor" dos InputParameters. Para obter mais informações sobre as transformações de sinistros json, consulte [as transformações de reivindicações json](json-transformations.md).

Adicione a seguinte transformação de reivindicações ao `<ClaimsTransformations>` elemento dentro `<BuildingBlocks>`. Faça as seguintes atualizações para a transformação de sinistros XML:

* Atualize `template_id` o valor do Parâmetro de Entrada com o ID do modelo transacional SendGrid que você criou anteriormente no [modelo Create SendGrid](#create-sendgrid-template).
* Atualize `from.email` o valor do endereço. Use um endereço de e-mail válido para ajudar a evitar que o e-mail de verificação seja marcado como spam.
* Atualize o `personalizations.0.dynamic_template_data.subject` valor do parâmetro de entrada da linha de assunto com uma linha de assunto apropriada para sua organização.

```XML
<ClaimsTransformation Id="GenerateSendGridRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
  </InputClaims>
  <InputParameters>
    <!-- Update the template_id value with the ID of your SendGrid template. -->
    <InputParameter Id="template_id" DataType="string" Value="d-989077fbba9746e89f3f6411f596fb96"/>
    <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com"/>
    <!-- Update with a subject line appropriate for your organization. -->
    <InputParameter Id="personalizations.0.dynamic_template_data.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="sendGridReqBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="add-datauri-content-definition"></a>Adicionar definição de conteúdo DataUri

Abaixo as transformações `<BuildingBlocks>`de sinistros dentro, adicione o seguinte [ContentDefinition](contentdefinitions.md) para referenciar o URI de dados da versão 2.0.0:

```XML
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>Criar um DisplayControl

Um controle de exibição de verificação é usado para verificar o endereço de e-mail com um código de verificação enviado ao usuário.

Este exemplo de controle de exibição está configurado para:

1. Colete `email` o tipo de solicitação de endereço do usuário.
1. Aguarde que o `verificationCode` usuário forneça o tipo de solicitação com o código enviado ao usuário.
1. Retorne `email` a volta ao perfil técnico auto-afirmado que tem uma referência a este controle de exibição.
1. Usando `SendCode` a ação, gere um código OTP e envie um e-mail com o código OTP para o usuário.

![Enviar ação de e-mail de código de verificação](media/custom-email/display-control-verification-email-action-01.png)

Em definições de `<BuildingBlocks>`conteúdo, ainda dentro, adicione o seguinte [DisplayControl](display-controls.md) do tipo [VerificationControl](display-control-verification.md) à sua diretiva.

```XML
<DisplayControls>
  <DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
    <DisplayClaims>
      <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
      <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
    </DisplayClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="email" />
    </OutputClaims>
    <Actions>
      <Action Id="SendCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
        </ValidationClaimsExchange>
      </Action>
      <Action Id="VerifyCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
        </ValidationClaimsExchange>
      </Action>
    </Actions>
  </DisplayControl>
</DisplayControls>
```

## <a name="add-otp-technical-profiles"></a>Adicionar perfis técnicos OTP

O `GenerateOtp` perfil técnico gera um código para o endereço de e-mail. O `VerifyOtp` perfil técnico verifica o código associado ao endereço de e-mail. Você pode alterar a configuração do formato e o vencimento da senha única. Para obter mais informações sobre perfis técnicos OTP, consulte [Definir um perfil técnico de senha única](one-time-password-technical-profile.md).

Adicione os seguintes perfis `<ClaimsProviders>` técnicos ao elemento.

```XML
<ClaimsProvider>
  <DisplayName>One time password technical profiles</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="GenerateOtp">
      <DisplayName>Generate one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">GenerateCode</Item>
        <Item Key="CodeExpirationInSeconds">1200</Item>
        <Item Key="CodeLength">6</Item>
        <Item Key="CharacterSet">0-9</Item>
        <Item Key="ReuseSameCode">true</Item>
        <Item Key="MaxNumAttempts">5</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="otp" PartnerClaimType="otpGenerated" />
      </OutputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="VerifyOtp">
      <DisplayName>Verify one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">VerifyCode</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
      </InputClaims>
    </TechnicalProfile>
   </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="add-a-rest-api-technical-profile"></a>Adicionar um perfil técnico da API REST

Este perfil técnico da API REST gera o conteúdo do e-mail (usando o formato SendGrid). Para obter mais informações sobre perfis técnicos RESTful, consulte [Definir um perfil técnico RESTful](restful-technical-profile.md).

Assim como nos perfis técnicos oTP, adicione `<ClaimsProviders>` os seguintes perfis técnicos ao elemento.

```XML
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SendGrid">
      <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridSecret" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>Faça uma referência ao DisplayControl

Na etapa final, adicione uma referência ao DisplayControl que você criou. Substitua seu `LocalAccountSignUpWithLogonEmail` perfil técnico auto-afirmado existente pelo seguinte se você usou uma versão anterior da diretiva Azure AD B2C. Este perfil `DisplayClaims` técnico é usa com uma referência ao DisplayControl.

Para obter mais informações, consulte [perfil técnico auto-afirmado](restful-technical-profile.md) e [DisplayControl](display-controls.md).

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <DisplayName>Email signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
        <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
        <Item Key="language.button_continue">Create</Item>
        
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" Required="true" />
        <OutputClaim ClaimTypeReferenceId="objectId" />
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" />
        <OutputClaim ClaimTypeReferenceId="newUser" />
      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
      </ValidationTechnicalProfiles>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="optional-localize-your-email"></a>[Opcional] Localize seu e-mail

Para localizar o e-mail, você deve enviar strings localizadas para o SendGrid ou para o seu provedor de e-mail. Por exemplo, para localizar o assunto de e-mail, corpo, sua mensagem de código ou assinatura do e-mail. Para isso, você pode usar a transformação de reivindicações [GetLocalizedStringsTransformation](string-transformations.md) para copiar strings localizadas em tipos de sinistros. Na `GenerateSendGridRequestBody` transformação de sinistros, que gera a carga útil JSON, utiliza-se reivindicações de entrada que contêm as strings localizadas.

1. Em sua política, defina as seguintes alegações de seqüência: assunto, mensagem, codeIntro e assinatura.
1. Defina uma [transformação de reivindicações GetLocalizedStringspara](string-transformations.md) substituir valores de seqüência localizados nas reivindicações da etapa 1.
1. Alterar `GenerateSendGridRequestBody` a transformação de sinistros para usar reivindicações de entrada com o seguinte trecho XML.
1. Atualize seu modelo SendGrind para usar parâmetros dinâmicos no lugar de todas as strings que serão localizadas pelo Azure AD B2C.

```XML
<ClaimsTransformation Id="GenerateSendGridRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="personalizations.0.dynamic_template_data.subject" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
    <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="personalizations.0.dynamic_template_data.message" />
    <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="personalizations.0.dynamic_template_data.codeIntro" />
    <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="personalizations.0.dynamic_template_data.signature" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="template_id" DataType="string" Value="d-1234567890" />
    <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="sendGridReqBody" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="next-steps"></a>Próximas etapas

Você pode encontrar um exemplo de uma política de verificação de e-mail personalizada no GitHub:

[Verificação personalizada de e-mail - DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)

Para obter informações sobre o uso de uma API REST personalizada ou qualquer provedor de e-mail SMTP baseado em HTTP, consulte [Definir um perfil técnico RESTful em uma política personalizada Azure AD B2C](restful-technical-profile.md).
