---
title: Introdução às filas do Barramento de Serviço do Azure | Microsoft Docs
description: Neste tutorial, você cria aplicativos de console .NET Core para enviar mensagens e receber mensagens de uma fila de Ônibus de Serviço.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 5718106aee0e60d111398efdb839945c2c7a8a06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471730"
---
# <a name="get-started-with-service-bus-queues"></a>Introdução às filas do Barramento de Serviço
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
Neste tutorial, você cria aplicativos de console .NET Core para enviar mensagens e receber mensagens de uma fila de Ônibus de Serviço.

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2019](https://www.visualstudio.com/vs).
- [NET Core SDK](https://www.microsoft.com/net/download/windows), versão 2.0 ou posterior.
- Uma assinatura do Azure. Para concluir este tutorial, você precisa de uma conta do Azure. Você pode ativar seus [benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou se inscrever em uma conta [gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Caso você não tenha uma fila para trabalhar, siga as etapas do artigo [Usar o portal do Azure para criar uma fila do Barramento de Serviço](service-bus-quickstart-portal.md) para criar uma fila.

  - Leia a visão geral rápida das filas do Barramento de Serviço.
  - Crie um espaço de nome do Service Bus.
  - Obter a cadeia de conexão.
  - Criará uma fila do Barramento de Serviço.

## <a name="send-messages-to-the-queue"></a>Enviar mensagens para a fila

Para enviar mensagens para a fila, escreva um aplicativo de console em C# usando o Visual Studio.

### <a name="create-a-console-application"></a>Criar um aplicativo de console

Inicie o Visual Studio e crie um novo projeto **de Console App (.NET Core)** para C#. Este exemplo nomeia o aplicativo *CoreSenderApp*.

### <a name="add-the-service-bus-nuget-package"></a>Adicionar o pacote NuGet do Barramento de Serviço

1. Clique com o botão direito do mouse no projeto recém-criado e selecione **Gerenciar Pacotes NuGet**.
1. Selecione **Procurar**. Procure e selecione **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)**.
1. Selecione **Instalar** para concluir a instalação e feche o NuGet Package Manager.

    ![Selecionar um pacote NuGet][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-queue"></a>Escreva o código para enviar mensagens para a fila

1. Em *Program.cs*, `using` adicione as seguintes instruções na parte superior da definição de namespace, antes da declaração de classe:

    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

1. Na `Program` classe, declare as seguintes variáveis:

    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

    Digite sua seqüência de `ServiceBusConnectionString` conexões para o namespace como a variável. Digite o nome da fila.

1. Substitua `Main()` o método pelo seguinte método **de assincronia.** `Main` Ele chama `SendMessagesAsync()` o método que você adicionará na próxima etapa para enviar mensagens para a fila. 

    ```csharp
    public static async Task Main(string[] args)
    {    
        const int numberOfMessages = 10;
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```
1. Logo após `MainAsync()` o método, `SendMessagesAsync()` adicione o seguinte método que faz o `numberOfMessagesToSend` trabalho de envio do número de mensagens especificadas por (atualmente definido como 10):

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the queue.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the queue.
                await queueClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```

Aqui está como seu arquivo *de Program.cs* deve ser.

```csharp
namespace CoreSenderApp
{
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;

    class Program
    {
        // Connection String for the namespace can be obtained from the Azure portal under the 
        // 'Shared Access policies' section.
        const string ServiceBusConnectionString = "<your_connection_string>";
        const string QueueName = "<your_queue_name>";
        static IQueueClient queueClient;

        public static async Task Main(string[] args)
        {    
            const int numberOfMessages = 10;
            queueClient = new QueueClient(ServiceBusConnectionString, QueueName);
    
            Console.WriteLine("======================================================");
            Console.WriteLine("Press ENTER key to exit after sending all the messages.");
            Console.WriteLine("======================================================");
    
            // Send messages.
            await SendMessagesAsync(numberOfMessages);
    
            Console.ReadKey();
    
            await queueClient.CloseAsync();
        }

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
    }
}
```

Execute o programa e verifique o portal Azure.

Selecione o nome da sua fila na janela **'Namespace's Overview para** exibir o **Essentials**da fila .

![Mensagens recebidas com contagem e tamanho][queue-message]

O valor **de contagem de mensagens ativas** para a fila agora é **de 10**. Cada vez que você executa este aplicativo de remetente sem recuperar as mensagens, esse valor aumenta em 10.

O tamanho atual da fila incrementa o valor **ATUAL** no **Essentials** cada vez que o aplicativo adiciona mensagens à fila.

A próxima seção descreve como recuperar essas mensagens.

## <a name="receive-messages-from-the-queue"></a>Receber mensagens da fila

Para receber as mensagens enviadas, crie outro aplicativo **console App (.NET Core).** Instale o pacote **Microsoft.Azure.ServiceBus** NuGet, como você fez para o aplicativo de remetente.

### <a name="write-code-to-receive-messages-from-the-queue"></a>Escrever código para receber mensagens da fila

1. Em *Program.cs*, `using` adicione as seguintes instruções na parte superior da definição de namespace, antes da declaração de classe:

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

1. Na `Program` classe, declare as seguintes variáveis:

    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

    Digite sua seqüência de `ServiceBusConnectionString` conexões para o namespace como a variável. Digite o nome da fila.

1. Substitua o método `Main()` pelo seguinte código:

    ```csharp
    static void Main(string[] args)
    {
        MainAsync().GetAwaiter().GetResult();
    }

    static async Task MainAsync()
    {
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register QueueClient's MessageHandler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```

1. Logo após `MainAsync()` o método, adicione o seguinte método, que registra o manipulador de mensagens e recebe as mensagens enviadas pelo aplicativo de remetente:

    ```csharp
    static void RegisterOnMessageHandlerAndReceiveMessages()
    {
        // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
        var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
        {
            // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
            // Set it according to how many messages the application wants to process in parallel.
            MaxConcurrentCalls = 1,

            // Indicates whether the message pump should automatically complete the messages after returning from user callback.
            // False below indicates the complete operation is handled by the user callback as in ProcessMessagesAsync().
            AutoComplete = false
        };

        // Register the function that processes messages.
        queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```

1. Diretamente após o método anterior, adicione o método `ProcessMessagesAsync()` a seguir para processar as mensagens recebidas:

    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the queue Client is created in ReceiveMode.PeekLock mode (which is the default).
        await queueClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
        // If queueClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

1. Por fim, adicione o seguinte método para lidar com qualquer exceção que possa ocorrer:

    ```csharp
    // Use this handler to examine the exceptions received on the message pump.
    static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
    {
        Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
        var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
        Console.WriteLine("Exception context for troubleshooting:");
        Console.WriteLine($"- Endpoint: {context.Endpoint}");
        Console.WriteLine($"- Entity Path: {context.EntityPath}");
        Console.WriteLine($"- Executing Action: {context.Action}");
        return Task.CompletedTask;
    }
    ```

Aqui está como seu arquivo *de Program.cs* deve ser:

```csharp
namespace CoreReceiverApp
{
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;

    class Program
    {
        // Connection String for the namespace can be obtained from the Azure portal under the 
        // 'Shared Access policies' section.
        const string ServiceBusConnectionString = "<your_connection_string>";
        const string QueueName = "<your_queue_name>";
        static IQueueClient queueClient;

        static void Main(string[] args)
        {
            MainAsync().GetAwaiter().GetResult();
        }

        static async Task MainAsync()
        {
            queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

            Console.WriteLine("======================================================");
            Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
            Console.WriteLine("======================================================");

            // Register QueueClient's MessageHandler and receive messages in a loop
            RegisterOnMessageHandlerAndReceiveMessages();
 
            Console.ReadKey();

            await queueClient.CloseAsync();
        }

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

        static async Task ProcessMessagesAsync(Message message, CancellationToken token)
        {
            // Process the message
            Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

            // Complete the message so that it is not received again.
            // This can be done only if the queueClient is created in ReceiveMode.PeekLock mode (which is default).
            await queueClient.CompleteAsync(message.SystemProperties.LockToken);

            // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
            // If queueClient has already been Closed, you may chose to not call CompleteAsync() or AbandonAsync() etc. calls 
            // to avoid unnecessary exceptions.
        }

        static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
        {
            Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
            var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
            Console.WriteLine("Exception context for troubleshooting:");
            Console.WriteLine($"- Endpoint: {context.Endpoint}");
            Console.WriteLine($"- Entity Path: {context.EntityPath}");
            Console.WriteLine($"- Executing Action: {context.Action}");
            return Task.CompletedTask;
        }
    }
}
```

Execute o programa e verifique o portal novamente. A **contagem de mensagens Ativa** e os valores **CURRENT** são agora **0**.

![Fila após mensagens terem sido recebidas][queue-message-receive]

Parabéns! Agora você criou uma fila, enviou um conjunto de mensagens para aquela fila e recebeu essas mensagens da mesma fila.

> [!NOTE]
> É possível gerenciar os recursos do Barramento de Serviço com o [Gerenciador de Barramento de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Service Bus Explorer permite que os usuários se conectem facilmente a um namespace de ônibus de serviço e administrem entidades de mensagens. A ferramenta fornece recursos avançados, como funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de relé, hubs de notificação e hubs de eventos.

## <a name="next-steps"></a>Próximas etapas

Confira nosso [Repositório GitHub com exemplos](https://github.com/Azure/azure-service-bus/tree/master/samples) que demonstram alguns dos recursos mais avançados de mensagens do Barramento de Serviço.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/messages-sent-to-essentials.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive-in-essentials.png

