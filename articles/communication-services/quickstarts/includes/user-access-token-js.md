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
ms.openlocfilehash: 22cfe369561eab1ca334c7ff2450162dfae3e761
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92347262"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/), versões Active LTS e Maintenance LTS (8.11.1 e 10.14.1 recomendadas).
- Um recurso e uma cadeia de conexão ativos dos Serviços de Comunicação. [Crie um recurso dos Serviços de Comunicação](../create-communication-resource.md).

## <a name="setting-up"></a>Ao configurar o

### <a name="create-a-new-nodejs-application"></a>Criar um aplicativo Node.js

Abra o terminal ou a janela de comando para criar um diretório para seu aplicativo e navegue até ele.

```console
mkdir user-tokens-quickstart && cd user-tokens-quickstart
```

Execute `npm init -y` para criar um arquivo **package.json** com as configurações padrão.

```console
npm init -y
```

### <a name="install-the-package"></a>Instalar o pacote

Use o comando `npm install` para instalar a biblioteca de clientes da Administração dos Serviços de Comunicação do Azure para JavaScript.

```console

npm install @azure/communication-administration --save

```

A opção `--save` lista a biblioteca como uma dependência no arquivo **package.json**.

## <a name="set-up-the-app-framework"></a>Configurar o framework de aplicativos

No diretório do projeto:

1. Abra um arquivo de texto novo no editor de código
1. Adicione uma chamada `require` para carregar o `CommunicationIdentityClient`
1. Crie a estrutura do programa, incluindo um tratamento de exceções básico

Use o seguinte código para começar:

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-administration');

const main = async () => {
  console.log("Azure Communication Services - User Access Tokens Quickstart")

  // Quickstart code goes here
};

main().catch((error) => {
  console.log("Encountered and error");
  console.log(error);
})
```

1. Salve o novo arquivo como **issue-token.js** no diretório *user-tokens-quickstart*.

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie a instância de um `CommunicationIdentityClient` com a sua cadeia de conexão. O código abaixo recupera a cadeia de conexão do recurso de uma variável de ambiente chamada `COMMUNICATION_SERVICES_CONNECTION_STRING`. Saiba como [gerenciar a cadeia de conexão do recurso](../create-communication-resource.md#store-your-connection-string).

Adicione o seguinte código ao método `main`:

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the user token client
const identityClient = new CommunicationIdentityClient(connectionString);
```

## <a name="create-a-user"></a>Criar um usuário

Os Serviços de Comunicação do Azure mantêm um diretório de identidade leve. Use o método `createUser` para criar uma entrada no diretório com um `Id` exclusivo. Você deve manter um mapeamento entre os usuários do aplicativo e as entidades geradas dos Serviços de Comunicação (por exemplo, armazenando-os em seu banco de dados do servidor de aplicativos).

```javascript
let userResponse = await identityClient.createUser();
console.log(`\nCreated a user with ID: ${userResponse.communicationUserId}`);
```

## <a name="issue-user-access-tokens"></a>Emitir tokens de acesso do usuário

Use o método `issueToken` para emitir um token de acesso para um usuário dos Serviços de Comunicação. Se você não fornecer o parâmetro `user` opcional, um usuário será criado e retornado com o token.

```javascript
// Issue an access token with the "voip" scope for a new user
let tokenResponse = await identityClient.issueToken(userResponse, ["voip"]);
const { token, expiresOn } = tokenResponse;
console.log(`\nIssued a token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

Os tokens de acesso do usuário são credenciais de curta duração que precisam ser reemitidas para impedir que os usuários tenham interrupções de serviço. A propriedade de resposta `expiresOn` indica o tempo de vida do token.

## <a name="revoke-user-access-tokens"></a>Revogar tokens de acesso do usuário

Em alguns casos, talvez seja necessário revogar explicitamente os tokens de acesso do usuário, por exemplo, quando um usuário altera a senha que eles usam para se autenticar no serviço. Isso usa o método `revokeTokens` para invalidar todos os tokens de acesso de um usuário.

```javascript  
await identityClient.revokeTokens(userResponse);
console.log(`\nSuccessfully revoked all tokens for user with Id: ${userResponse.communicationUserId}`);
```

## <a name="refresh-user-access-tokens"></a>Atualizar tokens de acesso do usuário

Para atualizar um token, use o objeto `CommunicationUser` para emitir novamente:

```javascript  
let userResponse = new CommunicationUser(existingUserId);
let tokenResponse = await identityClient.issueToken(userResponse, ["voip"]);
```

## <a name="delete-a-user"></a>Excluir um usuário

A exclusão de um usuário revoga todos os tokens ativos e impede que você emita tokens subsequentes para as identidades. Ele também remove todo o conteúdo persistente associado ao usuário.

```javascript
await identityClient.deleteUser(userResponse);
console.log(`\nDeleted the user with Id: ${userResponse.communicationUserId}`);
```

## <a name="run-the-code"></a>Executar o código

Em um prompt de console, navegue até o diretório que contém o arquivo *issue-token.js* e execute o comando `node` para executar o aplicativo.

```console
node ./issue-token.js
```
