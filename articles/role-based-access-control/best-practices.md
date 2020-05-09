---
title: Práticas recomendadas para o RBAC do Azure
description: Práticas recomendadas para usar o controle de acesso baseado em função do Azure (RBAC do Azure).
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
ms.openlocfilehash: 053e86f3493c7a11a3cbbaad0871e45345697878
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735327"
---
# <a name="best-practices-for-azure-rbac"></a>Práticas recomendadas para o RBAC do Azure

Este artigo descreve algumas práticas recomendadas para usar o controle de acesso baseado em função do Azure (RBAC do Azure). Essas práticas recomendadas derivam de nossa experiência com o RBAC do Azure e as experiências de clientes como você.

## <a name="only-grant-the-access-users-need"></a>Conceder somente aos usuários de acesso necessário

Com o Azure RBAC, você pode separar as tarefas dentro de sua equipe e permitir somente a quantidade de acesso de que os usuários precisam para realizar os trabalhos deles. Em vez de apresentar todos irrestrito permissões em sua assinatura do Azure ou recursos, você pode permitir apenas determinadas ações para um escopo específico.

Ao planejar sua estratégia de controle de acesso, uma melhor prática é conceder aos usuários o privilégio mínimo para realizarem seus trabalhos. O diagrama a seguir mostra um padrão sugerido para usar o RBAC do Azure.

![RBAC do Azure e privilégios mínimos](./media/best-practices/rbac-least-privilege.png)

Para obter informações sobre como adicionar atribuições de função, consulte [Adicionar ou remover atribuições de função do Azure usando o portal do Azure](role-assignments-portal.md).

## <a name="limit-the-number-of-subscription-owners"></a>Limitar o número de proprietários da assinatura

Você deve ter um máximo de 3 proprietários de assinatura para reduzir o potencial de violação por um proprietário comprometido. Essa recomendação pode ser monitorada na central de segurança do Azure. Para outras recomendações de identidade e acesso na central de segurança, consulte [recomendações de segurança – um guia de referência](../security-center/recommendations-reference.md).

## <a name="use-azure-ad-privileged-identity-management"></a>Usar Azure AD Privileged Identity Management

Para proteger contas privilegiadas contra ataques cibernéticos mal-intencionados, use o PIM (Azure Active Directory Privileged Identity Management) para reduzir o tempo de exposição de privilégios e aumentar a visibilidade de seu uso por meio de relatórios e alertas. O PIM ajuda a proteger contas privilegiadas fornecendo acesso privilegiado just-in-time ao Azure AD e aos recursos do Azure. O acesso pode ser associado a tempo após o qual os privilégios são revogados automaticamente. 

Para obter mais informações, confira [O que é o Privileged Identity Management do Azure AD?](../active-directory/privileged-identity-management/pim-configure.md).

## <a name="next-steps"></a>Próximas etapas

- [Solucionar problemas do RBAC do Azure](troubleshooting.md)