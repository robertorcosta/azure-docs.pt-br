---
title: Entidades duráveis – Azure Functions
description: Saiba o que são Entidades Duráveis e como usá-las na extensão Durable Functions do Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 864a641968268c439c65996998cbb822746b96f9
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838994"
---
# <a name="entity-functions-preview"></a>Funções de entidade (versão prévia)

As funções de entidade definem operações para leitura e atualização de pequenas partes de estado, conhecidas como *entidades duráveis*. Como as funções de orquestrador, as funções de entidade são funções com um tipo de gatilho especial, o *gatilho de entidade*. Ao contrário das funções de orquestrador, as funções de entidade não têm nenhuma restrição de código específica. As funções de entidade também gerenciam o estado explicitamente, em vez de representar implicitamente o estado por meio do fluxo de controle.

> [!NOTE]
> As funções de entidade e a funcionalidade relacionada estão disponíveis apenas nas Durable Functions 2.0 e superior. As funções de entidade estão em versão prévia pública no momento.

## <a name="entity-identity"></a>Identidade da entidade

As entidades (às vezes denominadas *instâncias* de entidade) são acessadas por meio de um identificador exclusivo, a *ID de entidade*. Uma ID de entidade é um par de cadeias de caracteres que identifica exclusivamente uma instância de entidade. Ele consiste em:

* Um **nome de entidade**: um nome que identifica o tipo da entidade (por exemplo, "Counter").
* Uma **chave de entidade**: uma cadeia de caracteres que identifica exclusivamente a entidade entre todas as outras entidades com o mesmo nome (por exemplo, um GUID).

Por exemplo, uma função de entidade *counter* pode ser usada para manter a pontuação em um jogo online. Cada instância do jogo terá uma ID de entidade exclusiva, como `@Counter@Game1`, `@Counter@Game2` e assim por diante. Todas as operações que direcionam uma entidade específica exigem a especificação de uma ID de entidade como um parâmetro.

## <a name="programming-models"></a>Modelos de programação

As entidades duráveis dão suporte a dois modelos de programação diferentes. O primeiro modelo é um modelo "funcional" dinâmico em que a entidade é definida por uma única função. O segundo modelo é um modelo orientado por objeto em que a entidade é definida por uma classe e métodos. Esses modelos e os modelos de programação para interagir com entidades são descritos nas próximas seções.

### <a name="defining-entities"></a>Definir entidades

Há dois modelos de programação opcionais para criar entidades duráveis. O código a seguir é um exemplo de uma entidade *Counter* simples implementada como uma função padrão. Essa função define três *operações*, `add`, `reset` e `get`, cada uma delas opera em um valor de estado inteiro, `currentValue`.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += amount;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

Esse modelo funciona melhor para implementações de entidades simples ou implementações que têm um conjunto dinâmico de operações. No entanto, também é possível usar um modelo de programação baseado em classe útil para entidades estáticas, mas que tem implementações mais complexas. O exemplo a seguir é uma implementação equivalente da entidade `Counter` usando métodos e classes.

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;
    
    public void Reset() => this.CurrentValue = 0;
    
    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

> [!NOTE]
> O método de ponto de entrada de função com o atributo `[FunctionName]` *deve* ser declarado `static` ao usar classes de entidade. Métodos de ponto de entrada não estáticos podem resultar em várias inicializações de objeto e potencialmente outros comportamentos indefinidos.

No modelo de programação baseado em classe, o objeto `IDurableEntityContext` está disponível na propriedade estática `Entity.Current`.

O modelo baseado em classe é semelhante ao modelo de programação popularizado pelo [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/). Nesse modelo, um tipo de entidade é definido como uma classe do .NET. Cada método da classe é uma operação que pode ser invocada por um cliente externo. Ao contrário do Orleans, no entanto, as interfaces do .NET são opcionais. O exemplo *Counter* anterior não usou uma interface, mas ela ainda pode ser invocada por meio de outras funções ou por meio de chamadas à API HTTP.

> [!NOTE]
> As funções de gatilho de entidade estão disponíveis nas Durable Functions 2.0 e superior. No momento, as funções de gatilho de entidade estão disponíveis somente para aplicativos de funções do .NET.

