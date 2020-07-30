---
title: Escrever código de autenticação do aplicativo
titleSuffix: Azure Digital Twins
description: Consulte como escrever código de autenticação em um aplicativo cliente
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-javascript
ms.openlocfilehash: 1eebacefa2e91970263c301de4ff160ea7c9f2eb
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420338"
---
# <a name="write-client-app-authentication-code"></a>Gravar o código de autenticação do aplicativo cliente

Depois de [Configurar uma instância e autenticação do gêmeos digital do Azure](how-to-set-up-instance-scripted.md), você pode criar um aplicativo cliente que será usado para interagir com a instância do. Depois de configurar um projeto de cliente inicial, este artigo mostra **como escrever código nesse aplicativo cliente para autenticá-lo na** instância do gêmeos digital do Azure.

Há duas abordagens para o código de exemplo neste artigo. Você pode usar o ideal para você, dependendo do idioma de sua escolha:
* A primeira seção do código de exemplo usa o SDK do .NET do Azure digital gêmeos (C#). O SDK é parte do SDK do Azure para .NET e está localizado aqui: [*biblioteca de cliente do Azure IOT digital para .net*](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core).
* A segunda seção de código de exemplo é para os usuários que não usam o SDK do .NET e, em vez disso, usam SDKs gerados pelo REST em outras linguagens. Para obter mais informações sobre essa estratégia, consulte [*como criar SDKs personalizados para o Azure digital gêmeos com o REST*](how-to-create-custom-sdks.md).

Você também pode ler mais sobre as APIs e os SDKs do Azure digital gêmeos em [*como: usar as APIs e os SDKs do gêmeos digital do Azure*](how-to-use-apis-sdks.md).

## <a name="prerequisites"></a>Pré-requisitos

Primeiro, conclua as etapas de configuração em [*como: configurar uma instância e uma autenticação*](how-to-set-up-instance-scripted.md). Isso garantirá que você tenha uma instância de gêmeos digital do Azure, seu usuário tem permissões de acesso e você configurou permissões para aplicativos cliente. Após toda essa configuração, você estará pronto para gravar o código do aplicativo cliente.

Para continuar, você precisará de um projeto de aplicativo cliente no qual você escreva seu código. Se você ainda não tiver um projeto de aplicativo cliente configurado, crie um projeto básico em seu idioma de escolha para usar com este tutorial.

## <a name="authentication-and-client-creation-net-c-sdk"></a>Autenticação e criação de cliente: SDK do .NET (C#)

Primeiro, inclua os seguintes pacotes em seu projeto para usar o SDK do .NET e as ferramentas de autenticação para este "como":
* `Azure.DigitalTwins.Core`(versão `1.0.0-preview.2` )
* `Azure.Identity`

Dependendo das ferramentas de sua escolha, você pode incluir os pacotes usando o Gerenciador de pacotes do Visual Studio ou a `dotnet` ferramenta de linha de comando. 

Para autenticar com o SDK do .NET, use um dos métodos de credencial-obtenção definidos na biblioteca [do Azure. Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) .

Aqui estão dois que são comumente usados: 
* [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet). Esse método destina-se a aplicativos interativos e abrirá um navegador da Web para autenticação.
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet). Esse método funciona muito bem em casos em que você precisa de [identidades gerenciadas (MSI)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)— por exemplo, ao trabalhar com Azure functions. 

