---
title: Registre um aplicativo web que chama APIs web - plataforma de identidade da Microsoft | Azure
description: Saiba como registrar um aplicativo web que chama APIs da Web
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
ms.openlocfilehash: 8cb7d86bd419563363779c499962c81f0c59e3b6
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881869"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>Um aplicativo web que chama APIs web: registro de aplicativos

Um aplicativo web que chama APIs web tem o mesmo registro que um aplicativo web que assina os usuários. Então, siga as instruções em [Um aplicativo web que assina nos usuários: Registro de aplicativos](scenario-web-app-sign-user-app-registration.md).

No entanto, como o aplicativo web agora também chama APIs da Web, ele se torna um aplicativo cliente confidencial. É por isso que algum registro extra é necessário. O aplicativo deve compartilhar credenciais ou *segredos*do cliente com a plataforma de identidade da Microsoft.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Permissões de API

Os aplicativos da Web chamam APIs em nome do usuário inscrito. Para isso, eles devem solicitar *permissões delegadas.* Para obter detalhes, consulte [Adicionar permissões para acessar APIs da Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Um aplicativo web que chama APIs da Web: configuração de código](scenario-web-app-call-api-app-configuration.md)