### <a name="accessing-entities-from-clients"></a>Acessar entidades de clientes

Entidades duráveis podem ser invocadas ou consultadas com base em funções comuns – também conhecidas como *funções de cliente* – usando a [associação de saída do cliente de entidade](durable-functions-bindings.md#entity-client). O exemplo a seguir mostra uma função disparada por fila *sinalizando* uma entidade que usa essa associação.

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

> [!NOTE]
> As funções do .NET dão suporte a métodos menos rígidos e fortemente tipados para sinalizar entidades. Confira a documentação de referência da [associação de cliente de entidade](durable-functions-bindings.md#entity-client-usage) para obter detalhes.

O termo *sinal* significa que a invocação de API de entidade é unidirecional e assíncrona. Não é possível que uma *função de cliente* saiba quando a entidade processou a operação, nem é possível que uma função de entidade retorne um valor a uma função de cliente. O sistema de mensagens unidirecional baseado em fila era uma opção de design intencional para as Entidades Duráveis priorizarem a durabilidade em vez do desempenho. Essa opção de design é uma das compensações das Entidades Duráveis comparada com outras tecnologias semelhantes. No momento, apenas orquestrações são capazes de lidar com valores de retorno de entidades, conforme descrito na próxima seção.

As funções de cliente também podem consultar o estado de entidades, conforme mostrado no seguinte exemplo:

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    JObject state = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, state);
}
```

As consultas de estado de entidade são enviadas para o armazenamento de acompanhamento durável e retornam o estado *persistido* mais recentemente da entidade. É possível que o estado retornado possa ser obsoleto comparado com o estado na memória da entidade. Somente orquestrações podem ler o estado na memória de uma entidade, conforme descrito na seção a seguir.

### <a name="accessing-entities-from-orchestrations"></a>Acessar entidades de orquestrações

As funções de orquestrador podem acessar entidades usando APIs na [associação de gatilho de orquestração](durable-functions-bindings.md#orchestration-trigger). As funções de orquestrador podem escolher entre comunicação unidirecional (acionar e esquecer, também conhecida como *sinalização*) e comunicação bidirecional (solicitação e resposta, também conhecida como *chamada*). O código de exemplo a seguir mostra uma função funções de orquestrador *chamando* e *sinalizando* uma entidade *Counter*.

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Synchronous call to the entity which returns a value - will await a response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // Asynchronous call which updates the value - will not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

Somente orquestrações são capazes de chamar entidades e obter uma resposta, que poderia ser um valor retornado ou uma exceção. As funções de cliente que usam a [associação de cliente](durable-functions-bindings.md#entity-client) só podem *sinalizar* entidades.

> [!NOTE]
> Chamar uma entidade de uma função funções de orquestrador é semelhante a chamar uma [função de atividade](durable-functions-types-features-overview.md#activity-functions) de uma função funções de orquestrador. A principal diferença é que as funções de entidade são objetos duráveis com um endereço (a *ID da entidade*) e dão suporte à especificação de um nome de operação. As funções de atividade, por outro lado, são sem estado e não têm o conceito das operações.

### <a name="dependency-injection-in-entity-classes-net"></a>Injeção de dependência em classes de entidade (.NET)

As classes de entidade dão suporte à [Injeção de Dependência do Azure Functions](../functions-dotnet-dependency-injection.md). O exemplo a seguir demonstra como registrar um serviço `IHttpClientFactory` em uma entidade baseada em classe.

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
        }
    }
}
```

O snippet a seguir demonstra como incorporar o serviço injetado à sua classe de entidade.

