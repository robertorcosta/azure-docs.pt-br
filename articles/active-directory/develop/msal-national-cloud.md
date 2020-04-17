---
title: Use o MSAL em um aplicativo nacional em nuvem | Azure
titleSuffix: Microsoft identity platform
description: A Microsoft Authentication Library (MSAL) permite que os desenvolvedores de aplicativos adquiram tokens para chamar APIs da Web seguras. Essas APIs da Web podem ser o Microsoft Graph, outras APIs da Microsoft, APIs da Web parceiras ou sua própria API web. A MSAL dá suporte a várias arquiteturas de aplicativos e plataformas.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f3bb4dd1c564e5f6c4a8ee1bb5bf7424a74a339e
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533982"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Use o MSAL em um ambiente de nuvem nacional

[Nuvens nacionais](authentication-national-cloud.md), também conhecidas como nuvens soberanas, são instâncias fisicamente isoladas do Azure. Essas regiões do Azure ajudam a garantir que os requisitos de residência de dados, soberania e conformidade sejam honrados dentro dos limites geográficos.

Além da nuvem mundial da Microsoft, a Microsoft Authentication Library (MSAL) permite que desenvolvedores de aplicativos em nuvens nacionais adquiram tokens a fim de autenticar e chamar APIs da Web seguras. Essas APIs da Web podem ser o Microsoft Graph ou outras APIs da Microsoft.

Incluindo a nuvem global, o Azure Active Directory (Azure AD) é implantado nas seguintes nuvens nacionais:  

- Azure Government
- Azure China 21Vianet
- Azure Alemanha

