---
title: Registre uma API web que chama APIs web - plataforma de identidade Microsoft | Azure
description: Aprenda a construir uma API web que chama APIs web downstream (registro de aplicativos).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: bafd71f34602535bb6193a8d8114a1182e4e8f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701783"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Uma API web que chama APIs web: registro de aplicativos

Uma API web que chama APIs web downstream tem o mesmo registro de uma API da Web protegida. Portanto, você precisa seguir as instruções na [API web protegida: Registro de aplicativos.](scenario-protected-web-api-app-registration.md)

Como o aplicativo web agora chama APIs da Web, ele se torna um aplicativo cliente confidencial. É por isso que são necessárias informações extras de registro: o aplicativo precisa compartilhar segredos (credenciais do cliente) com a plataforma de identidade da Microsoft.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Permissões de API

Os aplicativos da Web chamam APIs em nome dos usuários para os quais o token portador foi recebido. Os aplicativos web precisam solicitar permissões delegadas. Para obter mais informações, consulte [Adicionar permissões para acessar APIs da Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Uma API web que chama APIs da Web: configuração de código](scenario-web-api-call-api-app-configuration.md)
