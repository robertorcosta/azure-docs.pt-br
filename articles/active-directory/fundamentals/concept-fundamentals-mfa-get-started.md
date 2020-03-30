---
title: Autenticação multifatorial do Azure para sua organização - Azure Active Directory
description: Conheça os recursos disponíveis da Autenticação Multifatorial do Azure para sua organização com base no seu modelo de licença
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: febdb708c637ac322c0ca884eae627da9bd5904c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530386"
---
# <a name="overview-of-azure-multi-factor-authentication-for-your-organization"></a>Visão geral da Autenticação Multifatorial do Azure para sua organização

Existem várias maneiras de habilitar a autenticação multifatorial do Azure para seus usuários do Azure Active Directory (AD) com base nas licenças que sua organização possui. 

![Investigue sinais e imponha MFA se necessário](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Com base em nossos estudos, sua conta tem mais de 99,9% menos chances de ser comprometida se você usar autenticação multifatorial (MFA).

Então, como sua organização liga o MFA mesmo de graça, antes de se tornar uma estatística?

## <a name="free-option"></a>Opção gratuita

Os clientes que estão utilizando os benefícios [gratuitos](../fundamentals/concept-fundamentals-security-defaults.md) do Azure AD podem usar padrões de segurança para permitir a autenticação multifatorial em seu ambiente.

## <a name="office-365-business-premium-e3-or-e5"></a>Office 365 Business Premium, E3 ou E5

Para clientes com Office 365, há duas opções:

* A Autenticação Multifatorial do Azure está ativada ou desativada para todos os usuários, para todos os eventos de login. Não há capacidade de habilitar apenas autenticação multifatorial para um subconjunto de usuários, ou apenas em determinados cenários. A gestão é através do portal Office 365. 
* Para uma melhor experiência do usuário, atualize para Azure AD Premium P1 ou P2 e use o Conditional Access. Para obter mais informações, consulte recursos seguros do Office 365 com autenticação multifatorial.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Para clientes com Azure AD Premium P1 ou licenças similares que incluem essa funcionalidade, como Enterprise Mobility + Security E3, Microsoft 365 F1 ou Microsoft 365 E3: 

Use [o Azure AD Conditional Access](../conditional-access/overview.md) para solicitar aos usuários a autenticação multifatorial durante determinados cenários ou eventos para atender às suas necessidades de negócios.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Para clientes com Azure AD Premium P2 ou licenças similares que incluem essa funcionalidade, como Enterprise Mobility + Security E5 ou Microsoft 365 E5: 

Fornece a posição de segurança mais forte e melhor experiência do usuário. Adiciona [acesso condicional baseado em risco](../conditional-access/howto-conditional-access-policy-risk.md) aos recursos Azure AD Premium P1 que se adapta aos padrões do usuário e minimiza solicitações de autenticação de vários fatores.

## <a name="authentication-methods"></a>Métodos de autenticação

|   | Padrões de segurança | Todos os outros métodos |
| --- | --- | --- |
| Notificação pelo aplicativo móvel | X | X |
| O código de verificação do aplicativo móvel ou token de hardware |   | X |
| Mensagem de texto para telefone |   | X |
| Ligue para o telefone |   | X |

## <a name="next-steps"></a>Próximas etapas

Para começar, consulte o tutorial para [garantir eventos de login do usuário com a Autenticação Multifatorial do Azure](../authentication/tutorial-enable-azure-mfa.md).

Para obter mais informações sobre licenciamento, consulte [Recursos e licenças para Autenticação Multifatorial do Azure](../authentication/concept-mfa-licensing.md).
