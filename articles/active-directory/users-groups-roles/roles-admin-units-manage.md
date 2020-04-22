---
title: Adicionar e remover unidades administrativas (pré-visualização) - Azure Active Directory | Microsoft Docs
description: Use unidades administrativas para restringir o escopo de permissões de função no Diretório Ativo do Azure.
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
ms.openlocfilehash: 7a4a0dfaeda18b3f68ddc3c7cc7333b8c994d174
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684909"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Gerencie unidades administrativas no Diretório Ativo do Azure

Para obter mais controle administrativo granular no Azure Active Directory (Azure AD), você pode atribuir usuários a uma função AD do Azure com um escopo limitado a uma ou mais unidades administrativas (AUs).

## <a name="get-started"></a>Introdução

1. Para executar as consultas a partir das seguintes instruções via [Graph Explorer,](https://aka.ms/ge)faça o seguinte:

    a. No portal do Azure, vá para o AD do Azure. Na lista de aplicativos, selecione **Graf Explorer**e **selecione Conceder consentimento de admin ao Graph Explorer**.

    ![Captura de tela mostrando link para "Consentimento do Grant Admin"](./media/roles-admin-units-manage/select-graph-explorer.png)

    b. No Graph Explorer, selecione a versão **beta.**

    ![Captura de tela mostrando a versão beta selecionada](./media/roles-admin-units-manage/select-beta-version.png)

1. Use a versão de visualização do Azure AD PowerShell.

## <a name="add-an-administrative-unit"></a>Adicionar uma unidade administrativa

### <a name="use-the-azure-portal"></a>Use o Portal do Azure

1. No portal Azure, vá para a Azure AD, e depois, no painel esquerdo, selecione **unidades administrativas**.

    ![Captura de tela do link Unidades Administrativas (Preview) no Azure AD](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. Selecione **Adicionar** e, em seguida, digite o nome da unidade administrativa. Opcionalmente, adicione uma descrição da unidade administrativa.

    ![Captura de tela do botão Adicionar e da caixa de texto para inserir o nome da unidade administrativa](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. Selecione **Adicionar** para finalizar a unidade administrativa.

### <a name="use-powershell"></a>Usar o PowerShell

Instale o Azure AD PowerShell (visualização) antes de tentar executar os seguintes comandos:

    Connect-AzureAD
    New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"

Você pode modificar os valores que estão incluídos entre aspas, conforme necessário.

### <a name="use-microsoft-graph"></a>Use Microsoft Graph

    Http Request
    POST /administrativeUnits
    Request body
    {
        "displayName": "North America Operations",
        "description": "North America Operations administration"
    }

## <a name="remove-an-administrative-unit"></a>Remova uma unidade administrativa

No Azure AD, você pode remover uma unidade administrativa que você não precisa mais como uma unidade de escopo para funções administrativas.

### <a name="use-the-azure-portal"></a>Use o Portal do Azure

1. No portal Azure, vá para as**unidades administrativas** **do Azure AD** > . 
1. Selecione a unidade administrativa a ser excluída e, em seguida, **selecione Excluir**. 
1. Para confirmar se deseja excluir a unidade administrativa, selecione **Sim**. A unidade administrativa é excluída.

![Captura de tela do botão de exclusão da unidade administrativa e janela de confirmação](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>Usar o PowerShell

    $delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
    Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId

Você pode modificar os valores que estão incluídos entre aspas, conforme necessário para o ambiente específico.

### <a name="use-the-graph-api"></a>Use a API do gráfico

    HTTP request
    DELETE /administrativeUnits/{Admin id}
    Request body
    {}

## <a name="next-steps"></a>Próximas etapas

* [Gerencie usuários em uma unidade administrativa](roles-admin-units-add-manage-users.md)
* [Gerenciar grupos em uma unidade administrativa](roles-admin-units-add-manage-groups.md)
