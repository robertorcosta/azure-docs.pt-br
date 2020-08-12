---
title: Mover um aplicativo daemon que chama APIs da Web para produção – plataforma de identidade da Microsoft | Azure
description: Saiba como mover um aplicativo daemon que chama APIs da Web para produção
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
ms.openlocfilehash: 72e6d0a8677f7a8175223b80541c99026a9eb16f
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88119157"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Aplicativo daemon que chama APIs da Web – mover para produção

Agora que você sabe como adquirir e usar um token para uma chamada de serviço a serviço, saiba como mover seu aplicativo para produção.

## <a name="deployment---multitenant-daemon-apps"></a>Implantação – aplicativos de daemon multilocatário

Se você for um ISV criando um aplicativo daemon que pode ser executado em vários locatários, você precisará certificar-se de que o administrador do locatário:

- Provisiona uma entidade de serviço para o aplicativo.
- Concede consentimento ao aplicativo.

Você precisará explicar aos seus clientes como executar essas operações. Para obter mais informações, consulte [solicitando consentimento para um locatário inteiro](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Próximas etapas

Aqui estão alguns links para ajudá-lo a saber mais:

# <a name="net"></a>[.NET](#tab/dotnet)

- Início rápido: [adquirir um token e chamar Microsoft Graph API de um aplicativo de console usando a identidade do aplicativo](./quickstart-v2-netcore-daemon.md).
- Documentação de referência para:
  - Criando uma instância de [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder).
  - Chamando [AcquireTokenForClient](/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder).
- Outros exemplos/tutoriais:
  - [Microsoft-Identity-Platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) apresenta um simples aplicativo de console do daemon do .NET Core que exibe os usuários de uma consulta de locatário Microsoft Graph.

    ![Topologia de aplicativo de daemon de exemplo](media/scenario-daemon-app/daemon-app-sample.svg)

    O mesmo exemplo também ilustra uma variação com certificados:

    ![Topologia de aplicativo daemon de exemplo-certificados](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [Microsoft-Identity-Platform-ASPNET-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) apresenta um aplicativo Web ASP.NET MVC que sincroniza dados de Microsoft Graph usando a identidade do aplicativo em vez de em nome de um usuário. Este exemplo também ilustra o processo de consentimento do administrador.

    ![Topologia](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="python"></a>[Python](#tab/python)

Experimente o início rápido [adquira um token e chame Microsoft Graph API de um aplicativo de console do Python usando a identidade do aplicativo](./quickstart-v2-python-daemon.md).

# <a name="java"></a>[Java](#tab/java)

O MSAL Java está atualmente em visualização pública. Para obter mais informações, consulte [exemplos de desenvolvimento do MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples).

---