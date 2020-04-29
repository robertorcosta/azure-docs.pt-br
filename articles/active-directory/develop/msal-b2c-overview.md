---
title: Usar MSAL com Azure Active Directory B2CLearn | Azure
titleSuffix: Microsoft identity platform
description: A biblioteca de autenticação da Microsoft para JavaScript (MSAL. js) permite que os aplicativos trabalhem com Azure AD B2C e adquirem tokens para chamar APIs da Web protegidas. Essas APIs Web podem ser o Microsoft Graph, outras APIs da Microsoft, APIs Web de terceiros ou sua própria API Web.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8e076dfd6670265d458eb35d8e1b3e4500009a12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81534475"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-active-directory-b2c"></a>Usar a biblioteca de autenticação da Microsoft para JavaScript para trabalhar com Azure Active Directory B2C

A [biblioteca de autenticação da Microsoft para JavaScript (MSAL. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) permite que os desenvolvedores de JavaScript autentiquem usuários com identidades sociais e locais usando o [Azure Active Directory B2C (Azure ad B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). Usando Azure AD B2C como um serviço de gerenciamento de identidade, você pode personalizar e controlar como os clientes se inscrevem, entram e gerenciam seus perfis quando usam seus aplicativos.

O Azure AD B2C também permite que você marque e personalize a interface do usuário de seus aplicativos durante o processo de autenticação a fim de fornecer uma experiência direta aos seus clientes.

Este artigo demonstra como usar o MSAL. js para trabalhar com Azure AD B2C e resume os pontos-chave dos quais você deve estar ciente. Para obter uma discussão e tutorial completos, consulte a [documentação do Azure ad B2C](https://docs.microsoft.com/azure/active-directory-b2c/overview).

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não criou seu próprio [locatário de Azure ad B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), comece com a criação de um agora (você também poderá usar um locatário de Azure ad B2C existente se já tiver um).

Esta demonstração contém duas partes:

- como proteger uma API da Web.
- como registrar um aplicativo de página única para autenticar e chamar *essa* API da Web.

## <a name="nodejs-web-api"></a>API de Web Node.js

> [!NOTE]
> Neste momento, o MSAL. js para o nó ainda está em desenvolvimento (consulte o [roteiro](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)). Enquanto isso, sugerimos o uso [do Passport-Azure-ad](https://github.com/AzureAD/passport-azure-ad), uma biblioteca de autenticação para o Node. js desenvolvido e suportado pela Microsoft.

As etapas a seguir demonstram como uma **API Web** pode usar Azure ad B2C para se proteger e expor escopos selecionados a um aplicativo cliente.

### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo

Para proteger sua API Web com Azure AD B2C, primeiro você precisa registrá-la. Confira [Registrar seu aplicativo](https://docs.microsoft.com/azure/active-directory-b2c/add-web-application?tabs=applications) para ver as etapas detalhadas.

### <a name="step-2-download-the-sample-application"></a>Etapa 2: baixar o aplicativo de exemplo

Baixe o exemplo como um arquivo zip ou clone-o do GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>Etapa 3: configurar a autenticação

1. Abra o arquivo `config.js` no exemplo.

2. Configure o exemplo com as credenciais do aplicativo que você obteve anteriormente ao registrar seu aplicativo. Altere as linhas de código a seguir substituindo os valores pelos nomes de clientID, host, tenantid e nome da política.

```JavaScript
const clientID = "<Application ID for your Node.js web API - found on Properties page in Azure portal e.g. 93733604-cc77-4a3c-a604-87084dd55348>";
const b2cDomainHost = "<Domain of your B2C host eg. fabrikamb2c.b2clogin.com>";
const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (GUID)
const policyName = "<Name of your sign in / sign up policy, e.g. B2C_1_signupsignin1>";
```

Para obter mais informações, confira este [exemplo de API Web do B2C do node. js](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).

---

## <a name="javascript-spa"></a>JavaScript SPA

As etapas a seguir demonstram como um **aplicativo de página única** pode usar Azure ad B2C para se inscrever, entrar e chamar uma API Web protegida.

### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo

Para implementar a autenticação, primeiro você precisará registrar seu aplicativo. Confira [Registrar seu aplicativo](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications) para ver as etapas detalhadas.

### <a name="step-2-download-the-sample-application"></a>Etapa 2: baixar o aplicativo de exemplo

Baixe o exemplo como um arquivo zip ou clone-o do GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Etapa 3: configurar a autenticação

Há dois pontos de interesse na configuração de seu aplicativo:

- Configurar o ponto de extremidade de API e escopos expostos
- Configurar parâmetros de autenticação e escopos de token

1. Abra o arquivo `apiConfig.js` no exemplo.

2. Configure o exemplo com os parâmetros que você obteve anteriormente ao registrar sua API Web. Altere as linhas de código a seguir substituindo os valores pelo endereço de sua API Web e escopos expostos.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

3. Abra o arquivo `authConfig.js` no exemplo.

4. Configure o exemplo com os parâmetros que você obteve anteriormente ao registrar seu aplicativo de página única. Altere as linhas de código a seguir substituindo os valores pelos escopos de solicitação ClientId, metadados de autoridade e token.

   ```javascript
    // Config object to be passed to Msal on creation.
    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_signupsignin1",
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage", // This configures where your cache will be stored
            storeAuthStateInCookie: false // Set this to "true" to save cache in cookies
        }
    };

    // Add here scopes for id token to be used at the MS Identity Platform endpoint
    const loginRequest = {
        scopes: ["openid", "profile"],
    };
   ```

Para obter mais informações, confira este [exemplo de aplicativo de página única do JavaScript B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

---

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre:
- [Fluxos de usuário](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)
- [Políticas personalizadas](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)
- [Personalização de experiência do usuário](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-configure-user-input)
