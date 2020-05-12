---
title: Modelo de pull do feed de alterações
description: Saiba como usar o modelo de pull do feed de alteração Azure Cosmos DB para ler o feed de alterações e as diferenças entre o modelo de pull e o processador do feed de alterações
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/10/2020
ms.reviewer: sngun
ms.openlocfilehash: 0e6e243ceb73ca2a1180e59ba6c6b4095ed6069a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83116706"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Alterar o modelo de pull do feed no Azure Cosmos DB

Com o modelo de pull do feed de alterações, você pode consumir o Azure Cosmos DB feed de alterações em seu próprio ritmo. Como você já pode fazer com o [processador do feed de alterações](change-feed-processor.md), você pode usar o modelo de pull do feed de alterações para paralelizar o processamento de alterações em vários consumidores do feed de alterações.

> [!NOTE]
> O modelo de pull do feed de alterações está atualmente em [visualização somente no SDK Azure Cosmos DB .net](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview) . A versão prévia ainda não está disponível para outras versões do SDK.

## <a name="consuming-an-entire-containers-changes"></a>Consumindo as alterações de um contêiner inteiro

Você pode criar um `FeedIterator` para processar o feed de alterações usando o modelo de pull. Ao criar inicialmente um `FeedIterator` , você pode especificar um opcional `StartTime` dentro do `ChangeFeedRequestOptions` . Quando deixado não especificado, o `StartTime` será a hora atual.

O `FeedIterator` vem em duas versões. Além dos exemplos abaixo que retornam objetos de entidade, você também pode obter a resposta com `Stream` suporte. Os fluxos permitem que você leia os dados sem tê-los desserializados primeiro, economizando em recursos do cliente.

Veja um exemplo para obter um `FeedIterator` que retorna objetos de entidade, neste caso, um `User` objeto:

```csharp
FeedIterator<User> iteratorWithPOCOS = container.GetChangeFeedIterator<User>();
```

Veja um exemplo para obter um `FeedIterator` que retorna um `Stream` :

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator();
```

Usando um `FeedIterator` , você pode processar facilmente o feed de alterações de um contêiner inteiro em seu próprio ritmo. Aqui está um exemplo:

```csharp
FeedIterator<User> iteratorForTheEntireContainer= container.GetChangeFeedIterator(new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

while (iteratorForTheEntireContainer.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForTheEntireContainer.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="consuming-a-partition-keys-changes"></a>Consumindo as alterações de uma chave de partição

Em alguns casos, talvez você queira apenas processar as alterações de uma chave de partição específica. Você pode obter um `FeedIterator` para uma chave de partição específica e processar as alterações da mesma maneira que você pode para um contêiner inteiro:

```csharp
FeedIterator<User> iteratorForThePartitionKey = container.GetChangeFeedIterator(new PartitionKey("myPartitionKeyValueToRead"), new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

while (iteratorForThePartitionKey.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForThePartitionKey.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>Usando FeedRange para paralelização

No [processador do feed de alterações](change-feed-processor.md), o trabalho é distribuído automaticamente entre vários consumidores. No modelo de pull do feed de alterações, você pode usar o `FeedRange` para paralelizar o processamento do feed de alterações. Um `FeedRange` representa um intervalo de valores de chave de partição.

Aqui está um exemplo que mostra como obter uma lista de intervalos para seu contêiner:

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

Ao obter a lista de FeedRanges para seu contêiner, você obterá uma `FeedRange` por [partição física](partition-data.md#physical-partitions).

Usando um `FeedRange` , você pode criar um `FeedIterator` para paralelizar o processamento do feed de alterações em vários computadores ou threads. Ao contrário do exemplo anterior que mostrou como obter um único `FeedIterator` para o contêiner inteiro, você pode usar o `FeedRange` para obter vários FeedIterators que podem processar o feed de alterações em paralelo.

No caso em que você deseja usar o FeedRanges, você precisa ter um processo de orquestrador que obtém FeedRanges e os distribui para esses computadores. Essa distribuição pode ser:

* Usando `FeedRange.ToJsonString` e distribuindo esse valor de cadeia de caracteres. Os consumidores podem usar esse valor com`FeedRange.FromJsonString`
* Se a distribuição estiver em processo, passando a `FeedRange` referência do objeto.

Aqui está um exemplo que mostra como ler desde o início do feed de alterações do contêiner usando duas máquinas separadas hipotéticas que estão sendo lidas em paralelo:

Computador 1:

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<Person>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
while (iteratorA.HasMoreResults)
{
   FeedResponse<User> users = await iteratorA.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

Máquina 2:

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ranges[1], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
while (iteratorB.HasMoreResults)
{
   FeedResponse<User> users = await iteratorB.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="saving-continuation-tokens"></a>Salvando tokens de continuação

Você pode salvar a posição de seu `FeedIterator` criando um token de continuação. Um token de continuação é um valor de cadeia de caracteres que mantém o controle das últimas alterações processadas do FeedIterator. Isso permite que o `FeedIterator` retome neste ponto mais tarde. O código a seguir lerá o feed de alterações desde a criação do contêiner. Depois que não houver mais alterações disponíveis, ele manterá um token de continuação para que o consumo do feed de alterações possa ser retomado posteriormente.

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

string continuation = null;

while (iterator.HasMoreResults)
{
   FeedResponse<User> users = await iterator.ReadNextAsync();
   continuation = users.ContinuationToken;

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}

// Some time later
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(continuation);
```

## <a name="comparing-with-change-feed-processor"></a>Comparando com o processador do feed de alterações

Muitos cenários podem processar o feed de alterações usando o [processador do feed de alterações](change-feed-processor.md) ou o modelo de pull. Os tokens de continuação do modelo de pull e o contêiner de concessão do processador de feed de alterações são "indicadores" para o último item processado (ou lote de itens) no feed de alterações.
No entanto, não é possível converter tokens de continuação em um contêiner de concessão (ou vice-versa).

Você deve considerar o uso do modelo de pull nestes cenários:

- Você deseja fazer uma leitura única dos dados existentes no feed de alterações
- Você deseja apenas ler as alterações de uma chave de partição específica
- Você não quer um modelo de push e deseja consumir o feed de alterações em seu próprio ritmo

Aqui estão algumas diferenças importantes entre o processador do feed de alterações e o modelo de pull:

|  | Alterar o processador de feed| Modelo de pull |
| --- | --- | --- |
| Controlando o ponto atual no processamento do feed de alterações | Concessão (armazenada em um contêiner de Azure Cosmos DB) | Token de continuação (armazenado na memória ou persistido manualmente) |
| Capacidade de reprodução de alterações passadas | Sim, com o modelo de push | Sim, com o modelo de pull|
| Sondando alterações futuras | Verifica automaticamente se há alterações com base no especificado pelo usuário`WithPollInterval` | Manual |
| Processar alterações de todo o contêiner | Sim, e paralelizado automaticamente em vários threads/computador consumindo do mesmo contêiner| Sim, e paralelizado manualmente usando FeedTokens |
| Processar alterações de apenas uma chave de partição única | Sem suporte | Sim|
| Nível de suporte | Disponível para o público geral | Visualizar |

## <a name="next-steps"></a>Próximas etapas

* [Visão geral do feed de alterações](change-feed.md)
* [Usando o processador do feed de alterações](change-feed-processor.md)
* [Disparar Azure Functions](change-feed-functions.md)