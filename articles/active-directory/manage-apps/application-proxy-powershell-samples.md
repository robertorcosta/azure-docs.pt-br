---
title: Amostras do PowerShell para proxy de aplicativo Azure AD
description: Use essas amostras do PowerShell para o Azure AD Application Proxy para obter informações sobre aplicativos e conectores proxy de aplicativos em seu diretório, atribuir usuários e grupos a aplicativos e obter informações de certificado.
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
ms.openlocfilehash: 476bdfdd2c0b81d86900cee18024cc261ee80a07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481255"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Exemplos do Azure AD PowerShell para proxy de aplicativo Azure AD

A tabela a seguir inclui links para exemplos de script powerShell para proxy de aplicativo Azure AD. Essas amostras requerem o [módulo AzureAD V2 PowerShell for Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) ou o [AzureAD V2 PowerShell para versão de visualização do módulo Graph,](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)a menos que seja observado de outra forma.


Para obter mais informações sobre os cmdlets usados nessas amostras, consulte [O Gerenciamento do Aplicativo proxy](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) e o gerenciamento do [conector proxy do aplicativo](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management).

| | |
|---|---|
|**Aplicativos proxy de aplicativos**||
| [Liste informações básicas para todos os aplicativos proxy de aplicativos](scripts/powershell-get-all-app-proxy-apps-basic.md) | Lista informações básicas (AppId, DisplayName, ObjId) sobre todos os aplicativos proxy de aplicativos em seu diretório . |
| [Liste informações estendidas para todos os aplicativos proxy de aplicativos](scripts/powershell-get-all-app-proxy-apps-extended.md) | Lista informações estendidas (AppId, DisplayName, ExternalUrl, InternalUrl, ExternalAuthenticationType) sobre todos os aplicativos proxy de aplicativos em seu diretório.  |
| [Liste todos os aplicativos proxy de aplicativos por grupo de conectores](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | Lista informações sobre todos os aplicativos proxy de aplicativos em seu diretório e para quais grupos de conectores os aplicativos são atribuídos. |
| [Obtenha todos os aplicativos proxy de aplicativos com uma política de vida útil de token](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | Lista todos os aplicativos proxy do aplicativo em seu diretório com uma política de vida útil de token e seus detalhes. Esta amostra requer a versão de visualização do [módulo AzureAD V2 para o módulo Gráfico](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview). |
|**Grupos de conectores**||
| [Obtenha todos os grupos de conectores e conectores no diretório](scripts/powershell-get-all-connectors.md) | Lista todos os grupos de conectores e conectores em seu diretório. |
| [Mova todos os aplicativos atribuídos a um grupo de conectores para outro grupo de conectores](scripts/powershell-move-all-apps-to-connector-group.md) | Move todos os aplicativos atualmente atribuídos a um grupo conector para um grupo de conectores diferente. |
|**Usuários e grupo atribuídos**||
| [Exibir usuários e grupos atribuídos a um aplicativo proxy de aplicativo](scripts/powershell-display-users-group-of-app.md) | Lista os usuários e grupos atribuídos a um aplicativo proxy de aplicativo específico. |
| [Atribuir um usuário a um aplicativo](scripts/powershell-assign-user-to-app.md) | Atribui um usuário específico a um aplicativo. |
| [Atribuir um grupo a um aplicativo](scripts/powershell-assign-group-to-app.md) | Atribui um grupo específico a um aplicativo. |
|**Configuração de URL externa**||
| [Obtenha todos os aplicativos proxy de aplicativos usando domínios padrão (.msappproxy.net)](scripts/powershell-get-all-default-domain-apps.md)  | Lista todos os aplicativos proxy de aplicativos usando domínios padrão (.msappproxy.net). |
| [Obtenha todos os aplicativos proxy de aplicativos usando a publicação curinga](scripts/powershell-get-all-wildcard-apps.md) | Lista todos os aplicativos proxy de aplicativos usando publicação curinga. |
|**Configuração de domínio personalizado**||
| [Obtenha todos os aplicativos proxy de aplicativos usando domínios personalizados e informações de certificados](scripts/powershell-get-all-custom-domains-and-certs.md) | Lista todos os aplicativos proxy de aplicativos que estão usando domínios personalizados e as informações de certificado associadas aos domínios personalizados. |
| [Obtenha todos os aplicativos de aplicativo Azure AD Proxy publicados sem certificado carregado](scripts/powershell-get-all-custom-domain-no-cert.md) | Lista todos os aplicativos proxy de aplicativos que estão usando domínios personalizados, mas não têm um certificado TLS/SSL válido carregado. |
| [Publique todos os aplicativos de aplicativos Azure AD Proxy com o certificado idêntico](scripts/powershell-get-custom-domain-identical-cert.md) | Lista todos os aplicativos de aplicativos Azure AD Proxy publicados com o certificado idêntico. |
| [Publique todos os aplicativos de aplicativos Azure AD Proxy publicados com o certificado idêntico e substitua-o](scripts/powershell-get-custom-domain-replace-cert.md) | Para aplicativos de proxy Azure AD que são publicados com um certificado idêntico, permite que você substitua o certificado em massa. |
