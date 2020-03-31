---
title: Como lidar com as mudanças de cookies do SameSite no navegador Chrome | Azure
titleSuffix: Microsoft identity platform
description: Saiba como lidar com as alterações de cookies do SameSite no navegador Chrome.
services: active-directory
documentationcenter: ''
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: jmprieur
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: 056b787bbbcde6ba7f9510043deabdcf85ac7467
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050536"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>Manipular alterações de cookie SameSite no navegador Chrome

## <a name="what-is-samesite"></a>O que é o SameSite?

`SameSite`é uma propriedade que pode ser definida em cookies HTTP para evitar ataques de CSRF (Cross Site Request Forgery) em aplicativos web:

- Quando `SameSite` é definido como **Lax**, o cookie é enviado em solicitações dentro do mesmo site e em solicitações GET de outros sites. Ele não é enviado em solicitações GET que são de domínio cruzado.
- Um valor de **Strict** garante que o cookie seja enviado em solicitações apenas dentro do mesmo site.

Por padrão, `SameSite` o valor NÃO é definido nos navegadores e é por isso que não há restrições aos cookies que estão sendo enviados nas solicitações. Um aplicativo precisaria optar pela proteção CSRF definindo **Lax** ou **Strict** de acordo com seus requisitos.

## <a name="samesite-changes-and-impact-on-authentication"></a>Alterações e impacto no mesmosite na autenticação

Atualizações recentes [para os padrões no SameSite](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00) propõem `SameSite` proteger aplicativos, tornando o comportamento padrão de quando nenhum valor é definido como Lax. Essa mitigação significa que os cookies serão restritos em solicitações HTTP, exceto GET feitos de outros sites. Além disso, um valor de **None** é introduzido para remover restrições aos cookies que estão sendo enviados. Essas atualizações serão lançadas em breve em uma próxima versão do navegador Chrome.

Quando os aplicativos da Web autenticam com a plataforma Microsoft Identity usando o modo de resposta "form_post", o servidor de login responde ao aplicativo usando um POST HTTP para enviar os tokens ou o código auth. Como essa solicitação é uma solicitação `login.microsoftonline.com` de domínio cruzado `https://contoso.com/auth`(do seu domínio - por exemplo), os cookies que foram definidos pelo seu aplicativo agora se enquadram nas novas regras do Chrome. Os cookies que precisam ser usados em cenários entre sites são cookies que possuem os valores *de estado* e *nonce,* que também são enviados na solicitação de login. Há outros cookies descartados pelo Azure AD para realizar a sessão.

Se você não atualizar seus aplicativos da Web, esse novo comportamento resultará em falhas de autenticação.

## <a name="mitigation-and-samples"></a>Mitigação e amostras

Para superar as falhas de autenticação, os aplicativos da `SameSite` Web `None` autenticados com a plataforma de identidade da Microsoft podem definir a propriedade para cookies que são usados em cenários de domínio cruzado ao serem executados no navegador Chrome.
Outros navegadores (veja [aqui](https://www.chromium.org/updates/same-site/incompatible-clients) para uma lista `SameSite` completa) seguem o comportamento `SameSite=None` anterior e não incluirão os cookies se estiver definido.
É por isso que, para suportar a autenticação em `SameSite` vários `None` navegadores, os aplicativos da Web terão que definir o valor apenas no Chrome e deixar o valor vazio em outros navegadores.

Esta abordagem é demonstrada em nossas amostras de código abaixo.

# <a name="net"></a>[.NET](#tab/dotnet)

A tabela abaixo apresenta as solicitações de tração que trabalharam em torno das alterações do SameSite em nossas amostras de ASP.NET e ASP.NET Core.

| Amostra | Solicitação de pull |
| ------ | ------------ |
|  [ASP.NET tutorial incremental do Web App](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [Mesma correção de #261 de cookies do site](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [ASP.NET amostra do Aplicativo Web MVC](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [Mesma correção de #35 de correção de cookies do site](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [active-directory-dotnet-admin-restrito-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [Mesma correção de #28 de correção de cookies do site](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

para obter detalhes sobre como lidar com cookies do SameSite em ASP.NET e ASP.NET Core, consulte também:

- [Trabalhe com cookies do SameSite no ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/samesite) .
- [ASP.NET blog no site do Mesmo](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="python"></a>[Python](#tab/python)

| Amostra |
| ------ |
|  [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="java"></a>[Java](#tab/java)

| Amostra | Solicitação de pull |
| ------ | ------------ |
|  [ms-identidade-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [Mesma correção de #24 de cookies do site](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [ms-identidade-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [Mesma correção de #4 de cookies do site](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o SameSite e o cenário do aplicativo web:

> [!div class="nextstepaction"]
> [FaQ do Google Chrome no SameSite](https://www.chromium.org/updates/same-site/faq)

> [!div class="nextstepaction"]
> [Página do Chromium SameSite](https://www.chromium.org/updates/same-site)

> [!div class="nextstepaction"]
> [Cenário: Web app que assina em usuários](scenario-web-app-sign-user-overview.md)