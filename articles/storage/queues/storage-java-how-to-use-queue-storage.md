---
title: Como usar o armazenamento de fila do Java – armazenamento do Azure
description: Saiba como usar o armazenamento de fila para criar e excluir filas e inserir, obter e excluir mensagens com a biblioteca de cliente de armazenamento do Azure para Java.
author: mhopkins-msft
ms.service: storage
ms.author: mhopkins
ms.date: 12/08/2016
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 33123c823a2e0d6044e3419f62a512f3e84f953f
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72302994"
---
# <a name="how-to-use-queue-storage-from-java"></a>Como usar o Armazenamento de Fila no Java

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

Este guia lhe mostrará como executar cenários comuns usando o serviço de Armazenamento de Fila do Azure. As amostras são escritas em Java e usam o [SDK de Armazenamento do Azure para Java][Azure Storage SDK for Java]. Os cenários abrangidos incluem **inserir**, **exibir**, **obter** e **excluir** mensagens da fila, bem como **criar** e **excluir** filas. Para obter mais informações sobre filas, consulte a seção [Próximas etapas](#next-steps) .

> [!NOTE]
> Um SDK está disponível para os desenvolvedores que usam o Armazenamento do Azure em dispositivos Android. Para obter mais informações, consulte [SDK de Armazenamento do Azure para Android][Azure Storage SDK for Android].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Criar um aplicativo Java

Neste guia, você usará os recursos de armazenamento que podem ser executados em um aplicativo Java localmente ou no código em execução em um aplicativo Web no Azure.

Para isso, você precisará instalar o JDK (Java Development Kit) e criar uma conta de armazenamento do Azure na sua assinatura do Azure. Depois de fazer isso, você precisará verificar se o sistema de desenvolvimento atende aos requisitos mínimos e às dependências listadas no repositório [SDK do armazenamento do Azure para Java][Azure Storage SDK for Java] no github. Se o seu sistema atender a esses requisitos, você poderá seguir as instruções para baixar e instalar as Bibliotecas de Armazenamento do Azure para Java em seu sistema por meio desse repositório. Depois de concluir essas tarefas, você poderá criar um aplicativo Java que usa os exemplos neste artigo.

## <a name="configure-your-application-to-access-queue-storage"></a>Configurar seu aplicativo para acessar o armazenamento de filas

Adicione as seguintes instruções de importação na parte superior do arquivo Java em que deseja usar as APIs de armazenamento do Azure para acessar as filas:

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de conexão de armazenamento do Azure

Um cliente de armazenamento do Azure usa uma cadeia de conexão para armazenar pontos de extremidade e credenciais para acessar serviços de gerenciamento de dados. Ao executar um aplicativo cliente, é necessário fornecer a cadeia de conexão de armazenamento no formato a seguir, usando o nome de sua conta de armazenamento e a tecla de Acesso primário da conta de armazenamento listada no [portal do Azure](https://portal.azure.com) para os valores *AccountName* e *AccountKey*. Este exemplo mostra como você pode declarar um campo estático para armazenar a cadeia de conexão:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

Em um aplicativo que esteja sendo executado em uma função no Microsoft Azure, essa cadeia pode ser armazenada no arquivo de configuração do serviço, *ServiceConfiguration.cscfg*, podendo ser acessada com uma chamada para o método **RoleEnvironment.getConfigurationSettings** . Segue um exemplo de como obter a cadeia de conexão de um elemento **Setting** denominado *StorageConnectionString* no arquivo de configuração de serviço:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

Os exemplos abaixo pressupõem que você usou um desses dois métodos para obter a cadeia de conexão do armazenamento.

## <a name="how-to-create-a-queue"></a>Como: Criar uma fila
Um objeto **CloudQueueClient** permite que você obtenha objetos de referência para filas. O código a seguir cria um objeto **CloudQueueClient**. (Observação: Existem outras maneiras de criar objetos **CloudStorageAccount**. Para obter mais informações, confira **CloudStorageAccount** na [Referência de SDK do cliente de armazenamento do Azure].

Use o objeto **CloudQueueClient** para obter uma referência à fila que deseja usar. Você poderá criar a fila se ela não existir.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

   // Create the queue client.
   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

   // Retrieve a reference to a queue.
   CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Create the queue if it doesn't already exist.
   queue.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-add-a-message-to-a-queue"></a>Como: Adicionar uma mensagem a uma fila
Para inserir uma mensagem em uma fila existente, primeiro crie uma nova **CloudQueueMessage**. Em seguida, chame o método **addMessage** . Um **CloudQueueMessage** pode ser criado por meio de uma cadeia de caracteres (em formato UTF-8) ou de uma matriz de bytes. Aqui está o código que cria uma fila (se ela não existir) e insere a mensagem "Olá, mundo".

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.createIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-peek-at-the-next-message"></a>Como: Espiar a próxima mensagem
Você pode inspecionar a mensagem na frente de uma fila sem removê-la da fila chamando **peekMessages**.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Peek at the next message.
    CloudQueueMessage peekedMessage = queue.peekMessage();

    // Output the message value.
    if (peekedMessage != null)
    {
      System.out.println(peekedMessage.getMessageContentAsString());
   }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como: Alterar o conteúdo de uma mensagem na fila
Você pode alterar o conteúdo de uma mensagem in-loco na fila. Se a mensagem representar uma tarefa de trabalho, você poderá usar esse recurso para atualizar o status da tarefa de trabalho. O código a seguir atualiza a mensagem da fila com novo conteúdo e define o tempo limite de visibilidade para estender mais 60 segundos. Estender o tempo limite de visibilidade salva o estado do trabalho associado à mensagem e dá ao cliente outro minuto para continuar a trabalhar na mensagem. Você pode usar essa técnica para acompanhar fluxos de trabalho de várias etapas em mensagens em fila, sem a necessidade de começar desde o início, caso uma etapa de processamento falhar devido a uma falha de hardware ou de software. Normalmente, você mantém uma contagem de repetições e, se a mensagem for repetida mais de *n* vezes, você a exclui. Isso protege contra uma mensagem que dispara um erro do aplicativo sempre que for processada.

O exemplo de código a seguir pesquisa através da fila de mensagens, localiza a primeira mensagem que corresponde a "Hello, World” do conteúdo, em seguida, modifica o conteúdo da mensagem e sai.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // The maximum number of messages that can be retrieved is 32.
    final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

    // Loop through the messages in the queue.
    for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
    {
        // Check for a specific string.
        if (message.getMessageContentAsString().equals("Hello, World"))
        {
            // Modify the content of the first matching message.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 30 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 30, updateFields, null, null);
            break;
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

Como alternativa, o seguinte exemplo de código atualiza apenas a primeira mensagem visível na fila.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage message = queue.retrieveMessage();

    if (message != null)
    {
        // Modify the message content.
        message.setMessageContent("Updated contents.");
        // Set it to be visible in 60 seconds.
        EnumSet<MessageUpdateFields> updateFields =
            EnumSet.of(MessageUpdateFields.CONTENT,
            MessageUpdateFields.VISIBILITY);
        // Update the message.
        queue.updateMessage(message, 60, updateFields, null, null);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-get-the-queue-length"></a>Como: Obter o tamanho da fila
Você pode obter uma estimativa do número de mensagens em uma fila. O método **downloadAttributes** solicita vários valores atuais ao serviço Fila, incluindo uma contagem de quantas mensagens estão em uma fila. A contagem é aproximada apenas porque as mensagens podem ser adicionadas ou removidas depois que o serviço Fila responde à sua solicitação. O método **getApproximateMessageCount** retorna o último valor recuperado pela chamada a **downloadAttributes** sem chamar o serviço Fila.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Download the approximate message count from the server.
    queue.downloadAttributes();

    // Retrieve the newly cached approximate message count.
    long cachedMessageCount = queue.getApproximateMessageCount();

    // Display the queue length.
    System.out.println(String.format("Queue length: %d", cachedMessageCount));
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-dequeue-the-next-message"></a>Como: Remover a próxima mensagem da fila
Seu código remove uma mensagem de um fila em duas etapas. Quando você chama **retrieveMessage**, obtém a próxima mensagem em uma fila. As mensagens retornadas de **retrieveMessage** tornam-se invisíveis para todas as outras mensagens de leitura de código da fila. Por padrão, essa mensagem permanece invisível por 30 segundos. Para terminar de remover a mensagem da fila, você também deve chamar **deleteMessage**. Este processo de duas etapas de remover uma mensagem garante que quando o código não processa uma mensagem devido à falhas de hardware ou de software, outra instância do seu código pode receber a mesma mensagem e tentar novamente. Seu código chama **deleteMessage** logo depois que a mensagem é processada.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    if (retrievedMessage != null)
    {
        // Process the message in less than 30 seconds, and then delete the message.
        queue.deleteMessage(retrievedMessage);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="additional-options-for-dequeuing-messages"></a>Opções adicionais para remover mensagens da fila
Há duas maneiras de personalizar a recuperação da mensagem de uma fila. Primeiro, você pode obter um lote de mensagens (até 32). Segundo, você pode definir um tempo limite de invisibilidade mais longo ou mais curto, permitindo mais ou menos tempo para seu código processar totalmente cada mensagem.

O exemplo de código a seguir usa o método **retrieveMessages** para obter 20 mensagens em uma chamada. Em seguida, ele processa cada mensagem usando um loop **for** . Ele também define o tempo limite de invisibilidade como cinco minutos (300 segundos) para cada mensagem. Os cinco minutos começam para todas as mensagens ao mesmo tempo, portanto, quando cinco minutos passaram desde a chamada para **retrieveMessages**, todas as mensagens que não foram excluídas ficarão visíveis novamente.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
        // Do processing for all messages in less than 5 minutes,
        // deleting each message after processing.
        queue.deleteMessage(message);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-list-the-queues"></a>Como: Listar as filas
Para obter uma lista das filas atuais, chame o método **CloudQueueClient.listQueues()** , que retornará uma coleção de objetos **CloudQueue**.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient =
        storageAccount.createCloudQueueClient();

    // Loop through the collection of queues.
    for (CloudQueue queue : queueClient.listQueues())
    {
        // Output each queue name.
        System.out.println(queue.getName());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-delete-a-queue"></a>Como: Excluir uma fila
Para excluir uma fila e todas as mensagens contidas nela, chame o método **deleteIfExists** no objeto **CloudQueue**.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Delete the queue if it exists.
    queue.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu os conceitos básicos do armazenamento de fila, siga estes links para saber mais sobre tarefas de armazenamento mais complexas.

* [SDK de Armazenamento do Azure para Java][Azure Storage SDK for Java]
* [Referência de SDK do Cliente de Armazenamento do Azure][Azure Storage Client SDK Reference]
* [API REST de serviços de armazenamento do Azure][Azure Storage Services REST API]
* [Blog da equipe de Armazenamento do Azure][Azure Storage Team Blog]

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Referência de SDK do Cliente de Armazenamento do Azure]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage Services REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
