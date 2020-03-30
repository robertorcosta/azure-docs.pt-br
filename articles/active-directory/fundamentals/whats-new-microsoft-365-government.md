---
title: O que há de novo para o Azure AD no Governo Microsoft 365? | Microsoft Docs
description: Conheça algumas alterações no Azure Active Directory (Azure AD) na instância de nuvem do Governo Microsoft 365, o que pode impactá-lo.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.reviewer: sumitp
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48a7e5e810e509a97b6010218532f6e7dff0b73f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425537"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>O que há de novo para o Azure Active Directory no Governo Microsoft 365

Fizemos algumas alterações no Azure Active Directory (Azure AD) na instância de nuvem do Governo Microsoft 365, que é aplicável aos clientes que usam os seguintes serviços:

- Microsoft Azure Governamental

- Governo Microsoft 365 - GCC High

- Governo Microsoft 365 – DoD

Este artigo não se aplica aos clientes do Microsoft 365 Government – GCC.

## <a name="changes-to-the-initial-domain-name"></a>Alterações no nome de domínio inicial

Durante a inscrição inicial de sua organização para um serviço online do Governo Microsoft 365, você foi solicitado a escolher o nome de domínio da sua organização, `<your-domain-name>.onmicrosoft.com`. Se você já tiver um nome de domínio com o sufixo .com, nada mudará.

No entanto, se você estiver se inscrevendo para um novo serviço do Governo Microsoft `.us` 365, você será solicitado a escolher um nome de domínio usando o sufixo. Então, será. `<your-domain-name>.onmicrosoft.us`

>[!Note]
>Essa mudança não se aplica a nenhum cliente que seja gerenciado por provedores de serviços em nuvem (CSPs).

## <a name="changes-to-portal-access"></a>Alterações no acesso ao portal

Atualizamos os pontos finais do portal para o Governo Microsoft Azure, Microsoft 365 Government – GCC High e Microsoft 365 Government – DoD, como mostrado na [tabela de mapeamento Endpoint](#endpoint-mapping).

Anteriormente, os clientes podiam fazer login usando os portais azure (portal.azure.com) e Office 365 (portal.office.com). Com esta atualização, os clientes agora devem entrar usando os portais específicos Microsoft Azure Government, Microsoft 365 Government - GCC High e Microsoft 365 Government - DoD.

## <a name="endpoint-mapping"></a>Mapeamento de ponto de extremidade

A tabela a seguir mostra os pontos finais para todos os clientes:

| Nome | Detalhes do ponto final |
|------|------------------|
| Portais |Governo microsoft azure:https://portal.azure.us<p>Governo Microsoft 365 – GCC High:https://portal.office365.us<p>Governo Microsoft 365 – DoD:https://portal.apps.mil |
| Ponto final da Autoridade do Diretório Ativo do Azure | https://login.microsoftonline.us |
| Microsoft Graph API para Microsoft 365 Government - GCC High | https://graph.microsoft.us |
| Microsoft Graph API para Microsoft 365 Government - DoD | https://dod-graph.microsoft.us |
| Pontos finais de serviços do governo azure | Para obter detalhes, consulte [o guia de desenvolvedores do Governo Do Azure](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) |
| Governo Microsoft 365 - GCC Pontos finais altos | Para obter detalhes, consulte [Office 365 Pontos finais do Governo dos EUA GCC](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Governo Microsoft 365 - DoD | Para obter detalhes, consulte [os pontos finais do Office 365 do Governo dos EUA](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte estes artigos:

- [O que é o Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Atualização do ponto final da Autoridade AAD do Governo AZure](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [Pontos finais do Microsoft Graph na nuvem do governo dos EUA](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Escritório 365 Governo dos EUA GCC Alto e DoD](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)
