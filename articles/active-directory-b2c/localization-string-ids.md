---
title: IDs de cadeias de caracteres de localização – Azure Active Directory B2C | Microsoft Docs
description: Especifique as IDs de uma definição de conteúdo com uma ID de api.signuporsignin em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3af62a75228959478a80c2628307fff2b47c3c4a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78187484"
---
# <a name="localization-string-ids"></a>IDs de cadeia de caracteres de localização

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O elemento **Localization** permite que você dê suporte a várias localidades ou idiomas na política para os percursos do usuário. Este artigo fornece uma lista de IDs de localização que você pode usar em sua política. Para se familiarizar com a localização da interface do usuário, confira [Localização](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Elementos das páginas de inscrição ou entrada

As IDs a seguir são usadas para uma definição de conteúdo com uma ID de `api.signuporsignin`.

| ID | Valor padrão |
| -- | ------------- |
| **local_intro_email** | Entre com sua conta existente |
| **logonIdentifier_email** | Endereço de Email |
| **requiredField_email** | Insira seu email |
| **invalid_email** | Insira um endereço de email válido |
| **email_pattern** | ^ [a-zA-Z0-9.! # $% & ' ' *+/=? ^ _\`{\|} ~-] + @ [a-zA-Z0-9-] + (?:\\. [ a-zA-Z0-9-] +)* $ |
| **local_intro_username** | Entre com seu nome de usuário |
| **logonIdentifier_username** | Nome de Usuário |
| **requiredField_username** | Insira seu nome de usuário |
| **password** | Senha |
| **requiredField_password** | Digite sua senha |
| **invalid_password** | A senha digitada não está no formato esperado. |
| **forgotpassword_link** | Esqueceu sua senha? |
| **createaccount_intro** | Não tem uma conta? |
| **createaccount_link** | Inscreva-se agora mesmo |
| **divider_title** | OU |
| **cancel_message** | O usuário esqueceu a senha |
| **button_signin** | Entrar |
| **social_intro** | Entre com sua conta de redes sociais |
  **remember_me** |Manter-me conectado|
| **unknown_error** | Estamos com problemas para conectá-lo. Tente novamente mais tarde. |

O exemplo a seguir mostra o uso de alguns dos elementos de interface do usuário na página de inscrição ou entrada:

![Elementos das páginas de inscrição ou entrada da experiência do usuário](./media/localization-string-ids/localization-susi.png)

A ID dos provedores de identidade é configurada no elemento **ClaimsExchange** do percurso do usuário. Para localizar o título do provedor de identidade, **ElementType** é definido como `ClaimsProvider`, enquanto **StringId** é definido como a ID do `ClaimsExchange`.

```XML
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

O exemplo a seguir localiza o provedor de identidade do Facebook para árabe:

```XML
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

### <a name="sign-up-or-sign-in-error-messages"></a>Mensagens de erro de entrada ou inscrição

| ID | Valor padrão |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | A senha está incorreta. |
| **UserMessageIfClaimsPrincipalDoesNotExist** | Não estamos encontrando a sua conta. |
| **UserMessageIfOldPasswordUsed** | Parece que você usou uma senha antiga. |
| **DefaultMessage** | Senha ou nome de usuário inválido. |
| **UserMessageIfUserAccountDisabled** | Sua conta foi bloqueada. Contate seu suporte para desbloqueá-la e tente novamente. |
| **UserMessageIfUserAccountLocked** | Sua conta está temporariamente bloqueada para impedir o uso não autorizado. Tente novamente depois. |
| **AADRequestsThrottled** | Há muitas solicitações no momento. Aguarde alguns instantes e tente novamente. |

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Elementos da interface do usuário de páginas autodeclaradas e de entrada

A seguir, estão as IDs de definição de conteúdo com a ID `api.localaccountsignup` ou qualquer definição de conteúdo que começa com `api.selfasserted`, como `api.selfasserted.profileupdate` e `api.localaccountpasswordreset`.

| ID | Valor padrão |
| -- | ------------- |
| **ver_sent** | O código de verificação foi enviado para: |
| **ver_but_default** | Padrão |
| **cancel_message** | O usuário cancelou a inserção de informações autodeclaradas |
| **preloader_alt** | Aguarde |
| **ver_but_send** | Enviar código de verificação |
| **alert_yes** | Sim |
| **error_fieldIncorrect** | Um ou mais campos estão preenchidos incorretamente. Verifique suas entradas e tente novamente. |
| **year** | Ano |
| **verifying_blurb** | Aguarde enquanto processamos suas informações. |
| **button_cancel** | Cancelar |
| **ver_fail_no_retry** | Você fez muitas tentativas incorretas. Tente novamente mais tarde. |
| **month** | Month |
| **ver_success_msg** | Endereço de email verificado. Agora, você pode continuar. |
| **months** | Janeiro, fevereiro, março, abril, maio, junho, julho, agosto, setembro, outubro, novembro, dezembro |
| **ver_fail_server** | Estamos com problemas para verificar seu endereço de email. Insira um endereço de email válido e tente novamente. |
| **error_requiredFieldMissing** | Um campo obrigatório está ausente. Preencha todos os campos obrigatórios e tente novamente. |
| **initial_intro** | Forneça os seguintes detalhes. |
| **ver_but_resend** | Enviar novo código |
| **button_continue** | Criar |
| **error_passwordEntryMismatch** | Os campos de entrada de senha não correspondem. Insira a mesma senha nos dois campos e tente novamente. |
| **ver_incorrect_format** | Formato incorreto. |
| **ver_but_edit** | Alterar email |
| **ver_but_verify** | Verificar código |
| **alert_no** | Não |
| **ver_info_msg** | O código de verificação foi enviado para sua caixa de entrada. Copie-o para a caixa de entrada abaixo. |
| **day** | Dia |
| **ver_fail_throttled** | Houve muitas solicitações para verificar este endereço de email. Aguarde alguns instantes e tente novamente. |
| **helplink_text** | O que é isso? |
| **ver_fail_retry** | Esse código está incorreto. Tente novamente. |
| **alert_title** | Cancelar Inserção de Detalhes |
| **required_field** | Estas informações são necessárias. |
| **alert_message** | Tem certeza de que deseja cancelar a inserção de detalhes? |
| **ver_intro_msg** | A verificação é necessária. Clique no botão Enviar. |
| **ver_input** | Código de verificação |

### <a name="sign-up-and-self-asserted-pages-error-messages"></a>Mensagens de erro de páginas autodeclaradas e de entrada

| ID | Valor padrão |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Já existe um usuário com a ID especificada. Escolha uma ID diferente. |
| **UserMessageIfClaimNotVerified** | Declaração não verificada: {0} |
| **UserMessageIfIncorrectPattern** | Padrão incorreto para: {0} |
| **UserMessageIfMissingRequiredElement** | Elemento obrigatório ausente: {0} |
| **UserMessageIfValidationError** | Erro na validação por: {0} |
| **UserMessageIfInvalidInput** | {0} tem uma entrada inválida. |
| **ServiceThrottled** | Há muitas solicitações no momento. Aguarde alguns instantes e tente novamente. |

O exemplo a seguir mostra o uso de alguns dos elementos de interface do usuário na página de inscrição:

![Página de inscrição com seus nomes de elementos de interface do usuário rotulados](./media/localization-string-ids/localization-sign-up.png)

O exemplo a seguir mostra o uso de alguns dos elementos de interface do usuário na página de entrada após o usuário clicar no botão e envio do código de verificação:

![Elementos de experiência do usuário da verificação de email na página de inscrição](./media/localization-string-ids/localization-email-verification.png)


## <a name="phone-factor-authentication-page-user-interface-elements"></a>Elementos da interface do usuário da página autenticação com fator de telefone

A seguir, estão as IDs de uma definição de conteúdo com uma ID de `api.phonefactor`.

| ID | Valor padrão |
| -- | ------------- |
| **button_verify** | Ligar Para Mim |
| **country_code_label** | Código do país |
| **cancel_message** | O usuário cancelou a autenticação multifator |
| **text_button_send_second_code** | envie um novo código |
| **code_pattern** | \\d{6} |
| **intro_mixed** | Temos o seguinte número no registro para você. Podemos enviar um código via SMS ou ligar para autenticá-lo. |
| **intro_mixed_p** | Temos os seguintes números no registro para você. Escolha um número para que possamos telefonr ou enviar um código via SMS para autenticá-lo. |
| **button_verify_code** | Verificar Código |
| **requiredField_code** | Insira o código de verificação recebido |
| **invalid_code** | Insira o código de 6 dígitos recebido |
| **button_cancel** | Cancelar |
| **local_number_input_placeholder_text** | Número de telefone |
| **button_retry** | Repetir |
| **alternative_text** | Não estou com meu telefone |
| **intro_phone_p** | Temos os seguintes números no registro para você. Escolha um número para que possamos ligar para autenticá-lo. |
| **intro_phone** | Temos o seguinte número no registro para você. Telefonaremos para autenticá-lo. |
| **enter_code_text_intro** | Insira seu código de verificação abaixo ou  |
| **intro_entry_phone** | Insira um número abaixo para que possamos ligar para autenticá-lo. |
| **intro_entry_sms** | Insira um número abaixo para que possamos enviar um código via SMS para autenticá-lo. |
| **button_send_code** | Enviar Código |
| **invalid_number** | Insira um número de telefone válido |
| **intro_sms** | Temos o seguinte número no registro para você. Enviaremos um código via SMS para autenticá-lo. |
| **intro_entry_mixed** | Insira um número abaixo para que possamos enviar um código via SMS ou ligar para autenticá-lo. |
| **number_pattern** | ^\\+(?:[0-9][\\x20-]?){6,14}[0-9]$ |
| **intro_sms_p** |Temos os seguintes números no registro para você. Escolha um número para que possamos ou enviar um código via SMS para autenticá-lo. |
| **requiredField_countryCode** | Selecione o código do seu país/região |
| **requiredField_number** | Insira seu número de telefone |
| **country_code_input_placeholder_text** |País ou região |
| **number_label** | Número do telefone |
| **error_tryagain** | O número de telefone que você forneceu está ocupado ou não disponível. Verifique o número e tente novamente. |
| **error_incorrect_code** | O código de verificação inserido não corresponde aos nossos registros. Tente novamente ou solicite um novo código. |
| **countryList** | {\"DEFAULT\":\"Country/Region\",\"AF\":\"Afghanistan\",\"AX\":\"Åland Islands\",\"AL\":\"Albania\",\"DZ\":\"Algeria\",\"AS\":\"American Samoa\",\"AD\":\"Andorra\",\"AO\":\"Angola\",\"AI\":\"Anguilla\",\"AQ\":\"Antarctica\",\"AG\":\"Antigua and Barbuda\",\"AR\":\"Argentina\",\"AM\":\"Armenia\",\"AW\":\"Aruba\",\"AU\":\"Australia\",\"AT\":\"Austria\",\"AZ\":\"Azerbaijan\",\"BS\":\"Bahamas\",\"BH\":\"Bahrain\",\"BD\":\"Bangladesh\",\"BB\":\"Barbados\",\"BY\":\"Belarus\",\"BE\":\"Belgium\",\"BZ\":\"Belize\",\"BJ\":\"Benin\",\"BM\":\"Bermuda\",\"BT\":\"Bhutan\",\"BO\":\"Bolivia\",\"BQ\":\"Bonaire\",\"BA\":\"Bosnia and Herzegovina\",\"BW\":\"Botswana\",\"BV\":\"Bouvet Island\",\"BR\":\"Brazil\",\"IO\":\"British Indian Ocean Territory\",\"VG\":\"British Virgin Islands\",\"BN\":\"Brunei\",\"BG\":\"Bulgaria\",\"BF\":\"Burkina Faso\",\"BI\":\"Burundi\",\"CV\":\"Cabo Verde\",\"KH\":\"Cambodia\",\"CM\":\"Cameroon\",\"CA\":\"Canada\",\"KY\":\"Cayman Islands\",\"CF\":\"Central African Republic\",\"TD\":\"Chad\",\"CL\":\"Chile\",\"CN\":\"China\",\"CX\":\"Christmas Island\",\"CC\":\"Cocos (Keeling) Islands\",\"CO\":\"Colombia\",\"KM\":\"Comoros\",\"CG\":\"Congo\",\"CD\":\"Congo (DRC)\",\"CK\":\"Cook Islands\",\"CR\":\"Costa Rica\",\"CI\":\"Côte d’Ivoire\",\"HR\":\"Croatia\",\"CU\":\"Cuba\",\"CW\":\"Curaçao\",\"CY\":\"Cyprus\",\"CZ\":\"Czech Republic\",\"DK\":\"Denmark\",\"DJ\":\"Djibouti\",\"DM\":\"Dominica\",\"DO\":\"Dominican Republic\",\"EC\":\"Ecuador\",\"EG\":\"Egypt\",\"SV\":\"El Salvador\",\"GQ\":\"Equatorial Guinea\",\"ER\":\"Eritrea\",\"EE\":\"Estonia\",\"ET\":\"Ethiopia\",\"FK\":\"Falkland Islands\",\"FO\":\"Faroe Islands\",\"FJ\":\"Fiji\",\"FI\":\"Finland\",\"FR\":\"France\",\"GF\":\"French Guiana\",\"PF\":\"French Polynesia\",\"TF\":\"French Southern Territories\",\"GA\":\"Gabon\",\"GM\":\"Gambia\",\"GE\":\"Georgia\",\"DE\":\"Germany\",\"GH\":\"Ghana\",\"GI\":\"Gibraltar\",\"GR\":\"Greece\",\"GL\":\"Greenland\",\"GD\":\"Grenada\",\"GP\":\"Guadeloupe\",\"GU\":\"Guam\",\"GT\":\"Guatemala\",\"GG\":\"Guernsey\",\"GN\":\"Guinea\",\"GW\":\"Guinea-Bissau\",\"GY\":\"Guyana\",\"HT\":\"Haiti\",\"HM\":\"Heard Island and McDonald Islands\",\"HN\":\"Honduras\",\"HK\":\"Hong Kong SAR\",\"HU\":\"Hungary\",\"IS\":\"Iceland\",\"IN\":\"India\",\"ID\":\"Indonesia\",\"IR\":\"Iran\",\"IQ\":\"Iraq\",\"IE\":\"Ireland\",\"IM\":\"Isle of Man\",\"IL\":\"Israel\",\"IT\":\"Italy\",\"JM\":\"Jamaica\",\"JP\":\"Japan\",\"JE\":\"Jersey\",\"JO\":\"Jordan\",\"KZ\":\"Kazakhstan\",\"KE\":\"Kenya\",\"KI\":\"Kiribati\",\"KR\":\"Korea\",\"KW\":\"Kuwait\",\"KG\":\"Kyrgyzstan\",\"LA\":\"Laos\",\"LV\":\"Latvia\",\"LB\":\"Lebanon\",\"LS\":\"Lesotho\",\"LR\":\"Liberia\",\"LY\":\"Libya\",\"LI\":\"Liechtenstein\",\"LT\":\"Lithuania\",\"LU\":\"Luxembourg\",\"MO\":\"Macao SAR\",\"MK\":\"North Macedonia\",\"MG\":\"Madagascar\",\"MW\":\"Malawi\",\"MY\":\"Malaysia\",\"MV\":\"Maldives\",\"ML\":\"Mali\",\"MT\":\"Malta\",\"MH\":\"Marshall Islands\",\"MQ\":\"Martinique\",\"MR\":\"Mauritania\",\"MU\":\"Mauritius\",\"YT\":\"Mayotte\",\"MX\":\"Mexico\",\"FM\":\"Micronesia\",\"MD\":\"Moldova\",\"MC\":\"Monaco\",\"MN\":\"Mongolia\",\"ME\":\"Montenegro\",\"MS\":\"Montserrat\",\"MA\":\"Morocco\",\"MZ\":\"Mozambique\",\"MM\":\"Myanmar\",\"NA\":\"Namibia\",\"NR\":\"Nauru\",\"NP\":\"Nepal\",\"NL\":\"Netherlands\",\"NC\":\"New Caledonia\",\"NZ\":\"New Zealand\",\"NI\":\"Nicaragua\",\"NE\":\"Niger\",\"NG\":\"Nigeria\",\"NU\":\"Niue\",\"NF\":\"Norfolk Island\",\"KP\":\"North Korea\",\"MP\":\"Northern Mariana Islands\",\"NO\":\"Norway\",\"OM\":\"Oman\",\"PK\":\"Pakistan\",\"PW\":\"Palau\",\"PS\":\"Palestinian Authority\",\"PA\":\"Panama\",\"PG\":\"Papua New Guinea\",\"PY\":\"Paraguay\",\"PE\":\"Peru\",\"PH\":\"Philippines\",\"PN\":\"Pitcairn Islands\",\"PL\":\"Poland\",\"PT\":\"Portugal\",\"PR\":\"Puerto Rico\",\"QA\":\"Qatar\",\"RE\":\"Réunion\",\"RO\":\"Romania\",\"RU\":\"Russia\",\"RW\":\"Rwanda\",\"BL\":\"Saint Barthélemy\",\"KN\":\"Saint Kitts and Nevis\",\"LC\":\"Saint Lucia\",\"MF\":\"Saint Martin\",\"PM\":\"Saint Pierre and Miquelon\",\"VC\":\"Saint Vincent and the Grenadines\",\"WS\":\"Samoa\",\"SM\":\"San Marino\",\"ST\":\"São Tomé and Príncipe\",\"SA\":\"Saudi Arabia\",\"SN\":\"Senegal\",\"RS\":\"Serbia\",\"SC\":\"Seychelles\",\"SL\":\"Sierra Leone\",\"SG\":\"Singapore\",\"SX\":\"Sint Maarten\",\"SK\":\"Slovakia\",\"SI\":\"Slovenia\",\"SB\":\"Solomon Islands\",\"SO\":\"Somalia\",\"ZA\":\"South Africa\",\"GS\":\"South Georgia and South Sandwich Islands\",\"SS\":\"South Sudan\",\"ES\":\"Spain\",\"LK\":\"Sri Lanka\",\"SH\":\"St Helena, Ascension, Tristan da Cunha\",\"SD\":\"Sudan\",\"SR\":\"Suriname\",\"SJ\":\"Svalbard\",\"SZ\":\"Swaziland\",\"SE\":\"Sweden\",\"CH\":\"Switzerland\",\"SY\":\"Syria\",\"TW\":\"Taiwan\",\"TJ\":\"Tajikistan\",\"TZ\":\"Tanzania\",\"TH\":\"Thailand\",\"TL\":\"Timor-Leste\",\"TG\":\"Togo\",\"TK\":\"Tokelau\",\"TO\":\"Tonga\",\"TT\":\"Trinidad and Tobago\",\"TN\":\"Tunisia\",\"TR\":\"Turkey\",\"TM\":\"Turkmenistan\",\"TC\":\"Turks and Caicos Islands\",\"TV\":\"Tuvalu\",\"UM\":\"U.S. Outlying Islands\",\"VI\":\"U.S. Virgin Islands\",\"UG\":\"Uganda\",\"UA\":\"Ukraine\",\"AE\":\"United Arab Emirates\",\"GB\":\"United Kingdom\",\"US\":\"United States\",\"UY\":\"Uruguay\",\"UZ\":\"Uzbekistan\",\"VU\":\"Vanuatu\",\"VA\":\"Vatican City\",\"VE\":\"Venezuela\",\"VN\":\"Vietnam\",\"WF\":\"Wallis and Futuna\",\"YE\":\"Yemen\",\"ZM\":\"Zambia\",\"ZW\":\"Zimbabwe\"} |
| **error_448** | O número de telefone que você forneceu está inacessível. |
| **error_449** | O usuário excedeu o número de novas tentativas. |
| **verification_code_input_placeholder_text** | Código de verificação |

O exemplo a seguir mostra o uso de alguns dos elementos de interface do usuário na página de registro com MFA:

![Elementos de experiência do usuário da verificação de email na página de inscrição](./media/localization-string-ids/localization-mfa1.png)

O exemplo a seguir mostra o uso de alguns dos elementos de interface do usuário na página de validação com MFA:

![Elementos de experiência do usuário da verificação de email na página de inscrição](./media/localization-string-ids/localization-mfa2.png)

## <a name="verification-display-control-user-interface-elements"></a>Elementos da interface do usuário do controle de exibição de verificação

A seguir estão as IDs para um [controle de exibição de verificação](display-control-verification.md)

| ID | Valor padrão |
| -- | ------------- |
|verification_control_but_change_claims |Alterar |
|verification_control_fail_send_code |Falha ao enviar o código, tente novamente mais tarde. |
|verification_control_fail_verify_code |Falha ao verificar o código. tente novamente mais tarde. |
|verification_control_but_send_code |Enviar Código |
|verification_control_but_send_new_code |Enviar novo código |
|verification_control_but_verify_code |Verificar Código |

## <a name="one-time-password-error-messages"></a>Mensagens de erro de senha de uma vez
A seguir estão as IDs para mensagens de erro de [perfil técnico de senha de uma vez](one-time-password-technical-profile.md)

| ID | Valor padrão |
| -- | ------------- |
|UserMessageIfMaxRetryAttempted |A verificação de senha de uma vez fornecida excedeu o número máximo de tentativas |
|UserMessageIfSessionDoesNotExist |A sessão de verificação de senha de uma vez expirou |
|UserMessageIfSessionConflict |A sessão de verificação de senha de uma vez está em conflito |
|UserMessageIfInvalidCode |A senha de uso único fornecida para verificação está incorreta |








