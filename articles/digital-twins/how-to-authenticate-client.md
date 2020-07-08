---
title: Autenticar um aplicativo cliente
titleSuffix: Azure Digital Twins
description: Consulte como autenticar um aplicativo cliente no serviço de gêmeos digital do Azure.
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e52307c92d9371af6479f64841c6f269ed10e4b4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85390815"
---
# <a name="authenticate-a-client-application-with-azure-digital-twins"></a>Autenticar um aplicativo cliente com o Azure digital gêmeos

Depois de [criar uma instância do gêmeos digital do Azure](how-to-set-up-instance.md), você pode criar um aplicativo cliente que será usado para interagir com a instância do. Depois de configurar um projeto de cliente inicial, este artigo mostra como autenticar corretamente esse aplicativo cliente com a instância de gêmeos digital do Azure.

Isso é feito em duas etapas:
1. Criar o Registro do aplicativo
2. Gravar código de autenticação em um aplicativo cliente

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-an-app-registration"></a>Criar o Registro do aplicativo

Para autenticar no Azure digital gêmeos de um aplicativo cliente, você precisa configurar um **registro de aplicativo** no [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md).

Esse registro de aplicativo é onde você configura permissões de acesso para as [APIs do Azure digital gêmeos](how-to-use-apis-sdks.md). Seu aplicativo cliente é autenticado no registro do aplicativo e, como resultado, recebe as permissões de acesso configuradas para as APIs.

Para criar um registro de aplicativo, você precisa fornecer as IDs de recurso para as APIs do gêmeos digital do Azure e as permissões de linha de base para a API. No diretório de trabalho, abra um novo arquivo e insira o seguinte trecho de JSON para configurar esses detalhes: 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

Salve este arquivo como *manifest.jsem*.

> [!NOTE] 
> Há alguns lugares em que uma cadeia de caracteres "amigável", legível `https://digitaltwins.azure.net` por humanos, pode ser usada para a ID do aplicativo de recurso gêmeos digital do Azure em vez do GUID `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Por exemplo, muitos exemplos em todo esse conjunto de documentação usam a autenticação com a biblioteca MSAL, e a cadeia de caracteres amigável pode ser usada para isso. No entanto, durante essa etapa da criação do registro do aplicativo, a forma de GUID da ID é necessária, como mostrado acima. 

Na janela Cloud Shell, clique no ícone "carregar/baixar arquivos" e escolha "carregar".

:::image type="content" source="media/how-to-authenticate-client/upload-extension.png" alt-text="Janela Cloud Shell mostrando a seleção da opção carregar":::
Navegue até o *manifest.jsem* que você acabou de criar e pressione "abrir".

Em seguida, execute o seguinte comando para criar um registro de aplicativo (substituindo espaços reservados, conforme necessário):

```azurecli
az ad app create --display-name <name-for-your-app> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

A saída desse comando é semelhante a esta.

:::image type="content" source="media/how-to-authenticate-client/new-app-registration.png" alt-text="Novo registro de aplicativo do AAD":::

Depois de criar o registro do aplicativo, siga [este link](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) para navegar até a página de visão geral do registro de aplicativo do AAD na portal do Azure.

Nessa visão geral, selecione o registro do aplicativo que você acabou de criar na lista. Isso abrirá seus detalhes em uma página como esta:

:::image type="content" source="media/how-to-authenticate-client/get-authentication-ids.png" alt-text="Portal do Azure: IDs de autenticação":::

Anote a ID do *aplicativo (cliente)* e a *ID do diretório (locatário)* mostradas **na página.** Você usará esses valores posteriormente para autenticar um aplicativo cliente em relação às APIs do gêmeos digital do Azure.

> [!NOTE]
> Dependendo do seu cenário, talvez seja necessário fazer alterações adicionais no registro do aplicativo. Aqui estão alguns requisitos comuns que talvez você precise atender:
> * Ativar acesso de cliente público
> * Definir URLs de resposta específicas para acesso à Web e à área de trabalho
> * Permitir fluxos de autenticação OAuth2 implícitos
> * Se sua assinatura do Azure for criada usando um conta Microsoft como Live, Xbox ou hotmail, você precisará definir o *signInAudience* no registro do aplicativo para dar suporte a contas pessoais.
> A maneira mais fácil de definir essas configurações é usar o [portal do Azure](https://portal.azure.com/). Para obter mais informações sobre esse processo, consulte [registrar um aplicativo com a plataforma de identidade da Microsoft](https://docs.microsoft.com/graph/auth-register-app-v2).

## <a name="write-client-app-authentication-code-net-c-sdk"></a>Gravar o código de autenticação do aplicativo cliente: SDK do .NET (C#)

Esta seção descreve o código que você precisará incluir no aplicativo cliente para concluir o processo de autenticação usando o SDK do .NET (C#).
O SDK do Azure digital gêmeos C# faz parte do SDK do Azure para .NET. Ele está localizado aqui: [biblioteca de cliente do Azure IOT digital para .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core).

### <a name="prerequisites"></a>Pré-requisitos

Se você não tiver um projeto de aplicativo cliente inicial já configurado, crie um projeto .NET básico para usar com este tutorial.

Para usar o SDK do .NET, você precisará incluir os seguintes pacotes em seu projeto:
* `Azure.DigitalTwins.Core`(versão `1.0.0-preview.2` )
* `Azure.Identity`

Dependendo das ferramentas de sua escolha, você pode fazer isso com o Gerenciador de pacotes do Visual Studio ou a `dotnet` ferramenta de linha de comando. 

### <a name="authentication-and-client-creation-net"></a>Autenticação e criação de cliente: .NET

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

Consulte [como: configurar uma função do Azure para processar dados](how-to-create-azure-function.md) para obter um exemplo mais completo que explica algumas das opções de configuração importantes no contexto de funções.

Além disso, para usar a autenticação em uma função, lembre-se de:
* [Habilitar identidade gerenciada](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* [Variáveis de ambiente](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp)
* Atribua permissões ao aplicativo de funções que permite que ele acesse as APIs de gêmeos digital. Consulte [como: configurar uma função do Azure para processar dados](how-to-create-azure-function.md) para obter mais informações.

## <a name="authentication-in-an-autorest-generated-sdk"></a>Autenticação em um SDK gerado pelo REST

Se você não estiver usando o .NET, poderá optar por criar uma biblioteca do SDK em uma linguagem de sua escolha, conforme descrito em [como: criar SDKs personalizados para o Azure digital gêmeos com o REST](how-to-create-custom-sdks.md).

Esta seção explica como autenticar nesse caso.

### <a name="prerequisites"></a>Pré-requisitos

Este exemplo usa um SDK do typescript gerado com o REST. Como resultado, ele também requer:
* [MSAL-js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [MS-REST-js](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>Exemplo de código mínimo de autenticação

Para autenticar um aplicativo .NET com os serviços do Azure, você pode usar o seguinte código mínimo dentro de seu aplicativo cliente.

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

O MSAL tem muito mais opções que você pode usar para implementar coisas como cache e outros fluxos de autenticação. Para obter mais informações sobre isso, consulte [visão geral da biblioteca de autenticação da Microsoft (MSAL)](../active-directory/develop/msal-overview.md).

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre como funciona a segurança no Azure digital gêmeos:
* [Conceitos: segurança para soluções de gêmeos digitais do Azure](concepts-security.md)

Ou, agora que a autenticação está configurada, vá para a criação de modelos na sua instância:
* [Como: gerenciar modelos personalizados](how-to-manage-model.md)