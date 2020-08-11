---
title: Práticas recomendadas para melhorar o desempenho usando o barramento de serviço do Azure
description: Descreve como usar o Barramento de Serviço para otimizar o desempenho na troca de mensagens agenciadas.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: a81e6fa1c6097f46bbfa3016beb1b7780ad3c351
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88065294"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Práticas recomendadas para melhorias de desempenho usando o Sistema de Mensagens do Barramento de Serviço

Este artigo descreve como usar o Barramento de Serviço do Azure para otimizar o desempenho na troca de mensagens agenciadas. A primeira parte deste artigo descreve os diferentes mecanismos oferecidos para ajudar a melhorar o desempenho. A segunda parte oferece orientação sobre como usar o Barramento de Serviço de uma maneira que possa oferecer o melhor desempenho em um determinado cenário.

Ao longo deste artigo, o termo "cliente" refere-se a qualquer entidade que acesse o Barramento de Serviço. Um cliente pode assumir a função de um remetente ou de um receptor. O termo "remetente" é usado para um cliente de tópico ou fila de Barramento de Serviço que envia mensagens para uma assinatura de tópico ou fila de Barramento de Serviço. O termo "receptor" refere-se a um cliente de fila ou de assinatura do Barramento de Serviço que recebe mensagens de uma fila ou uma assinatura do Barramento de Serviço.

Estas seções apresentam vários conceitos usados pelo Barramento de Serviço para melhorar o desempenho.

## <a name="protocols"></a>Protocolos

O Barramento de Serviço permite que os clientes enviem e recebam mensagens por meio de um destes três protocolos:

1. Advanced Message Queuing Protocol (AMQP)
2. Protocolo do sistema de mensagens do Barramento de Serviço (SBMP)
3. Protocolo HTTP

O AMQP é o mais eficiente, pois mantém a conexão com o barramento de serviço. Ele também implementa o envio em lote e a pré-busca. A menos que mencionado explicitamente, todo o conteúdo deste artigo supõe o uso do AMQP ou do SBMP.

> [!IMPORTANT]
> O SBMP só está disponível para .NET Framework. AMQP é o padrão para .NET Standard.

## <a name="choosing-the-appropriate-service-bus-net-sdk"></a>Escolhendo o SDK do .NET do barramento de serviço apropriado

Há dois SDKs do .NET do barramento de serviço do Azure com suporte. Suas APIs são muito semelhantes e podem confundir qual delas escolher. Consulte a tabela a seguir para ajudar a orientar sua decisão. Sugerimos o SDK Microsoft. Azure. ServiceBus, pois ele é mais moderno, com bom desempenho e é compatível com plataforma cruzada. Além disso, ele dá suporte a AMQP sobre WebSockets e faz parte da coleção do SDK do .NET do Azure de projetos de software livre.

| Pacote NuGet | Namespaces primários | Plataforma (ões) mínima (s) | Protocolos(s) |
|---------------|----------------------|---------------------|-------------|
| <a href="https://www.nuget.org/packages/Microsoft.Azure.ServiceBus" target="_blank">Microsoft. Azure. ServiceBus<span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.Azure.ServiceBus`<br>`Microsoft.Azure.ServiceBus.Management` | .NET Core 2.0<br>.NET Framework 4.6.1<br>Mono 5.4<br>Xamarin.iOS 10.14<br>Xamarin.Mac 3.8<br>Xamarin.Android 8.0<br>Plataforma Universal do Windows 10.0.16299 | AMQP<br>HTTP |
| <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus" target="_blank">WindowsAzure. ServiceBus<span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.ServiceBus`<br>`Microsoft.ServiceBus.Messaging` | .NET Framework 4.6.1 | AMQP<br>SBMP<br>HTTP |

