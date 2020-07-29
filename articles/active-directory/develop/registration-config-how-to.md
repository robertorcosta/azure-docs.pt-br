---
title: Obter os pontos de extremidade para um registro de aplicativo do Azure AD
titleSuffix: Microsoft identity platform
description: Como localizar os pontos de extremidade de autenticação para um aplicativo personalizado que você está desenvolvendo ou registrando com o Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: ryanwi
ms.openlocfilehash: dc37ce474ad3226f5dbf7e6efd687ddf55f7da03
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82926673"
---
# <a name="how-to-discover-endpoints"></a>Como descobrir pontos de extremidade

Você pode encontrar os pontos de extremidade de autenticação para seu aplicativo no [Portal do Azure](https://portal.azure.com).

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione **Azure Active Directory**.
1. Em **gerenciar**, selecione **registros de aplicativo**e, em seguida, selecione **pontos de extremidade** no menu superior.

    A página **pontos de extremidade** é exibida, mostrando os pontos de extremidade de autenticação para seu locatário.
    
    Use o ponto de extremidade que corresponde ao protocolo de autenticação que você está usando em conjunto com a **ID do aplicativo (cliente)** para criar a solicitação de autenticação específica para seu aplicativo.

As **nuvens nacionais** (por exemplo, Azure ad China, Alemanha e governo dos EUA) têm seu próprio portal de registro de aplicativo e pontos de extremidade de autenticação do Azure AD. Saiba mais na [visão geral das nuvens nacionais](authentication-national-cloud.md).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre pontos de extremidade em diferentes ambientes do Azure, consulte [visão geral das nuvens nacionais](authentication-national-cloud.md).
