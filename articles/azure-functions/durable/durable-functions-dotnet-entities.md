---
title: Guia do Desenvolvedor para Entidades Duráveis em .NET - Funções Do Azure
description: Como trabalhar com entidades duráveis em .NET com a extensão Funções Duráveis para Funções Azure.
author: sebastianburckhardt
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: azfuncdf
ms.openlocfilehash: 01e07eaee705634b03cc4462c4058e290daa8bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278123"
---
# <a name="developers-guide-to-durable-entities-in-net"></a>Guia do desenvolvedor para entidades duráveis em .NET

Neste artigo, descrevemos as interfaces disponíveis para o desenvolvimento de entidades duráveis com .NET em detalhes, incluindo exemplos e conselhos gerais. 

As funções da entidade fornecem aos desenvolvedores de aplicativos sem servidor uma maneira conveniente de organizar o estado de aplicação como uma coleção de entidades de grãos finos. Para obter mais detalhes sobre os conceitos subjacentes, consulte o artigo [Entidades Duráveis: Conceitos.](durable-functions-entities.md)

Atualmente, oferecemos duas APIs para definir entidades:

- A **sintaxe baseada em classe** representa entidades e operações como classes e métodos. Esta sintaxe produz código sinibilável e permite que as operações sejam invocadas de forma type-checked através de interfaces. 

- A **sintaxe baseada em funções** é uma interface de nível inferior que representa entidades como funções. Fornece controle preciso sobre como as operações da entidade são despachadas e como o estado da entidade é gerenciado.  

