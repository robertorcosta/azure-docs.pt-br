---
title: Introdução ao armazenamento de filas do Azure usando o .NET-armazenamento do Azure
description: As filas do Azure fornecem uma mensagem assíncrona e confiável entre os componentes do aplicativo. A mensagem na nuvem permite que os componentes do aplicativo se dimensionem de modo independente.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/08/2020
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: devx-track-csharp
ms.openlocfilehash: bb53181355e292a885e8ffc2ac7c8a3aa48adaae
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92787458"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>Introdução ao armazenamento de Fila do Azure usando o .NET

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Visão geral

O armazenamento de filas do Azure fornece mensagens na nuvem entre os componentes do aplicativo. Na criação de aplicativos para escala, os componentes do aplicativo geralmente são dissociados para que possam ser dimensionados de forma independente. O armazenamento de filas fornece mensagens assíncronas entre componentes do aplicativo, estejam eles em execução na nuvem, na área de trabalho, em um servidor local ou em um dispositivo móvel. O armazenamento de Fila também dá suporte ao gerenciamento de tarefas assíncronas e à criação de fluxos de trabalho do processo.

### <a name="about-this-tutorial"></a>Sobre este tutorial

Este tutorial mostra como gravar código .NET para alguns cenários comuns usando o armazenamento de Fila do Azure. Os cenários abordados incluem a criação e a exclusão de filas e a adição, a leitura e a exclusão de mensagens da fila.

**Tempo estimado para conclusão:** 45 minutos

### <a name="prerequisites"></a>Pré-requisitos

