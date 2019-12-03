---
title: Adicionar ou remover proprietários do grupo – Azure Active Directory | Microsoft Docs
description: Instruções sobre como adicionar ou remover proprietários de grupos usando o Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ee4b452d087bfef0e9f5f7c820870da0df8dc3e
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74683929"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Adicionar ou remover proprietários do grupo no Azure Active Directory
Os grupos do Active Directory do Azure (Azure AD) são de propriedade e gerenciados pelos proprietários do grupo. Os proprietários do grupo podem ser usuários ou entidades de serviço e podem gerenciar o grupo, incluindo a associação. Somente proprietários de grupo existentes ou administradores de gerenciamento de grupo podem atribuir proprietários de grupo. Os proprietários do grupo não precisam ser membros do grupo.

Quando um grupo não tem proprietário, administradores de grupo de gerenciamento ainda podem gerenciar o grupo.

## <a name="add-an-owner-to-a-group"></a>Adicionar um proprietário a um grupo
Abaixo estão as instruções para adicionar um usuário como um proprietário a um grupo usando o portal do AD do Azure. Para adicionar uma entidade de serviço como um proprietário de um grupo, siga as instruções para fazer isso usando o [PowerShell](https://docs.microsoft.com/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0).

### <a name="to-add-a-group-owner"></a>Para adicionar um proprietário do grupo
1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta de administrador Global para o diretório.

2. Selecione **Active Directory do Azure**, selecione **Grupos** e selecione o grupo ao qual você deseja adicionar um proprietário (neste exemplo, *política do MDM - Oeste*).

3. Na página **Política de MDM - Visão Geral do Oeste**, selecione **Proprietários**.

    ![Política do MDM – página de visão geral do Oeste com a opção de proprietários realçada](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. Sobre o **proprietários de - Oeste - política de MDM** página, selecione **adicionar proprietários**e, em seguida, pesquise e selecione o usuário que será o novo proprietário do grupo e, em seguida, escolha **selecione**.

    ![Política do MDM - West - Página Proprietários com a opção Adicionar Proprietários destacada](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    Depois de selecionar o novo proprietário, você pode atualizar a página **Proprietários** e ver o nome adicionado à lista de proprietários.

## <a name="remove-an-owner-from-a-group"></a>Remover um proprietário de um grupo
Remova um proprietário de um grupo usando o Azure AD.

### <a name="to-remove-an-owner"></a>Para remover um proprietário
1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta de administrador Global para o diretório.

2. Selecione **Active Directory do Azure**, selecione **Grupos** e, em seguida, selecione o grupo para o qual você deseja remover um proprietário (neste exemplo, *política do MDM - Oeste*).

3. Na página **Política de MDM - Visão Geral do Oeste**, selecione **Proprietários**.

    ![Política do MDM – página de visão geral do Oeste com a opção de proprietários realçada](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. Sobre o **proprietários de - Oeste - política de MDM** , selecione o usuário que você deseja remover como proprietário de um grupo, escolha **remover** na página de informações do usuário e selecione **Sim** para confirmar sua decisão.

    ![Página de informações do usuário com a opção de remover realçada](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    Depois de remover o proprietário, você pode retornar para o **proprietários** página e ver o nome foi removido da lista de proprietários.

## <a name="next-steps"></a>Próximos passos
- [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)

- [Cmdlets do Azure Active Directory para definir configurações de grupo](../users-groups-roles/groups-settings-cmdlets.md)

- [Usar grupos para atribuir acesso a um aplicativo SaaS integrado](../users-groups-roles/groups-saasapps.md)

- [Integração de suas identidades locais com o Active Directory do Azure](../hybrid/whatis-hybrid-identity.md)

- [Cmdlets do Azure Active Directory para definir configurações de grupo](../users-groups-roles/groups-settings-v2-cmdlets.md)
