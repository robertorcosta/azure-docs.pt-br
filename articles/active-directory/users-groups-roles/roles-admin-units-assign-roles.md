---
title: Atribuir e listar funções com o escopo de unidade administrativa (visualização)-Azure Active Directory | Microsoft Docs
description: Usando unidades administrativas para restringir o escopo de atribuições de função no Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3af281846e2bd1a39e691d84e964d8a8f780a6f1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81870428"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Atribuir funções com escopo a uma unidade administrativa

No Azure Active Directory (AD do Azure), você pode atribuir usuários a uma função do Azure AD com um escopo limitado a uma ou mais unidades administrativas (AUs) para um controle administrativo mais granular.

Para obter as etapas para se preparar para usar o PowerShell e Microsoft Graph para o gerenciamento de unidade administrativa [, consulte Introdução](roles-admin-units-manage.md#get-started).

## <a name="roles-available"></a>Funções disponíveis

Função  |  Descrição
----- |  -----------
Administrador de Autenticação  |  Tem acesso para exibir, definir e redefinir informações do método de autenticação para qualquer usuário não administrador somente na unidade administrativa atribuída.
Administrador de grupos  |  Pode gerenciar todos os aspectos de grupos e configurações de grupos, como políticas de nomenclatura e expiração somente na unidade administrativa atribuída.
Administrador de assistência técnica  |  O pode redefinir senhas para não administradores e administradores de assistência técnica somente na unidade administrativa atribuída.
Administrador de Licenças  |  Pode atribuir, remover e atualizar atribuições de licença somente dentro da unidade administrativa.
Administrador de senha  |  O pode redefinir senhas para não administradores e administradores de senha somente dentro da unidade administrativa atribuída.
Administrador de usuários  |  Pode gerenciar todos os aspectos de usuários e grupos, incluindo a redefinição de senhas para administradores limitados somente na unidade administrativa atribuída.

## <a name="assign-a-scoped-role"></a>Atribuir uma função com escopo

### <a name="azure-portal"></a>Portal do Azure

Acesse o **Azure AD > unidades administrativas** no Portal. Selecione a unidade administrativa sobre a qual você deseja atribuir a função a um usuário. No painel esquerdo, selecione funções e administradores para listar todas as funções disponíveis.

![Selecionar uma unidade administrativa para alterar o escopo da função](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

Selecione a função a ser atribuída e, em seguida, selecione **Adicionar atribuições**. Isso fará com que o slide Abra um painel à direita, onde você pode selecionar um ou mais usuários a serem atribuídos à função.

![Selecione a função para o escopo e, em seguida, selecione Adicionar atribuições](./media/roles-admin-units-assign-roles/select-add-assignment.png)

### <a name="powershell"></a>PowerShell

    $administrative = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $AdminUser = Get-AzureADUser -ObjectId 'janedoe@fabidentity.onmicrosoft.com'
    $uaRoleMemberInfo = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo -Property @{ObjectId = $AdminUser.ObjectId}
    Add-AzureADScopedRoleMembership -RoleObjectId $UserAdminRole.ObjectId -ObjectId $administrative unitObj.ObjectId -RoleMemberInfo  $uaRoleMemberInfo

A seção realçada pode ser alterada conforme necessário para o ambiente específico.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{id}/scopedRoleMembers
    
    Request body
    {
      "roleId": "roleId-value",
      "roleMemberInfo": {
        "id": "id-value"
      }
    }

## <a name="list-the-scoped-admins-on-an-au"></a>Listar os administradores com escopo em uma AU

### <a name="azure-portal"></a>Portal do Azure

Todas as atribuições de função feitas com um escopo de unidade administrativa podem ser exibidas na [seção unidades administrativas do Azure ad](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit). Acesse o **Azure AD > unidades administrativas** no Portal. Selecione a unidade de administrador para as atribuições de função que você deseja listar. Selecione **funções e administradores** e abra uma função para exibir as atribuições na unidade do administrador.

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADScopedRoleMembership -ObjectId $administrative unitObj.ObjectId | fl *

A seção realçada pode ser alterada conforme necessário para o ambiente específico.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    GET /administrativeUnits/{id}/scopedRoleMembers
    Request body
    {}

## <a name="next-steps"></a>Próximas etapas

- [Perguntas frequentes e solução de problemas de unidades administrativas](roles-admin-units-faq-troubleshoot.md)
