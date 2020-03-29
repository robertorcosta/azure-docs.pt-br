---
title: Amostra powershell - Atribuir usuário a um aplicativo Proxy de aplicativo
description: Exemplo do PowerShell que atribui um usuário a um aplicativo proxy de aplicativo Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42667ebdfc37b679d56421e3a82d4a6aaaed8fdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483330"
---
# <a name="assign-a-user-to-a-specific-azure-ad-application-proxy-application"></a>Atribuir um usuário a um aplicativo proxy específico do Azure AD

Este exemplo de script do PowerShell permite atribuir um usuário a um aplicativo proxy específico do Aplicativo Azure AD.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Esta amostra requer o [módulo AzureAD V2 PowerShell for Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) ou o [AzureAD V2 PowerShell for Graph module versão](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Exemplo de script

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/assign-user-to-app.ps1 "Assign a user to an application")]

## <a name="script-explanation"></a>Explicação sobre o script

| Comando | Observações |
|---|---|
| [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/AzureAD/new-azureaduserapproleassignment?view=azureadps-2.0) | Atribui um usuário a uma função de aplicativo. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo Azure AD PowerShell, consulte a [visão geral do módulo Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Para outros exemplos do PowerShell para proxy de aplicativos, consulte [exemplos do Azure PowerShell para proxy de aplicativo Azure AD](../application-proxy-powershell-samples.md).