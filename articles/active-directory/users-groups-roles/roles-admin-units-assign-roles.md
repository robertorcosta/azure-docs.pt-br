---
title: Atribuir e listar funções com escopo de unidade administrativa-Azure Active Directory | Microsoft Docs
description: Usando unidades administrativas para restringir o escopo de atribuições de função no Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00d60d803fb73a0f299b00179e5f598f3189e03e
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92309661"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Atribuir funções com escopo a uma unidade administrativa

No Azure Active Directory (AD do Azure), você pode atribuir usuários a uma função do Azure AD com um escopo limitado a uma ou mais unidades administrativas (AUs) para um controle administrativo mais granular.

Para saber como se preparar para usar o PowerShell e o Microsoft Graph para o gerenciamento de unidade administrativa, consulte a [Introdução](roles-admin-units-manage.md#get-started).

## <a name="roles-available"></a>Funções disponíveis

Função  |  Descrição
----- |  -----------
Administrador de Autenticação  |  Tem acesso para exibir, definir e redefinir informações do método de autenticação para qualquer usuário não administrador somente na unidade administrativa atribuída.
Administrador de Grupos  |  Pode gerenciar todos os aspectos de grupos e configurações de grupos, como políticas de nomenclatura e expiração somente na unidade administrativa atribuída.
Administrador de assistência técnica  |  O pode redefinir senhas para não administradores e administradores de assistência técnica somente na unidade administrativa atribuída.
Administrador de Licenças  |  Pode atribuir, remover e atualizar atribuições de licença somente dentro da unidade administrativa.
Administrador de senha  |  O pode redefinir senhas para não administradores e administradores de senha somente dentro da unidade administrativa atribuída.
Administrador de usuários  |  Pode gerenciar todos os aspectos de usuários e grupos, incluindo a redefinição de senhas para administradores limitados somente na unidade administrativa atribuída.

## <a name="security-principals-that-can-be-assigned-to-a-scoped-role"></a>Entidades de segurança que podem ser atribuídas a uma função com escopo

As entidades de segurança a seguir podem ser atribuídas a uma função com um escopo de unidade administrativa:

* Usuários
* Grupos de nuvem atribuíveis de função (visualização)
* SPN (Nome da Entidade de Serviço)

## <a name="assign-a-scoped-role"></a>Atribuir uma função com escopo

### <a name="azure-portal"></a>Portal do Azure

Vá para **Azure Active Directory > Unidades administrativas** no portal. Selecione a unidade administrativa sobre a qual você deseja atribuir a função a um usuário. No painel esquerdo, selecione funções e administradores para listar todas as funções disponíveis.

![Selecionar uma unidade administrativa para alterar o escopo da função](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

Selecione a função a ser atribuída e, em seguida, selecione **Adicionar atribuições**. Um painel é aberto à direita onde você pode selecionar um ou mais usuários a serem atribuídos à função.

![Selecione a função para o escopo e, em seguida, selecione Adicionar atribuições](./media/roles-admin-units-assign-roles/select-add-assignment.png)

> [!Note]
>
> Para atribuir uma função em uma unidade administrativa usando o PIM, siga as etapas [aqui](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope).

### <a name="powershell"></a>PowerShell

```powershell
$AdminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$Role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Account Administrator"
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$RoleMember = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo
$RoleMember.ObjectId = $AdminUser.ObjectId
Add-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId -RoleObjectId $Role.ObjectId -RoleMemberInfo $RoleMember
```

A seção realçada pode ser alterada conforme necessário para o ambiente específico.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /directory/administrativeUnits/{id}/scopedRoleMembers
    
Request body
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="list-the-scoped-admins-on-an-au"></a>Listar os administradores com escopo em uma AU

### <a name="azure-portal"></a>Portal do Azure

Todas as atribuições de função feitas com um escopo de unidade administrativa podem ser exibidas na [seção unidades administrativas do Azure ad](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit). Vá para **Azure Active Directory > Unidades administrativas** no portal. Selecione a unidade de administrador para as atribuições de função que você deseja listar. Selecione **funções e administradores** e abra uma função para exibir as atribuições na unidade do administrador.

### <a name="powershell"></a>PowerShell

```powershell
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId | fl *
```

A seção realçada pode ser alterada conforme necessário para o ambiente específico.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
GET /directory/administrativeUnits/{id}/scopedRoleMembers
Request body
{}
```

## <a name="next-steps"></a>Próximas etapas

- [Usar grupos de nuvem para gerenciar atribuições de função](roles-groups-concept.md)
- [Solução de problemas de funções atribuídas a grupos de nuvem](roles-groups-faq-troubleshooting.md)