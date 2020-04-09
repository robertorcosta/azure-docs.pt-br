---
title: Mova um aplicativo daemon que chama APIs web para produção - plataforma de identidade Microsoft | Azure
description: Saiba como mover um aplicativo daemon que chama APIs da Web para a produção
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e5d25169aa3daee33a9f02e605cbff91776dc7f1
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885422"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Aplicativo Daemon que chama APIs da web - passar para a produção

Agora que você sabe como adquirir e usar um token para uma chamada de serviço, saiba como mover seu aplicativo para a produção.

## <a name="deployment---multitenant-daemon-apps"></a>Implantação - aplicativos daemon multilocatários

Se você é um ISV criando um aplicativo daemon que pode ser executado em vários inquilinos, você precisa ter certeza de que o inquilino é um administrado:

- Disposições um diretor de serviço para a aplicação.
- Concede consentimento ao pedido.

Você precisará explicar aos seus clientes como realizar essas operações. Para obter mais informações, consulte [Solicitar o consentimento para um inquilino inteiro](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Próximas etapas

Aqui estão alguns links para ajudá-lo a aprender mais:

# <a name="net"></a>[.NET](#tab/dotnet)

- Quickstart: [Adquira um token e chame a API do Microsoft Graph a partir de um aplicativo de console usando a identidade do aplicativo](./quickstart-v2-netcore-daemon.md).
- Documentação de referência para:
  - Instanciar [o ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder).
  - Chamando [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder).
- Outras amostras/tutoriais:
  - [microsoft-identity-platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) possui um aplicativo de console daemon .NET Core simples que exibe os usuários de um inquilino consultando o Microsoft Graph.

    ![Topologia do aplicativo daemon de amostra](media/scenario-daemon-app/daemon-app-sample.svg)

    A mesma amostra também ilustra uma variação com certificados:

    ![Topologia do aplicativo daemon amostra - certificados](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [a plataforma de identidade da Microsoft-aspnet-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) possui um aplicativo web mvc ASP.NET que sincroniza dados do Microsoft Graph usando a identidade do aplicativo em vez de em nome de um usuário. Esta amostra também ilustra o processo de consentimento do admin.

    ![Topologia](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="python"></a>[Python](#tab/python)

Experimente o quickstart [Adquira um token e chame a Microsoft Graph API de um aplicativo de console Python usando a identidade do aplicativo](./quickstart-v2-python-daemon.md).

# <a name="java"></a>[Java](#tab/java)

MSAL Java está atualmente em visualização pública. Para obter mais informações, consulte [msal java dev samples](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples).

---
