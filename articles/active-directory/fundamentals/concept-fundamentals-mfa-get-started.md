---
title: Autenticação multifator do Azure para sua organização-Azure Active Directory
description: Saiba mais sobre os recursos disponíveis da autenticação multifator do Azure para sua organização com base em seu modelo de licença
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79530386"
---
# <a name="overview-of-azure-multi-factor-authentication-for-your-organization"></a>Visão geral da autenticação multifator do Azure para sua organização

Há várias maneiras de habilitar a autenticação multifator do Azure para seus usuários do Azure Active Directory (AD) com base nas licenças que sua organização possui. 

![Investigar sinais e impor a MFA, se necessário](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Com base em nossos estudos, sua conta tem mais de 99,9% menos provável de ser comprometida se você usar a MFA (autenticação multifator).

Então, como a sua organização ativa a MFA mesmo gratuitamente, antes de se tornar uma estatística?

## <a name="free-option"></a>Opção gratuita

Os clientes que estão utilizando os benefícios gratuitos do Azure AD podem usar os [padrões de segurança](../fundamentals/concept-fundamentals-security-defaults.md) para habilitar a autenticação multifator em seu ambiente.

## <a name="office-365-business-premium-e3-or-e5"></a>Office 365 Business Premium, E3 ou e5

Para clientes com o Office 365, há duas opções:

* A autenticação multifator do Azure está habilitada ou desabilitada para todos os usuários, para todos os eventos de entrada. Não há capacidade de habilitar a autenticação multifator para um subconjunto de usuários ou somente em determinados cenários. O gerenciamento é por meio do portal do Office 365. 
* Para uma experiência de usuário aprimorada, atualize para Azure AD Premium P1 ou P2 e use o acesso condicional. Para obter mais informações, consulte proteger recursos do Office 365 com a autenticação multifator.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Para clientes com Azure AD Premium P1 ou licenças semelhantes que incluem essa funcionalidade, como Enterprise Mobility + Security E3, Microsoft 365 F1 ou Microsoft 365 E3: 

Use o [acesso condicional do Azure ad](../conditional-access/overview.md) para solicitar aos usuários a autenticação multifator durante determinados cenários ou eventos para atender às suas necessidades de negócios.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Para clientes com Azure AD Premium P2 ou licenças semelhantes que incluem essa funcionalidade, como Enterprise Mobility + Security E5 ou Microsoft 365 E5: 

Fornece a posição de segurança mais forte e a melhor experiência do usuário. Adiciona [acesso condicional com base em risco](../conditional-access/howto-conditional-access-policy-risk.md) aos recursos Azure ad Premium P1 que se adapta aos padrões do usuário e minimiza os prompts de autenticação multifator.

## <a name="authentication-methods"></a>Métodos de autenticação

|   | Padrões de segurança | Todos os outros métodos |
| --- | --- | --- |
| Notificação pelo aplicativo móvel | X | X |
| O código de verificação do aplicativo móvel ou token de hardware |   | X |
| Mensagem de texto para telefone |   | X |
| Ligue para o telefone |   | X |

## <a name="next-steps"></a>Próximas etapas

Para começar, consulte o tutorial para [proteger eventos de entrada do usuário com a autenticação multifator do Azure](../authentication/tutorial-enable-azure-mfa.md).

Para obter mais informações sobre licenciamento, consulte [recursos e licenças para a autenticação multifator do Azure](../authentication/concept-mfa-licensing.md).
