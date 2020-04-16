---
title: Adicionar e remover unidades administrativas (pré-visualização) - Azure Active Directory | Microsoft Docs
description: Use unidades administrativas para restringir o escopo de permissões de função no Diretório Ativo do Azure
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
ms.openlocfilehash: 351cbee9cbc7cc02c7f20553b6fc8c3f861541df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428154"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Gerencie unidades administrativas no Diretório Ativo do Azure

Para obter mais controle administrativo granular no Azure Active Directory (Azure AD), você pode atribuir usuários a uma função AD do Azure com um escopo limitado a uma ou mais unidades administrativas (AUs).

## <a name="getting-started"></a>Introdução

1. Para executar as consultas a partir das seguintes instruções via [Graph Explorer,](https://aka.ms/ge)certifique-se de que o seguinte:

    1. Vá para Azure AD no portal e, em seguida, nos aplicativos selecione Graph Explorer e forneça consentimento de administrador ao Graph Explorer.

        ![selecionar Graf Explorer e fornecer consentimento de admin nesta página](./media/roles-admin-units-manage/select-graph-explorer.png)

    1. No Graph Explorer, certifique-se de selecionar a versão beta.

        ![selecione a versão beta antes da operação POST](./media/roles-admin-units-manage/select-beta-version.png)

1. Por favor, use a versão de visualização do Azure AD PowerShell. Instruções detalhadas estão aqui.

## <a name="add-an-administrative-unit"></a>Adicionar uma unidade administrativa

### <a name="azure-portal"></a>Portal do Azure

1. Vá para O Diretório Ativo no portal e selecione Unidades Administrativas no painel esquerdo.

    ![navegar para unidades administrativas no Diretório Ativo do Azure](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. Selecione **Adicionar*** e fornecer o nome da unidade administrativa e, opcionalmente, pode adicionar uma descrição para a unidade administrativa.

    ![selecionar Adicionar e, em seguida, digitar um nome para a unidade administrativa](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. Selecione **Adicionar** para finalizar a unidade administrativa.

### <a name="powershell"></a>PowerShell

Instale o Azure AD PowerShell (versão de visualização) antes de tentar executar as ações abaixo:

    Connect-AzureAD
    New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"

Os valores destacados acima podem ser modificados conforme necessário.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http Request
    POST /administrativeUnits
    Request body
    {
        "displayName": "North America Operations",
        "description": "North America Operations administration"
    }

## <a name="remove-an-administrative-unit"></a>Remova uma unidade administrativa

No Azure Active Directory (Azure AD), você pode remover uma unidade administrativa que você não precisa mais como uma unidade de escopo para funções administrativas.

### <a name="azure-portal"></a>Portal do Azure

Vá para **Azure AD > unidades administrativas** no portal. Selecione a unidade administrativa a ser excluída e, em seguida, **selecione Excluir**. Após a confirmação **sim,** a unidade administrativa será suprimida.

![Selecione uma unidade administrativa para excluir](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="powershell"></a>PowerShell

    $delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
    Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId

A seção destacada pode ser alterada conforme necessário para o ambiente específico.

### <a name="graph-api"></a>API do Graph

    HTTP request
    DELETE /administrativeUnits/{Admin id}
    Request body
    {}

## <a name="next-steps"></a>Próximas etapas

[Gestão de usuários em grupos de](roles-admin-units-add-manage-users.md)
gestão de unidades[administrativas em unidade administrativa](roles-admin-units-add-manage-groups.md)