```csharp
public class HttpEntity
{
    private readonly HttpClient client;

    public class HttpEntity(IHttpClientFactory factory)
    {
        this.client = factory.CreateClient();
    }

    public Task<int> GetAsync(string url)
    {
        using (var response = await this.client.GetAsync(url))
        {
            return (int)response.StatusCode;
        }
    }

    // The function entry point must be declared static
    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> Diferentemente do que ocorre ao usar uma injeção de construtor no Azure Functions do .NET regular, o método de ponto de entrada de funções para entidades baseadas em classe *deve* ser declarado `static`. Declarar um ponto de entrada de função não estática pode causar conflitos entre o inicializador de objeto normal do Azure Functions e o inicializador de objeto de Entidades Duráveis.

### <a name="bindings-in-entity-classes-net"></a>Associações em classes de entidade (.NET)

Diferentemente das funções regulares, os métodos de classe de entidade não têm acesso direto a associações de entrada e saída. Em vez disso, os dados de associação devem ser capturados na declaração da função de ponto de entrada e passados para o método `DispatchAsync<T>`. Os objetos passados para `DispatchAsync<T>` serão passados automaticamente para o construtor da classe de entidade como um argumento.

O exemplo a seguir mostra como uma referência de `CloudBlobContainer` da [associação de entrada de blobs](../functions-bindings-storage-blob.md#input) pode ser disponibilizada em uma entidade baseada em classe.

```csharp
public class BlobBackedEntity
{
    private readonly CloudBlobContainer container;

    public BlobBackedEntity(CloudBlobContainer container)
    {
        this.container = container;
    }

    // ... entity methods can use this.container in their implementations ...
    
