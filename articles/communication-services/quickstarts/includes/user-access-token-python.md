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
ms.openlocfilehash: 4be8821a949527fefcc9005b1de7f4f7c438c568
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943236"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2.7, 3.5 ou superior.
- Um recurso e uma cadeia de conexão ativos dos Serviços de Comunicação. [Crie um recurso dos Serviços de Comunicação](../create-communication-resource.md).

## <a name="setting-up"></a>Ao configurar o

### <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

1. Abra o terminal ou a janela de comando para criar um diretório para seu aplicativo e navegue até ele.

   ```console
   mkdir user-tokens-quickstart && cd user-tokens-quickstart
   ```

1. Use um editor de texto para criar um arquivo chamado **issue-tokens.py** no diretório raiz do projeto e adicione a estrutura do programa, incluindo a manipulação de exceção básica. Você adicionará todo o código-fonte deste guia de início rápido a esse arquivo nas seções a seguir.

   ```python
   import os
   from azure.communication.administration import CommunicationIdentityClient

   try:
      print('Azure Communication Services - User Access Tokens Quickstart')
      # Quickstart code goes here
   except Exception as ex:
      print('Exception:')
      print(ex)
   ```

### <a name="install-the-package"></a>Instalar o pacote

Ainda no diretório do aplicativo, instale o pacote da biblioteca de clientes da Administração dos Serviços de Comunicação do Azure para Python usando o comando `pip install`.

```console
pip install azure-communication-administration
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie a instância de um `CommunicationIdentityClient` com a sua cadeia de conexão. O código abaixo recupera a cadeia de conexão do recurso de uma variável de ambiente chamada `COMMUNICATION_SERVICES_CONNECTION_STRING`. Saiba como [gerenciar a cadeia de conexão do recurso](../create-communication-resource.md#store-your-connection-string).

Adicione esse código ao bloco `try`:

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ['COMMUNICATION_SERVICES_CONNECTION_STRING']

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

## <a name="create-a-user"></a>Criar um usuário

Os Serviços de Comunicação do Azure mantêm um diretório de identidade leve. Use o método `create_user` para criar uma entrada no diretório com um `Id` exclusivo. Você deve manter um mapeamento entre os usuários do aplicativo e as entidades geradas dos Serviços de Comunicação (por exemplo, armazenando-os em seu banco de dados do servidor de aplicativos).

```python
user = client.create_user()
print("\nCreated a user with ID: " + user.identifier + ":")
```

## <a name="issue-user-access-tokens"></a>Emitir tokens de acesso do usuário

Use o método `issue_token` para emitir um token de acesso para um usuário dos Serviços de Comunicação. Se você não fornecer o parâmetro `user` opcional, um usuário será criado e retornado com o token.

```python
# Issue an access token with the "voip" scope for a new user
token_result = client.issue_token(user, ["voip"])
expires_on = token_result.expires_on.strftime('%d/%m/%y %I:%M %S %p')
print("\nIssued a token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

Os tokens de acesso do usuário são credenciais de curta duração que precisam ser reemitidas para impedir que os usuários tenham interrupções de serviço. A propriedade de resposta `expires_on` indica o tempo de vida do token.

## <a name="revoke-user-access-tokens"></a>Revogar tokens de acesso do usuário

Em alguns casos, talvez seja necessário revogar explicitamente os tokens de acesso do usuário, por exemplo, quando um usuário altera a senha que eles usam para se autenticar no serviço. Essa funcionalidade está disponível por meio da biblioteca de clientes da Administração dos Serviços de Comunicação do Azure.

```python  
client.revoke_tokens(user)
print("\nSuccessfully revoked all tokens for user with ID: " + user.identifier)
```

## <a name="delete-a-user"></a>Excluir um usuário

A exclusão de uma identidade revoga todos os tokens ativos e impede que você emita tokens subsequentes para as identidades. Ele também remove todo o conteúdo persistente associado ao usuário.

```python
client.delete_user(user)
print("\nDeleted the user with ID: " + user.identifier)
```

## <a name="run-the-code"></a>Executar o código

Em um prompt de console, navegue até o diretório que contém o arquivo *issue-token.py* e execute o comando `python` para executar o aplicativo.

```console
python ./issue-token.py
```
