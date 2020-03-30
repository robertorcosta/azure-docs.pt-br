---
title: Solucionaum um problema com o Privileged Identity Management - Azure Active Directory | Microsoft Docs
description: Aprenda a solucionar erros do sistema com funções no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/18/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 474f2634e6f7ddc1840548c39ae86cb54c3bf08e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299662"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>Solucionar problemas com o Gerenciamento de Identidade Privilegiada

Você está tendo um problema com o Privileged Identity Management (PIM) no Azure Active Directory (Azure AD)? As informações a seguir podem ajudá-lo a fazer as coisas funcionarem novamente.

## <a name="access-to-azure-resources-denied"></a>Acesso aos recursos do Azure negado

### <a name="problem"></a>Problema

Como um proprietário ativo ou administrador de acesso ao usuário para um recurso do Azure, você pode ver seu recurso dentro do Gerenciamento de Identidade Privilegiada, mas não pode executar nenhuma ação, como fazer uma atribuição elegível ou visualizar uma lista de atribuições de função do recurso página de visão geral. Qualquer uma dessas ações resulta em um erro de autorização.

### <a name="cause"></a>Causa

Esse problema pode acontecer quando a função administradora de acesso do usuário para o principal do serviço PIM foi acidentalmente removida da assinatura. Para que o serviço de Gerenciamento de Identidade Privilegiado possa acessar os recursos do Azure, o diretor de serviço sin-pim deve sempre ter sido atribuído à [função administrador de acesso](../../role-based-access-control/built-in-roles.md#user-access-administrator) ao usuário sobre a assinatura do Azure.

### <a name="resolution"></a>Resolução

Atribua a função de Administrador de Acesso ao Usuário ao nome principal do serviço de gerenciamento de identidade privilegiado (MS–PIM) no nível de assinatura. Essa atribuição deve permitir que o serviço de gerenciamento de identidade privilegiado acesse os recursos do Azure. A função pode ser atribuída em um nível de grupo de gerenciamento ou no nível de assinatura, dependendo de suas necessidades. Para obter mais diretores de serviçode informações, consulte [Atribuir um aplicativo a uma função](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application).

## <a name="next-steps"></a>Próximas etapas

- [Requisitos de licença para usar o Privileged Identity Management](subscription-requirements.md)
- [Protegendo o acesso privilegiado para implantações de nuvem e híbridos no Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Implantar o Privileged Identity Management](pim-deployment-plan.md)
