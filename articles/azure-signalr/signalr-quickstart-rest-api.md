---
title: 'Início Rápido: API REST do Serviço do Azure SignalR'
description: Saiba como usar a API REST com o Serviço do Azure SignalR seguindo exemplos. Encontre detalhes da especificação da API REST.
author: sffamily
ms.service: signalr
ms.topic: quickstart
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 17371e3bd426ea81b5e7e07610aac0073ea972c9
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74157689"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>Início Rápido: Difundir mensagens em tempo real do aplicativo de console

O Azure SignalR Service fornece [API REST](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) para dar suporte ao servidor para cenários de comunicação do cliente, como difusão. Você pode escolher qualquer linguagem de programação que pode fazer a chamada à API REST. Você pode postar mensagens para todos os clientes conectados, um cliente específico por nome ou um grupo de clientes.

Neste início rápido, você aprenderá como enviar mensagens de um aplicativo de linha de comando para aplicativos cliente conectados em C#.

## <a name="prerequisites"></a>Prerequisites

Este início rápido pode ser executado no macOS, Windows ou Linux.

* [SDK do .NET Core](https://www.microsoft.com/net/download/core)
* Um editor de texto ou editor de código de sua escolha.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no portal do Azure em <https://portal.azure.com/> com sua conta do Azure.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Durante a implantação do serviço, vamos alternar para preparar o código. Clone o [aplicativo de exemplo do GitHub](https://github.com/aspnet/AzureSignalR-samples.git), defina a cadeia de conexão do SignalR Service e execute o aplicativo localmente.

1. Abra uma janela de terminal git. Mude para uma pasta em que deseja clonar o projeto de exemplo.

1. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra

Este exemplo é um aplicativo de console mostrando o uso do Azure SignalR Service. Ele fornece dois modos:

- Modo de servidor: use comandos simples para chamar a API REST do Azure SignalR Service.
- Modo cliente: conectar-se ao Azure SignalR Service e receber mensagens de servidor.

Também é possível encontrar como gerar um token de acesso para autenticar com o Azure SignalR Service.

### <a name="build-the-executable-file"></a>Criar o arquivo executável

Como exemplo, usamos macOS osx.10.13-x64. Você pode encontrar [referências](https://docs.microsoft.com/dotnet/core/rid-catalog) sobre como criar em outras plataformas.

```bash
cd AzureSignalR-samples/samples/Serverless/

dotnet publish -c Release -r osx.10.13-x64
```

### <a name="start-a-client"></a>Iniciar um cliente

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="start-a-server"></a>Iniciar um servidor

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless server -c "<ConnectionString>" -h <HubName>
```

## <a name="run-the-sample-without-publishing"></a>Executar o exemplo sem publicação

Você também pode executar o comando a seguir para iniciar um servidor ou cliente

```bash
# Start a server
dotnet run -- server -c "<ConnectionString>" -h <HubName>

# Start a client
dotnet run -- client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="use-user-secrets-to-specify-connection-string"></a>Use segredos do usuário para especificar a Cadeia de Conexão

Você pode executar `dotnet user-secrets set Azure:SignalR:ConnectionString "<ConnectionString>"` no diretório raiz do exemplo. Depois disso, você não precisa mais da opção `-c "<ConnectionString>"`.

## <a name="usage"></a>Uso

Depois que o servidor for iniciado, use o comando para enviar a mensagem:

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

Você pode iniciar vários clientes com nomes de clientes diferentes.

## <a name="usage"> </a> Integração com serviços de terceiros

O serviço Azure SignalR permite que serviços de terceiros integrem-se ao sistema.

### <a name="definition-of-technical-specifications"></a>Definição de especificações técnicas

A tabela a seguir mostra todas as versões das APIs REST com suporte atualmente. Você também pode encontrar o arquivo de definição de cada versão específica

Versão | Estado de API | Porta | Específicas
--- | --- | --- | ---
`1.0-preview` | Disponível | 5002 | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1-preview.json)
`1.0` | Disponível | Standard | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1.json)

A lista de APIs disponíveis para cada versão específica está disponível na lista a seguir.

API | `1.0-preview` | `1.0`
--- | --- | ---
[Transmitir para todos](#broadcast) | **&#x2713;** | **&#x2713;**
[Transmitir para um grupo](#broadcast-group) | **&#x2713;** | **&#x2713;**
Transmitir para alguns grupos | **&#x2713;** (Preterido) | `N / A`
[Enviar a usuários específicos](#send-user) | **&#x2713;** | **&#x2713;**
Enviar para alguns usuários | **&#x2713;** (Preterido) | `N / A`
[Adicionar um usuário a um grupo](#add-user-to-group) | `N / A` | **&#x2713;**
[Remover um usuário de um grupo](#remove-user-from-group) | `N / A` | **&#x2713;**

<a name="broadcast"> </a>
### <a name="broadcast-to-everyone"></a>Transmitir para todos

Versão | Método HTTP da API | URL de Solicitação | Corpo da solicitação
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>` | O mesmo que o descrito acima

<a name="broadcast-group"> </a>
### <a name="broadcast-to-a-group"></a>Transmitir para um grupo

Versão | Método HTTP da API | URL de Solicitação | Corpo da solicitação
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/group/<group-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>` | O mesmo que o descrito acima

<a name="send-user"> </a>
### <a name="sending-to-specific-users"></a>Enviar por push para usuários específicos

Versão | Método HTTP da API | URL de Solicitação | Corpo da solicitação
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/user/<user-id>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>` | O mesmo que o descrito acima

<a name="add-user-to-group"> </a>
### <a name="adding-a-user-to-a-group"></a>Adicionar um usuário a um grupo

Versão | Método HTTP da API | URL de Solicitação
--- | --- | ---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<userid>`

<a name="remove-user-from-group"> </a>
### <a name="removing-a-user-from-a-group"></a>Remover um usuário de um grupo

Versão | Método HTTP da API | URL de Solicitação
--- | --- | ---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<userid>`

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como usar a API REST para transmitir mensagens em tempo real do SignalR Service aos clientes. Em seguida, saiba mais sobre como desenvolver e implantar o Azure Functions com SignalR Service associação, que é criada sobre a API REST.

> [!div class="nextstepaction"]
> [Desenvolver Azure Functions usando associações de serviço do Azure SignalR](signalr-quickstart-azure-functions-csharp.md)
