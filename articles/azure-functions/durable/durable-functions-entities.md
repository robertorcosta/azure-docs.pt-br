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
ms.openlocfilehash: e3a83730e47686e9d4757f057d2e8da4629fdd7a
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72312146"
---
# <a name="entity-functions-preview"></a>Funções de entidade (versão prévia)

As funções de entidade definem operações para leitura e atualização de pequenas partes de estado, conhecidas como *entidades duráveis*. Como as funções de orquestrador, as funções de entidade são funções com um tipo de gatilho especial, o *gatilho de entidade*. Ao contrário das funções de orquestrador, as funções de entidade gerenciam o estado de uma entidade explicitamente, em vez de representar implicitamente o estado por meio do fluxo de controle.
As entidades fornecem um meio para escalar horizontalmente os aplicativos, distribuindo o trabalho entre várias entidades, cada uma com um estado de tamanho modesto.

> [!NOTE]
> As funções de entidade e a funcionalidade relacionada estão disponíveis apenas nas Durable Functions 2.0 e superior. As funções de entidade estão em versão prévia pública no momento.

## <a name="general-concepts"></a>Conceitos gerais

As entidades se comportam de modo um pouco semelhante a pequenos serviços que se comunicam por meio de mensagens. Cada entidade tem uma identidade exclusiva e um estado interno (se existir). Assim como os serviços ou objetos, as entidades executam operações quando é solicitado a elas que o façam. Quando é executada, uma operação pode atualizar o estado interno da entidade. Ela também pode chamar serviços externos e aguardar uma resposta. As entidades se comunicam com outras entidades, orquestrações e clientes usando mensagens que são enviadas implicitamente por meio de filas confiáveis. 

Para evitar conflitos, todas as operações em uma única entidade têm garantia de execução em série, ou seja, uma após a outra. 

### <a name="entity-id"></a>ID da Entidade
As entidades são acessadas por meio de um identificador exclusivo, a *ID da entidade*. Uma ID de entidade é um par de cadeias de caracteres que identifica exclusivamente uma instância de entidade. Ele consiste em:

* Um **nome de entidade**: um nome que identifica o tipo da entidade (por exemplo, "Counter"). Esse nome deve corresponder ao nome da função de entidade que implementa a entidade. Ele não diferencia maiúsculas e minúsculas.
* Uma **chave de entidade**: uma cadeia de caracteres que identifica exclusivamente a entidade entre todas as outras entidades com o mesmo nome (por exemplo, um GUID).

Por exemplo, uma função de entidade *counter* pode ser usada para manter a pontuação em um jogo online. Cada instância do jogo terá uma ID de entidade exclusiva, como `@Counter@Game1`, `@Counter@Game2` e assim por diante. Todas as operações que direcionam uma entidade específica exigem a especificação de uma ID de entidade como um parâmetro.

### <a name="entity-operations"></a>Operações de entidade ###

Para invocar uma operação em uma entidade, uma pessoa especifica

* A *ID da entidade* de destino
* O *nome da operação*, uma cadeia de caracteres que especifica a operação a ser executada. Por exemplo, a entidade do contador pode dar suporte a operações de "adicionar", "obter" ou "redefinir".
* A *entrada da operação*, que é um parâmetro de entrada opcional para a operação. Por exemplo, a operação "adicionar" pode usar um valor inteiro como entrada.

As operações podem retornar um valor de resultado ou um resultado de erro (como um erro de JavaScript ou uma exceção .NET). Esse resultado ou erro pode ser observado por orquestrações que chamaram a operação.

Uma operação de entidade também pode criar, ler, atualizar e excluir o estado da entidade. O estado da entidade é sempre persistido no armazenamento de modo durável.

## <a name="defining-entities"></a>Definir entidades

Atualmente, oferecemos duas APIs distintas para definir entidades.

Uma **sintaxe baseada em função** em que as entidades são representadas como funções e as operações são expedidas explicitamente pelo aplicativo. Essa sintaxe funciona bem para entidades com estado simples, poucas operações ou um conjunto dinâmico de operações (como em estruturas do aplicativo). No entanto, pode ser chato mantê-la, pois ela não detecta erros de tipo no tempo de compilação.

