---
title: Escopos para aplicativos v 1.0 (MSAL) | Azure
description: Saiba mais sobre os escopos para um aplicativo v1.0 usando a Biblioteca de Autenticação da Microsoft (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 61d07c1ba912a0e24b2f4e5fa67243b4525db367
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536175"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Escopos para uma API Web aceitando tokens v 1.0

As permissões OAuth2 são escopos de permissão que um Azure Active Directory (Azure AD) para desenvolvedores (v 1.0) aplicativo de API Web (Resource) expõe para aplicativos cliente. Os escopos de permissões podem ser concedidos a aplicativos cliente durante o consentimento. Confira a seção sobre `oauth2Permissions` na [referência do manifesto do aplicativo Azure Active Directory](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Escopos para solicitar acesso a permissões específicas do OAuth2 de um aplicativo v1.0

Para adquirir tokens para escopos específicos de um aplicativo v 1.0 (por exemplo, a API Microsoft Graph https://graph.microsoft.com), que é, crie escopos concatenando um identificador de recurso desejado com uma permissão OAuth2 desejada para esse recurso.

Por exemplo, para acessar em nome do usuário uma API Web v 1.0 em que o URI da ID do aplicativo é `ResourceId`:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Para ler e gravar com o MSAL.NET Azure AD usando a API de Microsoft Graph (\/https:/Graph.Microsoft.com/), você precisa criar uma lista de escopos, conforme mostrado nos exemplos a seguir:

```csharp
string ResourceId = "https://graph.microsoft.com/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.microsoft.com/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Para gravar o escopo correspondente à API de Azure Resource Manager (https:\//Management.Core.Windows.net/), você precisa solicitar o seguinte escopo (Observe as duas barras):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Você precisa usar duas barras, pois a API do Azure Resource Manager espera uma barra em sua declaração de público-alvo (aud), e, em seguida, há uma barra para separar o nome da API do escopo.

A lógica usada pelo Azure AD é a seguinte:

- Para o ponto de extremidade ADAL (Azure AD v 1.0) com um token de acesso v 1.0 (o único possível), AUD = recurso
- Para o ponto de extremidade do MSAL (plataforma Microsoft Identity (v 2.0)), solicite um token de acesso para um recurso que aceita tokens v 2.0,`aud=resource.AppId`
- Para MSAL (ponto de extremidade v 2.0) que solicita um token de acesso para um recurso que aceita um token de acesso v 1.0 (que é o caso acima), o Azure AD analisa o público-alvo desejado do escopo solicitado, levando tudo antes da última barra e usando-o como o identificador de recurso. Portanto, se https:\//Database.Windows.net espera um público de "https:\//Database.Windows.net/", você precisará solicitar um escopo de "https:\//Database.Windows.net//.default". Veja também o problema [do GitHub #747: a barra à direita da URL do recurso é omitida, o que causou uma falha de autenticação do SQL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Escopos para solicitar acesso a todas as permissões de um aplicativo v1.0

Se você quiser adquirir um token para todos os escopos estáticos de um aplicativo v1.0, anexe ".default" ao URI da ID do aplicativo da API:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-a-client-credential-flowdaemon-app"></a>Escopos a serem solicitados para um aplicativo de fluxo de credencial do cliente/daemon

No caso do fluxo de credencial do cliente, o escopo a ser aprovado também seria `/.default`. Isso informa ao Azure AD: "todas as permissões no nível de aplicativo que o administrador consentiu no registro do aplicativo.
