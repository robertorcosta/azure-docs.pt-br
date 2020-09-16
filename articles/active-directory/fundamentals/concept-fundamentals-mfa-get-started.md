---
title: Autenticação Multifator do Azure para sua organização – Azure Active Directory
description: Saiba mais sobre os recursos disponíveis da Autenticação Multifator do Azure para sua organização com base em seu modelo de licença
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
ms.openlocfilehash: 13bb7782f6396466200fbb3e2df77158bb75d8a4
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90705394"
---
# <a name="overview-of-azure-multi-factor-authentication-for-your-organization"></a>Visão geral da Autenticação Multifator do Azure para sua organização

Há várias maneiras de habilitar a Autenticação Multifator do Azure para seus usuários do Azure AD (Active Directory) com base nas licenças que sua organização tem. 

![Investigar sinais e impor a MFA, se necessário](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Com base em nossos estudos, sua conta tem uma probabilidade mais de 99,9% menor de ser comprometida se você usar a MFA (autenticação multifator).

Então, como a sua organização ativa a MFA mesmo gratuitamente antes de se tornar uma estatística?

## <a name="free-option"></a>Opção gratuita

Os clientes que estão aproveitando os benefícios gratuitos do Azure AD podem usar [padrões de segurança](../fundamentals/concept-fundamentals-security-defaults.md) para habilitar a autenticação multifator no ambiente.

## <a name="microsoft-365-business-e3-or-e5"></a>Microsoft 365 Business, E3 ou E5

Para clientes com Microsoft 365, há duas opções:

* A Autenticação Multifator do Azure está habilitada ou desabilitada para todos os usuários para todos os eventos de conexão. Não é possível habilitar a autenticação multifator para um subconjunto de usuários ou somente em determinados cenários. O gerenciamento é por meio do portal do Office 365. 
* Para ter uma experiência de usuário aprimorada, atualize para o Azure AD Premium P1 ou P2 e use o acesso condicional. Para obter mais informações, consulte proteger recursos de Microsoft 365 com a autenticação multifator.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Para clientes com Azure AD Premium P1 ou licenças semelhantes que incluem essa funcionalidade, como Enterprise Mobility + Security E3, Microsoft 365 F1 ou Microsoft 365 E3: 

Use o [Acesso condicional do Azure AD](../authentication/tutorial-enable-azure-mfa.md) para solicitar aos usuários a autenticação multifator durante determinados cenários ou eventos para atender às suas necessidades de negócios.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Para clientes com Azure AD Premium P2 ou licenças semelhantes que incluem essa funcionalidade, como Enterprise Mobility + Security E5 ou Microsoft 365 E5: 

Fornece a posição de segurança mais forte e a melhor experiência do usuário. Adiciona [Acesso condicional baseado em risco](../conditional-access/howto-conditional-access-policy-risk.md) aos recursos do Azure AD Premium P1 que se adapta aos padrões do usuário e minimiza os prompts da autenticação multifator.

## <a name="authentication-methods"></a>Métodos de autenticação

| Método | Padrões de segurança | Todos os outros métodos |
| --- | --- | --- |
| Notificação pelo aplicativo móvel | X | X |
| O código de verificação do aplicativo móvel ou token de hardware |   | X |
| Mensagem de texto para telefone |   | X |
| Ligue para o telefone |   | X |

## <a name="next-steps"></a>Próximas etapas

Para começar, confira o tutorial para [proteger eventos de conexão do usuário com a autenticação multifator do Azure](../authentication/tutorial-enable-azure-mfa.md).

Para obter mais informações sobre licenciamento, confira [Recursos e licenças para a autenticação multifator do Azure](../authentication/concept-mfa-licensing.md).
