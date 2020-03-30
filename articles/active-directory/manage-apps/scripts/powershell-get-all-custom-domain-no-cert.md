---
title: Amostra PowerShell - Aplicativos proxy de aplicativos sem certificado
description: Exemplo do PowerShell que lista todos os aplicativos proxy do Azure Active Directory (Azure AD) que estão usando domínios personalizados, mas não têm um certificado TLS/SSL válido carregado.
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
ms.openlocfilehash: 97dc46a34e1bb5bb85dcb771a9720a19d356e6f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481204"
---
# <a name="get-all-azure-ad-proxy-application-apps-published-with-no-certificate-uploaded"></a>Obtenha todos os aplicativos de aplicativo Azure AD Proxy publicados sem certificado carregado

Este exemplo de script do PowerShell lista todos os aplicativos proxy do Azure Active Directory (Azure AD) que estão usando domínios personalizados, mas não têm um certificado TLS/SSL válido carregado.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Esta amostra requer o [módulo AzureAD V2 PowerShell for Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) ou o [AzureAD V2 PowerShell for Graph module versão](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Exemplo de script

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-custom-domain-no-cert.ps1 "Get all Azure AD Proxy application apps published with no certificate uploaded")]

## <a name="script-explanation"></a>Explicação sobre o script

| Comando | Observações |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Consegue um diretor de serviço. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Recebe um aplicativo Azure AD. |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Recupera um aplicativo configurado para proxy de aplicativo no Azure AD. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo Azure AD PowerShell, consulte a [visão geral do módulo Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Para outros exemplos do PowerShell para proxy de aplicativos, consulte [exemplos do Azure PowerShell para proxy de aplicativo Azure AD](../application-proxy-powershell-samples.md).