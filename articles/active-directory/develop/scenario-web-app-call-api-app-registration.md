---
title: Registrar um aplicativo Web que chama APIs da Web-plataforma de identidade da Microsoft | Azure
description: Saiba como registrar um aplicativo Web que chama APIs da Web
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
ms.openlocfilehash: 863d8d87d5de771293199dd60618128b0ac11bba
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442592"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>Um aplicativo Web que chama APIs da Web: registro de aplicativo

Um aplicativo Web que chama APIs da Web tem o mesmo registro de um aplicativo Web que assina usuários. Portanto, siga as instruções em [um aplicativo Web que conecta usuários: registro de aplicativo](scenario-web-app-sign-user-app-registration.md).

No entanto, como o aplicativo Web agora também chama APIs da Web, ele se torna um aplicativo cliente confidencial. É por isso que um registro extra é necessário. O aplicativo deve compartilhar credenciais de cliente ou *segredos* , com a plataforma de identidade da Microsoft.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Permissões de API

Os aplicativos Web chamam APIs em nome do usuário conectado. Para fazer isso, eles devem solicitar *permissões delegadas*. Para obter detalhes, consulte [adicionar permissões para acessar sua API Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo neste cenário, configuração de [código](scenario-web-app-call-api-app-configuration.md).
