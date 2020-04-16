---
title: Adicionar, remover e listar usuários em uma unidade administrativa (pré-visualização) - Azure Active Directory | Microsoft Docs
description: Gerencie os usuários e suas permissões de função em uma unidade administrativa no Azure Active Directory
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
ms.openlocfilehash: 92d96abd343266372984918fd0c942ec1cae865f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428297"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Adicionar e gerenciar usuários em uma unidade administrativa no Azure Active Directory

No Azure Active Directory (Azure AD), você pode adicionar usuários a uma unidade administrativa (UA) para um escopo administrativo mais granular de controle.

Para obter etapas para se preparar para usar o PowerShell e o Microsoft Graph para gerenciamento de unidades administrativas, consulte [Como começar](roles-admin-units-manage.md#getting-started).

## <a name="add-users-to-an-au"></a>Adicionar usuários a uma UA

### <a name="azure-portal"></a>Portal do Azure

Você pode atribuir usuários a unidades administrativas de duas maneiras.

1. Atribuição individual

    1. Você pode ir ao Azure AD no portal e selecionar Usuários e selecionar o usuário a ser atribuído a uma unidade administrativa. Em seguida, você pode selecionar unidades administrativas no painel esquerdo. O usuário pode ser designado para uma ou mais unidades administrativas clicando em Atribuir à unidade administrativa e selecionar as unidades administrativas onde o usuário deve ser designado.

       ![selecionar Adicionar e, em seguida, digitar um nome para a unidade administrativa](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

    1. Você pode ir ao Azure AD no portal e selecionar unidades administrativas no painel esquerdo e, em seguida, selecionar a unidade administrativa onde os usuários devem ser atribuídos. Selecione Todos os usuários no painel esquerdo e, em seguida, selecione Adicionar membro. Em seguida, você pode ir em frente e selecionar um ou mais usuários a serem atribuídos à unidade administrativa a partir do painel direito.

        ![selecionar uma unidade administrativa e, em seguida, selecionar Adicionar membro](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

1. Atribuição em massa

    Vá para Azure AD no portal e selecione unidades administrativas. Selecione a unidade administrativa onde os usuários devem ser adicionados. Continue clicando em Todos os usuários -> Adicionar membros do arquivo .csv. Em seguida, você pode baixar o modelo CSV e editar o arquivo. O formato é simples e precisa de um único UPN para ser adicionado em cada linha. Uma vez que o arquivo esteja pronto, salve-o em um local apropriado e, em seguida, carregue-o na etapa 3, conforme destacado no instantâneo.

    ![a granel atribuir usuários a uma unidade administrativa](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $UserObj.ObjectId

No exemplo acima, o cmdlet Add-AzureADAdministrativeUnitMember é usado para adicionar o usuário à unidade administrativa. O ID do objeto da Unidade Administrativa onde o usuário deve ser adicionado e o ID do objeto do usuário a ser adicionado são tomados como argumento. A seção destacada pode ser alterada conforme necessário para o ambiente específico.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref
    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/users/{id}"
    }

Exemplo:

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/johndoe@fabidentity.com"
    }

## <a name="list-administrative-units-for-a-user"></a>Liste unidades administrativas para um usuário

### <a name="azure-portal"></a>Portal do Azure

No portal Azure você pode abrir o perfil de um usuário indo para Azure AD > Users. Clique no usuário para abrir o perfil do usuário.

![Abra o perfil de um usuário no Azure Active Directory](./media/roles-admin-units-add-manage-users/user-profile-admin-units.png)

Selecione **unidades administrativas** no painel esquerdo para ver a lista de unidades administrativas onde o usuário foi designado.

![Liste as unidades administrativas para um usuário](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $userObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/users//memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-single-user-from-an-au"></a>Remova um único usuário de um UA

### <a name="azure-portal"></a>Portal do Azure

Há duas maneiras de remover um usuário de uma unidade administrativa. No portal Azure você pode abrir o perfil de um usuário indo para**Usuários** **Azure AD** > . Selecione o usuário para abrir o perfil do usuário. Selecione a unidade administrativa da que deseja que o usuário seja removido e **selecione Remover na unidade administrativa**.

![Remova um usuário de uma unidade administrativa do perfil do usuário](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

Você também pode remover um usuário em**unidades administrativas** **azure AD** > selecionando a unidade administrativa da que deseja remover os usuários. Selecione o usuário e selecione **Remover membro**.
  
![Remova um usuário no nível da unidade administrativa](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberUserObjId

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<user-id>/$ref

## <a name="bulk-remove-more-than-one-user"></a>Remover em massa mais de um usuário

Você pode ir às unidades administrativas do Azure AD > e selecionar a unidade administrativa da onde deseja remover os usuários. Clique em Bulk remover membro. Baixe o modelo CSV para fornecer a lista de usuários a serem removidos.

Edite o modelo CSV baixado com as entradas de usuário relevantes. Não remova as duas primeiras linhas do modelo. Adicione um UPN de usuário em cada linha.

![Editar o arquivo CSV para remoção em massa de usuários de unidades administrativas](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

Depois de salvar as entradas no arquivo, faça upload do arquivo, **selecione Enviar**.

![Enviar o arquivo de upload em massa](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>Próximas etapas

- [Atribuir uma função a uma unidade administrativa](roles-admin-units-assign-roles.md)
- [Adicionar grupos a uma unidade administrativa](roles-admin-units-add-manage-groups.md)
