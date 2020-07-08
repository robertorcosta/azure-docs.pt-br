---
title: Registrar uma API Web que chama APIs da Web-plataforma de identidade da Microsoft | Azure
description: Saiba como criar uma API Web que chama APIs da Web downstream (registro de aplicativo).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 048f7d41bd9d106121859e6b1fc013258067af9c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885116"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Uma API Web que chama APIs da Web: registro de aplicativo

Uma API da Web que chama APIs da Web downstream tem o mesmo registro de uma API Web protegida. Portanto, você precisa seguir as instruções em [API Web protegida: registro de aplicativo](scenario-protected-web-api-app-registration.md).

Como o aplicativo Web agora chama APIs da Web, ele se torna um aplicativo cliente confidencial. É por isso que as informações de registro extras são necessárias: o aplicativo precisa compartilhar segredos (credenciais de cliente) com a plataforma Microsoft Identity.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Permissões de API

Os aplicativos Web chamam APIs em nome dos usuários para os quais o token de portador foi recebido. Os aplicativos Web precisam solicitar permissões delegadas. Para obter mais informações, consulte [Adicionar permissões para acessar APIs Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Uma API Web que chama APIs da Web: configuração de código](scenario-web-api-call-api-app-configuration.md)