Você também precisará das seguintes instruções using:

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```

Para usar as credenciais do navegador interativo para criar um cliente SDK autenticado, adicione este código:

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new InteractiveBrowserCredential(tenantId, clientId);
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

>[!NOTE]
> Embora você possa colocar a ID do cliente, a ID do locatário e a URL da instância diretamente no código, como mostrado acima, é uma boa ideia fazer com que seu código obtenha esses valores de um arquivo de configuração ou variável de ambiente em vez disso.

Em uma função do Azure, você pode usar as credenciais de identidade gerenciadas da seguinte maneira:

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

Consulte [*como: configurar uma função do Azure para processar dados*](how-to-create-azure-function.md) para obter um exemplo mais completo que explica algumas das opções de configuração importantes no contexto de funções.

Além disso, para usar a autenticação em uma função, lembre-se de:
* [Habilitar a identidade gerenciada](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* Usar [variáveis de ambiente](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp) conforme apropriado
* Atribua permissões ao aplicativo de funções que permite que ele acesse as APIs de gêmeos digital. Para obter mais informações sobre processos de Azure Functions, consulte [*como: configurar uma função do Azure para processar dados*](how-to-create-azure-function.md).

## <a name="authentication-with-an-autorest-generated-sdk"></a>Autenticação com um SDK gerado pelo REST automaticamente

Se você não estiver usando o .NET, poderá optar por criar uma biblioteca do SDK em uma linguagem de sua escolha, conforme descrito em [*como: criar SDKs personalizados para o Azure digital gêmeos com o REST*](how-to-create-custom-sdks.md).

Esta seção explica como autenticar nesse caso.

### <a name="prerequisites"></a>Pré-requisitos

Primeiro, você deve concluir as etapas para criar um SDK personalizado com o recurso de REST, usando as etapas em [*como: criar SDKs personalizados para o Azure digital gêmeos com*](how-to-create-custom-sdks.md)o recurso de autorest.

Este exemplo usa um SDK do typescript gerado com o REST. Como resultado, ele também requer:
* [MSAL-js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [MS-REST-js](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>Exemplo de código mínimo de autenticação

Para autenticar um aplicativo com os serviços do Azure, você pode usar o seguinte código mínimo dentro de seu aplicativo cliente.

Você precisará da *ID do aplicativo (cliente)* e da *ID do diretório (locatário)* do anterior, bem como da URL da instância do gêmeos digital do Azure.

> [!TIP]
> A URL da instância do gêmeos digital do Azure é feita adicionando *https://* ao início do *nome de host*da instância do Azure digital gêmeos. Para ver o *nome do host*, juntamente com todas as propriedades de sua instância, você pode executar `az dt show --dt-name <your-Azure-Digital-Twins-instance>` . Você pode usar o `az account show --query tenantId` comando para ver sua *ID de diretório (locatário)*. 

```javascript
import * as Msal from "msal";
import { TokenCredentials } from "@azure/ms-rest-js";
// Autorest-generated SDK
import { AzureDigitalTwinsAPI } from './azureDigitalTwinsAPI';

// Client / app registration ID
var ClientId = "<your-client-ID>";
// Azure tenant / directory ID
var TenantId = "<your-tenant-ID>";
// URL of the Azure Digital Twins instance
var AdtInstanceUrl = "<your-instance-URL>"; 

var AdtAppId = "https://digitaltwins.azure.net";

let client = null;

export async function login() {

    const msalConfig = {
        auth: {
            clientId: ClientId,
            redirectUri: "http://localhost:3000",
            authority: "https://login.microsoftonline.com/"+TenantId
        }
    };

    const msalInstance = new Msal.UserAgentApplication(msalConfig);

    msalInstance.handleRedirectCallback((error, response) => {
        // handle redirect response or error
    });

    var loginRequest = {
        scopes: [AdtAppId + "/.default"] 
    };

    try {
        await msalInstance.loginPopup(loginRequest)
        var accessToken;
        // if the user is already logged in you can acquire a token
        if (msalInstance.getAccount()) {
            var tokenRequest = {
                scopes: [AdtAppId + "/.default"]
            };
            try {
                const response = await msalInstance.acquireTokenSilent(tokenRequest);
                accessToken = response.accessToken;
            } catch (err) {
                if (err.name === "InteractionRequiredAuthError") {
                    const response = await msalInstance.acquireTokenPopup(tokenRequest)
                    accessToken = response.accessToken;
                }
            }
        }
        if (accessToken!=null)
        {
            var tokenCredentials = new TokenCredentials(accessToken);
                
            // Add token and server URL to service instance
            const clientConfig = {
                baseUri: AdtInstanceUrl
            };
            client = new AzureDigitalTwinsAPI(tokenCredentials, clientConfig);
            appDataStore.client = client;
        }
    } catch (err) {
        ...
    }
}
```

Observe novamente que, em que o código acima coloca a ID do cliente, a ID do locatário e a URL da instância diretamente no código para simplificar, é uma boa ideia fazer com que seu código obtenha esses valores de um arquivo de configuração ou uma variável de ambiente em vez disso.

O MSAL tem muito mais opções que você pode usar para implementar coisas como cache e outros fluxos de autenticação. Para obter mais informações sobre isso, consulte [*visão geral da biblioteca de autenticação da Microsoft (MSAL)*](../active-directory/develop/msal-overview.md).

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre como funciona a segurança no Azure digital gêmeos:
* [*Conceitos: segurança para soluções de gêmeos digitais do Azure*](concepts-security.md)

Ou, agora que a autenticação está configurada, vá para a criação de modelos na sua instância:
* [*Como gerenciar modelos personalizados*](how-to-manage-model.md)