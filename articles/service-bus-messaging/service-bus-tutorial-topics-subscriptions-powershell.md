---
title: Tutorial - Atualizar sortimento de estoque de varejo usando canais de publicação/assinatura e filtros de tópicos com Azure PowerShell | Microsoft Docs
description: Neste tutorial, você aprenderá como enviar e receber mensagens de um tópico e uma assinatura e como adicionar e usar regras de filtro usando o Azure PowerShell
services: service-bus-messaging
author: spelluru
manager: timlt
ms.author: spelluru
ms.date: 09/22/2018
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: 3d1e1491ad045eba88ca7bbe54a1acb38199f7d7
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65987908"
---
# <a name="tutorial-update-inventory-using-powershell-and-topicssubscriptions"></a>Tutorial: Atualizar estoque usando o PowerShell e tópicos/assinaturas

O Barramento de Serviço do Microsoft Azure é um serviço de mensagens na nuvem multilocatário que envia informações entre aplicativos e serviços. Operações assíncronas oferecem um sistema de mensagens agenciado e flexível, juntamente com recursos de mensagens PEPS (primeiro a entrar, primeiro a sair) e de publicação/assinatura. 

Este tutorial mostra como enviar e receber mensagens de e para uma fila do Barramento de Serviço, usando o PowerShell para criar uma fila e um namespace de mensagens nesse namespace e obter as credenciais de autorização nesse namespace. O procedimento mostra como enviar e receber mensagens dessa fila usando a [	Biblioteca do .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus).

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Criar um tópico de Barramento de Serviço e uma ou mais assinaturas para esse tópico usando o Azure PowerShell
> * Adicionar filtros de tópicos usando o PowerShell
> * Criar duas mensagens com conteúdo diferente
> * Enviar as mensagens e verificar se chegaram nas assinaturas esperadas
> * Receber mensagens das assinaturas

Um exemplo desse cenário é uma atualização de sortimento de estoque para várias lojas de varejo. Nesse cenário, cada loja ou conjunto de lojas recebe mensagens que são destinadas para atualizar os respectivos sortimentos. Este tutorial mostra como implementar esse cenário usando assinaturas e filtros. Primeiro, você cria um tópico com 3 assinaturas, adiciona algumas regras e filtros e envia e recebe mensagens do tópico e das assinaturas.

![topic](./media/service-bus-tutorial-topics-subscriptions-powershell/about-service-bus-topic.png)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita][] antes de começar.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

Para concluir este tutorial, verifique se você tem o seguinte instalado:

