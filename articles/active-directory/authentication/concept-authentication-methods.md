---
title: Métodos e recursos de autenticação – Azure Active Directory
description: Saiba mais sobre os diferentes métodos e recursos de autenticação disponíveis no Azure Active Directory para ajudar a melhorar e proteger eventos de entrada
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: dcbfd05df84e32423df425f3bdd231a26e4f3bca
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90527030"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Quais métodos de autenticação e verificação estão disponíveis no Azure Active Directory?

Como parte da experiência de entrada para contas no Azure Active Directory (Azure AD), há diferentes maneiras pelas quais um usuário pode se autenticar. Um nome de usuário e uma senha é a maneira mais comum, historicamente, de um usuário fornecer credenciais. Com os recursos de autenticação e segurança modernos no Azure AD, essa senha básica deve ser complementada ou substituída por métodos de autenticação mais seguros.

![Tabela dos pontos fortes e métodos de autenticação preferenciais no Azure AD](media/concept-authentication-methods/authentication-methods.png)

Métodos de autenticação sem senha, como o Windows Hello, chaves de segurança FIDO2, e o aplicativo Microsoft Authenticator fornecem os eventos de entrada mais seguros.

A autenticação multifator do Azure adiciona segurança adicional apenas usando uma senha quando um usuário entra. O usuário pode ser solicitado a fornecer formulários adicionais de autenticação, como para responder a uma notificação por push, inserir um código de um token de software ou hardware ou responder a um SMS ou chamada telefônica.

Para simplificar a experiência de integração do usuário e registrar-se para MFA e SSPR, recomendamos que você [habilite o registro de informações de segurança combinadas](howto-registration-mfa-sspr-combined.md). Para resiliência, recomendamos que você exija que os usuários registrem vários métodos de autenticação. Quando um método não está disponível para um usuário durante a entrada ou SSPR, eles podem optar por autenticar com outro método. Para obter mais informações, consulte [criar uma estratégia de gerenciamento de controle de acesso resiliente no Azure ad](concept-resilient-controls.md).

## <a name="authentication-method-strength-and-security"></a>Segurança e força do método de autenticação

Ao implantar recursos como a autenticação multifator do Azure em sua organização, examine os métodos de autenticação disponíveis. Escolha os métodos que atendem ou excedem seus requisitos em termos de segurança, usabilidade e disponibilidade. Sempre que possível, use métodos de autenticação com o nível mais alto de segurança.

A tabela a seguir descreve as considerações de segurança para os métodos de autenticação disponíveis. A disponibilidade é uma indicação de que o usuário está sendo capaz de usar o método de autenticação, não a disponibilidade do serviço no Azure AD:

| Método de autenticação       | Segurança | Usabilidade | Phisable? | O canal é Jack? | Disponibilidade |
|-----------------------------|:--------:|:---------:|:---------:|:-----------------:|:------------:|
| Chave de segurança do FIDO2          | Alta     | Alta      | Não        | Não                | Alta         |
| Aplicativo Microsoft Authenticator | Alta     | Alta      | Sim       | Não <sup>1</sup>   | Alta         |
| Windows Hello for Business  | Alta     | Alta      | Não        | Não                | Alta         |
| Tokens OATH de hardware        | Médio   | Médio    | Sim       | Não                | Alta         |
| Tokens OATH de software        | Médio   | Médio    | Sim       | Não <sup>2</sup>   | Alta         |
| SMS                         | Médio   | Alta      | Sim       | Sim               | Médio       |
| Voz                       | Médio   | Médio    | Sim       | Sim               | Médio       |
| Senha                    | Baixo      | Alto      | Sim       | Sim               | Alta         |

<sup>1</sup> no modo de senha, quando o aplicativo é registrado em um dispositivo específico<br />
<sup>2</sup> supondo que o aplicativo requer um PIN de dispositivo para desbloquear

Para obter mais informações sobre vulnerabilidades e vetores de ataque, consulte [tomada de canal e phishing em tempo real](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124).

> [!TIP]
> Para flexibilidade e usabilidade, recomendamos que você use o aplicativo Microsoft Authenticator. Esse método de autenticação fornece a melhor experiência do usuário e vários modos, como notificações por push de MFA, e códigos OATH sem senha.

## <a name="how-each-authentication-method-works"></a>Como funciona cada método de autenticação

Alguns métodos de autenticação podem ser usados como o fator primário quando você entra em um aplicativo ou dispositivo, como usar uma chave de segurança FIDO2 ou uma senha. Outros métodos de autenticação só estão disponíveis como um fator secundário quando você usa a autenticação multifator do Azure ou o SSPR.

A tabela a seguir descreve quando um método de autenticação pode ser usado durante um evento de entrada:

| Método                         | Autenticação primária | Autenticação secundária  |
|--------------------------------|:----------------------:|:-------------------------:|
| Chaves de segurança FIDO2 (versão prévia)  | Sim                    | MFA                       |
| Aplicativo Microsoft Authenticator    | Sim (versão prévia)          | MFA e o SSPR              |
| Windows Hello for Business     | Sim                    | MFA                       |
| Tokens de hardware OATH (versão prévia) | Não                     | MFA                       |
| Tokens de software OATH           | Não                     | MFA                       |
| SMS                            | Sim (versão prévia)          | MFA e o SSPR              |
| Chamada de voz                     | Não                     | MFA e o SSPR              |
| Senha                       | Sim                    |                           |

Todos esses métodos de autenticação podem ser configurados no portal do Azure e cada vez mais usando a [API REST Microsoft Graph beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

Para saber mais sobre como funciona cada método de autenticação, consulte os seguintes artigos conceituais separados:

* [Chaves de segurança FIDO2 (versão prévia)](concept-authentication-passwordless.md#fido2-security-keys)
* [Aplicativo Microsoft Authenticator](concept-authentication-authenticator-app.md)
* [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-overview)
* [Tokens de software OATH](concept-authentication-oath-tokens.md#oath-software-tokens)
* [Tokens de hardware OATH (versão prévia)](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
* Entrada do SMS [(visualização)](howto-authentication-sms-signin.md) e [verificação](concept-authentication-phone-options.md#mobile-phone-verification)
* [Verificação de chamada de voz](concept-authentication-phone-options.md)
* Senha

> [!NOTE]
> No Azure AD, uma senha é geralmente um dos métodos de autenticação primária. Não é possível desabilitar o método de autenticação de senha. Se você usar uma senha como o fator de autenticação principal, aumente a segurança de eventos de entrada usando a autenticação multifator do Azure.

Os seguintes métodos de verificação adicionais podem ser usados em determinados cenários:

* [Senhas de aplicativo](howto-mfa-app-passwords.md) – usadas para aplicativos antigos que não dão suporte à autenticação moderna e podem ser configuradas para a autenticação multifator do Azure por usuário.
* [Perguntas de segurança](concept-authentication-security-questions.md) – usadas somente para SSPR
* [Endereço de email](concept-sspr-howitworks.md#authentication-methods) -usado somente para SSPR

## <a name="next-steps"></a>Próximas etapas

Para começar, confira o [tutorial da SSPR (redefinição de senha por autoatendimento)][tutorial-sspr] e a [Autenticação Multifator do Azure][tutorial-azure-mfa].

Para saber mais sobre os conceitos de SSPR, confira [Como funciona a redefinição de senha self-service do Azure AD][concept-sspr].

Para saber mais sobre conceitos de MFA, confira [Como funciona como a Autenticação Multifator do Azure][concept-mfa].

Saiba mais sobre como configurar métodos de autenticação usando a [API REST do Microsoft Graph versão beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
