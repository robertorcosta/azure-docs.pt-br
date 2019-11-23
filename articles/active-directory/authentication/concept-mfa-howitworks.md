---
title: How it works Azure MFA - Azure Active Directory
description: A Autenticação Multifator do Azure ajuda a proteger o acesso aos seus dados e aplicativos, ao mesmo tempo em que atende à demanda dos usuários de um processo de logon simples.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1036d7e8aef29e3185452d5088e660d474726e4
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381965"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Como funciona: autenticação multifator do Azure

A segurança da verificação em duas etapas baseia-se na sua abordagem em camadas. O comprometimento de vários fatores de autenticação apresenta um desafio significativo para os invasores. Mesmo que um invasor consiga descobrir a senha do usuário, ela será inútil se ele também não estiver de posse do método de autenticação adicional. Isso funciona exigindo dois ou mais dos seguintes métodos de autenticação:

* Algo que você sabe (normalmente, uma senha)
* Algo que você tem (um dispositivo confiável que não pode ser facilmente clonado, como um telefone)
* Algo seu (biometria)

<center>

![Conceptual authentication methods image](./media/concept-mfa-howitworks/methods.png)</center>

A MFA (Autenticação Multifator do Azure) ajuda a proteger o acesso a dados e aplicativos enquanto mantém a simplicidade para os usuários. Ele fornece segurança adicional exigindo uma segunda forma de autenticação e fornece autenticação forte por meio de uma variedade de [métodos de autenticação](concept-authentication-methods.md) fáceis de usar. Os usuários podem ou não ser desafiados para MFA com base em decisões de configuração tomadas por um administrador.

## <a name="how-to-get-multi-factor-authentication"></a>Como obter a Autenticação Multifator?

A autenticação multifator é fornecida como parte das seguintes ofertas:

* **Azure Active Directory Premium** or **Microsoft 365 Business** - Full featured use of Azure Multi-Factor Authentication using Conditional Access policies to require multi-factor authentication.

* **Azure AD Free** or standalone **Office 365** licenses - Use pre-created [Conditional Access baseline protection policies](../conditional-access/concept-baseline-protection.md) to require multi-factor authentication for your users and administrators.

* **Administradores Globais do Azure Active Directory** – um subconjunto das funcionalidades de Autenticação Multifator do Azure está disponível como um meio para proteger as contas de administrador global.

> [!NOTE]
> Novos clientes não poderão mais comprar Autenticação Multifator do Microsoft Azure como uma oferta independente a partir de 1º de setembro de 2018. A Autenticação Multifator continuará sendo um recurso disponível nas licenças do Azure AD Premium.

## <a name="supportability"></a>Capacidade de suporte

Como a maioria dos usuários está acostumada a usar apenas senhas para a autenticação, é importante que a sua empresa comunique todos os usuários sobre esse processo. A conscientização pode reduzir a probabilidade de que os usuários liguem para o seu suporte técnico para resolver pequenos problemas relacionados a MFA. No entanto, existem alguns cenários em que é necessário desabilitar temporariamente o MFA. Use as diretrizes abaixo para entender como lidar com esses cenários:

* Treine sua equipe de suporte para lidar com cenários em que o usuário não consegue entrar porque não têm acesso a seus métodos de autenticação ou esses métodos não estão funcionando corretamente.
   * Using Conditional Access policies for Azure MFA Service, your support staff can add a user to a group that is excluded from a policy requiring MFA.
* Consider using Conditional Access named locations as a way to minimize two-step verification prompts. With this functionality, administrators can bypass two-step verification for users that are signing in from a secure trusted network location such as a network segment used for new user onboarding.
* Deploy [Azure AD Identity Protection](../active-directory-identityprotection.md) and trigger two-step verification based on risk detections.

## <a name="next-steps"></a>Próximos passos

- [Step-by-step Azure Multi-Factor Authentication deployment](howto-mfa-getstarted.md)
