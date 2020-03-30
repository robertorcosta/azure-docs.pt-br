---
title: Desative logins de usuário para um aplicativo corporativo no Azure AD
description: Como desabilitar um aplicativo empresarial para que nenhum usuário possa entrar nele no Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10553898376c4b9236ee62718fffccd45b12d70b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274083"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Desabilitar entradas de usuário em um aplicativo empresarial no Azure Active Directory

É fácil desativar um aplicativo corporativo para que nenhum usuário possa fazer login nele no Azure Active Directory (Azure AD). Você precisa das permissões apropriadas para gerenciar o aplicativo corporativo. E, você deve ser o diretor global para o diretório.

## <a name="how-do-i-disable-user-sign-ins"></a>Como desabilitar entradas de usuário?

1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
1. Selecione **Todos os serviços,** **digite o Diretório Ativo do Azure** na caixa de texto e, em seguida, **selecione Enter**.
1. No painel de nome do -  ***diretório*** ativo do **Azure**(ou seja, o painel Azure AD para o diretório que você está gerenciando), selecione **aplicativos Corporativos**.
1. Nos **aplicativos Enterprise - Todos os aplicativos** pane, você vê uma lista dos aplicativos que você pode gerenciar. Selecione um aplicativo.
1. No painel ***nomedoaplicativo*** (ou seja, o painel com o nome do aplicativo selecionado no título), selecione **Propriedades**.
1. No painel**propriedades** do ***aplicativo,*** - selecione **Não** para **Habilitar para os usuários fazerem login?**.
1. Escolha o comando **Salvar** .

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Use o Azure AD PowerShell para desativar um aplicativo não listado

Se você conhece o AppId de um aplicativo que não aparece na lista de aplicativos da Enterprise (por exemplo, porque você excluiu o aplicativo ou o diretor do serviço ainda não foi criado devido ao aplicativo ser pré-autorizado pela Microsoft), você pode criar manualmente o principal de serviço para o aplicativo e, em seguida, desativá-lo usando [o cmdlet AzureAD PowerShell](https://docs.microsoft.com/powershell/module/azuread/New-AzureADServicePrincipal?view=azureadps-2.0).

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>Próximas etapas

* [Ver todos os meus grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](assign-user-or-group-access-portal.md)
* [Remover uma atribuição de usuário ou de grupo de um aplicativo empresarial](remove-user-or-group-access-portal.md)
* [Alterar o nome ou logotipo de um aplicativo empresarial](change-name-or-logo-portal.md)
