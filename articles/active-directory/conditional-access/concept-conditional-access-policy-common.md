---
title: Políticas comuns de acesso condicional - Diretório Ativo do Azure
description: Políticas de acesso condicional comumente usadas para organizações
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20b699f0672b49dd2f947e0cf00d0ffcef7961e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295299"
---
# <a name="common-conditional-access-policies"></a>Políticas de Acesso Condicional Comum

[Os padrões de segurança](../fundamentals/concept-fundamentals-security-defaults.md) são ótimos para alguns, mas muitas organizações precisam de mais flexibilidade do que oferecem. Por exemplo, muitos precisam da capacidade de excluir contas específicas, como suas contas de administração de acesso de emergência ou de quebra de vidro das políticas de acesso condicional que exigem autenticação multifatorial. Para essas organizações, as políticas comuns referenciadas neste artigo podem ser de uso.

![Políticas de Acesso Condicional no portal Azure](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Contas de acesso de emergência

Mais informações sobre contas de acesso a emergências e por que elas são importantes podem ser encontradas nos seguintes artigos: 

* [Gerenciar contas de acesso de emergência no Microsoft Azure Active Directory](../users-groups-roles/directory-emergency-access.md)
* [Criar uma estratégia de gerenciamento de controle de acesso resiliente com o Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>Políticas típicas implantadas pelas organizações

* [Bloqueie a autenticação do legado](howto-conditional-access-policy-block-legacy.md)\*
* [Exigir MFA para administradores](howto-conditional-access-policy-admin-mfa.md)\*
* [Exigir MFA para o gerenciamento do Azure](howto-conditional-access-policy-azure-management.md)\*
* [Exigir MFA para todos os usuários](howto-conditional-access-policy-all-users-mfa.md)\*

\*Essas quatro políticas, quando configuradas em conjunto, imitariam a funcionalidade ativada por [padrões de segurança](../fundamentals/concept-fundamentals-security-defaults.md).

## <a name="additional-policies"></a>Políticas adicionais

* [Acesso condicional baseado em risco (requer Azure AD Premium P2)](howto-conditional-access-policy-risk.md)
* [Exigir local confiável para registro de MFA](howto-conditional-access-policy-registration.md)
* [Bloquear o acesso por local](howto-conditional-access-policy-location.md)
* [Exigir dispositivo em conformidade](howto-conditional-access-policy-compliant-device.md)
* [Bloquear o acesso, exceto aplicativos específicos](howto-conditional-access-policy-block-access.md)

## <a name="next-steps"></a>Próximas etapas

- [Simule o comportamento do sinal usando a ferramenta Acesso Condicional E Se.](troubleshoot-conditional-access-what-if.md)

- [Use o modo somente relatório para acesso condicional para determinar o impacto de novas decisões políticas.](concept-conditional-access-report-only.md)
