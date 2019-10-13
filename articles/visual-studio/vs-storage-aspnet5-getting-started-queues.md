---
title: Introdução ao armazenamento de filas usando o Visual Studio (ASP.NET Core)
description: Como começar a usar o armazenamento de filas do Azure em um projeto ASP.NET Core no Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 11/14/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5cdf6f2644788674df91b533c9444fc88ab30b09
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300031"
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Introdução ao armazenamento de filas e aos serviços conectados do Visual Studio (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

Este artigo descreve como começar a usar o Armazenamento de Filas do Azure no Visual Studio depois de ter criado ou referenciado uma conta de Armazenamento do Azure em um projeto ASP.NET Core usando o recurso **Serviços Conectados** do Visual Studio. A operação **Serviços Conectados** instala os pacotes NuGet apropriados para acessar o armazenamento do Azure no seu projeto e adiciona a cadeia de conexão para a conta de armazenamento aos arquivos de configuração do projeto. (Veja a [documentação do Armazenamento](https://azure.microsoft.com/documentation/services/storage/) para obter informações gerais sobre o Armazenamento do Azure).

O armazenamento de filas do Azure é um serviço para armazenamento de um grande número de mensagens que podem ser acessadas de qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Uma única mensagem de fila pode ter até 64 KB (kilobytes) de tamanho e uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento. Veja também [Introdução ao Armazenamento de Filas do Azure usando .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) para obter detalhes sobre como manipular filas com programação.

Para começar, primeiramente crie uma fila do Azure em sua conta de armazenamento. Este artigo mostrará como criar uma fila no C# e como realizar operações básicas de fila, como adicionar, modificar, ler e remover entidades de fila.  O código usa a Biblioteca de Cliente de Armazenamento do Azure para .NET. Para saber mais sobre ASP.NET, confira [ASP.NET](https://www.asp.net).

Algumas das APIs de armazenamento do Azure são assíncronas e o código neste artigo supõe que os métodos assíncronos estejam sendo usados. Confira [Programação assíncrona](https://docs.microsoft.com/dotnet/csharp/async) para saber mais.

## <a name="access-queues-in-code"></a>Acessar filas em código

Para acessar filas em projetos do ASP.NET Core, inclua os itens a seguir em qualquer arquivo de origem de C# que acessa o armazenamento de filas do Azure. Use todo o código antes do código nas seções a seguir.

1. Adicione as instruções `using` necessárias:
    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Obtenha um objeto `CloudStorageAccount` que represente as informações da conta de armazenamento. Use o seguinte código para obter a cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenha um objeto `CloudQueueClient` para referenciar objetos de fila em sua conta de armazenamento:

    ```cs
    // Create the CloudQueueClient object for the storage account.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Obtenha um objeto `CloudQueue` para referenciar uma fila específica:

    ```cs
    // Get a reference to the CloudQueue named "messagequeue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messagequeue");
    ```

### <a name="create-a-queue-in-code"></a>Criar uma fila em código

Para criar a fila do Azure no código, chame `CreateIfNotExistsAsync`:

```cs
// Create the CloudQueue if it does not exist.
await messageQueue.CreateIfNotExistsAsync();
```

## <a name="add-a-message-to-a-queue"></a>Adicionar uma mensagem a uma fila

Para inserir uma mensagem em uma fila existente, crie um novo objeto `CloudQueueMessage` e chame o método `AddMessageAsync`. Um objeto `CloudQueueMessage` pode ser criado por meio de uma cadeia de caracteres (em formato UTF-8) ou de uma matriz de bytes.

```cs
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await messageQueue.AddMessageAsync(message);
```

## <a name="read-a-message-in-a-queue"></a>Ler uma mensagem em uma fila

Você pode inspecionar a mensagem na frente de uma fila sem removê-la da fila chamando o método `PeekMessageAsync`:

```cs
// Peek the next message in the queue.
CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Ler e remover uma mensagem em uma fila

Seu código pode remover uma mensagem de uma fila em duas etapas.

1. Chame `GetMessageAsync` para obter a próxima mensagem em uma fila. Uma mensagem retornada de `GetMessageAsync` torna-se invisível para todas as outras mensagens de leitura de código da fila. Por padrão, essa mensagem permanece invisível por 30 segundos.
1. Para concluir a remoção da mensagem da fila, chame `DeleteMessageAsync`.

Este processo de duas etapas de remover uma mensagem garante que quando o código não processa uma mensagem devido à falhas de hardware ou de software, outra instância do seu código pode receber a mesma mensagem e tentar novamente. O código a seguir chamará `DeleteMessageAsync` logo depois que a mensagem tiver sido processada:

```cs
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

// Process the message in less than 30 seconds.

// Then delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
```

## <a name="additional-options-for-dequeuing-messages"></a>Opções adicionais para remover mensagens da fila

Há duas maneiras de personalizar a recuperação da mensagem de uma fila. Primeiro, você pode obter um lote de mensagens (até 32). Segundo, você pode definir um tempo limite de invisibilidade mais longo ou mais curto, permitindo mais ou menos tempo para seu código processar totalmente cada mensagem. O exemplo de código a seguir usa o método `GetMessages` para receber 20 mensagens em uma chamada. Em seguida, ele processa cada mensagem usando um loop `foreach`. Ele também define o tempo limite de invisibilidade de cinco minutos para cada mensagem. Observe que o timer de cinco minutos começa para todas as mensagens ao mesmo tempo; portanto, depois de cinco minutos, todas as mensagens que não tiverem sido excluídas se tornarão visíveis novamente.

```cs
// Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
//   delete each message after processing.

foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

## <a name="get-the-queue-length"></a>Obter o tamanho da fila

Você pode obter uma estimativa do número de mensagens em uma fila. O método `FetchAttributes` solicita que o serviço de fila recupere os atributos da fila, incluindo a contagem de mensagens. A propriedade `ApproximateMethodCount` retorna o último valor recuperado pelo método `FetchAttributes`, sem chamar o serviço Fila.

```cs
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display the number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Usar o padrão Async-Await com APIs comuns de fila

Este exemplo mostra como usar o padrão async-await com APIs de fila comuns que terminam em `Async`. Quando um método assíncrono é usado, o padrão async-await suspende a execução local até que a chamada seja concluída. Esse comportamento permite que o thread atual faça outro trabalho que ajude a evitar gargalos de desempenho e melhora a capacidade de resposta geral do aplicativo.

```cs
// Create a message to add to the queue.
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message.
await messageQueue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="delete-a-queue"></a>Excluir uma fila

Para excluir uma fila e todas as mensagens que ela contém, chame o método `Delete` no objeto fila:

```cs
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
