---
title: Adicionar, remover e listar grupos em uma unidade administrativa (pré-visualização) - Azure Active Directory | Microsoft Docs
description: Gerenciar grupos e suas permissões de função em uma unidade administrativa no Azure Active Directory
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
ms.openlocfilehash: 78f45d9e1ca5b8237779fe096154c83749aa08fe
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428427"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>Adicionar e gerenciar grupos em unidades administrativas no Azure Active Directory

No Azure Active Directory (Azure AD), você pode adicionar grupos a uma unidade administrativa (AU) para um escopo administrativo mais granular de controle.

Para obter etapas para se preparar para usar o PowerShell e o Microsoft Graph para gerenciamento de unidades administrativas, consulte [Como começar](roles-admin-units-manage.md#getting-started).

## <a name="add-groups-to-an-au"></a>Adicionar grupos a uma UA

### <a name="azure-portal"></a>Portal do Azure

Na pré-visualização, você pode atribuir grupos apenas individualmente a uma unidade administrativa. Não há opção de cessão em massa de grupos para uma unidade administrativa. Você pode atribuir um grupo a uma unidade administrativa de uma das duas maneiras no portal:

1. Da página **Azure AD > Groups**

    Abra a página de visão geral de grupos no Azure AD e selecione o grupo que precisa ser atribuído à unidade administrativa. No lado esquerdo, selecione **unidades administrativas** para listar as unidades administrativas a que o grupo é designado. Na parte superior você encontrará a opção Atribuir à unidade administrativa e clicar nela dará um painel no lado direito para escolher a unidade administrativa.

    ![atribuir um grupo individualmente a uma unidade administrativa](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. Da página **Azure AD > Unidades Administrativas > Todos os Grupos**

    Abra a lâmina All Groups em Azure AD > Unidades Administrativas. Se houver grupos já designados para a unidade administrativa, eles serão exibidos no lado direito. Selecione **Adicionar** na parte superior e um painel à direita será deslizado na listagem dos grupos disponíveis na organização Azure AD. Selecione um ou mais grupos a serem atribuídos às unidades administrativas.

    ![selecionar uma unidade administrativa e, em seguida, selecionar Adicionar membro](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId

Neste exemplo, o cmdlet Add-AzureADAdministrativeUnitMember é usado para adicionar o grupo à unidade administrativa. O ID do objeto da unidade administrativa e o ID do objeto do grupo a ser adicionado são tomados como argumento. A seção destacada pode ser alterada conforme necessário para o ambiente específico.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref

    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/groups/{id}"
    }

Exemplo:

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
    }

## <a name="list-groups-in-an-au"></a>Listar grupos em uma UA

### <a name="azure-portal"></a>Portal do Azure

Vá para **Azure AD > unidades administrativas** no portal. Selecione a unidade administrativa para a qual deseja listar os usuários. Por padrão, **todos os usuários** são selecionados já no painel esquerdo. Selecione **Todos os grupos** e à direita você encontrará a lista de grupos que são membros da unidade administrativa selecionada.

![Selecione uma unidade administrativa para excluir](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId

Isso vai ajudá-lo a obter todos os membros da unidade administrativa. Se você quiser exibir todos os grupos que são membros da unidade administrativa, você pode usar o trecho de código abaixo:

    foreach ($member in (Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId)) 
    {
    if($member.ObjectType -eq "Group")
    {
    Get-AzureADGroup -ObjectId $member.ObjectId
    }
    }

### <a name="microsoft-graph"></a>Microsoft Graph

    HTTP request
    GET /administrativeUnits/{Admin id}/members/$/microsoft.graph.group
    Request body
    {}

## <a name="list-aus-for-a-group"></a>Listar UA para um grupo

### <a name="azure-portal"></a>Portal do Azure

No portal Azure AD, você pode abrir os detalhes de um grupo abrindo **Grupos**. Selecione um grupo para abrir o perfil do grupo. Selecione **unidades administrativas** para listar todas as unidades administrativas onde o grupo é membro.

![Listar unidades administrativas para um grupo](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-group-from-an-au"></a>Remova um grupo de uma UA

### <a name="azure-portal"></a>Portal do Azure

Há duas maneiras de remover um grupo de uma unidade administrativa no portal Azure.

Abra os > **grupos** **ad do Azure**e abra o perfil para o grupo que deseja remover da unidade administrativa. Selecione **unidades administrativas** no painel esquerdo para listar todas as unidades administrativas onde o grupo é membro. Selecione a unidade administrativa da a que deseja remover o grupo e, em seguida, **selecione Remover na unidade administrativa**.

![Remova um grupo de uma unidade administrativa](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

Alternativamente, você pode ir às**unidades administrativas** **do Azure AD** > e selecionar a unidade administrativa onde o grupo é membro. Selecione **Grupos** no painel esquerdo para listar os grupos membros. Selecione o grupo a ser removido da unidade administrativa e, em seguida, **selecione Remover grupos**.

![Listar grupos em uma unidade administrativa](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<group-id>/$ref

## <a name="next-steps"></a>Próximas etapas

- [Atribuir uma função a uma unidade administrativa](roles-admin-units-assign-roles.md)
- [Gerencie usuários em uma unidade administrativa](roles-admin-units-add-manage-users.md)
