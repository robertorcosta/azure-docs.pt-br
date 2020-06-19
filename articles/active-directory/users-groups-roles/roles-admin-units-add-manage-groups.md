---
title: Adicionar, remover e listar grupos em uma unidade administrativa (versão prévia) - Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: 2547a0fdbfbcc143ff822de333b41198f469375c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649328"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>Adicionar e gerenciar grupos em unidades administrativas no Azure Active Directory

No Azure Active Directory, você pode adicionar grupos a uma AU (unidade administrativa) para ter um escopo de controle administrativo mais detalhado.

Para saber como se preparar para usar o PowerShell e o Microsoft Graph para o gerenciamento de unidade administrativa, consulte a [Introdução](roles-admin-units-manage.md#get-started).

## <a name="add-groups-to-an-au"></a>Adicionar grupos a uma AU

### <a name="azure-portal"></a>Portal do Azure

Na versão prévia, você só pode atribuir grupos individualmente a uma unidade administrativa. Não há nenhuma opção de atribuição em massa de grupos a uma unidade administrativa. Você pode atribuir um grupo a uma unidade administrativa de duas maneiras no portal:

1. Na página **Azure Active Directory > Grupos**

    Abra a página de visão geral de grupos no Azure Active Directory e selecione o grupo que precisa ser atribuído à unidade administrativa. No lado esquerdo, selecione **Unidades administrativas** para listar as unidades administrativas às quais o grupo está atribuído. Na parte superior, você encontrará a opção Atribuir à unidade administrativa. Clique nela para exibir um painel no lado direito para escolher a unidade administrativa.

    ![atribuir um grupo individualmente a uma unidade administrativa](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. Na página **Azure Active Directory > Unidades administrativas > Todos os grupos**

    Abra a folha “Todos os grupos” em Azure Active Directory > Unidades administrativas. Se houver grupos já atribuídos à unidade administrativa, eles serão exibidos no lado direito. Selecione **Adicionar** na parte superior e um painel direito deslizará para mostrar uma lista dos grupos disponíveis em sua organização do Azure Active Directory. Selecione um ou mais grupos a serem atribuídos às unidades administrativas.

    ![selecione uma unidade administrativa e, em seguida, selecione Adicionar membro](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId

Neste exemplo, o cmdlet Add-AzureADAdministrativeUnitMember é usado para adicionar o grupo à unidade administrativa. A ID de objeto da unidade administrativa e a ID de objeto do grupo a ser adicionado são executadas como argumento. A seção realçada pode ser alterada conforme necessário para o ambiente específico.

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

## <a name="list-groups-in-an-au"></a>Listar grupos em uma AU

### <a name="azure-portal"></a>Portal do Azure

Vá para **Azure Active Directory > Unidades administrativas** no portal. Selecione a unidade administrativa para a qual você deseja listar os usuários. Por padrão, **Todos os usuários** está selecionado no painel esquerdo. Selecione **Todos os grupos** e, à direita, você verá a lista dos grupos membros da unidade administrativa selecionada.

![Listar grupos em uma unidade administrativa](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId

Isso ajudará a obter todos os membros da unidade administrativa. Se quiser exibir todos os grupos membros da unidade administrativa, você poderá usar o snippet de código abaixo:

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

## <a name="list-aus-for-a-group"></a>Listar AUs para um grupo

### <a name="azure-portal"></a>Portal do Azure

No portal do Azure Active Directory, você pode ver os detalhes de um grupo se abrir **Grupos**. Selecione um grupo para abrir o perfil do grupo. Selecione **Unidades administrativas** para listar todas as unidades administrativas em que o grupo é membro.

![Listar unidades administrativas para um grupo](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-group-from-an-au"></a>Remover um grupo de uma AU

### <a name="azure-portal"></a>Portal do Azure

Há duas maneiras de remover um grupo de uma unidade administrativa no portal do Azure.

Abra **Azure Active Directory** > **Grupos** e abra o perfil do grupo que você quer remover da unidade administrativa. Selecione **Unidades administrativas** no painel esquerdo para listar todas as unidades administrativas em que o grupo é membro. Selecione a unidade administrativa da qual você deseja remover o grupo e, em seguida, selecione **Remover da unidade administrativa**.

![Remover um grupo de uma unidade administrativa](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

Como alternativa, você pode ir para **Azure Active Directory** > **Unidades administrativas** e selecionar a unidade administrativa da qual o grupo é membro. Selecione **Grupos** no painel esquerdo para listar os grupos de membros. Selecione o grupo a ser removido da unidade administrativa e, em seguida, selecione **Remover grupos**.

![Listar grupos em uma unidade administrativa](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<group-id>/$ref

## <a name="next-steps"></a>Próximas etapas

- [Atribuir uma função a uma unidade administrativa](roles-admin-units-assign-roles.md)
- [Gerenciar usuários em uma unidade administrativa](roles-admin-units-add-manage-users.md)
