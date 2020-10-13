---
title: Escrever código de autenticação do aplicativo
titleSuffix: Azure Digital Twins
description: Consulte como escrever código de autenticação em um aplicativo cliente
author: baanders
ms.author: baanders
ms.date: 10/7/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f2cef34413f46608e8bc35a009a29212af5ddf20
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893587"
---
# <a name="write-client-app-authentication-code"></a>Gravar o código de autenticação do aplicativo cliente

Depois de [Configurar uma instância e autenticação do gêmeos digital do Azure](how-to-set-up-instance-portal.md), você pode criar um aplicativo cliente que será usado para interagir com a instância do. Depois de configurar um projeto de cliente inicial, você precisará **escrever código nesse aplicativo cliente para autenticá-lo** na instância do gêmeos digital do Azure.

O Azure digital gêmeos executa a autenticação usando [tokens de segurança do Azure AD com base no OAUTH 2,0](../active-directory/develop/security-tokens.md#json-web-tokens-jwts-and-claims). Para autenticar seu SDK, você precisará obter um token de portador com as permissões corretas para o Azure digital gêmeos e passá-lo junto com suas chamadas à API. 

Este artigo descreve como obter credenciais usando a `Azure.Identity` biblioteca de cliente. Embora este artigo mostre exemplos de código em C#, como o que você escreveria para o [SDK do .net (c#)](https://www.nuget.org/packages/Azure.DigitalTwins.Core), você pode usar uma versão do, `Azure.Identity` independentemente de qual SDK está usando (para obter mais informações sobre os SDKs disponíveis para o gêmeos digital do Azure, consulte [*como usar as APIs e SDKs do gêmeos digital do Azure*](how-to-use-apis-sdks.md)).

## <a name="prerequisites"></a>Pré-requisitos

Primeiro, conclua as etapas de configuração em [*como: configurar uma instância e uma autenticação*](how-to-set-up-instance-portal.md). Isso garantirá que você tenha uma instância de gêmeos digital do Azure, seu usuário tem permissões de acesso e você configurou permissões para aplicativos cliente. Após toda essa configuração, você estará pronto para gravar o código do aplicativo cliente.

Para continuar, você precisará de um projeto de aplicativo cliente no qual você escreva seu código. Se você ainda não tiver um projeto de aplicativo cliente configurado, crie um projeto básico em seu idioma de escolha para usar com este tutorial.

## <a name="common-authentication-methods-with-azureidentity"></a>Métodos de autenticação comuns com o Azure. Identity

`Azure.Identity` é uma biblioteca de cliente que fornece vários métodos de obtenção de credencial que você pode usar para obter um token de portador e autenticar com seu SDK. Embora este artigo dê exemplos em C#, você pode exibir `Azure.Identity` para vários idiomas, incluindo...
* [.NET (C#)](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)
* [Java](https://docs.microsoft.com/java/api/overview/azure/identity-readme?view=azure-java-stable&preserve-view=true)
* [JavaScript](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme?view=azure-node-latest&preserve-view=true)
* [Python](https://docs.microsoft.com/python/api/overview/azure/identity-readme?view=azure-python&preserve-view=true)

Três métodos comuns de obtenção de credenciais no `Azure.Identity` são:
* O [DefaultAzureCredential](https://docs.microsoft.com/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) fornece um `TokenCredential` fluxo de autenticação padrão para aplicativos que serão implantados no Azure e é **a opção recomendada para o desenvolvimento local**. Ele também pode ser habilitado para experimentar os outros dois métodos recomendados neste artigo; Ele encapsula `ManagedIdentityCredential` e pode acessar `InteractiveBrowserCredential` com uma variável de configuração.
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) funciona muito bem em casos em que você precisa de [identidades gerenciadas (MSI)](../active-directory/managed-identities-azure-resources/overview.md)e é um bom candidato para trabalhar com Azure Functions e implantar nos serviços do Azure.
* O [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) é destinado a aplicativos interativos e pode ser usado para criar um cliente SDK autenticado

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

O [DefaultAzureCredential](https://docs.microsoft.com/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) fornece um `TokenCredential` fluxo de autenticação padrão para aplicativos que serão implantados no Azure e é **a opção recomendada para o desenvolvimento local**.

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

### <a name="managedidentitycredential-method"></a>Método ManagedIdentityCredential

O método [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) funciona muito bem em casos em que você precisa de [identidades gerenciadas (MSI)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)— por exemplo, ao trabalhar com Azure functions.

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

O método [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) é destinado a aplicativos interativos e abrirá um navegador da Web para autenticação. Você pode usar isso em vez de `DefaultAzureCredential` em casos em que você precisa de autenticação interativa.

Para usar as credenciais interativas do navegador, você precisará de um **registro de aplicativo** que tenha permissões para as APIs do gêmeos digital do Azure. Para obter as etapas sobre como configurar esse registro de aplicativo, consulte a seção [*configurar permissões de acesso para aplicativos cliente*](how-to-set-up-instance-portal.md#set-up-access-permissions-for-client-applications) de *como: configurar uma instância e autenticação*. Depois que o registro do aplicativo for configurado, você precisará...
* a *ID do aplicativo (cliente)* do registro do aplicativo
* a *ID do diretório (locatário)* do registro do aplicativo
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
* [Habilitar a identidade gerenciada](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* Usar [variáveis de ambiente](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp) conforme apropriado
* Atribua permissões ao aplicativo de funções que permite que ele acesse as APIs de gêmeos digital. Para obter mais informações sobre processos de Azure Functions, consulte [*como: configurar uma função do Azure para processar dados*](how-to-create-azure-function.md).

## <a name="other-credential-methods"></a>Outros métodos de credencial

Se os cenários de autenticação destacados acima não atenderem às necessidades do seu aplicativo, você poderá explorar outros tipos de autenticação oferecidos na [**plataforma de identidade da Microsoft**](../active-directory/develop/v2-overview.md#getting-started). A documentação desta plataforma abrange cenários de autenticação adicionais, organizados por tipo de aplicativo.

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre como funciona a segurança no Azure digital gêmeos:
* [*Conceitos: segurança para soluções de gêmeos digitais do Azure*](concepts-security.md)

Ou, agora que a autenticação está configurada, vá para a criação de modelos na sua instância:
* [*Como gerenciar modelos personalizados*](how-to-manage-model.md)