Este guia demonstra como fazer login em contas de trabalho e escola, obter um token de acesso e chamar a API do Microsoft Graph no ambiente [de nuvem do Governo Azure.](https://azure.microsoft.com/global-infrastructure/government/)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de atender a esses pré-requisitos.

### <a name="choose-the-appropriate-identities"></a>Escolha as identidades apropriadas

Os aplicativos [do governo azure](https://docs.microsoft.com/azure/azure-government/) podem usar as identidades do governo azure AD e as identidades públicas do Azure AD para autenticar usuários. Como você pode usar qualquer uma dessas identidades, você precisa decidir qual ponto final de autoridade você deve escolher para o seu cenário:

- Azure AD Public: Comumente usado se sua organização já tem um inquilino Público Azure AD para apoiar o Office 365 (Público ou GCC) ou outro aplicativo.
- Azure AD Government: Comumente usado se sua organização já tem um inquilino do Azure AD Government para apoiar o Office 365 (GCC High ou DoD) ou está criando um novo inquilino no Azure AD Government.

Depois de decidir, uma consideração especial é onde você realiza seu registro de aplicativo. Se você escolher as identidades públicas do Azure AD para o seu aplicativo do Governo Azure, você deve registrar o aplicativo no seu inquilino Público Azure AD.

### <a name="get-an-azure-government-subscription"></a>Obtenha uma assinatura do Governo Azure

Para obter uma assinatura do Governo Azure, consulte [Gerenciar e conectar-se à sua assinatura no Governo Azure](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions).

Se você não tiver uma assinatura do Governo Do Azure, crie uma [conta gratuita](https://azure.microsoft.com/global-infrastructure/government/request/) antes de começar.

Para obter detalhes sobre o uso de uma nuvem nacional com uma linguagem de programação específica, escolha a guia que corresponde ao seu idioma:

## <a name="net"></a>[.NET](#tab/donet)

Você pode usar MSAL.NET para fazer login nos usuários, adquirir tokens e chamar a API do Microsoft Graph em nuvens nacionais.

Os tutoriais a seguir demonstram como criar um aplicativo Web .NET Core 2.2 MVC. O aplicativo usa o OpenID Connect para fazer login em usuários com uma conta de trabalho e escola em uma organização que pertence a uma nuvem nacional.

- Para entrar nos usuários e adquirir tokens, siga este tutorial: [Crie um ASP.NET usuários de login de aplicativos da Web em nuvens soberanas com a plataforma de identidade da Microsoft](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Para chamar a API do Microsoft Graph, siga este tutorial: [Usando a plataforma de identidade microsoft para chamar a API do Microsoft Graph a partir de um aplicativo web ASP.NET Core 2.x, em nome de um login de usuário usando sua conta de trabalho e escola no Microsoft National Cloud](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="javascript"></a>[JavaScript](#tab/javascript)

Para habilitar seu aplicativo MSAL.js para nuvens soberanas:

### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo

1. Entre no [portal do Azure](https://portal.azure.us/).

   Para encontrar os pontos finais do portal Azure para outras nuvens nacionais, consulte [pontos finais de registro do aplicativo](authentication-national-cloud.md#app-registration-endpoints).

1. Se sua conta lhe der acesso a mais de um inquilino, selecione sua conta no canto superior direito e defina sua sessão de portal para o inquilino Azure AD desejado.
1. Acesse a página [de registros](https://aka.ms/ra/ff) do App na plataforma de identidade da Microsoft para desenvolvedores.
1. Quando a página **Registrar um aplicativo** aparecer, insira um nome para o seu aplicativo.
1. Em **tipos de conta suportados,** selecione Contas em qualquer diretório **organizacional**.
1. Na seção **Redirecionar URI,** selecione a plataforma **Web** e defina o valor para a URL do aplicativo com base no servidor web. Consulte as próximas seções para obter instruções sobre como definir e obter a URL de redirecionamento no Visual Studio e noNo.
1. Selecione **Registrar**.
1. Na página **Visão geral** do aplicativo, anote o valor de **ID do aplicativo (cliente)**.
1. Este tutorial exige que você habilite o [fluxo implícito de subvenção](v2-oauth2-implicit-grant-flow.md). No painel esquerdo do aplicativo registrado, selecione **Autenticação**.
1. Em **Configurações avançadas**, em **Concessão implícita**, marque as caixas de seleção **Tokens de ID** e **Tokens de Acesso**. Tokens de ID e tokens de acesso são necessários porque este aplicativo precisa fazer login nos usuários e chamar uma API.
1. Clique em **Salvar**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Passo 2: Configure seu servidor web ou projeto

- [Baixe os arquivos do projeto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) para um servidor web local, como o Node.

  ou

- [Baixe o projeto Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Em seguida, pule para [Configurar seu SPA JavaScript](#step-4-configure-your-javascript-spa) para configurar a amostra de código antes de executá-la.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Passo 3: Use a Biblioteca de Autenticação da Microsoft para fazer login no usuário

Siga as etapas do [tutorial JavaScript](tutorial-v2-javascript-spa.md#create-your-project) para criar seu projeto e integre-se ao MSAL para fazer login no usuário.

### <a name="step-4-configure-your-javascript-spa"></a>Passo 4: Configure seu SPA JavaScript

No arquivo `index.html` criado durante a configuração do projeto, inclua as informações de registro do aplicativo. Adicione o seguinte código na parte superior dentro das marcas `<script></script>` no corpo do arquivo `index.html`:

```javascript
const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        }
}

const graphConfig = {
        graphEndpoint: "https://graph.microsoft.us",
        graphScopes: ["user.read"],
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

Nesse código:

- `Enter_the_Application_Id_here`é o valor **de ID do aplicativo (cliente)** para o aplicativo que você registrou.
- `Enter_the_Tenant_Info_Here`é definido como uma das seguintes opções:
    - Se o seu aplicativo suportar **contas neste diretório organizacional,** substitua esse valor pelo ID do inquilino ou nome do inquilino (por exemplo, contoso.microsoft.com).
    - Se o aplicativo tiver suporte **a Contas em qualquer diretório organizacional,** substitua esse valor por `organizations`.

    Para encontrar pontos finais de autenticação para todas as nuvens nacionais, consulte [pontos finais de autenticação Do Zure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).

    > [!NOTE]
    > Contas pessoais da Microsoft não são suportadas em nuvens nacionais.

- `graphEndpoint`é o ponto final do Microsoft Graph para a nuvem da Microsoft para o governo dos EUA.

   Para encontrar os pontos finais do Microsoft Graph para todas as nuvens nacionais, consulte [os pontos finais do Microsoft Graph em nuvens nacionais](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="python"></a>[Python](#tab/python)

Para habilitar seu aplicativo MSAL Python para nuvens soberanas:

- Registre sua inscrição em um portal específico, dependendo da nuvem. Para obter mais informações sobre como escolher o portal, consulte [pontos finais de inscrição do aplicativo](authentication-national-cloud.md#app-registration-endpoints)
- Use qualquer uma das [amostras](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) do repo com algumas alterações na configuração, dependendo da nuvem, que é mencionada em seguida.
- Use uma autoridade específica, dependendo da nuvem em que você registrou o aplicativo. Para obter mais informações sobre autoridades para diferentes nuvens, consulte [os pontos finais da Autenticação Azure AD](authentication-national-cloud.md#azure-ad-authentication-endpoints).

    Aqui está um exemplo de autoridade:

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```

- Para chamar o gráfico da Microsoft, é necessário uma URL de ponto final do Gráfico específica que depende de qual nuvem você está usando. Para encontrar os pontos finais do Microsoft Graph para todas as nuvens nacionais, consulte [os pontos finais do serviço Microsoft Graph e graph Explorer](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

    Aqui está um exemplo de um ponto final de gráfico, com escopo:

    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```

## <a name="java"></a>[Java](#tab/java)

Para habilitar seu aplicativo MSAL for Java para nuvens soberanas:

- Registre sua inscrição em um portal específico, dependendo da nuvem. Para obter mais informações sobre como escolher o portal, consulte [pontos finais de inscrição do aplicativo](authentication-national-cloud.md#app-registration-endpoints)
- Use qualquer uma das [amostras](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) do repo com algumas alterações na configuração, dependendo da nuvem, que são mencionadas a seguir.
- Use uma autoridade específica, dependendo da nuvem em que você registrou o aplicativo. Para obter mais informações sobre autoridades para diferentes nuvens, consulte [os pontos finais da Autenticação Azure AD](authentication-national-cloud.md#azure-ad-authentication-endpoints).

Aqui está um exemplo de autoridade:

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- Para chamar o gráfico da Microsoft, é necessário uma URL de ponto final do Gráfico específica que depende de qual nuvem você está usando. Para encontrar os pontos finais do Microsoft Graph para todas as nuvens nacionais, consulte [os pontos finais do serviço Microsoft Graph e graph Explorer](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

Aqui está um exemplo de um ponto final de gráfico, com escopo:

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-c"></a>[Objective-C](#tab/objc)

MSAL para iOS e macOS pode ser usado para adquirir tokens `MSALPublicClientApplication`em nuvens nacionais, mas requer configuração adicional ao criar .

Por exemplo, se você quiser que seu aplicativo seja um aplicativo de vários inquilinos em uma nuvem nacional (aqui governo dos EUA), você pode escrever:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

## <a name="swift"></a>[Swift](#tab/swift)

MSAL para iOS e macOS pode ser usado para adquirir tokens `MSALPublicClientApplication`em nuvens nacionais, mas requer configuração adicional ao criar .

Por exemplo, se você quiser que seu aplicativo seja um aplicativo de vários inquilinos em uma nuvem nacional (aqui governo dos EUA), você pode escrever:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre:

- [Autenticação em Nuvens Nacionais](authentication-national-cloud.md)
- [Azure Governamental](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Alemanha](https://docs.microsoft.com/azure/germany/)
