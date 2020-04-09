---
title: Use o MSAL com o Diretório Ativo Do Azure B2CLearn | Azure
titleSuffix: Microsoft identity platform
description: A Microsoft Authentication Library for JavaScript (MSAL.js) permite que os aplicativos trabalhem com o Azure AD B2C e adquiram tokens para chamar APIs da Web seguras. Essas APIs Web podem ser o Microsoft Graph, outras APIs da Microsoft, APIs Web de terceiros ou sua própria API Web.
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
ms.openlocfilehash: dc8a330bc09f37f7941534ed7c17d1ffd14d08c5
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875955"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-active-directory-b2c"></a>Use a Biblioteca de Autenticação da Microsoft para JavaScript para trabalhar com o Azure Active Directory B2C

[A Biblioteca de Autenticação da Microsoft para JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) permite que os desenvolvedores JavaScript autentiquem usuários com identidades sociais e locais usando [o Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). Ao usar o Azure AD B2C como um serviço de gerenciamento de identidade, você pode personalizar e controlar como os clientes se inscrevem, fazem login e gerenciam seus perfis quando usam seus aplicativos.

O Azure AD B2C também permite que você marque e personalize a interface do usuário durante o processo de autenticação, a fim de proporcionar uma experiência perfeita aos seus clientes.

Este artigo demonstra como usar o MSAL.js para trabalhar com o Azure AD B2C e resume pontos-chave que você deve estar ciente. Para uma discussão completa e tutorial, consulte [a documentação Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/overview).

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não criou seu próprio [inquilino Azure AD B2C,](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)comece a criar um agora (você também pode usar um inquilino Azure AD B2C existente se você já tiver um).

Esta demonstração contém duas partes:

- como proteger uma API web.
- como registrar um aplicativo de uma página única para autenticar e chamar *essa* API web.

## <a name="nodejs-web-api"></a>Node.js Web API

> [!NOTE]
> Neste momento, o MSAL.js for Node ainda está em desenvolvimento (veja o [roteiro](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)). Enquanto isso, sugerimos o uso [do passport-azure-ad](https://github.com/AzureAD/passport-azure-ad), uma biblioteca de autenticação para Node.js desenvolvida e suportada pela Microsoft.

As etapas a seguir demonstram como uma **API web** pode usar o Azure AD B2C para se proteger e expor escopos selecionados a um aplicativo cliente.

### <a name="step-1-register-your-application"></a>Etapa 1: Registrar seu aplicativo

Para proteger sua API web com o Azure AD B2C, primeiro você precisa registrá-la. Confira [Registrar seu aplicativo](https://docs.microsoft.com/azure/active-directory-b2c/add-web-application?tabs=applications) para ver as etapas detalhadas.

### <a name="step-2-download-the-sample-application"></a>Passo 2: Baixe o aplicativo de amostra

Baixe o exemplo como um arquivo zip ou clone-o do GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>Passo 3: Configurar autenticação

1. Abra o arquivo `config.js` no exemplo.

2. Configure a amostra com as credenciais de aplicativo que você obteve anteriormente ao registrar seu aplicativo. Altere as seguintes linhas de código substituindo os valores pelos nomes do seu clienteID, host, tenantId e nome da política.

```JavaScript
const clientID = "<Application ID for your Node.js Web API - found on Properties page in Azure portal e.g. 93733604-cc77-4a3c-a604-87084dd55348>";
const b2cDomainHost = "<Domain of your B2C host eg. fabrikamb2c.b2clogin.com>";
const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (GUID)
const policyName = "<Name of your sign in / sign up policy, e.g. B2C_1_signupsignin1>";
```

Para obter mais informações, confira esta [amostra de API web Node.js B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).

---

## <a name="javascript-spa"></a>JavaScript SPA

As etapas a seguir demonstram como um **aplicativo de uma página única** pode usar o Azure AD B2C para se inscrever, fazer login e chamar uma API da Web protegida.

### <a name="step-1-register-your-application"></a>Etapa 1: Registrar seu aplicativo

Para implementar a autenticação, primeiro você precisará registrar seu aplicativo. Confira [Registrar seu aplicativo](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications) para ver as etapas detalhadas.

### <a name="step-2-download-the-sample-application"></a>Passo 2: Baixe o aplicativo de amostra

Baixe o exemplo como um arquivo zip ou clone-o do GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Passo 3: Configurar autenticação

Existem dois pontos de interesse na configuração do seu aplicativo:

- Configurar o ponto final da API e os escopos expostos
- Configure parâmetros de autenticação e escopos de token

1. Abra o arquivo `apiConfig.js` no exemplo.

2. Configure a amostra com os parâmetros que você obteve anteriormente ao registrar sua API web. Altere as seguintes linhas de código substituindo os valores pelo endereço da sua API web e os escopos expostos.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello" 
    };
   ```

3. Abra o arquivo `authConfig.js` no exemplo.

4. Configure a amostra com os parâmetros obtidos anteriormente ao registrar seu aplicativo de página única. Altere as seguintes linhas de código substituindo os valores pelos escopos clientId, metadados de autoridade e solicitação de tokens.

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

Para obter mais informações, confira esta [amostra de aplicativo de página única JavaScript B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

---

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre:
- [Fluxos de usuários](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)
- [Políticas personalizadas](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)
- [Personalização de experiência do usuário](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-configure-user-input)
