---
title: Desenvolver & configurar o aplicativo Azure Functions - Azure SignalR
description: Detalhes sobre como desenvolver e configurar aplicativos em tempo real sem servidor usando funções do Azure e serviço Azure SignalR
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: e1157a695d34c75b237391427b37365421366ef8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523163"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Desenvolvimento de funções do Azure e a configuração com o serviço do Azure SignalR

Os aplicativos azure Functions podem aproveitar as vinculações do [Azure SignalR Service](../azure-functions/functions-bindings-signalr-service.md) para adicionar recursos em tempo real. Os aplicativos clientes usam SDKs clientes disponíveis em vários idiomas para se conectar ao Azure SignalR Service e receber mensagens em tempo real.

Este artigo descreve os conceitos para desenvolver e configurar um aplicativo Azure Function integrado ao SignalR Service.

## <a name="signalr-service-configuration"></a>Configuração do Serviço SignalR

O Azure SignalR Service pode ser configurado em diferentes modos. Quando usado com funções do Azure, o serviço deve ser configurado no modo *Sem Servidor.*

No portal Azure, localize a página *Configurações* do recurso SignalR Service. Defina o *modo Serviço* como *Sem Servidor*.

![Modo de Serviço do SignalR](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Desenvolvimento do Azure Functions

Um aplicativo em tempo real sem servidor criado com o Azure Functions e o serviço do Azure SignalR normalmente requer dois Azure Functions:

* Uma função "negotiate" que o cliente chama para obter um token de acesso de serviço válido do Serviço do SignalR e a URL do ponto de extremidade de serviço
* Uma ou mais funções que enviam mensagens ou gerenciam a associação do grupo

### <a name="negotiate-function"></a>negociar função

Um aplicativo cliente requer um token de acesso válido para se conectar ao Azure SignalR Service. Um token de acesso pode ser anônimo ou autenticado a uma determinada ID do usuário. Os aplicativos do Serviço SignalR sem servidor exigem um ponto final HTTP chamado "negociar" para obter um token e outras informações de conexão, como a URL de ponto final do SignalR Service.

Use uma função Azure acionada HTTP e a vinculação de entrada *SignalRConnectionInfo* para gerar o objeto de informações de conexão. A função deve ter uma `/negotiate`rota HTTP que termina em .

Para obter mais informações sobre como criar a função de negociação, consulte a [referência de vinculação de entrada *SignalRConnectionInfo* ](../azure-functions/functions-bindings-signalr-service-input.md).

Para saber como criar um token autenticado, consulte [Usando autenticação de serviço de aplicativo](#using-app-service-authentication).

### <a name="sending-messages-and-managing-group-membership"></a>Envio de mensagens e gerenciamento de membros do grupo

Use a vinculação de saída *SignalR* para enviar mensagens aos clientes conectados ao Azure SignalR Service. Você pode transmitir mensagens para todos os clientes, ou pode enviá-las para um subconjunto de clientes que são autenticados com um ID de usuário específico ou foram adicionados a um grupo específico.

Os usuários podem ser adicionados a um ou mais grupos. Você também pode usar a vinculação de saída *SignalR* para adicionar ou remover usuários para/de grupos.

Para obter mais informações, consulte a [referência de vinculação de saída *SignalR* ](../azure-functions/functions-bindings-signalr-service-output.md).

### <a name="signalr-hubs"></a>SignalR Hubs

SignalR tem um conceito de "hubs". Cada conexão do cliente e cada mensagem enviada das Funções do Azure é escopo para um hub específico. Você pode usar hubs como uma maneira de separar suas conexões e mensagens em namespaces lógicos.

## <a name="client-development"></a>Desenvolvimento de cliente

Os aplicativos cliente SignalR podem aproveitar o SDK do cliente SignalR em um dos vários idiomas para se conectar facilmente e receber mensagens do Azure SignalR Service.

### <a name="configuring-a-client-connection"></a>Configurando uma conexão com o cliente

Para se conectar ao SignalR Service, um cliente deve concluir uma negociação de conexão bem-sucedida que consiste nessas etapas:

1. Faça uma solicitação ao ponto final http *de negociação* discutido acima para obter informações de conexão válidas
1. Conecte-se ao SignalR Service usando o URL de ponto final de serviço e o token de acesso obtido no ponto final da *negociação*

Os SDKs do cliente SignalR já contêm a lógica necessária para realizar o aperto de mão de negociação. Passe a URL do ponto final `negotiate` de negociação, menos `HubConnectionBuilder`o segmento, para o SDK. Aqui está um exemplo em JavaScript:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Por convenção, o SDK `/negotiate` anexa automaticamente à URL e usa-a para iniciar a negociação.

> [!NOTE]
> Se você estiver usando o JavaScript/TypeScript SDK em um navegador, você precisa [habilitar o CORS (Cross-Origin Resource Sharing, compartilhamento de recursos de origem cruzada)](#enabling-cors) no seu Aplicativo de Função.

Para obter mais informações sobre como usar o SDK cliente SignalR, consulte a documentação do seu idioma:

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [Javascript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Envio de mensagens de um cliente para o serviço

Embora o SignalR SDK permita que os aplicativos clientes invoquem a lógica de backend em um hub SignalR, essa funcionalidade ainda não é suportada quando você usa o SignalR Service com funções do Azure. Use solicitações HTTP para invocar funções do Azure.

## <a name="azure-functions-configuration"></a>Configuração de Funções do Azure

Os aplicativos azure Function que se integram ao Azure SignalR Service podem ser implantados como qualquer aplicativo típico do Azure Function, usando técnicas como [implantação contínua,](../azure-functions/functions-continuous-deployment.md) [implantação de zip](../azure-functions/deployment-zip-push.md)e [execução a partir do pacote](../azure-functions/run-functions-from-deployment-package.md).

No entanto, existem algumas considerações especiais para aplicativos que usam as vinculações do SignalR Service. Se o cliente for executado em um navegador, o CORS deve ser habilitado. E se o aplicativo exigir autenticação, você pode integrar o ponto final de negociação com a Autenticação do Serviço de Aplicativo.

### <a name="enabling-cors"></a>Habilitando CORS

O cliente JavaScript/TypeScript faz solicitações HTTP para a função de negociação para iniciar a negociação de conexão. Quando o aplicativo cliente estiver hospedado em um domínio diferente do aplicativo Azure Function, o CORS (Cross-Origin Resource Sharing, compartilhamento de recursos de origem cruzada) deve ser ativado no aplicativo Function ou o navegador bloqueará as solicitações.

#### <a name="localhost"></a>Localhost

Ao executar o aplicativo Function em seu `Host` computador local, você pode adicionar uma seção ao *local.settings.json* para ativar o CORS. Na `Host` seção, adicione duas propriedades:

* `CORS`- inserir a URL base que é a origem do aplicativo do cliente
* `CORSCredentials`- configurá-lo para `true` permitir solicitações "comcredenciais"

Exemplo:

```json
{
  "IsEncrypted": false,
  "Values": {
    // values
  },
  "Host": {
    "CORS": "http://localhost:8080",
    "CORSCredentials": true
  }
}
```

#### <a name="cloud---azure-functions-cors"></a>Nuvem - Funções Do Azure CORS

Para ativar o CORS em um aplicativo azure Function, acesse a tela de configuração do CORS na *guia* Recursos da plataforma do aplicativo Function no portal Azure.

> [!NOTE]
> A configuração do CORS ainda não está disponível no plano de consumo Linux de funções do Azure. Use [o Gerenciamento de API do Azure](#cloud---azure-api-management) para habilitar o CORS.

O CORS com Access-Control-Allow-Credentials deve ser habilitado para que o cliente SignalR ligue para a função de negociação. Selecione a caixa de seleção para ativê-la.

Na seção *Origens Permitidas,* adicione uma entrada com a URL base de origem do seu aplicativo web.

![Configuração do CORS](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>Cloud - Gerenciamento de API do Azure

O Azure API Management fornece um gateway de API que adiciona recursos aos serviços de back-end existentes. Você pode usá-lo para adicionar CORS ao seu aplicativo de função. Oferece uma camada de consumo com preços de pagamento por ação e uma subvenção mensal gratuita.

Consulte a documentação de gerenciamento de API para obter informações sobre como [importar um aplicativo Azure Function](../api-management/import-function-app-as-api.md). Uma vez importado, você pode adicionar uma política de entrada para habilitar o CORS com suporte a Credenciais de Permitir acesso-controle.

```xml
<cors allow-credentials="true">
  <allowed-origins>
    <origin>https://azure-samples.github.io</origin>
  </allowed-origins>
  <allowed-methods>
    <method>GET</method>
    <method>POST</method>
  </allowed-methods>
  <allowed-headers>
    <header>*</header>
  </allowed-headers>
  <expose-headers>
    <header>*</header>
  </expose-headers>
</cors>
```

Configure seus clientes SignalR para usar a URL de gerenciamento de API.

### <a name="using-app-service-authentication"></a>Usando a autenticação do serviço de aplicativos

O Azure Functions possui autenticação incorporada, suportando provedores populares como Facebook, Twitter, Microsoft Account, Google e Azure Active Directory. Esse recurso pode ser integrado à vinculação *SignalRConnectionInfo* para criar conexões ao Azure SignalR Service que foram autenticadas em um ID do usuário. Seu aplicativo pode enviar mensagens usando a vinculação de saída *SignalR* que são direcionadas a esse ID do usuário.

No portal Azure, na guia *Recursos* do aplicativo Function, abra a janela de configurações *de autenticação/autorização.* Siga a documentação [da Autenticação do Serviço de Aplicativo](../app-service/overview-authentication-authorization.md) para configurar a autenticação usando um provedor de identidade de sua escolha.

Uma vez configuradas, as solicitações HTTP autenticadas incluirão `x-ms-client-principal-name` e `x-ms-client-principal-id` cabeçalhos contendo o nome de usuário e o ID de usuário da identidade autenticada, respectivamente.

Você pode usar esses cabeçalhos na configuração de vinculação *SignalRConnectionInfo* para criar conexões autenticadas. Aqui está um exemplo C# `x-ms-client-principal-id` função de negociação que usa o cabeçalho.

```csharp
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

Em seguida, você pode enviar mensagens para esse usuário definindo a `UserId` propriedade de uma mensagem SignalR.

```csharp
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

Para obter informações sobre outros idiomas, consulte as [vinculações do Azure SignalR Service](../azure-functions/functions-bindings-signalr-service.md) para referência de funções do Azure.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como desenvolver e configurar aplicativos de serviço SignalR sem servidor usando funções do Azure. Tente criar um aplicativo você mesmo usando uma das partidas rápidas ou tutoriais na página de visão geral do [SignalR Service](index.yml).