Este artigo se concentra principalmente na sintaxe baseada em classe, pois esperamos que seja mais adequada para a maioria das aplicações. No entanto, a [sintaxe baseada em função](#function-based-syntax) pode ser apropriada para aplicações que desejam definir ou gerenciar suas próprias abstrações para estado e operações de entidade. Além disso, pode ser apropriado para a implementação de bibliotecas que requerem genericidade não suportada atualmente pela sintaxe baseada em classe. 

> [!NOTE]
> A sintaxe baseada em classe é apenas uma camada em cima da sintaxe baseada em função, de modo que ambas as variantes podem ser usadas intercambiavelmente na mesma aplicação. 
 
## <a name="defining-entity-classes"></a>Definindo classes de entidades

O exemplo a seguir `Counter` é a implementação de uma entidade que armazena `Add` `Reset`um `Get`único `Delete`valor de valor inteiro de tipo, e oferece quatro operações, e .

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int Value { get; set; }

    public void Add(int amount) 
    {
        this.Value += amount;
    }

    public Task Reset() 
    {
        this.Value = 0;
        return Task.CompletedTask;
    }

    public Task<int> Get() 
    {
        return Task.FromResult(this.Value);
    }

    public void Delete() 
    {
        Entity.Current.DeleteState();
    }

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

A `Run` função contém a caldeira necessária para o uso da sintaxe baseada em classe. Deve ser uma função azure *estática.* Ele executa uma vez para cada mensagem de operação que é processada pela entidade. Quando `DispatchAsync<T>` é chamada e a entidade ainda não está na `T` memória, ela constrói um objeto de tipo e povoa seus campos a partir do último JSON persistido encontrado no armazenamento (se houver). Em seguida, ele invoca o método com o nome correspondente.

> [!NOTE]
> O estado de uma entidade baseada em classe é **criado implicitamente** antes que a entidade `Entity.Current.DeleteState()`processe uma operação, e pode ser excluído **explicitamente** em uma operação por chamada .

### <a name="class-requirements"></a>Requisitos de classe
 
As classes de entidade são POCOs (objetos CLR simples e antigos) que não requerem superclasses especiais, interfaces ou atributos. No entanto:

- A classe deve ser construgível (ver [Construção entidade](#entity-construction)).
- A classe deve ser json-serializable (ver [Serialização Entidade](#entity-serialization)).

Além disso, qualquer método destinado a ser invocado como operação deve satisfazer requisitos adicionais:

- Uma operação deve ter no máximo um argumento, e não deve ter sobrecargas ou argumentos genéricos.
- Uma operação destinada a ser chamada a partir `Task` `Task<T>`de uma orquestração usando uma interface deve retornar ou .
- Argumentos e valores de devolução devem ser valores ou objetos serializáveis.

### <a name="what-can-operations-do"></a>O que as operações podem fazer?

Todas as operações da entidade podem ler e atualizar o estado da entidade, e as alterações no estado são automaticamente persistidas ao armazenamento. Além disso, as operações podem realizar I/O externo ou outros cálculos, dentro dos limites gerais comuns a todas as Funções Azure.

As operações também têm acesso `Entity.Current` à funcionalidade fornecida pelo contexto:

* `EntityName`: o nome da entidade executora.
* `EntityKey`: a chave da entidade executora atualmente.
* `EntityId`: o ID da entidade executora (inclui nome e chave).
* `SignalEntity`: envia uma mensagem unidirecional para uma entidade.
* `CreateNewOrchestration`: inicia uma nova orquestração.
* `DeleteState`: exclui o estado desta entidade.

Por exemplo, podemos modificar a entidade de contador para que ela inicie uma orquestração quando o contador atingir 100 e passar o ID da entidade como um argumento de entrada:

```csharp
    public void Add(int amount) 
    {
        if (this.Value < 100 && this.Value + amount > 100)
        {
            Entity.Current.StartNewOrchestration("MilestoneReached", Entity.Current.EntityId)
        }
        this.Value += amount;      
    }
```

## <a name="accessing-entities-directly"></a>Acessando entidades diretamente

Entidades baseadas em classes podem ser acessadas diretamente, usando nomes de cadeias explícitos para a entidade e suas operações. Nós fornecemos alguns exemplos abaixo; para uma explicação mais profunda dos conceitos subjacentes (como sinais vs. chamadas) ver a discussão em [entidades de acesso](durable-functions-entities.md#access-entities). 

> [!NOTE]
> Sempre que possível, recomendamos [acessar entidades através de interfaces,](#accessing-entities-through-interfaces)pois fornece mais verificação de tipo.

### <a name="example-client-signals-entity"></a>Exemplo: entidade de sinais de cliente

A função Azure Http a seguir implementa uma operação DELETE usando convenções REST. Ele envia um sinal de exclusão para a entidade de contador cuja chave é passada no caminho url.

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync(entityId, "Delete");    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

### <a name="example-client-reads-entity-state"></a>Exemplo: cliente lê estado da entidade

A função Azure Http a seguir implementa uma operação GET usando convenções REST. Ele lê o estado atual da entidade de contador cuja chave é passada no caminho url.

```csharp
[FunctionName("GetCounter")]
public static async Task<HttpResponseMessage> GetCounter(
    [HttpTrigger(AuthorizationLevel.Function, "get", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    var state = await client.ReadEntityStateAsync<Counter>(entityId); 
    return req.CreateResponse(state);
}
```

> [!NOTE]
> O objeto devolvido `ReadEntityStateAsync` é apenas uma cópia local, ou seja, um instantâneo do estado da entidade de algum ponto anterior. Em particular, pode ser obsoleto, e modificar este objeto não tem efeito sobre a entidade real. 

### <a name="example-orchestration-first-signals-then-calls-entity"></a>Exemplo: orquestração primeiro sinaliza, em seguida, chama entidade

A orquestração a seguir sinaliza uma entidade contrária para incrementá-la e, em seguida, chama a mesma entidade para ler seu valor mais recente.

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");

    // One-way signal to the entity - does not await a response
    context.SignalEntity(entityId, "Add", 1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");

    return currentValue;
}
```

## <a name="accessing-entities-through-interfaces"></a>Acessando entidades através de interfaces

Interfaces podem ser usadas para acessar entidades através de objetos proxy gerados. Essa abordagem garante que o nome e o tipo de argumento de uma operação correspondam ao que é implementado. Recomendamos o uso de interfaces para acessar entidades sempre que possível.

Por exemplo, podemos modificar o exemplo de contador da seguinte forma:

```csharp
public interface ICounter
{
    void Add(int amount);
    Task Reset();
    Task<int> Get();
    void Delete();
}

public class Counter : ICounter
{
    ...
}
```

As classes de entidades e interfaces de entidades são semelhantes às interfaces de grãos e grãos popularizadas por [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/). Para obter mais informações sobre semelhanças e diferenças entre Entidades Duráveis e Orleans, consulte [Comparação com atores virtuais](durable-functions-entities.md#comparison-with-virtual-actors).

Além de fornecer verificação de tipo, as interfaces são úteis para uma melhor separação de preocupações dentro do aplicativo. Por exemplo, uma vez que uma entidade pode implementar várias interfaces, uma única entidade pode servir a várias funções. Além disso, uma vez que uma interface pode ser implementada por várias entidades, padrões gerais de comunicação podem ser implementados como bibliotecas reutilizáveis.

### <a name="example-client-signals-entity-through-interface"></a>Exemplo: cliente sinaliza entidade através da interface

O código `SignalEntityAsync<TEntityInterface>` do cliente pode ser `TEntityInterface`usado para enviar sinais para entidades que implementam . Por exemplo: 

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync<ICounter>(entityId, proxy => proxy.Delete());    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

Neste exemplo, `proxy` o parâmetro é uma instância `ICounter`dinamicamente gerada, `Delete` que traduz internamente a chamada para um sinal.

> [!NOTE]
> As `SignalEntityAsync` APIs só podem ser usadas para operações unidirecionais. Mesmo que uma `Task<T>`operação retorne, `T` o valor do `default`parâmetro será sempre nulo ou, não o resultado real.
Por exemplo, não faz sentido sinalizar `Get` a operação, pois nenhum valor é devolvido. Em vez disso, `ReadStateAsync` os clientes podem usar para acessar o estado `Get` de contador diretamente, ou podem iniciar uma função orquestradora que chama a operação. 

### <a name="example-orchestration-first-signals-then-calls-entity-through-proxy"></a>Exemplo: orquestração primeiro sinais, em seguida, chama entidade através de proxy

Para ligar ou sinalizar uma entidade `CreateEntityProxy` de dentro de uma orquestração, pode ser usado, juntamente com o tipo de interface, para gerar um proxy para a entidade. Este proxy pode então ser usado para encerrar ou sinalizar operações:

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");
    var proxy = context.CreateEntityProxy<ICounter>(entityId);

    // One-way signal to the entity - does not await a response
    proxy.Add(1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await proxy.Get();

    return currentValue;
}
```

Implicitamente, todas `void` as operações que retornam `Task` são `Task<T>` sinalizadas, e quaisquer operações que retornem ou sejam chamadas. Pode-se alterar esse comportamento padrão e sinalizar operações `SignalEntity<IInterfaceType>` mesmo se retornarem Task, usando o método explicitamente.

### <a name="shorter-option-for-specifying-the-target"></a>Opção mais curta para especificar o destino

Ao ligar ou sinalizar uma entidade usando uma interface, o primeiro argumento deve especificar a entidade-alvo. O destino pode ser especificado especificando o ID da entidade, ou, nos casos em que há apenas uma classe que implementa a entidade, apenas a chave da entidade:

```csharp
context.SignalEntity<ICounter>(new EntityId(nameof(Counter), "myCounter"), ...);
context.SignalEntity<ICounter>("myCounter", ...);
```

Se apenas a chave da entidade for especificada e uma `InvalidOperationException` implementação única não puder ser encontrada em tempo de execução, será lançada. 

### <a name="restrictions-on-entity-interfaces"></a>Restrições em interfaces de entidades

Como de costume, todos os tipos de parâmetros e retornos devem ser serializáveis json. Caso contrário, as exceções de serialização são lançadas em tempo de execução.

Também aplicamos algumas regras adicionais:
* As interfaces de entidade saem apenas definindo métodos.
* As interfaces de entidade não devem conter parâmetros genéricos.
* Os métodos de interface da entidade não devem ter mais de um parâmetro.
* Os métodos de `void` `Task`interface da entidade devem retornar, ou`Task<T>` 

Se alguma dessas regras for `InvalidOperationException` violada, uma é lançada em tempo de `SignalEntity` `CreateProxy`execução quando a interface é usada como um argumento de tipo para ou . A mensagem de exceção explica qual regra foi quebrada.

> [!NOTE]
> Os métodos `void` de interface que retornam só podem ser sinalizados (unidirecional), não chamados (vias a duas vias). Métodos de `Task` interface `Task<T>` retornando ou podem ser chamados ou sinalizados. Se forem chamados, eles retornam o resultado da operação, ou rejogam exceções lançadas pela operação. No entanto, quando sinalizados, eles não retornam o resultado real ou exceção da operação, mas apenas o valor padrão.

## <a name="entity-serialization"></a>Serialização de entidades

Uma vez que o estado de uma entidade é duradouramente persistido, a classe entidade deve ser serializável. O tempo de execução Funções Duráveis usa a biblioteca [Json.NET](https://www.newtonsoft.com/json) para este fim, que suporta uma série de políticas e atributos para controlar o processo de serialização e desserialização. Os tipos de dados C# mais comumente usados (incluindo arrays e tipos de coleta) já são serializáveis e podem ser facilmente usados para definir o estado das entidades duráveis.

Por exemplo, Json.NET pode facilmente serializar e desserializar a seguinte classe:

```csharp
[JsonObject(MemberSerialization = MemberSerialization.OptIn)]
public class User
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("yearOfBirth")]
    public int YearOfBirth { get; set; }

    [JsonProperty("timestamp")]
    public DateTime Timestamp { get; set; }

    [JsonProperty("contacts")]
    public Dictionary<Guid, Contact> Contacts { get; set; } = new Dictionary<Guid, Contact>();

    [JsonObject(MemberSerialization = MemberSerialization.OptOut)]
    public struct Contact
    {
        public string Name;
        public string Number;
    }

    ...
}
```

### <a name="serialization-attributes"></a>Atributos de serialização

No exemplo acima, optamos por incluir vários atributos para tornar a serialização subjacente mais visível:
- Nós anotamos a `[JsonObject(MemberSerialization.OptIn)]` classe com para nos lembrar que a classe deve ser serializável, e para persistir apenas membros que são explicitamente marcados como propriedades JSON.
-  Nós anotamos os campos a `[JsonProperty("name")]` serem permanecidos para nos lembrar que um campo faz parte do estado da entidade persistida, e para especificar o nome da propriedade a ser usado na representação JSON.

No entanto, esses atributos não são necessários; outras convenções ou atributos são permitidos desde que trabalhem com Json.NET. Por exemplo, pode-se usar `[DataContract]` atributos ou nenhum atributo:

```csharp
[DataContract]
public class Counter
{
    [DataMember]
    public int Value { get; set; }
    ...
}

public class Counter
{
    public int Value;
    ...
}
```

Por padrão, o nome da classe *não* é armazenado como parte da `TypeNameHandling.None` representação JSON: ou seja, usamos como configuração padrão. Esse comportamento padrão pode ser `JsonObject` `JsonProperty` substituído usando ou atributos.

### <a name="making-changes-to-class-definitions"></a>Fazendo alterações nas definições de classe

Alguns cuidados são necessários ao fazer alterações em uma definição de classe após a execução de um aplicativo, porque o objeto JSON armazenado pode não corresponder mais à nova definição de classe. Ainda assim, muitas vezes é possível lidar corretamente com a alteração dos formatos `JsonConvert.PopulateObject`de dados, desde que se entenda o processo de desserialização usado por .

Por exemplo, aqui estão alguns exemplos de mudanças e seu efeito:

1. Se uma nova propriedade for adicionada, que não está presente no JSON armazenado, ela assume seu valor padrão.
1. Se uma propriedade for removida, que está presente no JSON armazenado, o conteúdo anterior será perdido.
1. Se uma propriedade for renomeada, o efeito é como se fosse remover a antiga e adicionar uma nova.
1. Se o tipo de propriedade for alterado para que ele não possa mais ser desserializado do JSON armazenado, uma exceção será lançada.
1. Se o tipo de propriedade for alterado, mas ainda pode ser desserializado do JSON armazenado, ele o fará.

Existem muitas opções disponíveis para personalizar o comportamento de Json.NET. Por exemplo, para forçar uma exceção se o JSON armazenado contiver `JsonObject(MissingMemberHandling = MissingMemberHandling.Error)`um campo que não está presente na classe, especifique o atributo . Também é possível escrever código personalizado para desserialização que pode ler JSON armazenado em formatos arbitrários.

## <a name="entity-construction"></a>Construção de entidades

Às vezes queremos exercer mais controle sobre como os objetos da entidade são construídos. Agora descrevemos várias opções para alterar o comportamento padrão ao construir objetos de entidade. 

### <a name="custom-initialization-on-first-access"></a>Inicialização personalizada no primeiro acesso

Ocasionalmente, precisamos realizar alguma inicialização especial antes de enviar uma operação para uma entidade que nunca foi acessada, ou que foi excluída. Para especificar esse comportamento, pode-se `DispatchAsync`adicionar um condicional antes de:

```csharp
[FunctionName(nameof(Counter))]
public static Task Run([EntityTrigger] IDurableEntityContext ctx)
{
    if (!ctx.HasState)
    {
        ctx.SetState(...);
    }
    return ctx.DispatchAsync<Counter>();
}
```

### <a name="bindings-in-entity-classes"></a>Vinculações em classes de entidades

Ao contrário das funções regulares, os métodos de classe de entidade não têm acesso direto às vinculações de entrada e saída. Em vez disso, os dados de associação devem ser capturados na declaração da função de ponto de entrada e passados para o método `DispatchAsync<T>`. Os objetos passados para `DispatchAsync<T>` serão passados automaticamente para o construtor da classe de entidade como um argumento.

O exemplo a seguir mostra como uma referência de `CloudBlobContainer` da [associação de entrada de blobs](../functions-bindings-storage-blob-input.md) pode ser disponibilizada em uma entidade baseada em classe.

```csharp
public class BlobBackedEntity
{
    [JsonIgnore]
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

### <a name="dependency-injection-in-entity-classes"></a>Injeção de dependência nas classes de entidades

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
    [JsonIgnore]
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

    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> Para evitar problemas com serialização, certifique-se de excluir campos destinados a armazenar valores injetados da serialização.

> [!NOTE]
> Diferentemente do que ocorre ao usar uma injeção de construtor no Azure Functions do .NET regular, o método de ponto de entrada de funções para entidades baseadas em classe *deve* ser declarado `static`. Declarar um ponto de entrada de função não estática pode causar conflitos entre o inicializador de objeto normal do Azure Functions e o inicializador de objeto de Entidades Duráveis.

## <a name="function-based-syntax"></a>Sintaxe baseada em função

Até agora, focamos na sintaxe baseada em classe, pois esperamos que ela seja mais adequada para a maioria das aplicações. No entanto, a sintaxe baseada em função pode ser apropriada para aplicações que desejam definir ou gerenciar suas próprias abstrações para estado e operações de entidade. Além disso, pode ser apropriado ao implementar bibliotecas que requerem genericidade não suportada atualmente pela sintaxe baseada em classe. 

Com a sintaxe baseada em função, a Função entidade lida explicitamente com o despacho da operação e gerencia explicitamente o estado da entidade. Por exemplo, o código a seguir mostra a *entidade Counter* implementada usando a sintaxe baseada em função.  

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
        case "delete":
            ctx.DeleteState();
            break;
    }
}
```

### <a name="the-entity-context-object"></a>O objeto de contexto da entidade

A funcionalidade específica da entidade pode ser acessada através de um objeto de contexto do tipo `IDurableEntityContext`. Este objeto de contexto está disponível como parâmetro para a função `Entity.Current`da entidade e através da propriedade assíncrona local .

Os membros a seguir fornecem informações sobre a operação atual e nos permitem especificar um valor de retorno. 

* `EntityName`: o nome da entidade executora.
* `EntityKey`: a chave da entidade executora atualmente.
* `EntityId`: o ID da entidade executora (inclui nome e chave).
* `OperationName`: o nome da operação atual.
* `GetInput<TInput>()`: obtém a entrada para a operação atual.
* `Return(arg)`: devolve um valor à orquestração que chamou a operação.

Os seguintes membros gerenciam o estado da entidade (criar, ler, atualizar, excluir). 

* `HasState`: se a entidade existe, ou seja, tem algum estado. 
* `GetState<TState>()`: fica o estado atual da entidade. Se ele ainda não existe, ele é criado.
* `SetState(arg)`: cria ou atualiza o estado da entidade.
* `DeleteState()`: exclui o estado da entidade, se existir. 

Se o estado `GetState` devolvido for um objeto, ele pode ser diretamente modificado pelo código do aplicativo. Não há necessidade `SetState` de ligar novamente no final (mas também nenhum dano). Se `GetState<TState>` for chamado várias vezes, o mesmo tipo deve ser usado.

Finalmente, os seguintes membros são usados para sinalizar outras entidades ou iniciar novas orquestrações:

* `SignalEntity(EntityId, operation, input)`: envia uma mensagem unidirecional para uma entidade.
* `CreateNewOrchestration(orchestratorFunctionName, input)`: inicia uma nova orquestração.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Conheça os conceitos da entidade](durable-functions-entities.md)
