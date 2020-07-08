---
title: Ocultar um aplicativo empresarial da experiência do usuário no Azure AD
description: Como ocultar um aplicativo corporativo da experiência do usuário em painéis de acesso Azure Active Directory ou em iniciadores do Office 365.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 03/25/2020
ms.author: kenwith
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: d21ba14fba24c9b8e0b460e56b93d0e5212bfb27
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85367692"
---
# <a name="hide-enterprise-applications-from-end-users-in-azure-active-directory"></a>Ocultar aplicativos empresariais de usuários finais no Azure Active Directory

Instruções sobre como ocultar aplicativos do painel MyApps dos usuários finais ou inicializador do Office 365. Quando um aplicativo estiver oculto, os usuários ainda terão permissões para o aplicativo. 

## <a name="prerequisites"></a>Pré-requisitos

Os privilégios de administrador do aplicativo são necessários para ocultar um aplicativo do painel MyApps e do inicializador do Office 365.

São necessários privilégios globais de administrador para ocultar todos os aplicativos do Office 365.


## <a name="hide-an-application-from-the-end-user"></a>Ocultar um aplicativo do usuário final
Use as etapas a seguir para ocultar um aplicativo do painel MyApps e do inicializador do aplicativo do Office 365.

1.  Entre no [portal do Azure](https://portal.azure.com) como o administrador global do diretório.
2.  Selecione **Azure Active Directory**.
3.  Selecione **Aplicativos empresariais**. A folha **Aplicativos empresariais - Todos os aplicativos** abre.
4.  Em **Tipo de Aplicativo**, selecione **Aplicativos Empresariais**, se já não estiver selecionado.
5.  Procure o aplicativo que você quer ocultar e clique no aplicativo.  A visão geral do aplicativo é aberta.
6.  Clique em **Propriedades**. 
7.  Para a pergunta **Visível para os usuários?**, clique em **Não**.
8.  Clique em **Save** (Salvar).

> [!NOTE]
> Essas instruções se aplicam somente a aplicativos empresariais.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Usar o PowerShell do Azure AD para ocultar um aplicativo

Para ocultar um aplicativo do painel myapps, você pode adicionar manualmente a marca HideApp à entidade de serviço para o aplicativo. Execute os comandos do [PowerShell do AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#service_principals) a seguir para definir a propriedade visível do aplicativo **para usuários?** como **não**. 

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
2.  Selecione **Azure Active Directory**.
3.  Selecione **usuários**.
4.  Selecione **configurações do usuário**.
5.  Em **Aplicativos empresariais**, clique em **Gerenciar como os usuários finais inicializam e exibem os aplicativos.**
6.  Para **Usuários só podem ver aplicativos do Office 365 no portal do Office 365**, clique em **Sim**.
7.  Clique em **Save** (Salvar).

## <a name="next-steps"></a>Próximas etapas
* [Ver todos os meus grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](assign-user-or-group-access-portal.md)
* [Remover uma atribuição de usuário ou de grupo de um aplicativo empresarial](remove-user-or-group-access-portal.md)
* [Alterar o nome ou logotipo de um aplicativo empresarial](change-name-or-logo-portal.md)

