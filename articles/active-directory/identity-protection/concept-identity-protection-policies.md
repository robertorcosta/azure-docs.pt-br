---
title: Políticas de proteção de identidade Azure AD
description: Identificando as três políticas habilitadas com proteção de identidade
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
ms.openlocfilehash: 4a44e32efa3e38cf9c4df7dc00e3189c129db418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72887410"
---
# <a name="identity-protection-policies"></a>Políticas do Identity Protection

O Azure Active Directory Identity Protection inclui três políticas padrão que os administradores podem escolher para habilitar. Essas políticas incluem personalização limitada, mas são aplicáveis à maioria das organizações. Todas as políticas permitem excluir usuários, como seu [acesso de emergência ou contas de administrador de vidro sumido](../users-groups-roles/directory-emergency-access.md).

![Políticas do Identity Protection](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-mfa-registration-policy"></a>Política de registro do Azure MFA

A Proteção de Identidade pode ajudar as organizações a implementar o MFA (Azure Multi-Factor Authentication, autenticação multifatorial) usando uma política de Acesso Condicional que exige registro no login. Habilitar essa política é uma ótima maneira de garantir que novos usuários em sua organização tenham se registrado no MFA em seu primeiro dia. A autenticação multifatorial é um dos métodos de auto-remediação para eventos de risco dentro da Identity Protection. A auto-remediação permite que seus usuários tomem medidas por conta própria para reduzir o volume de chamadas do helpdesk.

Mais informações sobre a Autenticação Multifatorial do Azure podem ser encontradas no [artigo, Como funciona: Autenticação Multifatorial Azure](../authentication/concept-mfa-howitworks.md).

## <a name="sign-in-risk-policy"></a>Política de risco de entrada

O Identity Protection analisa sinais de cada login, tanto em tempo real quanto offline, e calcula uma pontuação de risco com base na probabilidade de que o login não tenha sido realizado pelo usuário. Os administradores podem tomar uma decisão com base nesse sinal de pontuação de risco para impor os requisitos organizacionais. Os administradores podem optar por bloquear o acesso, permitir o acesso ou permitir o acesso, mas exigem autenticação multifatorial.

Se o risco for detectado, os usuários podem realizar autenticação multifatorial para auto-remediar e fechar o evento de login arriscado para evitar ruídos desnecessários para os administradores.

> [!NOTE] 
> Os usuários devem ter se registrado anteriormente para autenticação multifatorial do Azure antes de acionar a política de risco de login.

### <a name="custom-conditional-access-policy"></a>Política de acesso condicional personalizado

Os administradores também podem optar por criar uma política de acesso condicional personalizado, incluindo o risco de login como condição de atribuição. Mais informações sobre o Acesso Condicional podem ser encontradas no artigo, [O que é acesso condicional?](../conditional-access/overview.md)

![Política de risco de login de acesso condicional personalizado](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>Política de risco do usuário

A Identity Protection pode calcular o que acredita ser normal para o comportamento de um usuário e usá-lo para basear decisões para seu risco. O risco do usuário é um cálculo de probabilidade de que uma identidade tenha sido comprometida. Os administradores podem tomar uma decisão com base nesse sinal de pontuação de risco para impor os requisitos organizacionais. Os administradores podem optar por bloquear o acesso, permitir o acesso ou permitir o acesso, mas exigem uma alteração de senha usando [a redefinição de senha de autoatendimento do Azure AD](../authentication/howto-sspr-deployment.md).

Se o risco for detectado, os usuários podem executar a redefinição de senha de autoatendimento para auto-remediar e fechar o evento de risco do usuário para evitar ruídos desnecessários para os administradores.

> [!NOTE] 
> Os usuários devem ter se registrado previamente para redefinição de senha de autoatendimento antes de acionar a política de risco do usuário.

## <a name="next-steps"></a>Próximas etapas

- [Habilitar a redefinição da senha de autoatendimento do Azure AD](../authentication/howto-sspr-deployment.md)

- [Habilitar a Autenticação Multifator do Microsoft Azure](../authentication/howto-mfa-getstarted.md)

- [Habilite a política de registro de autenticação multifatorial do Azure](howto-identity-protection-configure-mfa-policy.md)

- [Habilite políticas de login e risco de usuário](howto-identity-protection-configure-risk-policies.md)
