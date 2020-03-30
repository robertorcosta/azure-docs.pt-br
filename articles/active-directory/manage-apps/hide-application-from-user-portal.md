---
title: Ocultar um aplicativo da experiência do usuário no Azure AD
description: Como ocultar um aplicativo da experiência do usuário em painéis de acesso do Microsoft Azure Active Directory ou inicializadores do Office 365.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5718adf4fd76e2fbd0ff793dd2fa33ee08f7c0fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295041"
---
# <a name="hide-applications-from-end-users-in-azure-active-directory"></a>Ocultar aplicativos de usuários finais no Azure Active Directory

Instruções sobre como ocultar aplicativos do painel MyApps dos usuários finais ou inicializador do Office 365. Quando um aplicativo estiver oculto, os usuários ainda terão permissões para o aplicativo. 

## <a name="prerequisites"></a>Pré-requisitos

Os privilégios de administrador do aplicativo são necessários para ocultar um aplicativo do painel MyApps e do inicializador do Office 365.

São necessários privilégios globais de administrador para ocultar todos os aplicativos do Office 365.


## <a name="hide-an-application-from-the-end-user"></a>Ocultar um aplicativo do usuário final
Use as etapas a seguir para ocultar um aplicativo do painel MyApps e do inicializador do aplicativo do Office 365.

1.  Entre no [portal do Azure](https://portal.azure.com) como o administrador global do diretório.
2.  Selecione **O Diretório Ativo do Azure**.
3.  Selecione **Aplicativos empresariais**. A folha **Aplicativos empresariais - Todos os aplicativos** abre.
4.  Em **Tipo de Aplicativo**, selecione **Aplicativos Empresariais**, se já não estiver selecionado.
5.  Procure o aplicativo que você quer ocultar e clique no aplicativo.  A visão geral do aplicativo é aberta.
6.  Clique em **Propriedades**. 
7.  Para a pergunta **Visível para os usuários?**, clique em **Não**.
8.  Clique em **Salvar**.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Use o Azure AD PowerShell para ocultar um aplicativo

Para ocultar um aplicativo do painel MyApps, você pode adicionar manualmente a tag HideApp ao diretor de serviço do aplicativo. Execute os seguintes comandos [AzureAD PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#service_principals) para definir a **No**propriedade Visible to Users do **aplicativo?** 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-office-365-applications-from-the-myapps-panel"></a>Ocultar aplicativos do Office 365 no painel MyApps

Use as seguintes etapas para ocultar todos os aplicativos do Office 365 no painel MyApps. Os aplicativos ainda estão visíveis no portal do Office 365.

1.  Entre no [Portal do Azure](https://portal.azure.com) como um administrador global do diretório.
2.  Selecione **O Diretório Ativo do Azure**.
3.  Selecione **configurações do usuário**.
4.  Em **Aplicativos empresariais**, clique em **Gerenciar como os usuários finais inicializam e exibem os aplicativos.**
5.  Para **Usuários só podem ver aplicativos do Office 365 no portal do Office 365**, clique em **Sim**.
6.  Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas
* [Ver todos os meus grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](assign-user-or-group-access-portal.md)
* [Remover uma atribuição de usuário ou de grupo de um aplicativo empresarial](remove-user-or-group-access-portal.md)
* [Alterar o nome ou logotipo de um aplicativo empresarial](change-name-or-logo-portal.md)

