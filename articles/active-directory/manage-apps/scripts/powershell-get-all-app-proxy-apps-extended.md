---
title: Exemplo do PowerShell – listar informações estendidas para aplicativos do Proxy de Aplicativo
description: Exemplo do PowerShell que lista todos os aplicativos do Proxy de Aplicativo do Azure AD (Azure Active Directory) junto com a ID do aplicativo (AppId), o nome (DisplayName), a URL externa (ExternalUrl), a URL interna (InternalUrl) e o tipo de autenticação (ExternalAuthenticationType).
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 8994abc61e27a9d7c7097aacab92282f0c47c989
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88506875"
---
# <a name="get-all-application-proxy-apps-and-list-extended-information"></a>Obter todos os aplicativos do Proxy de Aplicativo e listar informações estendidas

Este exemplo de script do PowerShell lista informações sobre todos os aplicativos do Proxy de Aplicativo do Azure AD (Azure Active Directory), incluindo a ID do aplicativo (AppId), o nome (DisplayName), a URL externa (ExternalUrl), a URL interna (InternalUrl) e o tipo de autenticação (ExternalAuthenticationType).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Este exemplo requer o [módulo do PowerShell do AzureAD v2 para o Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) ou a [versão prévia do módulo do PowerShell do AzureAD v2 para o Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Exemplo de script

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-extended.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Explicação sobre o script

| Comando | Observações |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Obtém uma entidade de serviço. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Obtém um aplicativo do Azure AD. |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Recupera um aplicativo configurado para o Proxy de Aplicativo no Azure AD. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do PowerShell do Azure AD, confira [Visão geral do módulo do PowerShell do Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Para obter outros exemplos do PowerShell para o Proxy de Aplicativo, confira [Exemplos do PowerShell do Azure AD para o Proxy de Aplicativo do Azure AD](../application-proxy-powershell-samples.md).