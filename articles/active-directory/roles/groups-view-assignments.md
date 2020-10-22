---
title: Exibir funções atribuídas a um grupo no Azure Active Directory | Microsoft Docs
description: Saiba como as funções atribuídas a um grupo podem ser exibidas usando o centro de administração do Azure AD. A exibição de grupos e funções atribuídas são permissões de usuário padrão.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e578c90a05085df33c2d547402256cfc38229aa3
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92374449"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Exibir funções atribuídas a um grupo no Azure Active Directory

Esta seção descreve como as funções atribuídas a um grupo podem ser exibidas usando o centro de administração do Azure AD. A exibição de grupos e funções atribuídas são permissões de usuário padrão.

1. Entre no centro de [Administração do Azure ad](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com quaisquer credenciais de administrador ou não administrador.

1. Selecione o grupo no qual você está interessado.

1. Selecione **funções atribuídas**. Agora você pode ver todas as funções do Azure AD atribuídas a esse grupo.

   ![Exibir todas as funções atribuídas a um grupo selecionado](./media/groups-view-assignments/view-assignments.png)

## <a name="using-powershell"></a>Usando o PowerShell

### <a name="get-object-id-of-the-group"></a>Obter ID de objeto do grupo

```powershell
Get-AzureADMSGroup -SearchString “Contoso_Helpdesk_Administrators”
```

### <a name="view-role-assignment-to-a-group"></a>Exibir a atribuição de função a um grupo

```powershell
Get-AzureADMSRoleAssignment -Filter "principalId eq '<object id of group>" 
```

## <a name="using-microsoft-graph-api"></a>Usando a API Microsoft Graph

### <a name="get-object-id-of-the-group"></a>Obter ID de objeto do grupo

```powershell
GET https://graph.microsoft.com/beta/groups?$filter displayName eq ‘Contoso_Helpdesk_Administrator’ 
```

### <a name="get-role-assignments-to-a-group"></a>Obter atribuições de função a um grupo

```powershell
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq
```

## <a name="next-steps"></a>Próximas etapas

- [Usar grupos de nuvem para gerenciar atribuições de função](groups-concept.md)
- [Solução de problemas de funções atribuídas a grupos de nuvem](groups-faq-troubleshooting.md)