    [FunctionName(nameof(BlobBackedEntity))]
    public static Task Run(
        [EntityTrigger] IDurableEntityContext context,
        [Blob("my-container", FileAccess.Read)] CloudBlobContainer container)
    {
        // passing the binding object as a parameter makes it available to the
        // entity class constructor
        return context.DispatchAsync<BlobBackedEntity>(container);
    }
}
```

Para obter mais informações sobre associações no Azure Functions, confira a documentação [Gatilhos e Associações do Azure Functions](../functions-triggers-bindings.md).

## <a name="entity-coordination"></a>Coordenação de entidades

Pode haver ocasiões em que você precisa coordenar operações em várias entidades. Por exemplo, em um aplicativo bancário, você pode ter entidades que representam contas bancárias individuais. Ao transferir fundos de uma conta para outra, é necessário verificar se a conta de _origem_ tem fundos suficientes e se as atualizações nas contas de _origem_ e _destino_ são realizadas de maneira consistente transacionalmente.

### <a name="transfer-funds-example-in-c"></a>Exemplo de transferência de fundos em C#

O código de exemplo a seguir transfere fundos entre duas entidades de _conta_ usando uma função funções de orquestrador. A coordenação de atualizações de entidade requer o uso do método `LockAsync` para criar uma _seção crítica_ na orquestração:

> [!NOTE]
> Para simplificar, este exemplo reutiliza a entidade `Counter` definida anteriormente. No entanto, em um aplicativo real, seria melhor definir uma entidade `BankAccount` mais detalhada.

```csharp
// This is a method called by an orchestrator function
public static async Task<bool> TransferFundsAsync(
    string sourceId,
    string destinationId,
    int transferAmount,
    IDurableOrchestrationContext context)
{
    var sourceEntity = new EntityId(nameof(Counter), sourceId);
    var destinationEntity = new EntityId(nameof(Counter), destinationId);

    // Create a critical section to avoid race conditions.
    // No operations can be performed on either the source or
    // destination accounts until the locks are released.
    using (await context.LockAsync(sourceEntity, destinationEntity))
    {
        ICounter sourceProxy = 
            context.CreateEntityProxy<ICounter>(sourceEntity);
        ICounter destinationProxy =
            context.CreateEntityProxy<ICounter>(destinationEntity);

        int sourceBalance = await sourceProxy.Get();

        if (sourceBalance >= transferAmount)
        {
            await sourceProxy.Add(-transferAmount);
            await destinationProxy.Add(transferAmount);

            // the transfer succeeded
            return true;
        }
        else
        {
            // the transfer failed due to insufficient funds
            return false;
        }
    }
}
```

No .NET, `LockAsync` retorna um `IDisposable` que encerra a seção crítica quando descartado. Esse resultado `IDisposable` pode ser usado junto com um bloco `using` para obter uma representação sintática da seção crítica.

No exemplo anterior, uma função funções de orquestrador transferiu fundos de uma entidade de _origem_ para uma entidade de _destino_. O método `LockAsync` bloqueou as entidades de conta de _origem_ e de _destino_. Com esse bloqueio, nenhum outro cliente pôde consultar ou modificar o estado de qualquer conta até que a lógica da orquestração saísse da _seção crítica_ no fim da instrução `using`. Isso efetivamente evitou a possibilidade de a conta _de origem_ entrar no cheque especial.

### <a name="critical-section-behavior"></a>Comportamento da seção crítico

O método `LockAsync` cria uma _seção crítica_ em uma orquestração. Essas _seções críticas_ impedem que outras orquestrações façam alterações sobrepostas em um conjunto especificado de entidades. Internamente, a API `LockAsync` envia operações de "bloqueio" para as entidades e retorna quando recebe uma mensagem de resposta de "bloqueio adquirido" de cada uma dessas mesmas entidades. O *bloqueio* e o *desbloqueio* são operações internas compatíveis com todas as entidades.

Nenhuma operação de outros clientes é permitida em uma entidade enquanto ela está em um estado bloqueado. Esse comportamento verifica se apenas uma instância de orquestração pode bloquear uma entidade por vez. Se um chamador tentar invocar uma operação em uma entidade enquanto ela estiver bloqueada por uma orquestração, essa operação será colocada em uma *fila de operação pendente*. Nenhuma operação pendente será processada até depois que a orquestração em retenção libere seu bloqueio.

> [!NOTE] 
> Isso é um pouco diferente dos primitivos de sincronização usados na maioria das linguagens de programação, como a instrução `lock` em C#. Por exemplo, em C#, a instrução `lock` deve ser usada por todos os threads para garantir a sincronização apropriada em vários threads. Contudo, as entidades não exigem que todos os chamadores _bloqueiem_ explicitamente uma entidade. Se qualquer chamador bloquear uma entidade, todas as outras operações nessa entidade serão bloqueadas e enfileiradas atrás desse bloqueio.

Os bloqueios em entidades são duráveis. Portanto, eles persistirão mesmo se o processo de execução for reciclado. Os bloqueios são persistidos internamente como parte do estado durável de uma entidade.

### <a name="critical-section-restrictions"></a>Restrições da seção crítica

Impomos várias restrições sobre como as seções críticas podem ser usadas. Essas restrições servem para evitar deadlocks e reentrância.

* As seções críticas não podem ser aninhadas.
* As seções críticas não podem criar suborquestrações.
* As seções críticas podem chamar somente entidades que elas bloquearam.
* As seções críticas não podem chamar a mesma entidade usando várias chamadas paralelas.
* As seções críticas podem sinalizar apenas entidades que elas não bloquearam.

## <a name="comparison-with-virtual-actors"></a>Comparação com atores virtuais

Muitos recursos de Entidades Duráveis são inspirados pelo [modelo de ator](https://en.wikipedia.org/wiki/Actor_model). Se você já estiver familiarizado com atores, poderá reconhecer muitos conceitos descritos neste artigo. Em particular, as entidades duráveis são semelhantes aos [atores virtuais](https://research.microsoft.com/projects/orleans/) de várias maneiras:

* As entidades duráveis podem ser abordadas por meio de uma *ID de entidade*.
* As operações de entidade durável são executadas de maneira serial, uma por vez, para impedir condições de corrida.
* As entidades duráveis são criadas automaticamente quando são chamadas ou sinalizadas.
* Quando não executam operações, as entidades duráveis são descarregadas silenciosamente da memória.

Há algumas diferenças importantes, contudo, que vale a pena serem observadas:

* As entidades duráveis priorizam a *durabilidade* em relação à *latência* e, portanto, podem não ser apropriadas para aplicativos com requisitos estritos de latência.
* As mensagens enviadas entre entidades são entregues de maneira confiável e em ordem.
* As entidades duráveis podem ser usadas em conjunto com orquestrações duráveis e dão suporte a mecanismos de bloqueio distribuídos.
* Os padrões de solicitação/resposta em entidades estão limitados a orquestrações. Para a comunicação de *cliente com entidade* e de *entidade com entidade*, apenas o sistema de mensagens unidirecional (também conhecido como “sinalização”) é permitido, como no modelo de ator original. Esse comportamento impede deadlocks distribuídos.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba sobre os hubs de tarefa](durable-functions-task-hubs.md)