Uma **sintaxe baseada em classe** em que as entidades e operações são representadas por classes e métodos. Essa sintaxe produz um código mais fácil de ler e permite que as operações sejam invocadas de modo fortemente tipado. A sintaxe baseada em classe é apenas uma camada fina sobre a sintaxe baseada em função, então essas duas variantes podem ser usadas de maneira intercambiável no mesmo aplicativo.

### <a name="example-function-based-syntax"></a>Exemplo: Sintaxe baseada em função

O código a seguir é um exemplo de uma entidade *Counter* simples implementada como uma função durável. Essa função define três operações, `add`, `reset` e `get`, cada uma delas opera em um valor de estado inteiro.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
    }
}
```

Para obter mais informações sobre a sintaxe baseada em função e como usá-la, confira [Sintaxe baseada em função](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="example-class-based-syntax"></a>Exemplo: Sintaxe baseada em classe

O exemplo a seguir é uma implementação equivalente da entidade `Counter` usando métodos e classes.

```csharp
[JsonObject(MemberSerialization.OptIn)]
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

O estado dessa entidade é um objeto do tipo `Counter`, que contém um campo que armazena o valor atual do contador. Para persistir esse objeto no armazenamento, ele é serializado e desserializado pela biblioteca [Json.NET](https://www.newtonsoft.com/json). 

Para obter mais informações sobre a sintaxe baseada em classe e como usá-la, confira [Definir classes de entidade](durable-functions-dotnet-entities.md#defining-entity-classes).

## <a name="accessing-entities"></a>Acessar entidades

As entidades podem ser acessadas usando uma comunicação unidirecional ou bidirecional. Usamos a seguinte terminologia para distinguir: 

* **Chamar** uma entidade significa que usamos comunicação bidirecional (ida e volta): enviamos uma mensagem de operação para a entidade e aguardamos a mensagem de resposta antes de continuar. A mensagem de resposta pode fornecer um valor de resultado ou um resultado de erro (como um erro de JavaScript ou uma exceção .NET). Esse resultado ou erro é então observado pelo chamador.
* **Sinalizar** a uma entidade significa que usamos comunicação unidirecional (acionar e esquecer): enviamos uma mensagem de operação, mas não aguardamos uma resposta. Embora haja garantia de a mensagem ser eventualmente entregue, o remetente não sabe quando e não pode observar nenhum erro ou valor de resultado.

As entidades podem ser acessadas de dentro de funções de cliente, de dentro de funções de orquestrador ou de dentro de funções de entidade. Nem todas as formas de comunicação são compatíveis com todos os contextos:

* De dentro dos clientes, você pode *sinalizar* a entidades e pode *ler* o estado da entidade.
* De dentro de orquestrações, você pode *sinalizar* a entidades e pode *chamar* entidades.
* De dentro de entidades, você pode *sinalizar* a entidades.

Abaixo, mostramos alguns exemplos que ilustram essas várias maneiras de acessar entidades.

> [!NOTE]
> Para simplificar, os exemplos a seguir mostram a sintaxe fracamente tipada para acessar entidades. Em geral, é recomendável [Acessar entidades por meio de interfaces](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces), pois isso fornece mais verificação de tipo.

### <a name="example-client-signals-an-entity"></a>Exemplo: o cliente sinaliza a uma entidade

Para acessar entidades de uma função comum do Azure – também conhecida como *função de cliente* – use a [associação de saída do cliente da entidade](durable-functions-bindings.md#entity-client). O exemplo a seguir mostra uma função disparada por fila *sinalizando* uma entidade que usa essa associação.

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

O termo *sinal* significa que a invocação de API de entidade é unidirecional e assíncrona. Não é possível que uma *função do cliente* saiba quando a entidade processou a operação. Além disso, a função do cliente não pode observar nenhum valor de resultado ou exceções. 

### <a name="example-client-reads-an-entity-state"></a>Exemplo: o cliente lê um estado de entidade

As funções de cliente também podem consultar o estado de uma entidade, conforme mostrado no seguinte exemplo:

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

As consultas de estado de entidade são enviadas para o armazenamento de acompanhamento durável e retornam o estado *persistido* mais recentemente da entidade. Esse estado é sempre um estado "confirmado", ou seja, ele nunca é um estado intermediário temporário assumido no meio da execução de uma operação. No entanto, é possível que esse estado seja obsoleto se comparado com o estado na memória da entidade. Somente orquestrações podem ler o estado na memória de uma entidade, conforme descrito na seção a seguir.

### <a name="example-orchestration-signals-and-calls-an-entity"></a>Exemplo: a orquestração sinaliza a uma entidade e a chama

As funções de orquestrador podem acessar entidades usando APIs na [associação de gatilho de orquestração](durable-functions-bindings.md#orchestration-trigger). O código de exemplo a seguir mostra uma função funções de orquestrador *chamando* e *sinalizando* uma entidade *Counter*.

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

   // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // One-way signal to the entity which updates the value - does not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

Somente orquestrações são capazes de chamar entidades e obter uma resposta, que poderia ser um valor retornado ou uma exceção. As funções de cliente que usam a [associação de cliente](durable-functions-bindings.md#entity-client) só podem *sinalizar* entidades.

> [!NOTE]
> Chamar uma entidade de uma função de orquestrador é semelhante a chamar uma [função de atividade](durable-functions-types-features-overview.md#activity-functions) de uma função de orquestrador. A principal diferença é que as funções de entidade são objetos duráveis com um endereço (a *ID da entidade*) e dão suporte à especificação de um nome de operação. As funções de atividade, por outro lado, são sem estado e não têm o conceito das operações.

### <a name="example-entity-signals-an-entity"></a>Exemplo: a entidade sinaliza a uma entidade

Uma função de entidade pode enviar sinais para outras entidades (ou até mesmo para si própria) enquanto executa uma operação.
Por exemplo, podemos modificar o exemplo de entidade de contador acima para que ela envie um sinal de "marco-atingido" para alguma entidade de monitor quando o contador atingir o valor 100:

```csharp
   case "add":
        var amount = ctx.GetInput<int>();
        if (currentValue < 100 && currentValue + amount >= 100)
        {
            ctx.SignalEntity(new EntityId("MonitorEntity", ""), "milestone-reached", ctx.EntityKey);
        }
        currentValue += amount;
        break;
```

O snippet a seguir demonstra como incorporar o serviço injetado à sua classe de entidade.

```csharp
public class HttpEntity
{
    private readonly HttpClient client;

    public HttpEntity(IHttpClientFactory factory)
    {
        this.client = factory.CreateClient();
    }

    public async Task<int> GetAsync(string url)
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

### <a name="example-transfer-funds"></a>Exemplo: Transferir fundos

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

> [!NOTE] 
> Quando uma orquestração é encerrada (normalmente ou com um erro), todas as seções críticas em andamento são encerradas implicitamente e todos os bloqueios são liberados.

### <a name="critical-section-behavior"></a>Comportamento da seção crítico

O método `LockAsync` cria uma _seção crítica_ em uma orquestração. Essas _seções críticas_ impedem que outras orquestrações façam alterações sobrepostas em um conjunto especificado de entidades. Internamente, a API `LockAsync` envia operações de "bloqueio" para as entidades e retorna quando recebe uma mensagem de resposta de "bloqueio adquirido" de cada uma dessas mesmas entidades. O *bloqueio* e o *desbloqueio* são operações internas compatíveis com todas as entidades.

Nenhuma operação de outros clientes é permitida em uma entidade enquanto ela está em um estado bloqueado. Esse comportamento verifica se apenas uma instância de orquestração pode bloquear uma entidade por vez. Se um chamador tentar invocar uma operação em uma entidade enquanto ela estiver bloqueada por uma orquestração, essa operação será colocada em uma *fila de operação pendente*. Nenhuma operação pendente será processada até depois que a orquestração em retenção libere seu bloqueio.

> [!NOTE] 
> Isso é um pouco diferente dos primitivos de sincronização usados na maioria das linguagens de programação, como a instrução `lock` em C#. Por exemplo, em C#, a instrução `lock` deve ser usada por todos os threads para garantir a sincronização apropriada em vários threads. Contudo, as entidades não exigem que todos os chamadores _bloqueiem_ explicitamente uma entidade. Se qualquer chamador bloquear uma entidade, todas as outras operações nessa entidade serão bloqueadas e enfileiradas atrás desse bloqueio.

Os bloqueios em entidades são duráveis. Portanto, eles persistirão mesmo se o processo de execução for reciclado. Os bloqueios são persistidos internamente como parte do estado durável de uma entidade.

Ao contrário das transações, as seções críticas não revertem automaticamente as alterações em caso de erros. Em vez disso, qualquer tratamento de erro (reversão, repetição ou outro) precisa ser codificado explicitamente; por exemplo, capturando erros ou exceções. Essa opção de design é intencional. Em geral, a reversão automática de todos os efeitos de uma orquestração é difícil ou impossível, pois as orquestrações podem executar atividades e fazer chamadas para serviços externos que não podem ser revertidos. Além disso, as tentativas de reverter podem falhar e exigir tratamento de erro adicional.

### <a name="critical-section-rules"></a>Regras de seção críticas

Ao contrário do que ocorre com os primitivos de bloqueio de nível baixo na maioria das linguagens de programação, com as seções críticas **há garantia de que não ocorrerá deadlock**. Para evitar deadlocks, impomos as seguintes restrições: 

* As seções críticas não podem ser aninhadas.
* As seções críticas não podem criar suborquestrações.
* As seções críticas podem chamar somente entidades que elas bloquearam.
* As seções críticas não podem chamar a mesma entidade usando várias chamadas paralelas.
* As seções críticas podem sinalizar apenas entidades que elas não bloquearam.

Quaisquer violações dessas regras causam um erro de runtime (como um `LockingRulesViolationException` no .NET) que inclui uma mensagem explicando qual regra foi violada.

## <a name="comparison-with-virtual-actors"></a>Comparação com atores virtuais

Muitos recursos de Entidades Duráveis são inspirados pelo [modelo de ator](https://en.wikipedia.org/wiki/Actor_model). Se você já estiver familiarizado com atores, poderá reconhecer muitos conceitos descritos neste artigo. Entidades duráveis são particularmente semelhantes a [atores virtuais](https://research.microsoft.com/projects/orleans/) ou *grãos*, conforme popularizado pelo [projeto Orleans](http://dotnet.github.io/orleans/). Por exemplo:

* As entidades duráveis podem ser abordadas por meio de uma *ID de entidade*.
* As operações de entidade durável são executadas de maneira serial, uma por vez, para impedir condições de corrida.
* As entidades duráveis são criadas implicitamente quando são chamadas ou sinalizadas.
* Quando não executam operações, as entidades duráveis são descarregadas silenciosamente da memória.

Há algumas diferenças importantes, contudo, que vale a pena serem observadas:

* As entidades duráveis priorizam a *durabilidade* em relação à *latência* e, portanto, podem não ser apropriadas para aplicativos com requisitos estritos de latência.
* Entidades duráveis não têm tempos limite internos para mensagens. No Orleans, todas as mensagens atingem o tempo limite após um tempo configurável (30 segundos por padrão).
* As mensagens enviadas entre entidades são entregues de maneira confiável e em ordem. No Orleans, a entrega confiável ou ordenada é compatível com conteúdo enviado por meio de fluxos, mas não é garantida para todas as mensagens entre os grãos.
* Os padrões de solicitação/resposta em entidades estão limitados a orquestrações. De dentro das entidades, apenas o sistema de mensagens unidirecional (também conhecido como "sinalização") é permitido, assim como no modelo de ator original e ao contrário dos grãos no Orleans. 
* As entidades duráveis não geram deadlock. No Orleans, os deadlocks podem ocorrer (e não se resolver até atingir o tempo limite das mensagens).
* As entidades duráveis podem ser usadas em conjunto com orquestrações duráveis e dão suporte a mecanismos de bloqueio distribuídos. 


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Leia o guia do desenvolvedor para entidades duráveis no .NET](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [Saiba sobre os hubs de tarefa](durable-functions-task-hubs.md)
