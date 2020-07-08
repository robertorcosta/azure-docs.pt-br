---
title: Adicionar, remover e listar usuários em uma unidade administrativa (visualização)-Azure Active Directory | Microsoft Docs
description: Gerenciar usuários e suas permissões de função em uma unidade administrativa no Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9b76ac103b873026dce3d3f8f92e54dc3afc14c
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85850940"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Adicionar e gerenciar usuários em uma unidade administrativa no Azure Active Directory

No Azure Active Directory (AD do Azure), você pode adicionar usuários a uma AU (unidade administrativa) para um escopo administrativo mais granular do controle.

Para saber como se preparar para usar o PowerShell e o Microsoft Graph para o gerenciamento de unidade administrativa, consulte a [Introdução](roles-admin-units-manage.md#get-started).

## <a name="add-users-to-an-au"></a>Adicionar usuários a uma AU

### <a name="azure-portal"></a>Portal do Azure

Você pode atribuir usuários a unidades administrativas de duas maneiras.

1. Atribuição individual

    1. Você pode ir para o Azure AD no portal e Selecionar usuários e selecionar o usuário a ser atribuído a uma unidade administrativa. Em seguida, você pode selecionar unidades administrativas no painel esquerdo. O usuário pode ser atribuído a uma ou mais unidades administrativas clicando em atribuir à unidade administrativa e selecionando as unidades administrativas nas quais o usuário deve ser atribuído.

       ![selecione Adicionar e, em seguida, insira um nome para a unidade administrativa](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

    1. Você pode ir para o Azure AD no portal e selecionar unidades administrativas no painel esquerdo e, em seguida, selecionar a unidade administrativa onde os usuários serão atribuídos. Selecione todos os usuários no painel esquerdo e, em seguida, selecione Adicionar membro. Em seguida, você pode selecionar um ou mais usuários a serem atribuídos à unidade administrativa no painel direito.

        ![selecione uma unidade administrativa e, em seguida, selecione Adicionar membro](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

1. Atribuição em massa

    Vá para Azure AD no portal e selecione unidades administrativas. Selecione a unidade administrativa onde os usuários devem ser adicionados. Continue clicando em todos os usuários – > adicionar membros do arquivo. csv. Em seguida, você pode baixar o modelo CSV e editar o arquivo. O formato é simples e precisa que um único UPN seja adicionado em cada linha. Quando o arquivo estiver pronto, salve-o em um local apropriado e, em seguida, carregue-o na etapa 3 como realçado no instantâneo.

    ![atribuir usuários em massa a uma unidade administrativa](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrativeunitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
Add-AzureADAdministrativeUnitMember -ObjectId $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId
```

No exemplo acima, o cmdlet Add-AzureADAdministrativeUnitMember é usado para adicionar o usuário à unidade administrativa. A ID de objeto da unidade administrativa onde o usuário deve ser adicionado e a ID de objeto do usuário que será adicionado é executada como argumento. A seção realçada pode ser alterada conforme necessário para o ambiente específico.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref
Request body
{
  "@odata.id":"https://graph.microsoft.com/beta/users/{id}"
}
```

Exemplo:

```http
{
  "@odata.id":"https://graph.microsoft.com/beta/users/johndoe@fabidentity.com"
}
```

## <a name="list-administrative-units-for-a-user"></a>Listar unidades administrativas para um usuário

### <a name="azure-portal"></a>Portal do Azure

No portal do Azure você pode abrir o perfil de um usuário acessando o Azure AD > usuários. Clique no usuário para abrir o perfil do usuário.

![Abrir um perfil de usuário no Azure Active Directory](./media/roles-admin-units-add-manage-users/user-profile-admin-units.png)

Selecione **unidades administrativas** no painel esquerdo para ver a lista de unidades administrativas em que o usuário foi atribuído.

![Listar as unidades administrativas de um usuário](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $userObjId} }
```

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/beta/users//memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-au"></a>Remover um único usuário de uma AU

### <a name="azure-portal"></a>Portal do Azure

Há duas maneiras de remover um usuário de uma unidade administrativa. No portal do Azure você pode abrir o perfil de um usuário acessando os usuários **do Azure ad**  >  **Users**. Selecione o usuário para abrir o perfil do usuário. Selecione a unidade administrativa da qual você deseja que o usuário seja removido e selecione **remover da unidade administrativa**.

![Remover um usuário de uma unidade administrativa do perfil do usuário](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

Você também pode remover um usuário em unidades administrativas **do Azure ad**  >  **Administrative units** selecionando a unidade administrativa da qual deseja remover os usuários. Selecione o usuário e selecione **Remover membro**.
  
![Remover um usuário no nível de unidade administrativa](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberUserObjId
```

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<user-id>/$ref

## <a name="bulk-remove-more-than-one-user"></a>Remover em massa mais de um usuário

Você pode acessar as unidades administrativas do Azure AD > e selecionar a unidade administrativa da qual deseja remover os usuários. Clique em remover membro em massa. Baixe o modelo CSV para fornecer a lista de usuários a serem removidos.

Edite o modelo CSV baixado com as entradas de usuário relevantes. Não remova as duas primeiras linhas do modelo. Adicione um UPN de usuário em cada linha.

![Editar o arquivo CSV para remoção de usuário em massa de unidades administrativas](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

Depois de salvar as entradas no arquivo, carregue o arquivo, selecione **Enviar**.

![Enviar o arquivo de carregamento em massa](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>Próximas etapas

- [Atribuir uma função a uma unidade administrativa](roles-admin-units-assign-roles.md)
- [Adicionar grupos a uma unidade administrativa](roles-admin-units-add-manage-groups.md)
