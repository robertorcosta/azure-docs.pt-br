---
title: Políticas do Azure AD Identity Protection
description: Identificação das três políticas que são habilitadas com o Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7587ed6d414a69cff67aca9446aebf6260c99fcd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83736493"
---
# <a name="identity-protection-policies"></a>Políticas do Identity Protection

O Azure Active Directory Identity Protection inclui três políticas padrão que os administradores podem habilitar. A personalização dessas políticas é limitada, mas são aplicáveis à maioria das organizações. Todas as políticas permitem a exclusão de usuários, como suas [contas de administrador de interrupção ou de acesso de emergência](../users-groups-roles/directory-emergency-access.md).

![Políticas do Identity Protection](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-mfa-registration-policy"></a>Política de registro com Azure MFA

O Identity Protection ajuda as organizações a implantar a Autenticação multifator do Azure (MFA) do Azure usando uma política de Acesso Condicional que requer um registro para entrar. Habilitar essa política é uma ótima maneira de garantir que novos usuários da organização tenham se registrado para MFA no primeiro dia. A autenticação multifator é um dos métodos de autocorreção para eventos de risco no Identity Protection. A autocorreção permite que os usuários tomem providências por conta própria para reduzir o volume de chamadas para assistência técnica.

Mais informações sobre a Autenticação Multifator do Azure podem ser encontradas no artigo [Como funciona: Autenticação Multifator do Azure](../authentication/concept-mfa-howitworks.md).

## <a name="sign-in-risk-policy"></a>Política de risco de entrada

O Identity Protection analisa os sinais de cada entrada, tanto em tempo real quanto offline, e calcula uma pontuação de risco com base na probabilidade de que a entrada não tenha sido realizada pelo usuário. Os administradores podem tomar uma decisão com base nesse sinal de pontuação de risco para impor os requisitos organizacionais. Os administradores podem optar por bloquear o acesso, permitir o acesso ou permitir o acesso, mas exigir a autenticação multifator.

Se for detectado um risco, os usuários podem executar a autenticação multifator para autocorrigir e fechar a entrada suspeita para evitar passar problemas desnecessários para os administradores.

> [!NOTE] 
> Os usuários devem ter se registrado anteriormente para a Autenticação Multifator do Azure antes de disparar a política de risco de entrada.

### <a name="custom-conditional-access-policy"></a>Política de Acesso Condicional personalizada

Os administradores também podem optar por criar uma política de Acesso Condicional personalizada, incluindo o risco de entrada como uma condição de atribuição. Mais informações sobre o risco como uma condição em uma política de Acesso Condicional podem ser encontradas no artigo [Acesso Condicional: condições](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk)

![Política de risco de entrada de Acesso Condicional personalizada](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>Política de risco do usuário

O Identity Protection pode calcular o que acredita ser normal para o comportamento de um usuário e usar isso como base para decidir aquilo que representa um risco. O risco do usuário é um cálculo da probabilidade de que uma identidade tenha sido comprometida. Os administradores podem tomar uma decisão com base nesse sinal de pontuação de risco para impor os requisitos organizacionais. Os administradores podem optar por bloquear o acesso, permitir o acesso ou permitir o acesso, mas exigir uma alteração de senha usando a [redefinição de senha self-service do Azure AD](../authentication/howto-sspr-deployment.md).

Se for detectado um risco, os usuários podem realizar a redefinição de senha self-service e fechar o evento de risco do usuário para evitar passar problemas desnecessários para os administradores.

> [!NOTE] 
> Os usuários devem ter se registrado previamente para redefinição de senha self-service antes de disparar a política de risco do usuário.

## <a name="next-steps"></a>Próximas etapas

- [Habilitar a redefinição de senha self-service do Azure AD](../authentication/howto-sspr-deployment.md)

- [Habilitar a Autenticação Multifator do Microsoft Azure](../authentication/howto-mfa-getstarted.md)

- [Habilitar a política de registro da Autenticação Multifator do Azure](howto-identity-protection-configure-mfa-policy.md)

- [Habilitar as políticas de entrada e de risco do usuário](howto-identity-protection-configure-risk-policies.md)