1. [Atualização 3 do Visual Studio 2017 (versão 15.3, 26730.01)](https://www.visualstudio.com/vs) ou posterior.
2. [NET Core SDK](https://www.microsoft.com/net/download/windows), versão 2.0 ou posterior.

Este tutorial requer que esteja em execução a última versão do Azure PowerShell. Se precisar instalar ou atualizar, confira [Instalar e configurar o Azure PowerShell][].

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Entrar no Azure

Envie os comandos a seguir para entrar no Azure. Essas etapas não serão necessárias, se estiver executando comandos do PowerShell no Cloud Shell: 

1. Instale o módulo PowerShell do Barramento de Serviço:

   ```azurepowershell-interactive
   Install-Module Az.ServiceBus
   ```

2. Execute o comando a seguir para entrar no Azure:

   ```azurepowershell-interactive
   Login-AzAccount
   ```

4. Defina o contexto da assinatura atual ou veja a assinatura ativa no momento:

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "MyAzureSubName" 
   Get-AzContext
   ```

## <a name="provision-resources"></a>Provisionar recursos

Após entrar no Azure, envie os comandos a seguir para provisionar os recursos do Barramento de Serviço. Verifique se todos os espaços reservados foram substituídos pelos valores apropriados:

```azurepowershell-interactive
# Create a resource group 
New-AzResourceGroup –Name my-resourcegroup –Location westus2

# Create a Messaging namespace
New-AzServiceBusNamespace -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Location westus2

# Create a queue 
New-AzServiceBusQueue -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Name queue-name -EnablePartitioning $False

# Get primary connection string (required in next step)
Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
```

Depois que o cmdlet `Get-AzServiceBusKey` executar, copie e cole a cadeia de conexão e o nome da fila selecionados em um local temporário, como no Bloco de Notas. Isso será necessário na próxima etapa.

## <a name="send-and-receive-messages"></a>Enviar e receber mensagens

Depois que o namespace e a fila forem criados e você tiver as credenciais necessárias, estará pronto para enviar e receber mensagens. É possível examinar o código [nesta pasta de exemplo do GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveQuickStart).

Para executar o código, faça o seguinte:

1. Clone o [repositório GitHub do Barramento de Serviço](https://github.com/Azure/azure-service-bus/), emitindo o comando a seguir:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Abra um prompt do PowerShell.

3. Navegue até a pasta de exemplo `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveQuickStart\BasicSendReceiveQuickStart`.

4. Se você ainda não fez isso, obtenha a cadeia de conexão usando o cmdlet a seguir do PowerShell. Certifique-se de substituir `my-resourcegroup` e `namespace-name` pelos valores específicos: 

   ```azurepowershell-interactive
   Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
   ```
5. No prompt do PowerShell, digite o comando a seguir:

   ```shell
   dotnet build
   ```
6. Navegue até a pasta `\bin\Debug\netcoreapp2.0`.
7. Digite o comando a seguir para executar o programa. Verifique se você substituiu `myConnectionString` pelo valor obtido anteriormente e `myQueueName` pelo nome da fila que você criou:

   ```shell
   dotnet BasicSendReceiveQuickStart.dll -ConnectionString "myConnectionString" -QueueName "myQueueName"
   ``` 
8. Observe 10 mensagens sendo enviadas para a fila e, subsequentemente, recebidas da fila:

   ![saída do programa](./media/service-bus-quickstart-powershell/dotnet.png)

## <a name="clean-up-resources"></a>Limpar recursos

Execute o comando a seguir para remover o grupo de recursos, o namespace e todos os recursos relacionados:

```powershell-interactive
Remove-AzResourceGroup -Name my-resourcegroup
```

## <a name="understand-the-sample-code"></a>Entender o código de exemplo

Esta seção contém mais detalhes sobre o que o código de exemplo faz. 

### <a name="get-connection-string-and-queue"></a>Obter cadeia de conexão e fila

A cadeia de conexão e o nome da fila são transmitidos para o método `Main()` como argumentos de linha de comando. `Main()` declara duas variáveis de cadeia de caracteres para manter esses valores:

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string QueueName = "";

    for (int i = 0; i < args.Length; i++)
    {
        var p = new Program();
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i+1]}");
            ServiceBusConnectionString = args[i + 1]; 
        }
        else if(args[i] == "-QueueName")
        {
            Console.WriteLine($"QueueName: {args[i+1]}");
            QueueName = args[i + 1];
        }                
    }

    if (ServiceBusConnectionString != "" && QueueName != "")
        MainAsync(ServiceBusConnectionString, QueueName).GetAwaiter().GetResult();
    else
    {
        Console.WriteLine("Specify -Connectionstring and -QueueName to execute the example.");
        Console.ReadKey();
    }                            
}
```
 
Em seguida, o método `Main()` inicia o loop de mensagem assíncrona, `MainAsync()`.

### <a name="message-loop"></a>Loop de mensagem

O método `MainAsync()` cria um cliente de fila com os argumentos de linha de comando, chama um manipulador de mensagens de recebimento nomeado `RegisterOnMessageHandlerAndReceiveMessages()` e envia o conjunto de mensagens:

```csharp
static async Task MainAsync(string ServiceBusConnectionString, string QueueName)
{
    const int numberOfMessages = 10;
    queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

    Console.WriteLine("======================================================");
    Console.WriteLine("Press any key to exit after receiving all the messages.");
    Console.WriteLine("======================================================");

    // Register QueueClient's MessageHandler and receive messages in a loop
    RegisterOnMessageHandlerAndReceiveMessages();

    // Send Messages
    await SendMessagesAsync(numberOfMessages);

    Console.ReadKey();

    await queueClient.CloseAsync();
}
```

O método `RegisterOnMessageHandlerAndReceiveMessages()` define algumas opções de manipulador de mensagens e, em seguida, chama o métodos `RegisterMessageHandler()` do cliente de fila, que inicia o recebimento:

```csharp
static void RegisterOnMessageHandlerAndReceiveMessages()
{
    // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
    var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
    {
        // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
        // Set it according to how many messages the application wants to process in parallel.
        MaxConcurrentCalls = 1,

        // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
        // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
        AutoComplete = false
    };

    // Register the function that will process messages
    queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
} 
```

### <a name="send-messages"></a>Enviar mensagens

As operações de criação e envio de mensagens ocorrem no método `SendMessagesAsync()`:

```csharp
static async Task SendMessagesAsync(int numberOfMessagesToSend)
{
    try
    {
        for (var i = 0; i < numberOfMessagesToSend; i++)
        {
            // Create a new message to send to the queue
            string messageBody = $"Message {i}";
            var message = new Message(Encoding.UTF8.GetBytes(messageBody));

            // Write the body of the message to the console
            Console.WriteLine($"Sending message: {messageBody}");

            // Send the message to the queue
            await queueClient.SendAsync(message);
        }
    }
    catch (Exception exception)
    {
        Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
    }
}
```

### <a name="process-messages"></a>Processar mensagens

O método `ProcessMessagesAsync()` reconhece, processa e conclui o recebimento das mensagens:

```csharp
static async Task ProcessMessagesAsync(Message message, CancellationToken token)
{
    // Process the message
    Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

    // Complete the message so that it is not received again.
    await queueClient.CompleteAsync(message.SystemProperties.LockToken);
}
```

> [!NOTE]
> É possível gerenciar os recursos do Barramento de Serviço com o [Gerenciador de Barramento de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador de Barramento de Serviço permite que usuários se conectem a um namespace de serviço do Barramento de Serviço e administrem entidades de mensagens de uma maneira fácil. A ferramenta fornece recursos avançados, como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você provisionou recursos usando o Azure PowerShell e, em seguida, enviou e recebeu mensagens de um tópico do Barramento de Serviço e as respectivas assinaturas. Você aprendeu como:

> [!div class="checklist"]
> * Criar um tópico de Barramento de Serviço e uma ou mais assinaturas para esse tópico usando o portal do Azure
> * Adicionar filtros de tópicos usando código .NET
> * Criar duas mensagens com conteúdo diferente
> * Enviar as mensagens e verificar se chegaram nas assinaturas esperadas
> * Receber mensagens das assinaturas

Para obter mais exemplos de envio e recebimento de mensagens, comece com os [Exemplos de Barramento de Serviço no GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Avance para o próximo tutorial para saber mais sobre como usar os recursos de publicação/assinatura do Barramento de Serviço.

> [!div class="nextstepaction"]
> [Atualizar estoque usando o PowerShell e tópicos/assinaturas](service-bus-tutorial-topics-subscriptions-cli.md)

[conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Instalar e configurar o Azure PowerShell]: /powershell/azure/install-Az-ps