- [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
- Uma [conta de armazenamento do Azure](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>Configurar seu ambiente de desenvolvimento

Em seguida, configure seu ambiente de desenvolvimento no Visual Studio para poder ficar pronto para experimentar os exemplos de código neste guia.

### <a name="create-a-windows-console-application-project"></a>Criar um projeto de aplicativo de console do Windows.

No Visual Studio, crie um novo aplicativo de console do Windows. As etapas a seguir mostram como criar um aplicativo de console no Visual Studio 2019. As etapas são semelhantes em outras versões do Visual Studio.

1. Selecione **arquivo**  >  **novo**  >  **projeto**
2. Selecionar **Platform**  >  **janelas** de plataforma
3. Selecionar **aplicativo de console (.NET Framework)**
4. Selecione **Avançar**
5. No campo **nome do projeto** , insira um nome para seu aplicativo
6. Escolha **Criar**

Todos os exemplos de código neste tutorial podem ser adicionados ao método **Main ()** do arquivo **Program.cs** do seu aplicativo de console.

Você pode usar as bibliotecas de cliente de armazenamento do Azure em qualquer tipo de aplicativo .NET, incluindo um serviço de nuvem do Azure ou aplicativo Web, e aplicativos móveis e de desktop. Neste guia, usamos um aplicativo de console para simplificar.

### <a name="use-nuget-to-install-the-required-packages"></a>Use o NuGet para instalar os pacotes necessários

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Você precisa fazer referência aos quatro pacotes a seguir em seu projeto para concluir este tutorial:

- [Biblioteca principal do Azure para .net](https://www.nuget.org/packages/Azure.Core/): Este pacote fornece primitivos compartilhados, abstrações e auxiliares para bibliotecas de cliente modernos do SDK do .net Azure.
- [Biblioteca de cliente comum do armazenamento do Azure para .net](https://www.nuget.org/packages/Azure.Storage.Common/): Este pacote fornece infraestrutura compartilhada por outras bibliotecas de cliente de armazenamento do Azure.
- [Biblioteca de filas de armazenamento do Azure para .net](https://www.nuget.org/packages/Azure.Storage.Queues/): Este pacote permite trabalhar com o serviço fila de armazenamento do Azure para armazenar mensagens que podem ser acessadas por um cliente.
- [Biblioteca de Configuration Manager para .net](https://www.nuget.org/packages/System.Configuration.ConfigurationManager/): Este pacote fornece acesso a arquivos de configuração para aplicativos cliente.

Você pode usar o NuGet para obter esses pacotes. Siga estas etapas:

1. Clique com o botão direito do mouse em seu projeto no **Gerenciador de soluções** e escolha **gerenciar pacotes NuGet** .
1. Selecione **procurar**
1. Pesquise online "Azure. Storage. Queues" e selecione **instalar** para instalar a biblioteca de cliente de armazenamento e suas dependências. Isso também instalará as bibliotecas Azure. Storage. Common e Azure. Core, que são dependências da biblioteca de filas.
1. Pesquise online por "System.Configuration.ConfigurationManager" e selecione **instalar** para instalar o Configuration Manager.

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Você precisa fazer referência aos três pacotes a seguir em seu projeto para concluir este tutorial:

- [Armazenamento do Microsoft Azure biblioteca de cliente comum para .net](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/): Este pacote fornece acesso programático a recursos de dados em sua conta de armazenamento.
- [Biblioteca de fila de armazenamento do Microsoft Azure para .net](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/): essa biblioteca de cliente permite trabalhar com o serviço Fila de armazenamento do Microsoft Azure para armazenar mensagens que podem ser acessadas por um cliente.
- [Biblioteca do Gerenciador de Configuração do Microsoft Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/): este pacote fornece uma classe para analisar uma cadeia de conexão em um arquivo de configuração, independentemente de onde o aplicativo está sendo executado.

Você pode usar o NuGet para obter esses pacotes. Siga estas etapas:

1. Clique com o botão direito do mouse em seu projeto no **Gerenciador de soluções** e escolha **gerenciar pacotes NuGet** .
1. Selecione **procurar**
1. Pesquise online "Microsoft. Azure. Storage. Queue" e selecione **instalar** para instalar a biblioteca de cliente de armazenamento e suas dependências. Isso também instalará a biblioteca Microsoft. Azure. Storage. Common, que é uma dependência da biblioteca de filas.
1. Pesquise online por "Microsoft.Azure.ConfigurationManager" e selecione **instalar** para instalar o Configuration Manager do Azure.

---

### <a name="determine-your-target-environment"></a>Determinar o ambiente de destino

Você tem duas opções de ambiente para executar os exemplos neste guia:

- Você pode executar o código em uma conta de Armazenamento do Azure na nuvem.
- Você pode executar seu código no emulador de armazenamento azurite. Azurite é um ambiente local que emula uma conta de armazenamento do Azure na nuvem. Azurite é uma opção gratuita para testar e depurar seu código enquanto seu aplicativo está em desenvolvimento. O emulador usa uma conta e chave bem conhecidas. Para obter mais informações, consulte [usar o emulador azurite para desenvolvimento e teste de armazenamento local do Azure](../common/storage-use-azurite.md).

> [!NOTE]
> Você pode utilizar o emulador de armazenamento como destino para evitar quaisquer custos associados ao Armazenamento do Azure. Todavia, se você escolher utilizar como destino uma conta de armazenamento do Azure na nuvem, os custos para realização das operações neste tutorial serão insignificantes.

## <a name="get-your-storage-connection-string"></a>Obter sua cadeia de conexão de armazenamento

As bibliotecas de cliente de armazenamento do Azure para .NET dão suporte ao uso de uma cadeia de conexão de armazenamento para configurar pontos de extremidade e credenciais para acessar serviços de armazenamento. Para obter mais informações, confira [Gerenciar chaves de acesso da conta de armazenamento](../common/storage-account-keys-manage.md).

### <a name="copy-your-credentials-from-the-azure-portal"></a>Copiar suas credenciais no Portal do Azure

O código de exemplo precisa autorizar o acesso à sua conta de armazenamento. Para autorizar, forneça suas credenciais de conta de armazenamento ao aplicativo na forma de uma cadeia de conexão. Para verificar as credenciais da conta de armazenamento:

1. Navegue até o [Portal do Azure](https://portal.azure.com).
2. Localize sua cadeia de conexão.
3. Na seção **Configurações** da visão geral da conta de armazenamento, selecione **Chaves de acesso** . Suas chaves de acesso da conta são exibidas, bem como a cadeia de conexão completa para cada chave.
4. Encontre o valor da **Cadeia de conexão** em **key1** e clique no botão **Copiar** para copiar a cadeia de conexão. Você adicionará o valor de cadeia de conexão para uma variável de ambiente na próxima etapa.

    ![Captura de tela mostrando como copiar uma cadeia de conexão do portal do Azure](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

Para obter mais detalhes sobre as sequências de conexão, veja [Configurar uma cadeia de conexão para o Armazenamento do Azure](../common/storage-configure-connection-string.md).

> [!NOTE]
> Sua chave de conta de armazenamento é semelhante para a senha raiz da sua conta de armazenamento. Sempre tenha cuidado para proteger a chave de sua conta de armazenamento. Evite distribuí-la a outros usuários, embuti-la no código ou salvá-lo em um arquivo de texto sem formatação que esteja acessível a outras pessoas. Regenere a chave usando o portal do Azure se você achar que ela pode ter sido comprometida.

A melhor maneira de manter a cadeia de conexão de armazenamento é em um arquivo de configuração. Para configurar a cadeia de conexão, abra o arquivo *app.config* do Gerenciador de soluções no Visual Studio. Adicionar o conteúdo do elemento `\<appSettings\>` mostrado abaixo. Substitua *Connection-String* pelo valor que você copiou de sua conta de armazenamento no Portal:

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="connection-string" />
    </appSettings>
</configuration>
```

Por exemplo, a configuração é semelhante a:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==EndpointSuffix=core.windows.net" />
```

Para direcionar o emulador de armazenamento azurite, você pode usar um atalho que mapeia para a chave e o nome da conta conhecida. Nesse caso, a configuração da cadeia de conexão é:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
```

### <a name="add-using-directives"></a>Adicionar diretivas using

Adicione as seguintes diretivas `using` à parte superior do arquivo `Program.cs`:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UsingStatements":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
using System; // Namespace for Console output
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

---

### <a name="create-the-queue-service-client"></a>Criar o cliente do serviço Fila

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

A classe [QueueClient](/dotnet/api/azure.storage.queues.queueclient) permite que você recupere filas armazenadas no armazenamento de filas. Veja uma maneira de criar o cliente de serviço:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateClient":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

A classe [CloudQueueClient](/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet-legacy&preserve-view=true) permite que você recupere as filas armazenadas no Armazenamento de filas. Veja uma maneira de criar o cliente de serviço:

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

---

Agora você está pronto para escrever um código que lê e grava dados no Armazenamento de Filas.

## <a name="create-a-queue"></a>Criar uma fila

Este exemplo mostra como criar uma fila:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateQueue":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

---

## <a name="insert-a-message-into-a-queue"></a>Inserir uma mensagem em uma fila

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Para inserir uma mensagem em uma fila existente, chame o método [SendMessage](/dotnet/api/azure.storage.queues.queueclient.sendmessage) . Uma mensagem pode ser um `string` (em formato UTF-8) ou uma `byte` matriz. O código a seguir cria uma fila (se ela não existir) e insere uma mensagem:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_InsertMessage":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Para inserir uma mensagem em uma fila existente, primeiro crie uma nova [CloudQueueMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy&preserve-view=true). Em seguida, chame o método [AddMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy&preserve-view=true) . Um `CloudQueueMessage` pode ser criado por meio de um `string` (em formato UTF-8) ou de uma `byte` matriz. Aqui está o código que cria uma fila (se ela não existir) e insere a mensagem "Olá, mundo":

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();

// Create a message and add it to the queue
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

---

## <a name="peek-at-the-next-message"></a>Espiar a próxima mensagem

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Você pode inspecionar as mensagens na fila sem removê-las da fila chamando o método [PeekMessages](/dotnet/api/azure.storage.queues.queueclient.peekmessages) . Se você não passar um valor para o parâmetro *maxMessages* , o padrão será inspecionar uma mensagem.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_PeekMessage":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Você pode inspecionar a mensagem na frente de uma fila sem removê-la da fila chamando o método [PeekMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet-legacy&preserve-view=true) .

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Peek at the next message
CloudQueueMessage peekedMessage = queue.PeekMessage();

// Display message.
Console.WriteLine(peekedMessage.AsString);
```

---

## <a name="change-the-contents-of-a-queued-message"></a>Alterar o conteúdo de uma mensagem na fila

Você pode alterar o conteúdo de uma mensagem in-loco na fila. Se a mensagem representar uma tarefa de trabalho, você poderá usar esse recurso para atualizar o status da tarefa de trabalho. O código a seguir atualiza a mensagem da fila com novo conteúdo e define o tempo limite de visibilidade para estender mais 60 segundos. Isso salva o estado do trabalho associado à mensagem e dá ao cliente mais um minuto para continuar trabalhando na mensagem. Você pode usar essa técnica para acompanhar fluxos de trabalho de várias etapas em mensagens em fila, sem a necessidade de começar desde o início, caso uma etapa de processamento falhar devido a uma falha de hardware ou de software. Normalmente, você mantém uma contagem de repetições e, se a mensagem for repetida mais de *n* vezes, você a exclui. Isso protege contra uma mensagem que dispara um erro do aplicativo sempre que for processada.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UpdateMessage":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the message from the queue and update the message contents.
CloudQueueMessage message = queue.GetMessage();
message.SetMessageContent2("Updated contents.", false);
queue.UpdateMessage(message,
    TimeSpan.FromSeconds(60.0),  // Make it invisible for another 60 seconds.
    MessageUpdateFields.Content | MessageUpdateFields.Visibility);
```

---

## <a name="de-queue-the-next-message"></a>Remover a próxima mensagem da fila

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Desenfileira uma mensagem de uma fila em duas etapas. Ao chamar [ReceiveMessages](/dotnet/api/azure.storage.queues.queueclient.receivemessages), você obtém a próxima mensagem em uma fila. Uma mensagem retornada de `ReceiveMessages` torna-se invisível para todas as outras mensagens de leitura de código da fila. Por padrão, essa mensagem permanece invisível por 30 segundos. Para terminar de remover a mensagem da fila, você também deve chamar [DeleteMessage](/dotnet/api/azure.storage.queues.queueclient.deletemessage). Este processo de duas etapas de remover uma mensagem garante que quando o código não processa uma mensagem devido à falhas de hardware ou de software, outra instância do seu código pode receber a mesma mensagem e tentar novamente. Seu código chama `DeleteMessage` logo após a mensagem ser processada.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessage":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Seu código remove uma mensagem de um fila em duas etapas. Ao chamar o [GetMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet-legacy&preserve-view=true), você receberá a próxima mensagem em uma fila. Uma mensagem retornada de `GetMessage` torna-se invisível para todas as outras mensagens de leitura de código da fila. Por padrão, essa mensagem permanece invisível por 30 segundos. Para terminar de remover a mensagem da fila, você também deve chamar [DeleteMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet-legacy&preserve-view=true). Este processo de duas etapas de remover uma mensagem garante que quando o código não processa uma mensagem devido à falhas de hardware ou de software, outra instância do seu código pode receber a mesma mensagem e tentar novamente. Seu código chama `DeleteMessage` logo após a mensagem ser processada.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the next message
CloudQueueMessage retrievedMessage = queue.GetMessage();

//Process the message in less than 30 seconds, and then delete the message
queue.DeleteMessage(retrievedMessage);
```

---

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>Usar padrão Async-Await com APIs comuns de armazenamento da fila

Este exemplo mostra como usar o padrão Async-Await com APIs de armazenamento da fila comuns. O exemplo chama a versão assíncrona de cada um dos métodos determinados, conforme indicado pelo sufixo *Async* de cada método. Quando um método assíncrono é usado, o padrão async-await suspende a execução local até que a chamada seja concluída. Esse comportamento permite que o thread atual faça outro trabalho que ajude a evitar gargalos de desempenho e melhora a capacidade de resposta geral do aplicativo. Para obter mais detalhes sobre como usar o padrão de Async-Await no .NET [, consulte Async e Await (C# e Visual Basic)](/previous-versions/hh191443(v=vs.140))

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_AsyncQueue":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
// Create the queue if it doesn't already exist
if(await queue.CreateIfNotExistsAsync())
{
    Console.WriteLine("Queue '{0}' Created", queue.Name);
}
else
{
    Console.WriteLine("Queue '{0}' Exists", queue.Name);
}

// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message
await queue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

---

## <a name="leverage-additional-options-for-de-queuing-messages"></a>Aproveitar opções adicionais para remover mensagens da fila

Há duas maneiras de personalizar a recuperação da mensagem de uma fila. Primeiro, você pode obter um lote de mensagens (até 32). Segundo, você pode definir um tempo limite de invisibilidade mais longo ou mais curto, permitindo mais ou menos tempo para seu código processar totalmente cada mensagem.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

O exemplo de código a seguir usa o método [ReceiveMessages](/dotnet/api/azure.storage.queues.queueclient.receivemessages) para obter 20 mensagens em uma chamada. Em seguida, ele processa cada mensagem usando um loop `foreach`. Ele também define o tempo limite de invisibilidade de cinco minutos para cada mensagem. Observe que os 5 minutos começam para todas as mensagens ao mesmo tempo; portanto, após 5 minutos desde a chamada para `ReceiveMessages` , todas as mensagens que não foram excluídas ficarão visíveis novamente.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessages":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

O exemplo de código a seguir usa o método [GetMessages](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet-legacy&preserve-view=true) para obter 20 mensagens em uma chamada. Em seguida, ele processa cada mensagem usando um loop `foreach`. Ele também define o tempo limite de invisibilidade de cinco minutos para cada mensagem. Observe que os 5 minutos começam para todas as mensagens ao mesmo tempo; portanto, após 5 minutos desde a chamada para `GetMessages` , todas as mensagens que não foram excluídas ficarão visíveis novamente.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

---

## <a name="get-the-queue-length"></a>Obter o tamanho da fila

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Você pode obter uma estimativa do número de mensagens em uma fila. O método [GetProperties](/dotnet/api/azure.storage.queues.queueclient.getproperties) solicita ao serviço fila recuperar as propriedades da fila, incluindo a contagem de mensagens. A propriedade [ApproximateMessagesCount](/dotnet/api/azure.storage.queues.models.queueproperties.approximatemessagescount) contém o número aproximado de mensagens na fila. Esse número não é menor do que o número real de mensagens na fila, mas pode ser maior.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_GetQueueLength":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Você pode obter uma estimativa do número de mensagens em uma fila. O método [fetchattributes](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet-legacy&preserve-view=true) solicita que o serviço fila recupere os atributos da fila, incluindo a contagem de mensagens. A propriedade [ApproximateMessageCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet-legacy&preserve-view=true) retorna o último valor recuperado pelo `FetchAttributes` método, sem chamar o serviço fila.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Fetch the queue attributes.
queue.FetchAttributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

---

## <a name="delete-a-queue"></a>Excluir uma fila

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Para excluir uma fila e todas as mensagens que ela contém, chame o método [Delete](/dotnet/api/azure.storage.queues.queueclient.delete) no objeto fila.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DeleteQueue":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Para excluir uma fila e todas as mensagens que ela contém, chame o método [Delete](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet-legacy&preserve-view=true) no objeto fila.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Delete the queue.
queue.Delete();
```

---

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de Fila, siga estes links para saber mais sobre tarefas de armazenamento mais complexas.

- Consulte a documentação de referência do serviço Fila para obter detalhes completos sobre as APIs disponíveis:
  - [Referência à Biblioteca de Cliente de Armazenamento para .NET](/dotnet/api/overview/azure/storage)
  - [Referência da API REST](/rest/api/storageservices/)
- Consulte outros guias de recursos para obter informações sobre opções adicionais para armazenar dados no Azure.
  - [Introdução ao armazenamento de Tabelas do Azure usando o .NET](../../cosmos-db/tutorial-develop-table-dotnet.md) para armazenar dados estruturados.
  - [Introdução ao armazenamento de Blobs do Azure usando o .NET](../blobs/storage-quickstart-blobs-dotnet.md) para armazenar dados não estruturados.
  - [Conectar-se ao Banco de Dados SQL usando .NET (C#)](../../azure-sql/database/connect-query-dotnet-core.md) para armazenar dados relacionais.
- Saiba como simplificar o código que você escreve para trabalhar com o Armazenamento do Azure usando o [SDK do Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).