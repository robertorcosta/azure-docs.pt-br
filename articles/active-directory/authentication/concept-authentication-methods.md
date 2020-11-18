---
title: Métodos e recursos de autenticação – Azure Active Directory
description: Saiba mais sobre os diferentes métodos e recursos de autenticação disponíveis no Azure Active Directory para ajudar a melhorar e proteger eventos de entrada
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: a166a451c405c2321453e02751baad91c2a14c60
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94840010"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Quais métodos de autenticação e verificação estão disponíveis no Azure Active Directory?

Como parte da experiência de entrada para contas no Azure Active Directory (Azure AD), há diferentes maneiras pelas quais um usuário pode se autenticar. Um nome de usuário e uma senha é a maneira mais comum, historicamente, de um usuário fornecer credenciais. Com os recursos de autenticação e segurança modernos no Azure AD, essa senha básica deve ser complementada ou substituída por métodos de autenticação mais seguros.

![Tabela dos pontos fortes e métodos de autenticação preferenciais no Azure AD](media/concept-authentication-methods/authentication-methods.png)

Métodos de autenticação sem senha, como o Windows Hello, chaves de segurança FIDO2, e o aplicativo Microsoft Authenticator fornecem os eventos de entrada mais seguros.

A autenticação multifator do Azure AD adiciona segurança adicional apenas usando uma senha quando um usuário entra. O usuário pode ser solicitado a fornecer formulários adicionais de autenticação, como para responder a uma notificação por push, inserir um código de um token de software ou hardware ou responder a um SMS ou chamada telefônica.

Para simplificar a experiência de integração do usuário e registrar-se para MFA e SSPR, recomendamos que você [habilite o registro de informações de segurança combinadas](howto-registration-mfa-sspr-combined.md). Para resiliência, recomendamos que você exija que os usuários registrem vários métodos de autenticação. Quando um método não está disponível para um usuário durante a entrada ou SSPR, eles podem optar por autenticar com outro método. Para obter mais informações, consulte [criar uma estratégia de gerenciamento de controle de acesso resiliente no Azure ad](concept-resilient-controls.md).

## <a name="authentication-method-strength-and-security"></a>Segurança e força do método de autenticação

Ao implantar recursos como a autenticação multifator do Azure AD em sua organização, examine os métodos de autenticação disponíveis. Escolha os métodos que atendem ou excedem seus requisitos em termos de segurança, usabilidade e disponibilidade. Sempre que possível, use métodos de autenticação com o nível mais alto de segurança.

A tabela a seguir descreve as considerações de segurança para os métodos de autenticação disponíveis. A disponibilidade é uma indicação de que o usuário está sendo capaz de usar o método de autenticação, não a disponibilidade do serviço no Azure AD:

| Método de autenticação          | Segurança | Usabilidade | Disponibilidade |
|--------------------------------|:--------:|:---------:|:------------:|
| Windows Hello para Empresas     | Alta     | Alta      | Alta         |
| Aplicativo Microsoft Authenticator    | Alta     | Alta      | Alta         |
| Chave de segurança do FIDO2 (versão prévia)   | Alta     | Alta      | Alta         |
| Tokens de hardware OATH (versão prévia) | Médio   | Médio    | Alta         |
| Tokens de software OATH           | Médio   | Médio    | Alta         |
| sms                            | Média   | Alto      | Médio       |
| Voz                          | Médio   | Médio    | Médio       |
| Senha                       | Baixo      | Alto      | Alta         |

Para obter mais informações sobre segurança, consulte [vulnerabilidades de autenticação e vetores de ataque](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124).

> [!TIP]
> Para flexibilidade e usabilidade, recomendamos que você use o aplicativo Microsoft Authenticator. Esse método de autenticação fornece a melhor experiência do usuário e vários modos, como notificações por push de MFA, e códigos OATH sem senha.

## <a name="how-each-authentication-method-works"></a>Como funciona cada método de autenticação

Alguns métodos de autenticação podem ser usados como o fator primário quando você entra em um aplicativo ou dispositivo, como usar uma chave de segurança FIDO2 ou uma senha. Outros métodos de autenticação só estão disponíveis como um fator secundário quando você usa a autenticação multifator do Azure AD ou o SSPR.

A tabela a seguir descreve quando um método de autenticação pode ser usado durante um evento de entrada:

| Método                         | Autenticação primária | Autenticação secundária  |
|--------------------------------|:----------------------:|:-------------------------:|
| Windows Hello para Empresas     | Sim                    | MFA                       |
| Aplicativo Microsoft Authenticator    | Sim (versão prévia)          | MFA e o SSPR              |
| Chave de segurança do FIDO2 (versão prévia)   | Sim                    | MFA                       |
| Tokens de hardware OATH (versão prévia) | Não                     | MFA                       |
| Tokens de software OATH           | Não                     | MFA                       |
| sms                            | Sim (versão prévia)          | MFA e o SSPR              |
| Chamada de voz                     | Não                     | MFA e o SSPR              |
| Senha                       | Sim                    |                           |

Todos esses métodos de autenticação podem ser configurados no portal do Azure e cada vez mais usando a [API REST Microsoft Graph beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

Para saber mais sobre como funciona cada método de autenticação, consulte os seguintes artigos conceituais separados:

* [Windows Hello para empresas](/windows/security/identity-protection/hello-for-business/hello-overview)
* [Aplicativo Microsoft Authenticator](concept-authentication-authenticator-app.md)
* [Chave de segurança do FIDO2 (versão prévia)](concept-authentication-passwordless.md#fido2-security-keys)
* [Tokens de hardware OATH (versão prévia)](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
* [Tokens de software OATH](concept-authentication-oath-tokens.md#oath-software-tokens)
* Entrada do SMS [(visualização)](howto-authentication-sms-signin.md) e [verificação](concept-authentication-phone-options.md#mobile-phone-verification)
* [Verificação de chamada de voz](concept-authentication-phone-options.md)
* Senha

> [!NOTE]
> No Azure AD, uma senha é geralmente um dos métodos de autenticação primária. Não é possível desabilitar o método de autenticação de senha. Se você usar uma senha como o fator de autenticação principal, aumente a segurança de eventos de entrada usando a autenticação multifator do Azure AD.

Os seguintes métodos de verificação adicionais podem ser usados em determinados cenários:

* [Senhas de aplicativo](howto-mfa-app-passwords.md) – usadas para aplicativos antigos que não dão suporte à autenticação moderna e podem ser configuradas para autenticação multifator do Azure ad por usuário.
* [Perguntas de segurança](concept-authentication-security-questions.md) – usadas somente para SSPR
* [Endereço de email](concept-sspr-howitworks.md#authentication-methods) -usado somente para SSPR

## <a name="next-steps"></a>Próximas etapas

Para começar, consulte o [tutorial para redefinição de senha de autoatendimento (SSPR)][tutorial-sspr] e [autenticação multifator do Azure ad][tutorial-azure-mfa].

Para saber mais sobre os conceitos de SSPR, confira [Como funciona a redefinição de senha self-service do Azure AD][concept-sspr].

Para saber mais sobre os conceitos de MFA, confira [como funciona a autenticação multifator do Azure ad][concept-mfa].

Saiba mais sobre como configurar métodos de autenticação usando a [API REST do Microsoft Graph versão beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

Para examinar quais métodos de autenticação estão em uso, consulte [análise do método de autenticação de autenticação multifator do Azure AD com o PowerShell](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/).

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
