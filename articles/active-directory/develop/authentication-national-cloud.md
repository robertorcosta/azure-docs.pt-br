---
title: Autenticação Azure AD & nuvens nacionais | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre o registro do aplicativo e os pontos de extremidade de autenticação para nuvens nacionais.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: negoe
ms.reviewer: negoe,celested
ms.custom: aaddev
ms.openlocfilehash: 20a053369149dc29d6485c49bb091a75bb9fb591
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262991"
---
# <a name="national-clouds"></a>Nuvens nacionais

Nuvens nacionais são casos fisicamente isolados de Azure. Essas regiões do Azure são projetadas para garantir que os requisitos de residência, de soberania e de conformidade de dados sejam respeitados dentro de limites geográficos.

Incluindo a nuvem global, o Azure Active Directory (Azure AD) é implantado nas seguintes nuvens nacionais:  

- Azure Government
- Azure Alemanha
- Azure China 21Vianet

As nuvens nacionais são únicas e um ambiente separado do Azure global. É importante estar ciente das principais diferenças ao desenvolver sua aplicação para esses ambientes. As diferenças incluem o registro de aplicativos, a aquisição de tokens e a configuração de pontos finais.

## <a name="app-registration-endpoints"></a>Pontos de extremidade de registro do aplicativo

Há um portal Azure separado para cada uma das nuvens nacionais. Para integrar aplicativos com a plataforma de identidade Microsoft em uma nuvem nacional, você é obrigado a registrar seu aplicativo separadamente em cada portal do Azure específico para o ambiente.

A tabela a seguir lista os URLs base para os pontos finais do Azure AD usados para registrar um aplicativo para cada nuvem nacional.

| Nuvem nacional | Ponto de extremidade do portal do Azure AD |
|----------------|--------------------------|
| Azure AD for US Government | `https://portal.azure.us` |
| Azure AD Alemanha | `https://portal.microsoftazure.de` |
| Azure AD China operado pela 21Vianet | `https://portal.azure.cn` |
| Azure AD (serviço global) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Pontos de extremidade de autenticação do Azure AD

Todas as nuvens nacionais autenticam usuários separadamente em cada ambiente e têm pontos de extremidade de autenticação separados.

A tabela a seguir lista os URLs base para os pontos finais do Azure AD usados para adquirir tokens para cada nuvem nacional.

| Nuvem nacional | Ponto final de autenticação do Azure AD |
|----------------|-------------------------|
| Azure AD for US Government | `https://login.microsoftonline.us` |
| Azure AD Alemanha| `https://login.microsoftonline.de` |
| Azure AD China operado pela 21Vianet | `https://login.chinacloudapi.cn` |
| Azure AD (serviço global)| `https://login.microsoftonline.com` |

Você pode formar solicitações para a autorização do Azure AD ou pontos finais de token usando a URL base específica da região apropriada. Por exemplo, para o Azure Alemanha:

  - O ponto de extremidade de autorização comum é `https://login.microsoftonline.de/common/oauth2/authorize`.
  - O ponto de extremidade de token comum é `https://login.microsoftonline.de/common/oauth2/token`.

Para aplicativos de inquilino único, substitua "comum" nas URLs anteriores pelo seu ID ou nome de inquilino. Um exemplo é `https://login.microsoftonline.de/contoso.com`.

## <a name="microsoft-graph-api"></a>API do Microsoft Graph

Para saber como chamar as APIs do Microsoft Graph em um ambiente de nuvem nacional, acesse [o Microsoft Graph em implantações em nuvem nacionais](https://developer.microsoft.com/graph/docs/concepts/deployments).

> [!IMPORTANT]
> Certos serviços e recursos que estão em regiões específicas do serviço global podem não estar disponíveis em todas as nuvens nacionais. Para saber quais serviços estão disponíveis, acesse [Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast)

Para aprender como construir um aplicativo usando a plataforma de identidade Microsoft, siga o tutorial da [Microsoft Authentication Library (MSAL).](msal-national-cloud.md) Especificamente, este aplicativo entrará em um usuário e obterá um token de acesso para chamar a API do Microsoft Graph.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre:

- [Azure Governamental](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Alemanha](https://docs.microsoft.com/azure/germany/)
- [Noções básicas de autenticação do Azure AD](authentication-scenarios.md)
