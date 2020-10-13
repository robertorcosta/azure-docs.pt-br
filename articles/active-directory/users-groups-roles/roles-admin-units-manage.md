---
title: Adicionar e remover unidades administrativas-Azure Active Directory | Microsoft Docs
description: Use unidades administrativas para restringir o escopo das permissões de função no Azure Active Directory.
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
ms.openlocfilehash: 1166d2ad17aea97a4dd7fdda53c42d6b3df75936
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91450372"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Gerenciar unidades administrativas no Azure Active Directory

Para um controle administrativo mais granular no Azure Active Directory (AD do Azure), você pode atribuir usuários a uma função do Azure AD com um escopo limitado a uma ou mais unidades administrativas (AUs).

## <a name="get-started"></a>Introdução

1. Para executar consultas das instruções a seguir por meio do [Graph Explorer](https://aka.ms/ge), faça o seguinte:

    a. No portal do Azure, vá para o AD do Azure. Na lista de aplicativos, selecione **Graph Explorer**e, em seguida, selecione **conceder consentimento do administrador ao explorador do Graph**.

    ![Captura de tela mostrando o link para "conceder consentimento de administrador"](./media/roles-admin-units-manage/select-graph-explorer.png)


1. Use a versão de visualização do PowerShell do Azure AD.

## <a name="add-an-administrative-unit"></a>Adicionar uma unidade administrativa

### <a name="use-the-azure-portal"></a>Usar o portal do Azure

1. No portal do Azure, acesse Azure AD e, no painel esquerdo, selecione **unidades administrativas**.

    ![Captura de tela do unitslink administrativo no Azure AD](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. Selecione **Adicionar** e, em seguida, digite o nome da unidade administrativa. Opcionalmente, adicione uma descrição da unidade administrativa.

    ![Captura de tela do botão Adicionar e da caixa de texto para inserir o nome da unidade administrativa](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. Selecione **Adicionar** para finalizar a unidade administrativa.

### <a name="use-powershell"></a>Usar o PowerShell

Instale o PowerShell do Azure AD (versão prévia) antes de tentar executar os seguintes comandos:

```powershell
Connect-AzureAD
New-AzureADMSAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

Você pode modificar os valores que são colocados entre aspas, conforme necessário.

### <a name="use-microsoft-graph"></a>Usar Microsoft Graph

```http
Http Request
POST /administrativeUnits
Request body
{
  "displayName": "North America Operations",
  "description": "North America Operations administration"
}
```

## <a name="remove-an-administrative-unit"></a>Remover uma unidade administrativa

No Azure AD, você pode remover uma unidade administrativa que não é mais necessária como uma unidade de escopo para funções administrativas.

### <a name="use-the-azure-portal"></a>Usar o portal do Azure

1. Na portal do Azure, vá para as unidades administrativas **do Azure ad**  >  **Administrative units**. 
1. Selecione a unidade administrativa a ser excluída e, em seguida, selecione **excluir**. 
1. Para confirmar que deseja excluir a unidade administrativa, selecione **Sim**. A unidade administrativa é excluída.

![Captura de tela do botão excluir da unidade administrativa e da janela de confirmação](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>Usar o PowerShell

```powershell
$delau = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADMSAdministrativeUnit -ObjectId $delau.ObjectId
```

Você pode modificar os valores que são colocados entre aspas, conforme necessário para o ambiente específico.

### <a name="use-the-graph-api"></a>Usar o API do Graph

```http
HTTP request
DELETE /administrativeUnits/{Admin id}
Request body
{}
```

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar usuários em uma unidade administrativa](roles-admin-units-add-manage-users.md)
* [Gerenciar grupos em uma unidade administrativa](roles-admin-units-add-manage-groups.md)
