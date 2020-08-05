---
title: Gerenciar gêmeos digitais
titleSuffix: Azure Digital Twins
description: Consulte como recuperar, atualizar e excluir gêmeos e relações individuais.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0f4d9811dc288222c0a2190805a8b052cb1ae47b
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87563918"
---
# <a name="manage-digital-twins"></a>Gerenciar gêmeos digitais

As entidades em seu ambiente são representadas por [gêmeos digital](concepts-twins-graph.md). Gerenciar seu gêmeos digital pode incluir criação, modificação e remoção. Para executar essas operações, você pode usar as [**APIs do DigitalTwins**](how-to-use-apis-sdks.md), o [SDK do .net (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)ou a [CLI do gêmeos digital do Azure](how-to-use-cli.md).

Este artigo se concentra no gerenciamento de gêmeos digitais; para trabalhar com relações e o [gráfico de bispersão](concepts-twins-graph.md) como um todo, confira [*como gerenciar o grafo de entrelaçamento com relações*](how-to-manage-graph.md).

> [!TIP]
> Todas as funções do SDK são fornecidas em versões síncronas e assíncronas.

## <a name="create-a-digital-twin"></a>Criar um teledigital

Para criar um entrelaçamento, use o `CreateDigitalTwin` método no cliente de serviço da seguinte maneira:

```csharp
await client.CreateDigitalTwinAsync("myNewTwinID", initData);
```

Para criar um teledigital, você precisa fornecer:
* A ID desejada para o teledigital
* O [modelo](concepts-models.md) que você deseja usar 

Opcionalmente, você pode fornecer valores iniciais para todas as propriedades da digital. 

O modelo e os valores de propriedade inicial são fornecidos por meio do `initData` parâmetro, que é uma cadeia de caracteres JSON que contém os dados relevantes.

> [!TIP]
> Depois de criar ou atualizar um vertical, pode haver uma latência de até 10 segundos antes que as alterações sejam refletidas nas [consultas](how-to-query-graph.md). A `GetDigitalTwin` API (descrita [mais adiante neste artigo](#get-data-for-a-digital-twin)) não tem esse atraso, portanto, use a chamada à API em vez de consultar para ver o gêmeos criado recentemente se precisar de uma resposta instantânea. 

### <a name="initialize-properties"></a>Propriedades de inicialização

A API de criação de entrelaçamento aceita um objeto que pode ser serializado em uma descrição JSON válida das propriedades biup. Consulte [*Concepts: digital gêmeos e o gráfico de bispersão*](concepts-twins-graph.md) para obter uma descrição do formato JSON para um "r".

Você pode criar um objeto de parâmetro manualmente ou usando uma classe auxiliar fornecida. Aqui está um exemplo de cada um.

#### <a name="create-twins-using-manually-created-data"></a>Criar gêmeos usando dados criados manualmente

```csharp
// Define the model type for the twin to be created
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:com:contoso:Room;1" }
};
// Initialize the twin properties
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", temperature},
    { "Humidity", humidity},
};
client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<Dictionary<string, object>>(twin));
```

#### <a name="create-twins-with-the-helper-class"></a>Criar gêmeos com a classe auxiliar

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
props.Add("Humidity", 50.0);
twin.CustomProperties = props;

client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<BasicDigitalTwin>(twin));
```

## <a name="get-data-for-a-digital-twin"></a>Obter dados de uma teledigital

Você pode acessar os dados completos de qualquer FileUp digital chamando:

```csharp
object result = await client.GetDigitalTwin(id);
```

Essa chamada retorna dados de texto como uma cadeia de caracteres JSON. 

> [!TIP]
> Somente as propriedades que foram definidas pelo menos uma vez são retornadas quando você recupera um conjunto de entrelaças com `GetDigitalTwin` .

Para recuperar vários gêmeos usando uma única chamada à API, consulte os exemplos de API de consulta em [*instruções: consultar o gráfico de entrelaçamento*](how-to-query-graph.md).

Considere o modelo a seguir (escrito na [DTDL (digital gêmeos Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL)) que define uma *lua*:

```json
{
    "@id": " dtmi:com:contoso:Moon;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "contents": [
        {
            "@type": "Property",
            "name": "radius",
            "schema": "double",
            "writable": true
        },
        {
            "@type": "Property",
            "name": "mass",
            "schema": "double",
            "writable": true
        }
    ]
}
```

O resultado da chamada `object result = await client.DigitalTwins.GetByIdAsync("my-moon");` em um tipo de *lua*de texto pode ser semelhante ao seguinte:

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:com:contoso:Moon;1",
    "radius": {
      "desiredValue": 1737.1,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "mass": {
      "desiredValue": 0.0734,
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

As propriedades definidas de 10 digitais são retornadas como propriedades de nível superior no digital. Metadados ou informações do sistema que não fazem parte da definição DTDL são retornados com um `$` prefixo. As propriedades de metadados incluem:
* A ID do cópia digital nesta instância de gêmeos digital do Azure, como `$dtId` .
* `$etag`, um campo HTTP padrão atribuído pelo servidor Web
* Outras propriedades em uma `$metadata` seção. Eles incluem:
    - O DTMI do modelo do teledigital.
    - Status de sincronização para cada propriedade gravável. Isso é mais útil para dispositivos, em que é possível que o serviço e o dispositivo tenham status divergente (por exemplo, quando um dispositivo estiver offline). Atualmente, essa propriedade só se aplica a dispositivos físicos conectados ao Hub IoT. Com os dados na seção de metadados, é possível entender o status completo de uma propriedade, bem como os últimos carimbos de data/hora modificados. Para obter mais informações sobre o status de sincronização, consulte [este tutorial do Hub IOT](../iot-hub/tutorial-device-twins.md) sobre como sincronizar o estado do dispositivo.
    - Metadados específicos do serviço, como do Hub IoT ou do gêmeos digital do Azure. 

Você pode analisar o JSON retornado para o tipo de entrelaçamento usando uma biblioteca de análise de JSON de sua escolha, como `System.Text.Json` .

Você também pode usar a classe auxiliar de serialização `BasicDigitalTwin` que está incluída no SDK, que retornará os metadados e as propriedades principais de entrelaçamento no formulário previamente analisado. Veja um exemplo:

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

Você pode ler mais sobre as classes auxiliares de serialização em [*How-to: Use the Azure digital gêmeos APIs and SDKs*](how-to-use-apis-sdks.md).

## <a name="update-a-digital-twin"></a>Atualizar um gêmeo digital

Para atualizar as propriedades de uma troca digital, você escreve as informações que deseja substituir no formato de [patch JSON](http://jsonpatch.com/) . Dessa forma, você pode substituir várias propriedades de uma só vez. Em seguida, você passa o documento de patch JSON para um `Update` método:

```csharp
await client.UpdateDigitalTwin(id, patch);
```

> [!TIP]
> Depois de criar ou atualizar um vertical, pode haver uma latência de até 10 segundos antes que as alterações sejam refletidas nas [consultas](how-to-query-graph.md). A `GetDigitalTwin` API (descrita [anteriormente neste artigo](#get-data-for-a-digital-twin)) não enfrenta esse atraso, portanto, use a chamada à API em vez de consultar para ver seu gêmeos atualizado recentemente se precisar de uma resposta instantânea. 

Aqui está um exemplo de código de patch JSON. Este documento substitui os valores de propriedade de *massa* e de *RADIUS* do teledigital em que é aplicado.

```json
[
  {
    "op": "replace",
    "path": "/mass",
    "value": 0.0799
  },
  {
    "op": "replace",
    "path": "/radius",
    "value": 0.800
  }
]
```

Você pode criar patches manualmente ou usando uma classe auxiliar de serialização no [SDK](how-to-use-apis-sdks.md). Aqui está um exemplo de cada um.

#### <a name="create-patches-manually"></a>Criar patches manualmente
```csharp
List<object> twinData = new List<object>();
twinData.Add(new Dictionary<string, object>() {
    { "op", "add"},
    { "path", "/Temperature"},
    { "value", 25.0}
});

await client.UpdateDigitalTwinAsync(twinId, JsonConvert.SerializeObject(twinData));
```

#### <a name="create-patches-using-the-helper-class"></a>Criar patches usando a classe auxiliar

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", 25.0);
await client.UpdateDigitalTwinAsync(twinId, uou.Serialize());
```

### <a name="update-properties-in-digital-twin-components"></a>Atualizar propriedades em componentes de atualização digitais

Lembre-se de que um modelo pode conter componentes, permitindo que ele seja composto por outros modelos. 

Para aplicar as propriedades de patch em um dos componentes digitais do r, você usará a sintaxe de caminho no patch JSON:

```json
[
  {
    "op": "replace",
    "path": "/mycomponentname/mass",
    "value": 0.0799
  }
]
```

### <a name="update-a-digital-twins-model"></a>Atualizar um modelo de r. digital

A `Update` função também pode ser usada para migrar uma migração digital para um modelo diferente. 

Por exemplo, considere o seguinte documento de patch JSON que substitui o campo de metadados do digital bificado `$model` :

```json
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:contoso:foo;1"
  }
]
```

Esta operação só terá sucesso se o tipo de atualização digital estiver sendo modificado pelo patch estiver em conformidade com o novo modelo. 

Considere o exemplo a seguir:
1. Imagine um "up digital" com um modelo de *foo_old*. *foo_old* define uma propriedade necessária *em massa*.
2. O novo modelo *foo_new* define uma propriedade em massa e adiciona uma nova *temperatura*de propriedade necessária.
3. Após o patch, o FileUp digital deve ter uma propriedade Mass e de temperatura. 

O patch para essa situação precisa atualizar o modelo e a propriedade de temperatura de entrelaçamento, desta forma:

```json
[
  {
    "op": "replace",
    "path": "$metadata.$model",
    "value": "dtmi:com:contoso:foo_new"
  },
  {
    "op": "add",
    "path": "temperature",
    "value": 60
  }
]
```

## <a name="delete-a-digital-twin"></a>Excluir uma teledigital

Você pode excluir gêmeos usando `DeleteDigitalTwin(ID)` . No entanto, você só pode excluir um "n" quando ele não tiver mais relações. Você deve excluir todas as relações primeiro. 

Aqui está um exemplo do código para isso:

```csharp
static async Task DeleteTwin(string id)
{
    await FindAndDeleteOutgoingRelationshipsAsync(id);
    await FindAndDeleteIncomingRelationshipsAsync(id);
    try
    {
        await client.DeleteDigitalTwin(id);
    } catch (RequestFailedException exc)
    {
        Console.WriteLine($"*** Error:{exc.Error}/{exc.Message}");
    }
}

public async Task FindAndDeleteOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<string> relsJson = client.GetRelationshipsAsync(dtId);

        await foreach (string relJson in relsJson)
        {
            var rel = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relJson);
            await client.DeleteRelationshipAsync(dtId, rel.Id).ConfigureAwait(false);
            Log.Ok($"Deleted relationship {rel.Id} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
    }
}

async Task FindAndDeleteIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipssAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        await foreach (IncomingRelationship incomingRel in incomingRels)
        {
            await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
            Log.Ok($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
    }
}
```

### <a name="delete-all-digital-twins"></a>Excluir todos os gêmeos digitais

Para obter um exemplo de como excluir todos os gêmeos de uma vez, baixe o aplicativo de exemplo usado no [*tutorial: explorar os conceitos básicos com um aplicativo cliente de exemplo*](tutorial-command-line-app.md). O arquivo *CommandLoop.cs* faz isso em uma `CommandDeleteAllTwins` função.

## <a name="manage-twins-with-cli"></a>Gerenciar o gêmeos com a CLI

O gêmeos também pode ser gerenciado usando a CLI do gêmeos digital do Azure. Os comandos podem ser encontrados em [*How-to: Use the Azure digital gêmeos CLI*](how-to-use-cli.md).

## <a name="next-steps"></a>Próximas etapas

Consulte como criar e gerenciar relações entre seu gêmeos digital:
* [*Como: gerenciar o grafo de entrelaçamento com relações*](how-to-manage-graph.md)