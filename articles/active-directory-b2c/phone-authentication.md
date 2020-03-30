---
title: Inscrição por telefone e login com políticas personalizadas (Visualização)
titleSuffix: Azure AD B2C
description: Envie senhas únicas (OTP) em mensagens de texto para os telefones dos usuários do aplicativo com políticas personalizadas no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eadac0e973b361b1fdee63dcc9cfa848a0b2bacb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183951"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c-preview"></a>Configure o login telefônico e o login com políticas personalizadas no Azure AD B2C (Preview)

O login e login no Azure Active Directory B2C (Azure AD B2C) permite que seus usuários se inscrevam e entrem em seus aplicativos usando uma senha única (OTP) enviada em uma mensagem de texto para seu telefone. Senhas únicas podem ajudar a minimizar o risco de seus usuários esquecerem ou terem suas senhas comprometidas.

Siga as etapas deste artigo para usar as políticas personalizadas para permitir que seus clientes se inscrevam e entrem em seus aplicativos usando uma senha única enviada para seu telefone.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="pricing"></a>Preços

Senhas únicas são enviadas aos seus usuários usando mensagens de texto SMS, e você pode ser cobrado por cada mensagem enviada. Para obter informações sobre preços, consulte a seção **Taxas separadas** do [preço B2C do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="prerequisites"></a>Pré-requisitos

Você precisa dos seguintes recursos no local antes de configurar o OTP.

* [Locatário do Azure AD B2C](tutorial-create-tenant.md)
* [Aplicativo web registrado](tutorial-register-applications.md) em seu inquilino
* [Políticas personalizadas](custom-policy-get-started.md) enviadas ao seu inquilino

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Obtenha o pacote de entrada de inscrição & entrada de login

Comece atualizando o login telefônico e arquivos de política personalizados para trabalhar com seu inquilino Azure AD B2C.

As etapas a seguir supõem que você completou os [pré-requisitos](#prerequisites) e já clonou o repositório [de pacote de inicialização de políticas personalizadas][starter-pack] para sua máquina local.

1. Encontre o [formulário de inscrição do telefone e os arquivos de política personalizados de login][starter-pack-phone] em seu clone local do repo do pacote inicial ou baixe-os diretamente. Os arquivos de diretiva XML estão localizados no seguinte diretório:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. Em cada arquivo, `yourtenant` substitua a seqüência com o nome do seu inquilino Azure AD B2C. Por exemplo, se o nome do seu inquilino B2C `yourtenant.onmicrosoft.com` for `contosob2c.onmicrosoft.com` *contosob2c*, todas as instâncias de se tornar .

1. Complete as etapas nos [IDs](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) de aplicativos adicionais à seção de diretiva personalizada do [Iniciar com políticas personalizadas no Azure Active Directory B2C](custom-policy-get-started.md). Neste caso, `/phone-number-passwordless/` **`Phone_Email_Base.xml`** atualize-se com os **IDs de aplicativo (cliente)** dos dois aplicativos registrados ao completar os pré-requisitos, *IdentityExperienceFramework* e *ProxyIdentityExperienceFramework*.

## <a name="upload-the-policy-files"></a>Faça upload dos arquivos de apólice

1. Faça login no [portal Azure](https://portal.azure.com) e navegue até o seu inquilino Azure AD B2C.
1. Em **Políticas,** selecione **Identity Experience Framework**.
1. Selecione **Enviar política personalizada**.
1. Faça o upload dos arquivos de diretiva na seguinte ordem:
    1. *Phone_Email_Base.xml*
    1. *Inscreva-seououSignInWithPhone.xml*
    1. *Inscreva-seouousignincomPhoneOrEmail.xml*
    1. *ProfileEditPhoneOnly.xml*
    1. *ProfileEditPhoneEmail.xml*
    1. *ChangePhoneNumber.xml*
    1. *PasswordResetEmail.xml*

Ao carregar cada arquivo, o Azure adiciona o prefixo `B2C_1A_`.

## <a name="test-the-custom-policy"></a>Teste a política personalizada

1. Em **políticas personalizadas,** selecione **B2C_1A_SignUpOrSignInWithPhone**.
1. Em **Select application**, selecione o aplicativo *webapp1* que você registrou ao completar os pré-requisitos.
1. Para selecionar url `https://jwt.ms`de **resposta,** escolha .
1. Selecione **Executar agora** e inscreva-se usando um endereço de e-mail ou um número de telefone.
1. Selecione **Executar agora** mais uma vez e faça login com a mesma conta para confirmar se você tem a configuração correta.

## <a name="get-user-account-by-phone-number"></a>Obtenha conta de usuário por número de telefone

Um usuário que se inscreve com um número de telefone, mas não fornece um endereço de e-mail de recuperação, é registrado no diretório Azure AD B2C com seu número de telefone como seu nome de login. Se o usuário desejar alterar seu número de telefone, sua central de ajuda ou equipe de suporte deve primeiro encontrar sua conta e, em seguida, atualizar seu número de telefone.

Você pode encontrar um usuário pelo seu número de telefone (nome de login) usando [o Microsoft Graph](manage-user-accounts-graph-api.md):

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

Por exemplo: 

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>Próximas etapas

Você pode encontrar o pacote de inicialização de políticas personalizadas de inscrição e login (e outros pacotes iniciais) no GitHub:

[Azure-Samples/active-directory-b2c-custom-policy-starterpack/scenarios/phone-number-passwordless][starter-pack-phone]

Os arquivos de política de pacote inicial usam perfis técnicos de autenticação multifatorial e transformações de reivindicações de número de telefone:

* [Defina um perfil técnico de autenticação multifatorial do Azure](multi-factor-auth-technical-profile.md)
* [Definir transformações de reivindicações de números de telefone](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
