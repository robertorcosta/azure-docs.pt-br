---
title: Solucionar um problema com o Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Saiba como solucionar problemas de erros do sistema com funções no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 10/18/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d2ba5806e23aa87e5d7787be300512444f95b25
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84742140"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>Solucionar um problema com Privileged Identity Management

Você está tendo um problema com Privileged Identity Management (PIM) no Azure Active Directory (Azure AD)? As informações a seguir podem ajudá-lo a fazer as coisas funcionarem novamente.

## <a name="access-to-azure-resources-denied"></a>Acesso aos recursos do Azure negado

### <a name="problem"></a>Problema

Como proprietário ativo ou administrador de acesso do usuário para um recurso do Azure, você pode ver seu recurso dentro de Privileged Identity Management, mas não pode executar ações como fazer uma atribuição qualificada ou exibir uma lista de atribuições de função na página Visão geral do recurso. Qualquer uma dessas ações resulta em um erro de autorização.

### <a name="cause"></a>Causa

Esse problema pode ocorrer quando a função Administrador de acesso do usuário para a entidade de serviço PIM foi acidentalmente removida da assinatura. Para que o serviço de Privileged Identity Management seja capaz de acessar recursos do Azure, a entidade de serviço do MS-PIM sempre deve ter atribuído a [função de administrador de acesso do usuário](../../role-based-access-control/built-in-roles.md#user-access-administrator) pela assinatura do Azure.

### <a name="resolution"></a>Resolução

Atribua a função de administrador de acesso do usuário ao nome da entidade de serviço do Privileged Identity Management (MS – PIM) no nível da assinatura. Essa atribuição deve permitir que o serviço Privileged Identity Management acesse os recursos do Azure. A função pode ser atribuída em um nível de grupo de gerenciamento ou no nível de assinatura, dependendo de seus requisitos. Para obter mais informações sobre entidades de serviço, consulte [atribuir um aplicativo a uma função](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application).

## <a name="next-steps"></a>Próximas etapas

- [Requisitos de licença para usar o Privileged Identity Management](subscription-requirements.md)
- [Protegendo o acesso privilegiado para implantações de nuvem e híbridos no Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Implantar o Privileged Identity Management](pim-deployment-plan.md)
