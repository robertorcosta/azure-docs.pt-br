---
title: Registre uma API web que chama APIs web - plataforma de identidade Microsoft | Azure
description: Aprenda a construir uma API web que chama APIs web downstream (registro de aplicativos).
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885116"
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
