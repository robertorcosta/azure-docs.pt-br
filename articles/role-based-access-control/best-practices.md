---
title: Melhores práticas para Azure RBAC
description: As melhores práticas para usar o controle de acesso baseado em função do Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: dc86dd488ff9e8649ae80f4768941791dd37fce6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726768"
---
# <a name="best-practices-for-azure-rbac"></a>Melhores práticas para Azure RBAC

Este artigo descreve algumas práticas recomendadas para usar o controle de acesso baseado em função do Azure (Azure RBAC). Essas melhores práticas são derivadas de nossa experiência com o Azure RBAC e das experiências de clientes como você.

## <a name="only-grant-the-access-users-need"></a>Apenas conceda o acesso que os usuários precisam

Usando o Azure RBAC, você pode segregar funções dentro de sua equipe e conceder apenas a quantidade de acesso aos usuários que eles precisam para realizar seus trabalhos. Em vez de apresentar todos irrestrito permissões em sua assinatura do Azure ou recursos, você pode permitir apenas determinadas ações para um escopo específico.

Ao planejar sua estratégia de controle de acesso, uma melhor prática é conceder aos usuários o privilégio mínimo para realizarem seus trabalhos. O diagrama a seguir mostra um padrão sugerido para o uso de RBAC.

![RBAC e privilégio mínimo](./media/best-practices/rbac-least-privilege.png)

Para obter informações sobre como adicionar atribuições de função, consulte [Adicionar ou remover atribuições de função](role-assignments-portal.md).

## <a name="limit-the-number-of-subscription-owners"></a>Limitar o número de proprietários de assinaturas

Você deve ter um máximo de 3 proprietários de assinatura para reduzir o potencial de violação por um proprietário comprometido. Esta recomendação pode ser monitorada no Azure Security Center. Para outras recomendações de identidade e acesso no Security Center, consulte [recomendações de segurança - um guia de referência](../security-center/recommendations-reference.md).

## <a name="use-azure-ad-privileged-identity-management"></a>Use Azure AD Privileged Identity Management

Para proteger contas privilegiadas contra ataques cibernéticos mal-intencionados, use o PIM (Azure Active Directory Privileged Identity Management) para reduzir o tempo de exposição de privilégios e aumentar a visibilidade de seu uso por meio de relatórios e alertas. O PIM ajuda a proteger contas privilegiadas, fornecendo acesso privilegiado just-in-time aos recursos do Azure AD e do Azure. O acesso pode ser limitado após o qual os privilégios são revogados automaticamente. 

Para obter mais informações, confira [O que é o Privileged Identity Management do Azure AD?](../active-directory/privileged-identity-management/pim-configure.md).

## <a name="next-steps"></a>Próximas etapas

- [Solução de problemas Azure RBAC](troubleshooting.md)