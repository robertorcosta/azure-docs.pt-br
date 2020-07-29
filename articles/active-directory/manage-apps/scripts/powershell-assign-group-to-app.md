---
title: Exemplo do PowerShell – atribuir grupo a um aplicativo de proxy de aplicativo
description: Exemplo do PowerShell que atribui um grupo a um aplicativo de proxy de aplicativo Azure Active Directory (Azure AD).
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
ms.openlocfilehash: 540453e5de3eed97b1207ca16443b46dffd5f8e6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75483343"
---
# <a name="assign-a-group-to-a-specific-azure-ad-application-proxy-application"></a>Atribuir um grupo a um aplicativo de Proxy de Aplicativo do AD específico do Azure

Este exemplo de script do PowerShell permite atribuir um grupo específico a um aplicativo de proxy de aplicativo Azure Active Directory (Azure AD).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Este exemplo requer o [módulo AzureAD v2 PowerShell for Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) ou o [PowerShell do AzureAD v2 para a versão de visualização do módulo do Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Exemplo de script

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/assign-group-to-app.ps1 "Assign a group to a specific Azure AD Application Proxy application")]

## <a name="script-explanation"></a>Explicação sobre o script

| Comando | Observações |
|---|---|
| [New-AzureADGroupAppRoleAssignment](https://docs.microsoft.com/powershell/module/AzureAD/New-azureadgroupapproleassignment?view=azureadps-2.0) | Atribui um grupo a uma função de aplicativo. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do PowerShell do Azure AD, consulte [visão geral do módulo do PowerShell do Azure ad](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Para obter outros exemplos do PowerShell para o proxy de aplicativo, consulte [exemplos do PowerShell do Azure ad para azure proxy de aplicativo do AD](../application-proxy-powershell-samples.md).