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
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fac7074cf85a585c93ece60be9eea8ffb9a6345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72895196"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Funções que você não pode gerenciar no Gerenciamento de Identidade Privilegiada

O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) permite que você gerencie todas as [funções do Azure AD](../users-groups-roles/directory-assign-admin-roles.md) e todas as [funções de recursos do Azure.](../../role-based-access-control/built-in-roles.md) Essas funções também incluem as funções personalizadas anexadas a grupos de gerenciamento, assinaturas, grupos de recursos e recursos. No entanto, há algumas poucas funções que não podem ser gerenciadas. Este artigo descreve as funções que você não pode gerenciar no Gerenciamento de Identidade Privilegiada.

## <a name="classic-subscription-administrator-roles"></a>Funções de administrador de assinatura Clássico

Não é possível gerenciar as seguintes funções clássicas de administrador de assinatura no Gerenciamento de Identidade Privilegiada:

- Administrador de conta
- Administrador de serviços
- Coadministrador

Para saber mais sobre as funções administrador de assinatura clássica, confira o artigo [Funções de administrador de assinatura clássica, funções RBAC do Azure e funções de administrador do Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>E as funções de administrador do Office 365?

As funções no Exchange Online ou no SharePoint Online, exceto no Administrador de Exchange e no Administrador do SharePoint, não estão representadas no Azure AD e, portanto, não podem ser gerenciadas no Gerenciamento de Identidade Privilegiada. Para saber mais sobre esses serviços do Office 365, confira [Funções de administrador do Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> O administrador do SharePoint tem acesso administrativo ao SharePoint Online por meio do Centro de Administração do SharePoint Online e pode executar quase qualquer tarefa no SharePoint Online. Os usuários elegíveis podem sofrer atrasos usando essa função no SharePoint após a ativação no Gerenciamento de Identidade Privilegiada.

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções ad do Azure no gerenciamento de identidade privilegiado](pim-how-to-add-role-to-user.md)
- [Atribuir funções de recurso do Azure no Gerenciamento de Identidade Privilegiada](pim-resource-roles-assign-roles.md)
