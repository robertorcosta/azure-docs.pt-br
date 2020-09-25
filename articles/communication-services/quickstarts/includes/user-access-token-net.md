---
title: incluir arquivo
description: incluir arquivo
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 12a8bccbf9c177c92160d52f4506618d03c791e6
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943235"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- A versão mais recente da [biblioteca de clientes do .NET Core](https://dotnet.microsoft.com/download/dotnet-core) para o seu sistema operacional.
- Um recurso e uma cadeia de conexão ativos dos Serviços de Comunicação. [Crie um recurso dos Serviços de Comunicação](../create-communication-resource.md).

## <a name="setting-up"></a>Ao configurar o

### <a name="create-a-new-c-application"></a>Criar um aplicativo em C#

Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `dotnet new` para criar um novo aplicativo do console com o nome `UserAccessTokensQuickstart`. Esse comando cria um projeto simples C# "Olá, Mundo" com um arquivo de origem único: **Program.cs**.

```console
dotnet new console -o UserAccessTokensQuickstart
```

Altere o seu diretório para a pasta de aplicativo recém-criada e use o comando `dotnet build` para compilar o seu aplicativo.

```console
cd UserAccessTokensQuickstart
dotnet build
```

### <a name="install-the-package"></a>Instalar o pacote

Ainda no diretório do aplicativo, instale o pacote da biblioteca da Administração dos Serviços de Comunicação do Azure para .NET usando o comando `dotnet add package`.

```console
dotnet add package Azure.Communication.Administration
```

### <a name="set-up-the-app-framework"></a>Configurar o framework de aplicativos

No diretório do projeto:

1. Abra o arquivo **Program.cs** em um editor de texto
1. Adicione uma diretiva `using` para incluir o namespace `Azure.Communication.Administration`
1. Atualize a declaração do método `Main` para dar suporte ao código assíncrono

Use o seguinte código para começar:

```csharp
using System;
using Azure.Communication.Administration;

namespace UserAccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - User Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Inicialize um `CommunicationIdentityClient` com a sua cadeia de conexão. O código abaixo recupera a cadeia de conexão do recurso de uma variável de ambiente chamada `COMMUNICATION_SERVICES_CONNECTION_STRING`. Saiba como [gerenciar a cadeia de conexão do recurso](../create-communication-resource.md#store-your-connection-string).

Adicione o seguinte código ao método `Main`:

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string ConnectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(ConnectionString);
```

## <a name="create-a-user"></a>Criar um usuário

Os Serviços de Comunicação do Azure mantêm um diretório de identidade leve. Use o método `createUser` para criar uma entrada no diretório com um `Id` exclusivo. Você deve manter um mapeamento entre os usuários do aplicativo e as entidades geradas dos Serviços de Comunicação (por exemplo, armazenando-os em seu banco de dados do servidor de aplicativos).

```csharp
var userResponse = await client.CreateUserAsync();
var user = userResponse.Value;
Console.WriteLine($"\nCreated a user with ID: {user.Id}");
```

## <a name="issue-user-access-tokens"></a>Emitir tokens de acesso do usuário

Use o método `issueToken` para emitir um token de acesso para um usuário dos Serviços de Comunicação. Se você não fornecer o parâmetro `user` opcional, um usuário será criado e retornado com o token.

```csharp
// Issue an access token with the "voip" scope for a new user
var tokenResponse = await client.IssueTokenAsync(user, scopes: new [] { CommunicationTokenScope.VoIP });
var token =  tokenResponse.Value.Token;
var expiresOn = tokenResponse.Value.ExpiresOn;
Console.WriteLine($"\nIssued a token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

Os tokens de acesso do usuário são credenciais de curta duração que precisam ser reemitidas para impedir que os usuários tenham interrupções de serviço. A propriedade de resposta `expiresOn` indica o tempo de vida do token.

## <a name="revoke-user-access-tokens"></a>Revogar tokens de acesso do usuário

Em alguns casos, talvez seja necessário revogar explicitamente os tokens de acesso do usuário, por exemplo, quando um usuário altera a senha que eles usam para se autenticar no serviço. Essa funcionalidade está disponível por meio da biblioteca de clientes da Administração dos Serviços de Comunicação do Azure.

```csharp  
await client.RevokeTokensAsync(user);
Console.WriteLine($"\nSuccessfully revoked all tokens for user with ID: {user.Id}");
```

## <a name="delete-a-user"></a>Excluir um usuário

A exclusão de uma identidade revoga todos os tokens ativos e impede que você emita tokens subsequentes para as identidades. Ele também remove todo o conteúdo persistente associado ao usuário.

```csharp
await client.DeleteUserAsync(user);
Console.WriteLine($"\nDeleted the user with ID: {user.Id}");
```

## <a name="run-the-code"></a>Executar o código

Execute o aplicativo do seu diretório de aplicativo com o comando `dotnet run`.

```console
dotnet run
```
