---
title: Registrar um aplicativo Web que chama APIs da Web-plataforma de identidade da Microsoft | Azure
description: Saiba como registrar um aplicativo Web que chama APIs da Web
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
ms.openlocfilehash: 5a57fcef3569734964bf6e8a41faa49800798f9b
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759050"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>Um aplicativo Web que chama APIs da Web: registro de aplicativo

Um aplicativo Web que chama APIs da Web tem o mesmo registro de um aplicativo Web que assina usuários. Portanto, siga as instruções em [um aplicativo Web que conecta usuários: registro de aplicativo](scenario-web-app-sign-user-app-registration.md).

No entanto, como o aplicativo Web agora também chama APIs da Web, ele se torna um aplicativo cliente confidencial. É por isso que um registro extra é necessário. O aplicativo deve compartilhar credenciais de cliente ou *segredos*, com a plataforma de identidade da Microsoft.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Permissões de API

Os aplicativos Web chamam APIs em nome do usuário conectado. Para fazer isso, eles devem solicitar *permissões delegadas*. Para obter detalhes, consulte [adicionar permissões para acessar APIs da Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Um aplicativo Web que chama APIs da Web: configuração de código](scenario-web-app-call-api-app-configuration.md)
