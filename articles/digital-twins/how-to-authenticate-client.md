---
title: Escrever código de autenticação do aplicativo
titleSuffix: Azure Digital Twins
description: Consulte como escrever código de autenticação em um aplicativo cliente
author: baanders
ms.author: baanders
ms.date: 10/7/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7f5ca063bcc784498dddf87f34f0f7974b95ecaf
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027305"
---
# <a name="write-client-app-authentication-code"></a>Gravar o código de autenticação do aplicativo cliente

Depois de [Configurar uma instância e autenticação do gêmeos digital do Azure](how-to-set-up-instance-portal.md), você pode criar um aplicativo cliente que será usado para interagir com a instância do. Depois de configurar um projeto de cliente inicial, você precisará **escrever código nesse aplicativo cliente para autenticá-lo** na instância do gêmeos digital do Azure.

O Azure digital gêmeos executa a autenticação usando [tokens de segurança do Azure AD com base no OAUTH 2,0](../active-directory/develop/security-tokens.md#json-web-tokens-jwts-and-claims). Para autenticar seu SDK, você precisará obter um token de portador com as permissões corretas para o Azure digital gêmeos e passá-lo junto com suas chamadas à API. 

Este artigo descreve como obter credenciais usando a `Azure.Identity` biblioteca de cliente. Embora este artigo mostre exemplos de código em C#, como o que você escreveria para o [SDK do .net (c#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet-preview&preserve-view=true), você pode usar uma versão do, `Azure.Identity` independentemente de qual SDK está usando (para obter mais informações sobre os SDKs disponíveis para o gêmeos digital do Azure, consulte [*como usar as APIs e SDKs do gêmeos digital do Azure*](how-to-use-apis-sdks.md)).

## <a name="prerequisites"></a>Pré-requisitos

Primeiro, conclua as etapas de configuração em [*como: configurar uma instância e uma autenticação*](how-to-set-up-instance-portal.md). Isso garantirá que você tenha uma instância de gêmeos digital do Azure e que seu usuário tenha permissões de acesso. Após essa configuração, você estará pronto para gravar o código do aplicativo cliente.

Para continuar, você precisará de um projeto de aplicativo cliente no qual você escreva seu código. Se você ainda não tiver um projeto de aplicativo cliente configurado, crie um projeto básico em seu idioma de escolha para usar com este tutorial.

## <a name="common-authentication-methods-with-azureidentity"></a>Métodos de autenticação comuns com o Azure. Identity

`Azure.Identity` é uma biblioteca de cliente que fornece vários métodos de obtenção de credencial que você pode usar para obter um token de portador e autenticar com seu SDK. Embora este artigo dê exemplos em C#, você pode exibir `Azure.Identity` para vários idiomas, incluindo...
* [.NET (C#)](/dotnet/api/azure.identity?preserve-view=true&view=azure-dotnet)
* [Java](/java/api/overview/azure/identity-readme?preserve-view=true&view=azure-java-stable)
* [JavaScript](/javascript/api/overview/azure/identity-readme?preserve-view=true&view=azure-node-latest)
* [Python](/python/api/overview/azure/identity-readme?preserve-view=true&view=azure-python)

Três métodos comuns de obtenção de credenciais no `Azure.Identity` são:
* O [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) fornece um `TokenCredential` fluxo de autenticação padrão para aplicativos que serão implantados no Azure e é **a opção recomendada para o desenvolvimento local** . Ele também pode ser habilitado para experimentar os outros dois métodos recomendados neste artigo; Ele encapsula `ManagedIdentityCredential` e pode acessar `InteractiveBrowserCredential` com uma variável de configuração.
* [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential?preserve-view=true&view=azure-dotnet) funciona muito bem em casos em que você precisa de [identidades gerenciadas (MSI)](../active-directory/managed-identities-azure-resources/overview.md)e é um bom candidato para trabalhar com Azure Functions e implantar nos serviços do Azure.
* O [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet) é destinado a aplicativos interativos e pode ser usado para criar um cliente SDK autenticado

O exemplo a seguir mostra como usar cada um deles com o SDK do .NET (C#).

## <a name="authentication-examples-net-c-sdk"></a>Exemplos de autenticação: SDK do .NET (C#)

Esta seção mostra um exemplo em C# para usar o SDK do .NET fornecido para escrever o código de autenticação.

Primeiro, inclua o pacote do SDK `Azure.DigitalTwins.Core` e o `Azure.Identity` pacote em seu projeto. Dependendo das ferramentas de sua escolha, você pode incluir os pacotes usando o Gerenciador de pacotes do Visual Studio ou a `dotnet` ferramenta de linha de comando. 

Você também precisará adicionar as seguintes instruções using ao código do projeto:

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```

Em seguida, adicione o código para obter credenciais usando um dos métodos no `Azure.Identity` .

### <a name="defaultazurecredential-method"></a>Método DefaultAzureCredential

O [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) fornece um `TokenCredential` fluxo de autenticação padrão para aplicativos que serão implantados no Azure e é **a opção recomendada para o desenvolvimento local** .

Para usar as credenciais padrão do Azure, você precisará da URL da instância do gêmeos digital do Azure ([instruções a serem encontradas](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

Aqui está um exemplo de código para adicionar um `DefaultAzureCredential` ao seu projeto:

```csharp
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new DefaultAzureCredential();
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

#### <a name="set-up-local-azure-credentials"></a>Configurar credenciais locais do Azure

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](../../includes/digital-twins-local-credentials-inner.md)]

### <a name="managedidentitycredential-method"></a>Método ManagedIdentityCredential

O método [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential?preserve-view=true&view=azure-dotnet) funciona muito bem em casos em que você precisa de [identidades gerenciadas (MSI)](../active-directory/managed-identities-azure-resources/overview.md)— por exemplo, ao trabalhar com Azure functions.

Isso significa que você pode usar `ManagedIdentityCredential` o no mesmo projeto como `DefaultAzureCredential` ou `InteractiveBrowserCredential` , para autenticar uma parte diferente do projeto.

Para usar as credenciais padrão do Azure, você precisará da URL da instância do gêmeos digital do Azure ([instruções a serem encontradas](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

Em uma função do Azure, você pode usar as credenciais de identidade gerenciadas da seguinte maneira:

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

### <a name="interactivebrowsercredential-method"></a>Método InteractiveBrowserCredential

O método [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet) é destinado a aplicativos interativos e abrirá um navegador da Web para autenticação. Você pode usar isso em vez de `DefaultAzureCredential` em casos em que você precisa de autenticação interativa.

Para usar as credenciais interativas do navegador, você precisará de um **registro de aplicativo** que tenha permissões para as APIs do gêmeos digital do Azure. Para obter as etapas sobre como configurar esse registro de aplicativo, consulte [*como criar um registro de aplicativo*](how-to-create-app-registration.md). Depois que o registro do aplicativo for configurado, você precisará...
* a *ID do aplicativo (cliente)* do registro do aplicativo ( [instruções a serem encontradas](how-to-create-app-registration.md#collect-client-id-and-tenant-id))
* a *ID do diretório (locatário)* do registro do aplicativo ( [instruções a serem encontradas](how-to-create-app-registration.md#collect-client-id-and-tenant-id))
* a URL da instância do gêmeos digital do Azure ([instruções a serem encontradas](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))

Aqui está um exemplo do código para criar um cliente SDK autenticado usando o `InteractiveBrowserCredential` .

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-URL>";

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

#### <a name="other-notes-about-authenticating-azure-functions"></a>Outras observações sobre autenticação Azure Functions

Consulte [*como: configurar uma função do Azure para processar dados*](how-to-create-azure-function.md) para obter um exemplo mais completo que explica algumas das opções de configuração importantes no contexto de funções.

Além disso, para usar a autenticação em uma função, lembre-se de:
* [Habilitar a identidade gerenciada](../app-service/overview-managed-identity.md?tabs=dotnet)
* Usar [variáveis de ambiente](/sandbox/functions-recipes/environment-variables?tabs=csharp) conforme apropriado
* Atribua permissões ao aplicativo de funções que permite que ele acesse as APIs de gêmeos digital. Para obter mais informações sobre processos de Azure Functions, consulte [*como: configurar uma função do Azure para processar dados*](how-to-create-azure-function.md).

## <a name="other-credential-methods"></a>Outros métodos de credencial

Se os cenários de autenticação destacados acima não atenderem às necessidades do seu aplicativo, você poderá explorar outros tipos de autenticação oferecidos na [**plataforma de identidade da Microsoft**](../active-directory/develop/v2-overview.md#getting-started). A documentação desta plataforma abrange cenários de autenticação adicionais, organizados por tipo de aplicativo.

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre como funciona a segurança no Azure digital gêmeos:
* [*Conceitos: segurança para soluções de gêmeos digitais do Azure*](concepts-security.md)

Ou, agora que a autenticação está configurada, vá para a criação de modelos na sua instância:
* [*Como gerenciar modelos personalizados*](how-to-manage-model.md)