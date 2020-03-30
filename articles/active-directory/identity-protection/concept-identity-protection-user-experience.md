---
title: Experiências do usuário com a Proteção de Identidade Azure AD
description: Experiência do usuário do Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc10fb4f9894a355c9eed024ae9f87747214999b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72886994"
---
# <a name="user-experiences-with-azure-ad-identity-protection"></a>Experiências do usuário com a Proteção de Identidade Azure AD

Com o Azure Active Directory Identity Protection, é possível:

* Exigir que os usuários se registrem para o Azure Multi-Factor Authentication (MFA)
* Automatize a remediação de logins arriscados e usuários comprometidos

Todas as políticas de Proteção de Identidade têm um impacto no sinal na experiência para os usuários. Permitir que os usuários se registrem e usem ferramentas como o Azure MFA e a redefinição de senha de autoatendimento pode diminuir o impacto. Essas ferramentas, juntamente com as escolhas de políticas apropriadas, dão aos usuários uma opção de auto-remediação quando precisam.

## <a name="multi-factor-authentication-registration"></a>Registro de autenticação multifator

Habilitar a política de Proteção de Identidade que exija o registro de autenticação multifatorial e direcionar todos os seus usuários, garantirá que eles tenham a capacidade de usar o Azure MFA para se auto-remediar no futuro. A configuração desta diretiva dá aos seus usuários um período de 14 dias onde eles podem optar por se registrar e, no final, são forçados a se registrar. A experiência para os usuários é descrita abaixo. Mais informações podem ser encontradas na documentação do usuário final no artigo, [Visão geral para verificação de dois fatores e sua conta de trabalho ou escola](../user-help/user-help-two-step-verification-overview.md).

### <a name="registration-interrupt"></a>Interrupção de inscrição

1. Ao fazer login em qualquer aplicativo integrado ao Azure AD, o usuário recebe uma notificação sobre a necessidade de configurar a conta para autenticação multifatorial. Essa política também é acionada no Windows 10 Out of Box Experience para novos usuários com um novo dispositivo.
   
    ![Mais informações necessárias](./media/concept-identity-protection-user-experience/identity-protection-experience-more-info-mfa.png)

1. Complete as etapas guiadas para se registrar na Autenticação Multifatorial do Azure e complete seu login.

## <a name="risky-sign-in-remediation"></a>Remediação de login arriscada

Quando um administrador configurou uma diretiva para riscos de login, os usuários afetados são notificados quando tentam entrar e acionar o nível de risco de políticas. 

### <a name="risky-sign-in-self-remediation"></a>Auto-remediação de sign-in arriscada

1. O usuário é informado de que algo incomum foi detectado em sua entrada, por exemplo, a entrada a partir de um novo local, dispositivo ou aplicativo.
   
    ![Algo incomum pronta](./media/concept-identity-protection-user-experience/120.png)

1. O usuário é obrigado a provar sua identidade completando o Azure MFA com um de seus métodos previamente registrados. 

### <a name="risky-sign-in-administrator-unblock"></a>Administrador de login arriscado desbloqueia

Os administradores podem optar por bloquear os usuários no momento do login, dependendo do nível de risco. Para serem desbloqueados, os usuários finais devem entrar em contato com sua equipe de TI ou podem tentar fazer login a partir de um local ou dispositivo familiar. A auto-remediação realizando autenticação multifatorial não é uma opção neste caso.

![Bloqueado pela política de risco de login](./media/concept-identity-protection-user-experience/200.png)

A equipe de TI pode seguir as instruções na seção [Desbloquear usuários](howto-identity-protection-remediate-unblock.md#unblocking-based-on-sign-in-risk) para permitir que os usuários voltem a entrar.

## <a name="risky-user-remediation"></a>Remediação de usuários arriscados

Quando uma política de risco do usuário foi configurada, os usuários que atendem ao nível de risco do usuário, a probabilidade de comprometimento devem passar pelo fluxo de recuperação de compromisso do usuário antes de poderem fazer login. 

### <a name="risky-user-self-remediation"></a>Auto-remediação de usuários arriscados

1. O usuário é informado de que a segurança da conta está em risco devido à atividade suspeita ou credenciais vazadas.
   
    ![Correção](./media/concept-identity-protection-user-experience/101.png)

1. O usuário é obrigado a provar sua identidade completando o Azure MFA com um de seus métodos previamente registrados. 
1. Finalmente, o usuário é forçado a alterar sua senha usando a senha de autoatendimento redefinida, uma vez que outra pessoa pode ter tido acesso à sua conta.

## <a name="risky-sign-in-administrator-unblock"></a>Administrador de login arriscado desbloqueia

Os administradores podem optar por bloquear os usuários no momento do login, dependendo do nível de risco. Para serem desbloqueados, os usuários finais devem entrar em contato com sua equipe de TI. A auto-remediação ao realizar autenticação multifatorial e redefinição de senha de autoatendimento não é uma opção neste caso.

![Bloqueado pela política de risco do usuário](./media/concept-identity-protection-user-experience/104.png)

A equipe de TI pode seguir as instruções na seção [Desbloquear usuários](howto-identity-protection-remediate-unblock.md#unblocking-based-on-user-risk) para permitir que os usuários voltem a entrar.

## <a name="see-also"></a>Confira também

- [Remediar riscos e desbloquear usuários](howto-identity-protection-remediate-unblock.md)

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) 
