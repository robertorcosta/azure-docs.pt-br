---
title: Funções que você não pode gerenciar no Gerenciamento de Identidade Privilegiada - Diretório Ativo do Azure | Microsoft Docs
description: Descreve as funções que não podem ser gerenciadas no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 03/31/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6eaa50f57dd8037ef0ad96b69284f565bd3558f
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607535"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Funções que você não pode gerenciar no Gerenciamento de Identidade Privilegiada

O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) permite que você gerencie todas as [funções do Azure AD](../users-groups-roles/directory-assign-admin-roles.md) e todas as [funções do Azure](../../role-based-access-control/built-in-roles.md). As funções do Azure também podem incluir suas funções personalizadas anexadas a seus grupos de gerenciamento, assinaturas, grupos de recursos e recursos. No entanto, há algumas poucas funções que não podem ser gerenciadas. Este artigo descreve as funções que você não pode gerenciar no Gerenciamento de Identidade Privilegiada.

## <a name="classic-subscription-administrator-roles"></a>Funções de administrador de assinatura Clássico

Não é possível gerenciar as seguintes funções clássicas de administrador de assinatura no Gerenciamento de Identidade Privilegiada:

- Administrador de conta
- Administrador de serviços
- Coadministrador

Para saber mais sobre as funções administrador de assinatura clássica, confira o artigo [Funções de administrador de assinatura clássica, funções RBAC do Azure e funções de administrador do Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>E as funções de administrador do Office 365?

Apoiamos todas as funções do Office365 na experiência do portal Azure AD Roles and Administrators, como Administrador de Exchange e Administrador de SharePoint, mas não suportamos funções específicas dentro do Exchange RBAC ou do SharePoint RBAC. Para saber mais sobre esses serviços do Office 365, confira [Funções de administrador do Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Os usuários elegíveis para a função de administrador do SharePoint, bem como quaisquer funções que tentam acessar o Microsoft Security and Compliance Center podem sofrer atrasos de até algumas horas após ativar sua função. Estamos trabalhando com essas equipes para resolver os problemas.

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções ad do Azure no gerenciamento de identidade privilegiado](pim-how-to-add-role-to-user.md)
- [Atribuir funções de recurso do Azure no Gerenciamento de Identidade Privilegiada](pim-resource-roles-assign-roles.md)
