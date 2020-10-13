---
title: Gerenciar o grafo gêmeo com relacionamentos
titleSuffix: Azure Digital Twins
description: Veja como gerenciar um grafo de gêmeos digital conectando-os com relações.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: ad0111f9be8c0b981093618be7296d0ec7f90e30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326534"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Gerenciar um grafo de gêmeos digital usando relações

O coração do Azure digital gêmeos é o [grafo de entrelaçamento](concepts-twins-graph.md) que representa todo o seu ambiente. O gráfico de entrelaçamento é composto de gêmeos digitais individuais conectados por meio de **relações**.

Depois que você tiver uma [instância do gêmeos do Azure digital](how-to-set-up-instance-portal.md) em funcionamento e tiver configurado o código de [autenticação](how-to-authenticate-client.md) em seu aplicativo cliente, poderá usar as [**APIs do DigitalTwins**](how-to-use-apis-sdks.md) para criar, modificar e excluir gêmeos digitais e suas relações em uma instância do gêmeos digital do Azure. Você também pode usar o [SDK do .net (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)ou a [CLI do gêmeos digital do Azure](how-to-use-cli.md).

Este artigo se concentra no gerenciamento de relações e no grafo como um todo; para trabalhar com gêmeos digitais individuais, consulte [*como gerenciar o digital gêmeos*](how-to-manage-twin.md).

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Criar relações

As relações descrevem como diferentes gêmeos digitais são conectadas entre si, que formam a base do grafo de entrelaçamento.

As relações são criadas usando a `CreateRelationship` chamada. 

Para criar uma relação, você precisa especificar:
* A ID de fonte de código de origem (o "File", onde a relação se origina)
* A ID de bidirecional de destino (o filetreme onde a relação chega)
* Um nome de relação
* Uma ID de relação

A ID da relação deve ser exclusiva dentro da fonte fornecida. Ele não precisa ser globalmente exclusivo.
Por exemplo, para o *filefoo*, cada ID de relação específica deve ser exclusiva. No entanto, outra *barra* de entrelaçamento pode ter uma relação de saída que corresponde à mesma ID de uma relação *foo* . 

O exemplo de código a seguir ilustra como adicionar uma relação à instância de gêmeos digital do Azure.

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
{
    var relationship = new BasicRelationship
    {
        TargetId = targetId,
        Name = "contains"
    };

    try
    {
        string relId = $"{srcId}-contains->{targetId}";
        await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

Para obter mais informações sobre a classe auxiliar `BasicRelationship` , consulte [*How-to: Use the Azure digital gêmeos APIs and SDKs*](how-to-use-apis-sdks.md).

### <a name="create-multiple-relationships-between-twins"></a>Criar várias relações entre gêmeos

Não há nenhuma restrição quanto ao número de relações que você pode ter entre dois gêmeos — você pode ter quantas relações entre gêmeos desejar. 

Isso significa que você pode expressar vários tipos diferentes de relações entre duas gêmeos ao mesmo tempo. Por exemplo, *a* bificar a pode ter uma relação *armazenada* e uma relação *fabricada* com a *n º B*.

Você pode até mesmo criar várias instâncias do mesmo tipo de relação entre os mesmos dois gêmeos, se desejar. Neste exemplo, isso significa que *A* "n" pode ter duas relações *armazenadas* em DISTINCT com a *n º B*.

## <a name="list-relationships"></a>Listar relações

Para acessar a lista de relações de um determinado matreme no grafo, você pode usar:

```csharp
await client.GetRelationshipsAsync(id);
```

Isso retorna um `Azure.Pageable<T>` ou `Azure.AsyncPageable<T>` , dependendo de se você usar a versão síncrona ou assíncrona da chamada.

Aqui está um exemplo completo que recupera uma lista de relações:

```csharp
public async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw if an error occurs
        AsyncPageable<string> relsJson = client.GetRelationshipsAsync(dtId);
        List<BasicRelationship> results = new List<BasicRelationship>();
        await foreach (string relJson in relsJson)
        {
            var rel = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relJson);
            results.Add(rel);
        }
        return results;
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
        return null;
    }
}
```

Você pode usar as relações recuperadas para navegar para outros gêmeos em seu grafo. Para fazer isso, leia o `target` campo da relação retornada e use-o como a ID para a próxima chamada para `GetDigitalTwin` . 

### <a name="find-relationships-to-a-digital-twin"></a>Localizar relações com uma teledigital

O gêmeos digital do Azure também tem uma API para localizar todas as relações de entrada para um determinado. Isso geralmente é útil para navegação reversa ou ao excluir um "r".

O exemplo de código anterior concentrou-se na localização de relações de saída. O exemplo a seguir é semelhante, mas localiza relações de entrada em vez disso. Ele também os exclui quando eles são encontrados.

Observe que as `IncomingRelationship` chamadas não retornam o corpo completo da relação.

```csharp
async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        List<IncomingRelationship> results = new List<IncomingRelationship>();
        await foreach (IncomingRelationship incomingRel in incomingRels)
            results.Add(incomingRel);
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
    }
}
```

## <a name="delete-relationships"></a>Excluir relações

Você pode excluir relações usando `DeleteRelationship(source, relId);` .

O primeiro parâmetro especifica a fonte de los (o nome do entrelaçado no qual a relação se origina). O outro parâmetro é a ID da relação. Você precisa da ID de FileUp e da ID de relação, pois as IDs de relação são exclusivas apenas no escopo de um entrelaçamento.

## <a name="create-a-twin-graph"></a>Criar um gráfico de entrelaçamento 

O trecho de código a seguir usa as operações de relação deste artigo para criar um grafo de entrelaçamento de gêmeos e relações digitais.

```csharp
static async Task CreateTwins()
{
    // Create twins - see utility functions below 
    await CreateRoom("Room01", 68, 50, false, "");
    await CreateRoom("Room02", 70, 66, true, "EId-00124");
    await CreateFloorOrBuilding("Floor01", makeFloor:true);

    // Create relationships
    await AddRelationship("Floor01", "contains", "Floor-to-Room01", "Room01");
    await AddRelationship("Floor01", "contains", "Floor-to-Room02", "Room02");
}

static async Task<bool> AddRelationship(string source, string relationship, string id, string target)
{
    var relationship = new BasicRelationship
    {
        TargetId = target,
        Name = relationship
    };

    try
    {
        string relId = $"{source}-contains->{target}";
        await client.CreateRelationshipAsync(source, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
        return true;
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
        return false;
    }
}

static async Task<bool> CreateRoom(string id, double temperature, double humidity)
{
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = "dtmi:com:contoso:Room;2";
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("Temperature", temperature);
    props.Add("Humidity", humidity);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin)); 
        return true;       
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}

static async Task<bool> CreateFloorOrBuilding(string id, bool makeFloor=true)
{
    string type = "dtmi:com:contoso:Building;3";
    if (makeFloor==true)
        type = "dtmi:com:contoso:Floor;2";
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = type;
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("AverageTemperature", 0);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));  
        return true;      
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}
```

### <a name="create-a-twin-graph-from-a-spreadsheet"></a>Criar um gráfico de entrelaçamento de uma planilha

Em casos de uso prático, muitas vezes, as hierarquias de entrelaçamento serão criadas a partir dos dados armazenados em um banco de dado diferente ou talvez em uma planilha. Esta seção ilustra como uma planilha pode ser analisada.

Considere a tabela de dados a seguir, descrevendo um conjunto de gêmeos e relações digitais a serem criadas.

| Modelar    | ID | Pai | Nome da Relação | Outros dados |
| --- | --- | --- | --- | --- |
| floor    | Floor01 | | | … |
| quarto    | Room10 | Floor01 | contains | … |
| quarto    | Room11 | Floor01 | contains | … |
| quarto    | Room12 | Floor01 | contains | … |
| floor    | Floor02 | | | … |
| quarto    | Room21 | Floor02 | contains | … |
| quarto    | Room22 | Floor02 | contains | … |

O código a seguir usa a [API Microsoft Graph](https://docs.microsoft.com/graph/overview) para ler uma planilha e construir um grafo do Azure digital gêmeos bispersão dos resultados.

```csharp
var range = msftGraphClient.Me.Drive.Items["BuildingsWorkbook"].Workbook.Worksheets["Building"].usedRange;
JsonDocument data = JsonDocument.Parse(range.values);
List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
foreach (JsonElement row in data.RootElement.EnumerateArray())
{
    string type = row[0].GetString();
    string id = row[1].GetString();
    string relSource = row[2].GetString();
    string relName = row[3].GetString();
    // Parse spreadsheet extra data into a JSON string to initialize the digital twin
    // Left out for compactness
    Dictionary<string, object> initData = new Dictionary<string, object>() { ... };

    if (relSource != null)
    {
        BasicRelationship br = new BasicRelationship()
        {
            SourceId = relSource,
            TargetId = id,
            Name = relName
        };
        RelationshipRecordList.Add(br);
    }

    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.CustomProperties = initData;
    // Set the type of twin to be created
    switch (type)
    {
        case "room":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Room;2" };
            break;
        case "floor":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Floor;2" };
            break;
        ... handle additional types
    }
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
    }
    catch (RequestFailedException e)
    {
        Log.Error($"Error {e.Status}: {e.Message}");
    }
    foreach (BasicRelationship rec in RelationshipRecordList)
    { 
        try { 
            client.CreateRelationship(rec.SourceId, Guid.NewGuid().ToString(), JsonSerializer.Serialize<BasicRelationship>(rec));
        }
        catch (RequestFailedException e)
        {
            Log.Error($"Error {e.Status}: {e.Message}");
        }
    }
}
```
## <a name="manage-relationships-with-cli"></a>Gerenciar relações com a CLI

Gêmeos e suas relações também podem ser gerenciadas usando a CLI do gêmeos digital do Azure. Os comandos podem ser encontrados em [*How-to: Use the Azure digital gêmeos CLI*](how-to-use-cli.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como consultar um grafo gêmeos do Azure digital:
* [*Conceitos: linguagem de consulta*](concepts-query-language.md)
* [*Como consultar o gráfico de entrelaçamento*](how-to-query-graph.md)