Para obter mais informações sobre o suporte mínimo à plataforma .NET Standard, consulte [suporte à implementação do .net](/dotnet/standard/net-standard#net-implementation-support).

## <a name="reusing-factories-and-clients"></a>Reutilizando fábricas e clientes

# <a name="microsoftazureservicebus-sdk"></a>[SDK do Microsoft. Azure. ServiceBus](#tab/net-standard-sdk)

Os objetos de cliente do barramento de serviço, como implementações de [`IQueueClient`][QueueClient] ou [`IMessageSender`][MessageSender] , devem ser registrados para injeção de dependência como singletons (ou instanciadas uma vez e compartilhadas). É recomendável que você não feche fábricas de mensagens ou os clientes de fila, de tópico e de assinatura depois de enviar uma mensagem e então recriá-los ao enviar a próxima mensagem. Fechar uma fábrica do sistema de mensagens exclui a conexão com o serviço Barramento de Serviço e uma nova conexão é estabelecida na recriação da fábrica. O estabelecimento de uma conexão é uma operação cara que você pode evitar usando o mesmo alocador e objetos de cliente para diversas operações. Você pode usar esses objetos de cliente com segurança em operações assíncronas simultâneas e de vários threads.

# <a name="windowsazureservicebus-sdk"></a>[SDK do WindowsAzure. ServiceBus](#tab/net-framework-sdk)

Os objetos de cliente do barramento de serviço, como `QueueClient` ou `MessageSender` , são criados por meio de um objeto [MessagingFactory][MessagingFactory] , que também fornece gerenciamento interno de conexões. É recomendável que você não feche fábricas de mensagens ou os clientes de fila, de tópico e de assinatura depois de enviar uma mensagem e então recriá-los ao enviar a próxima mensagem. Fechar uma fábrica do sistema de mensagens exclui a conexão com o serviço Barramento de Serviço e uma nova conexão é estabelecida na recriação da fábrica. O estabelecimento de uma conexão é uma operação cara que você pode evitar usando o mesmo alocador e objetos de cliente para diversas operações. Você pode usar esses objetos de cliente com segurança em operações assíncronas simultâneas e de vários threads.

---

## <a name="concurrent-operations"></a>Operações simultâneas

A execução de uma operação (enviar, receber, excluir etc.) leva algum tempo. Esse tempo inclui o processamento da operação pelo serviço do barramento de serviço, além da latência da solicitação e da resposta. Para aumentar o número de operações por hora, elas devem ser executadas simultaneamente.

O cliente agenda operações simultâneas realizando operações assíncronas. A próxima solicitação é iniciada antes que a solicitação anterior seja concluída. O snippet de código a seguir é um exemplo de uma operação de envio assíncrono:

# <a name="microsoftazureservicebus-sdk"></a>[SDK do Microsoft. Azure. ServiceBus](#tab/net-standard-sdk)

```csharp
var messageOne = new Message(body);
var messageTwo = new Message(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

# <a name="windowsazureservicebus-sdk"></a>[SDK do WindowsAzure. ServiceBus](#tab/net-framework-sdk)

```csharp
var messageOne = new BrokeredMessage(body);
var messageTwo = new BrokeredMessage(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

---

O código a seguir é um exemplo de uma operação de recebimento assíncrono.

# <a name="microsoftazureservicebus-sdk"></a>[SDK do Microsoft. Azure. ServiceBus](#tab/net-standard-sdk)

Consulte o repositório GitHub para obter <a href="https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues" target="_blank">exemplos <span class="docon docon-navigate-external x-hidden-focus"></span> de código-fonte </a>completo:

```csharp
var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);

static Task LogErrorAsync(Exception exception)
{
    Console.WriteLine(exception);
    return Task.CompletedTask;
};

receiver.RegisterMessageHandler(
    async (message, cancellationToken) =>
    {
        Console.WriteLine("Handle message");
        await receiver.CompleteAsync(message.SystemProperties.LockToken);
    },
    new MessageHandlerOptions(e => LogErrorAsync(e.Exception))
    {
        AutoComplete = false,
        MaxConcurrentCalls = 20
    });
```

O `MessageReceiver` objeto é instanciado com a cadeia de conexão, o nome da fila e um modo de recebimento de aparência. Em seguida, a `receiver` instância é usada para registrar o manipulador de mensagens.

# <a name="windowsazureservicebus-sdk"></a>[SDK do WindowsAzure. ServiceBus](#tab/net-framework-sdk)

Consulte o repositório GitHub para obter <a href="https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/SendersReceiversWithQueues" target="_blank">exemplos <span class="docon docon-navigate-external x-hidden-focus"></span> de código-fonte </a>completo:

```csharp
var factory = MessagingFactory.CreateFromConnectionString(connectionString);
var receiver = await factory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);

// Register the handler to receive messages asynchronously
receiver.OnMessageAsync(
    async message =>
    {
        Console.WriteLine("Handle message");
        await message.CompleteAsync();
    },
    new OnMessageOptions
    {
        AutoComplete = false,
        MaxConcurrentCalls = 20
    });
```

O `MessagingFactory` cria um `factory` objeto a partir da cadeia de conexão. Com a `factory` instância, um `MessageReceiver` é instanciado. Em seguida, a `receiver` instância é usada para registrar o manipulador na mensagem.

---

## <a name="receive-mode"></a>Modo de recebimento

Ao criar um cliente de fila ou de assinatura, você poderá especificar um modo de recebimento: *Bloqueio de pico* ou *Receber e Excluir*. Modo de recebimento padrão é `PeekLock`. Ao operar no modo padrão, o cliente envia uma solicitação para receber uma mensagem do barramento de serviço. Depois que o cliente tiver recebido a mensagem, ele enviará uma solicitação para concluir a mensagem.

Ao definir o modo de recebimento para `ReceiveAndDelete`, as duas etapas são combinadas em uma única solicitação. Essas etapas reduzem o número total de operações e podem melhorar a taxa de transferência geral da mensagem. Este ganho de desempenho vem com o risco de perda de mensagens.

O Barramento de Serviço não dá suporte a transações para operações de receber e excluir. Além disso, a semântica do bloqueio de pico é necessária para todos os cenários em que o cliente deseje adiar uma mensagem ou enviá-la para as [mensagens mortas](service-bus-dead-letter-queues.md).

## <a name="client-side-batching"></a>Envio em lote no lado do cliente

O envio em lote no lado do cliente permite que um cliente de fila ou de tópico atrase o envio de uma mensagem por um determinado período. Se o cliente enviar mensagens adicionais durante esse período, ele transmitirá as mensagens em um único lote. O envio em lote do lado do cliente também faz com que um cliente de fila ou assinatura agrupe em lote diversas solicitações **Concluir** em uma única solicitação. O envio em lote está disponível apenas para operações **Enviar** e **Concluir**. As operações síncronas são imediatamente enviadas para o serviço Barramento de Serviço. O envio em lote não ocorre para as operações de pico ou de recebimento, e também não ocorre entre clientes.

# <a name="microsoftazureservicebus-sdk"></a>[SDK do Microsoft. Azure. ServiceBus](#tab/net-standard-sdk)

A funcionalidade de envio em lote para o SDK do .NET Standard, ainda não expõe uma propriedade para manipular.

# <a name="windowsazureservicebus-sdk"></a>[SDK do WindowsAzure. ServiceBus](#tab/net-framework-sdk)

Por padrão, um cliente usa um intervalo de lote de 20 ms. Você pode alterar o intervalo de lote definindo a propriedade [BatchFlushInterval][BatchFlushInterval] antes de criar a fábrica de mensagens. Essa configuração afeta todos os clientes criados por essa fábrica.

Para desabilitar o envio em lote, defina a propriedade [BatchFlushInterval][BatchFlushInterval] como **TimeSpan.Zero**. Por exemplo:

```csharp
var settings = new MessagingFactorySettings
{
    NetMessagingTransportSettings =
    {
        BatchFlushInterval = TimeSpan.Zero
    }
};
var factory = MessagingFactory.Create(namespaceUri, settings);
```

O envio em lote não afeta o número de operações faturáveis do sistema de mensagens e está disponível somente para o protocolo de cliente do Barramento de Serviço usando a biblioteca [Microsoft.ServiceBus.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/). O protocolo HTTP não dá suporte ao envio em lote.

> [!NOTE]
> `BatchFlushInterval`A configuração garante que o envio em lote seja implícito da perspectiva do aplicativo. ou seja,; o aplicativo faz `SendAsync` e `CompleteAsync` chama e não faz chamadas de lote específicas.
>
> O envio em lote explícito do cliente pode ser implementado utilizando a chamada de método abaixo:
> ```csharp
> Task SendBatchAsync(IEnumerable<BrokeredMessage> messages);
> ```
> Aqui, o tamanho combinado das mensagens deve ser menor que o tamanho máximo suportado pelo tipo de preço.

---

## <a name="batching-store-access"></a>Acesso ao repositório do envio em lote

Para aumentar a taxa de transferência de uma fila, tópico ou assinatura, o Barramento de Serviço agrupa várias mensagens ao gravar em seu repositório interno. Se habilitada em uma fila ou tópico, a gravação de mensagens no repositório ocorrerá em lote. Se habilitada em uma fila ou assinatura, a exclusão de mensagens no repositório ocorrerá em lote. Se o acesso ao repositório em lote estiver habilitado para uma entidade, o Barramento de Serviço atrasará uma operação de gravação do repositório em relação àquela entidade por até 20 ms.

> [!NOTE]
> Não há nenhum risco de perda de mensagens com o envio em lote, mesmo se houver uma falha de barramento de serviço ao final de um intervalo de envio em lote de 20 ms.

As operações de armazenamento adicionais que ocorrerem durante esse intervalo serão adicionadas ao lote. O acesso ao repositório em lote só afeta as operações **Enviar** e **Concluir**; as operações de recebimento não são afetadas. O acesso ao repositório em lote é uma propriedade em uma entidade. O envio em lote ocorrerá em todas as entidades que permitirem o acesso ao repositório em lote.

Quando uma nova fila, um novo tópico ou uma nova assinatura for criada, o acesso ao repositório em lote será habilitado por padrão.

# <a name="microsoftazureservicebus-sdk"></a>[SDK do Microsoft. Azure. ServiceBus](#tab/net-standard-sdk)

Para desabilitar o acesso ao repositório em lote, você precisará de uma instância do `ManagementClient` . Crie uma fila de uma descrição da fila que defina a `EnableBatchedOperations` propriedade como `false` .

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = await managementClient.CreateQueueAsync(queueDescription);
```

Para saber mais, consulte o seguinte:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[SDK do WindowsAzure. ServiceBus](#tab/net-framework-sdk)

Para desabilitar o acesso ao repositório em lote, você precisará de uma instância do `NamespaceManager` . Crie uma fila de uma descrição da fila que defina a `EnableBatchedOperations` propriedade como `false` .

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = namespaceManager.CreateQueue(queueDescription);
```

Para saber mais, consulte o seguinte:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

O acesso ao repositório em lote não afeta o número de operações faturáveis do sistema de mensagens faturáveis e é uma propriedade de uma fila, um tópico ou uma assinatura. É independente do modo de recebimento e do protocolo usado entre um cliente e o serviço Barramento de Serviço.

## <a name="prefetching"></a>Pré-busca

A [pré-busca](service-bus-prefetch.md) permite que o cliente de fila ou de assinatura carregue mensagens adicionais do serviço ao executar uma operação de recebimento. O cliente armazena essas mensagens em um cache local. O tamanho do cache é determinado pelas `QueueClient.PrefetchCount` `SubscriptionClient.PrefetchCount` Propriedades ou. Cada cliente que permite a pré-busca mantém seu próprio cache. Um cache não é compartilhado entre os clientes. Se o cliente iniciar uma operação de recebimento e se o cache estiver vazio, o serviço transmitirá um lote de mensagens. O tamanho do lote é igual ao tamanho do cache ou a 256 KB, o que for menor. Se o cliente iniciar uma operação de recebimento e se o cache contiver uma mensagem, a mensagem será retirada do cache.

Quando uma mensagem for pré-buscada, ela será bloqueada pelo serviço. Com o bloqueio, a mensagem pré-buscada não pode ser recebida por um receptor diferente. Se o receptor não puder concluir a mensagem antes da expiração do bloqueio, a mensagem ficará disponível para outros destinatários. A cópia pré-buscada da mensagem permanecerá no cache. O receptor que consumir a cópia armazenada em cache expirada receberá uma exceção ao tentar concluir essa mensagem. Por padrão, o bloqueio da mensagem expira após 60 segundos. Esse valor pode ser estendido para 5 minutos. Para impedir o consumo de mensagens expiradas, o tamanho do cache sempre deverá ser menor do que o número de mensagens que podem ser consumidas por um cliente no intervalo de tempo limite de bloqueio.

Ao usar a expiração de bloqueio padrão de 60 segundos, um bom valor para `PrefetchCount` é 20 vezes as taxas de processamento máximo de todos os destinatários da fábrica. Por exemplo, um alocador cria três receptores e cada receptor pode processar até dez mensagens por segundo. A contagem de pré-busca não deve exceder 20 X 3 X 10 = 600. Por padrão, `PrefetchCount` é definido como 0, o que significa que nenhuma mensagem adicional é buscada do serviço.

A pré-busca de mensagens aumenta a taxa de transferência geral de uma fila ou uma assinatura porque reduz o número geral de operações de mensagem, ou as viagens de ida e volta. A busca da primeira mensagem, no entanto, demorará mais (devido ao tamanho maior da mensagem). O recebimento de mensagens pré-buscadas será mais rápido porque essas mensagens já foram baixadas pelo cliente.

A propriedade de vida útil (TTL) de uma mensagem é verificada pelo servidor no momento em que o servidor envia a mensagem para o cliente. O cliente não verifica a propriedade TTL da mensagem quando a mensagem é recebida. Em vez disso, a mensagem pode ser recebida mesmo que a TTL da mensagem tenha passado enquanto a mensagem fosse armazenada em cache pelo cliente.

A pré-busca não afeta o número de operações faturáveis do sistema de mensagens e está disponível somente para o protocolo de cliente do Barramento de Serviço. O protocolo HTTP não dá suporte à pré-busca. A pré-busca está disponível para as operações de recebimento síncrono e assíncrono.

# <a name="microsoftazureservicebus-sdk"></a>[SDK do Microsoft. Azure. ServiceBus](#tab/net-standard-sdk)

Para obter mais informações, consulte as seguintes `PrefetchCount` Propriedades:

* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[SDK do WindowsAzure. ServiceBus](#tab/net-framework-sdk)

Para obter mais informações, consulte as seguintes `PrefetchCount` Propriedades:

* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

## <a name="prefetching-and-receivebatch"></a>Pré-busca e ReceiveBatch

> [!NOTE]
> Esta seção se aplica somente ao SDK do WindowsAzure. ServiceBus, pois o SDK do Microsoft. Azure. ServiceBus não expõe funções em lote.

Embora os conceitos de pré-busca de várias mensagens tenham uma semântica semelhante ao processamento de mensagens em um lote ( `ReceiveBatch` ), há algumas pequenas diferenças que devem ser mantidas em mente ao aproveitá-las juntas.

Pré-busca é uma configuração (ou modo) no cliente ( `QueueClient` e `SubscriptionClient` ) e `ReceiveBatch` é uma operação (que tem semântica de solicitação-resposta).

Ao usá-los juntos, considere os seguintes casos:

* A pré-busca deve ser maior ou igual ao número de mensagens que você espera receber `ReceiveBatch` .
* A pré-busca pode ser até n/3 vezes o número de mensagens processadas por segundo, em que n é a duração de bloqueio padrão.

Há alguns desafios com uma abordagem aproximada (ou seja, manter a contagem de pré-busca muito alta), porque isso implica que a mensagem está bloqueada para um receptor específico. A recomendação é testar os valores de prefetch entre os limites mencionados acima e empiricamente identificar o que se ajusta.

## <a name="multiple-queues"></a>Várias filas

Se a carga esperada não puder ser tratada por uma única fila ou tópico, você deverá usar várias entidades de mensagens. Ao usar várias entidades, crie um cliente dedicado para cada entidade em vez de usar o mesmo cliente para todas as entidades.

## <a name="development-and-testing-features"></a>Recursos de desenvolvimento e teste

> [!NOTE]
> Esta seção se aplica somente ao SDK do WindowsAzure. ServiceBus, pois o SDK do Microsoft. Azure. ServiceBus não expõe essa funcionalidade.

O barramento de serviço tem um recurso, usado especificamente para desenvolvimento, que **nunca deve ser usado em configurações de produção**: [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] .

Quando novas regras ou filtros são adicionados ao tópico, você pode usar [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] o para verificar se a nova expressão de filtro está funcionando conforme o esperado.

## <a name="scenarios"></a>Cenários

As seções a seguir descrevem cenários típicos de mensagens e as configurações preferenciais do Barramento de Serviço. As taxas de transferência são classificadas como pequena (menos de 1 mensagem/segundo), moderada (1 mensagem/segundo ou mais, mas menos de 100 mensagens por segundo) e alta (100 mensagens/segundo ou mais). O número de clientes é classificado como pequeno (5 ou menos), moderado (de 5 a 20) e grande (mais de 20).

### <a name="high-throughput-queue"></a>Fila de alta taxa de transferência

Meta: maximizar a taxa de transferência de uma única fila. O número de remetentes e de receptores é pequeno.

* Para aumentar a taxa geral de envio para a fila, use diversas fábricas de mensagens para criar remetentes. Para cada remetente, use operações assíncronas ou vários threads.
* Para aumentar a taxa geral de recebimento da fila, use diversas fábricas de mensagens para criar receptores.
* Use operações assíncronas para tirar proveito do envio em lote no lado do cliente.
* Defina o intervalo de envio em lote como 50 ms para reduzir o número de transmissões de protocolo de cliente do Barramento de Serviço. Se forem usados vários remetentes, aumente o intervalo de envio em lote para 100 ms.
* Deixe o acesso ao repositório em lote habilitado. Esse acesso aumenta a taxa geral em que as mensagens podem ser gravadas na fila.
* Defina a contagem de pré-busca como 20 vezes as taxas máximas de processamento de todos os receptores de uma fábrica. Essa contagem reduz o número de transmissões de protocolo de cliente do Barramento de Serviço.

### <a name="multiple-high-throughput-queues"></a>Várias filas de alta taxa de transferência

Meta: maximize a taxa de transferência geral de diversas filas. A taxa de transferência de uma fila individual é moderada ou alta.

Para obter a taxa de transferência máxima em várias filas, use as configurações descritas para maximizar a taxa de transferência de uma única fila. Além disso, use fábricas diferentes para criar clientes que enviem ou recebam de filas diferentes.

### <a name="low-latency-queue"></a>Fila de baixa latência

Meta: minimize a latência de ponta a ponta de uma fila ou um tópico. O número de remetentes e de receptores é pequeno. A taxa de transferência da fila é pequena ou moderada.

* Desabilite o envio em lote no lado do cliente. O cliente envia uma mensagem imediatamente.
* Desabilite o acesso ao repositório em lote. O serviço grava imediatamente a mensagem no repositório.
* Se estiver usando um único cliente, defina a contagem de pré-busca como 20 vezes a taxa de processamento do receptor. Se várias mensagens chegarem à fila ao mesmo tempo, o protocolo de cliente do Barramento de Serviço transmite todas ao mesmo tempo. Quando o cliente receber a próxima mensagem, essa mensagem já estará no cache local. O cache deve ser pequeno.
* Se você estiver usando vários clientes, defina a contagem de pré-busca como 0. Ao definir a contagem, o segundo cliente poderá receber a segunda mensagem enquanto o primeiro cliente ainda estiver processando a primeira mensagem.

### <a name="queue-with-a-large-number-of-senders"></a>Fila com um grande número de remetentes

Meta: maximizar a taxa de transferência de uma fila ou tópico com um grande número de remetentes. Cada remetente envia mensagens com uma taxa moderada. O número de receptores é pequeno.

O barramento de serviço permite até 1000 conexões simultâneas com uma entidade de mensagens. Esse limite é imposto no nível de namespace e tópicos/filas/assinaturas são controlados pelo limite de conexões simultâneas por namespace. Para filas, esse número é compartilhado entre remetentes e receptores. Se todas as 1000 conexões forem obrigatórias para os remetentes, substitua a fila por um tópico e uma assinatura única. Um tópico aceita até 1000 conexões simultâneas dos remetentes, enquanto a assinatura aceita 1000 conexões simultâneas adicionais dos receptores. Se mais de 1000 remetentes simultâneos forem necessários, os remetentes deverão enviar mensagens para o protocolo do Barramento de Serviço via HTTP.

Para maximizar a taxa de transferência, execute as etapas a seguir:

* Se cada remetente residir em um processo diferente, use somente uma única fábrica por processo.
* Use operações assíncronas para tirar proveito do envio em lote no lado do cliente.
* Use o intervalo de lote padrão de 20 ms para reduzir o número de transmissões de protocolo de cliente do Barramento de Serviço.
* Deixe o acesso ao repositório em lote habilitado. Esse acesso aumenta a taxa geral em que as mensagens podem ser gravadas na fila ou no tópico.
* Defina a contagem de pré-busca como 20 vezes as taxas máximas de processamento de todos os receptores de uma fábrica. Essa contagem reduz o número de transmissões de protocolo de cliente do Barramento de Serviço.

### <a name="queue-with-a-large-number-of-receivers"></a>Fila com um grande número de receptores

Meta: maximizar a taxa de recebimento de uma fila ou de uma assinatura com um grande número de receptores. Cada receptor recebe mensagens a uma taxa moderada. O número de remetentes é pequeno.

O Barramento de Serviço permite até 1000 conexões simultâneas a uma entidade. Se uma fila exigir mais de 1.000 receptores, substitua a fila por um tópico e várias assinaturas. Cada assinatura pode dar suporte a até 1000 conexões simultâneas. Como alternativa, os receptores poderão acessar a fila por meio do protocolo HTTP.

Para maximizar a taxa de transferência, faça o seguinte:

* Se cada receptor residir em um processo diferente, use somente uma única fábrica por processo.
* Os receptores poderão usar operações síncronas ou assíncronas. Dada a taxa de recebimento moderada de um receptor individual, o envio em lote no lado do cliente de uma solicitação Concluir não afetará a taxa de transferência do receptor.
* Deixe o acesso ao repositório em lote habilitado. Esse acesso reduz a carga geral da entidade. Isso também reduz a taxa geral em que as mensagens podem ser gravadas na fila ou no tópico.
* Defina a contagem de pré-busca como um valor pequeno (por exemplo, PrefetchCount = 10). Essa contagem impede que os receptores fiquem ociosos enquanto outros receptores tenham grandes quantidades de mensagens armazenadas em cache.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Tópico com um pequeno número de assinaturas

Meta: maximizar a taxa de transferência de um tópico com um pequeno número de assinaturas. Uma mensagem é recebida por muitas assinaturas, o que significa que a taxa de recebimento combinada em todas as assinaturas é maior do que a taxa de envio. O número de remetentes é pequeno. O número de receptores por assinatura é pequeno.

Para maximizar a taxa de transferência, faça o seguinte:

* Para aumentar a taxa geral de envio para o tópico, use diversas fábricas de mensagens para criar remetentes. Para cada remetente, use operações assíncronas ou vários threads.
* Para aumentar a taxa geral de recebimento de uma assinatura, use diversas fábricas de mensagens para criar receptores. Para cada receptor, use operações assíncronas ou vários threads.
* Use operações assíncronas para tirar proveito do envio em lote no lado do cliente.
* Use o intervalo de lote padrão de 20 ms para reduzir o número de transmissões de protocolo de cliente do Barramento de Serviço.
* Deixe o acesso ao repositório em lote habilitado. Esse acesso aumenta a taxa geral em que as mensagens podem ser gravadas no tópico.
* Defina a contagem de pré-busca como 20 vezes as taxas máximas de processamento de todos os receptores de uma fábrica. Essa contagem reduz o número de transmissões de protocolo de cliente do Barramento de Serviço.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Tópico com um grande número de assinaturas

Meta: maximizar a taxa de transferência de um tópico com um grande número de assinaturas. Uma mensagem é recebida por várias assinaturas, o que significa que a taxa de recebimento combinada em todas as assinaturas é muito maior do que a taxa de envio. O número de remetentes é pequeno. O número de receptores por assinatura é pequeno.

Os tópicos com um grande número de assinaturas normalmente terão uma taxa de transferência geral baixa se todas as mensagens forem roteadas a todas as assinaturas. Essa taxa de transferência baixa é causada pelo fato de que cada mensagem é recebida várias vezes e todas as mensagens contidas em um tópico e todas as suas assinaturas são armazenadas no mesmo repositório. Supõe-se que o número de remetentes e o número de receptores por assinatura é baixo. O Barramento de Serviço dá suporte a até 2.000 assinaturas por tópico.

Para maximizar a taxa de transferência, experimente as etapas a seguir:

* Use operações assíncronas para tirar proveito do envio em lote no lado do cliente.
* Use o intervalo de lote padrão de 20 ms para reduzir o número de transmissões de protocolo de cliente do Barramento de Serviço.
* Deixe o acesso ao repositório em lote habilitado. Esse acesso aumenta a taxa geral em que as mensagens podem ser gravadas no tópico.
* Defina a contagem de pré-busca como 20 vezes a taxa de recebimento esperada em segundos. Essa contagem reduz o número de transmissões de protocolo de cliente do Barramento de Serviço.

<!-- .NET Standard SDK, Microsoft.Azure.ServiceBus -->
[QueueClient]: /dotnet/api/microsoft.azure.servicebus.queueclient
[MessageSender]: /dotnet/api/microsoft.azure.servicebus.core.messagesender

<!-- .NET Framework SDK, Microsoft.Azure.ServiceBus -->
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[BatchFlushInterval]: /dotnet/api/microsoft.servicebus.messaging.messagesender.batchflushinterval
[ForcePersistence]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence
[EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnableFiltering]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing

<!-- Local links -->
[Partitioned messaging entities]: service-bus-partitioning.md