---
title: Exemplo do PowerShell – listar todos os grupos de conectores do Proxy de Aplicativo
description: Exemplo do PowerShell que lista todos os conectores e grupos de conectores do Proxy de Aplicativo do Azure AD (Azure Active Directory) no seu diretório.
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
ms.openlocfilehash: f7d665ddf7b1902275eebd74bcbf148426ac6ddd
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88506851"
---
# <a name="get-all-connector-groups-and-connectors-in-the-directory"></a>Obter todos os grupos de conectores e conectores no diretório

Este exemplo de script do PowerShell lista todos os conectores e grupos de conectores do Proxy de Aplicativo do Azure AD (Azure Active Directory) no seu diretório.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Este exemplo requer o [módulo do PowerShell do AzureAD v2 para o Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) ou a [versão prévia do módulo do PowerShell do AzureAD v2 para o Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Exemplo de script

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-connectors.ps1 "Get all connector groups and connectors in the directory")]

## <a name="script-explanation"></a>Explicação sobre o script

| Comando | Observações |
|---|---|
| [Get-AzureADApplicationProxyConnectorGroup](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup?view=azureadps-2.0) | Recupera uma lista de todos os grupos de conectores ou, se especificado, os detalhes de um grupo de conectores. |
| [Get-AzureADApplicationProxyConnectorGroupMembers](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroupmembers?view=azureadps-2.0) | Obtém todos os conectores do Proxy de Aplicativo associados a cada grupo de conectores.|

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do PowerShell do Azure AD, confira [Visão geral do módulo do PowerShell do Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Para obter outros exemplos do PowerShell para o Proxy de Aplicativo, confira [Exemplos do PowerShell do Azure AD para o Proxy de Aplicativo do Azure AD](../application-proxy-powershell-samples.md).