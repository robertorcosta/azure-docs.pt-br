---
title: Atribuir e listar funções com escopo de unidade administrativa (visualização) - Diretório Ativo do Azure | Microsoft Docs
description: Usando unidades administrativas para restringir o escopo de atribuições de função no Diretório Ativo do Azure
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
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870428"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Atribuir funções escopo a uma unidade administrativa

No Azure Active Directory (Azure AD), você pode atribuir usuários a uma função AD do Azure com um escopo limitado a uma ou mais unidades administrativas (AUs) para um controle administrativo mais granular.

Para obter etapas para se preparar para usar o PowerShell e o Microsoft Graph para gerenciamento de unidades administrativas, consulte [Iniciar](roles-admin-units-manage.md#get-started).

## <a name="roles-available"></a>Funções disponíveis

Função  |  Descrição
----- |  -----------
Administrador de Autenticação  |  Tem acesso às informações do método de autenticação de exibição, conjunto e reset para qualquer usuário não administrativo apenas na unidade administrativa atribuída.
Administrador de grupos  |  Pode gerenciar todos os aspectos das configurações de grupos e grupos, como políticas de nomeação e expiração apenas na unidade administrativa atribuída.
Administrador de assistência técnica  |  Pode redefinir senhas para não-administradores e administradores do Helpdesk apenas na unidade administrativa atribuída.
Administrador de Licenças  |  Pode atribuir, remover e atualizar atribuições de licença somente na unidade administrativa.
Administrador de senha  |  Pode redefinir senhas para não-administradores e administradores de senha somente na unidade administrativa atribuída.
Administrador de usuários  |  Pode gerenciar todos os aspectos dos usuários e grupos, incluindo a redefinição de senhas para administração limitada apenas na unidade administrativa atribuída.

## <a name="assign-a-scoped-role"></a>Atribuir um papel escopo

### <a name="azure-portal"></a>Portal do Azure

Vá para **Azure AD > unidades administrativas** no portal. Selecione a unidade administrativa sobre a qual deseja atribuir a função a um usuário. No painel esquerdo, selecione Funções e administradores para listar todas as funções disponíveis.

![Selecione uma unidade administrativa para alterar o escopo da função](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

Selecione a função a ser atribuída e, em seguida, **selecione Adicionar atribuições**. Isso irá deslizar um painel à direita onde você pode selecionar um ou mais usuários a serem atribuídos à função.

![Selecione a função para escopo e, em seguida, selecione Adicionar atribuições](./media/roles-admin-units-assign-roles/select-add-assignment.png)

### <a name="powershell"></a>PowerShell

    $administrative = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $AdminUser = Get-AzureADUser -ObjectId 'janedoe@fabidentity.onmicrosoft.com'
    $uaRoleMemberInfo = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo -Property @{ObjectId = $AdminUser.ObjectId}
    Add-AzureADScopedRoleMembership -RoleObjectId $UserAdminRole.ObjectId -ObjectId $administrative unitObj.ObjectId -RoleMemberInfo  $uaRoleMemberInfo

A seção destacada pode ser alterada conforme necessário para o ambiente específico.

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

## <a name="list-the-scoped-admins-on-an-au"></a>Liste os admins escopo em um UA

### <a name="azure-portal"></a>Portal do Azure

Todas as atribuições de função feitas com escopo de unidade administrativa podem ser visualizadas na [seção unidades administrativas do Azure AD](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit). Vá para **Azure AD > unidades administrativas** no portal. Selecione a unidade de admin para as atribuições de função que deseja listar. Selecione **Funções e administradores** e abra uma função para visualizar as atribuições na unidade de administração.

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADScopedRoleMembership -ObjectId $administrative unitObj.ObjectId | fl *

A seção destacada pode ser alterada conforme necessário para o ambiente específico.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    GET /administrativeUnits/{id}/scopedRoleMembers
    Request body
    {}

## <a name="next-steps"></a>Próximas etapas

- [Solucionando problemas de unidades administrativas e perguntas frequentes](roles-admin-units-faq-troubleshoot.md)
