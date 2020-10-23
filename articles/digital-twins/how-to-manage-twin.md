---
title: Gerenciar gêmeos digitais
titleSuffix: Azure Digital Twins
description: Consulte como recuperar, atualizar e excluir gêmeos e relações individuais.
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 58ee064d4946442bff70e97d56a68080333e2197
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426149"
---
# <a name="manage-digital-twins"></a>Gerenciar gêmeos digitais

As entidades em seu ambiente são representadas por [gêmeos digital](concepts-twins-graph.md). Gerenciar seu gêmeos digital pode incluir criação, modificação e remoção. Para executar essas operações, você pode usar as [**APIs do DigitalTwins**](how-to-use-apis-sdks.md), o [SDK do .net (C#)](https://www.nuget.org/packages/Azure.DigitalTwins.Core)ou a [CLI do gêmeos digital do Azure](how-to-use-cli.md).

Este artigo se concentra no gerenciamento de gêmeos digitais; para trabalhar com relações e o [gráfico de bispersão](concepts-twins-graph.md) como um todo, confira [*como gerenciar o grafo de entrelaçamento com relações*](how-to-manage-graph.md).

> [!TIP]
> Todas as funções do SDK são fornecidas em versões síncronas e assíncronas.

## <a name="create-a-digital-twin"></a>Criar um teledigital

Para criar um entrelaçamento, use o `CreateDigitalTwin()` método no cliente de serviço da seguinte maneira:

```csharp
await client.CreateDigitalTwinAsync("myTwinId", initData);
```

Para criar um teledigital, você precisa fornecer:
* A ID desejada para o teledigital
* O [modelo](concepts-models.md) que você deseja usar

Opcionalmente, você pode fornecer valores iniciais para todas as propriedades da digital. 

O modelo e os valores de propriedade inicial são fornecidos por meio do `initData` parâmetro, que é uma cadeia de caracteres JSON que contém os dados relevantes. Para obter mais informações sobre como estruturar esse objeto, vá para a próxima seção.

> [!TIP]
> Depois de criar ou atualizar um vertical, pode haver uma latência de até 10 segundos antes que as alterações sejam refletidas nas [consultas](how-to-query-graph.md). A `GetDigitalTwin` API (descrita [mais adiante neste artigo](#get-data-for-a-digital-twin)) não experimenta esse atraso, portanto, se você precisar de uma resposta instantânea, use a chamada à API em vez de consultar para ver seu gêmeos recém-criado. 

### <a name="initialize-model-and-properties"></a>Inicializar modelo e propriedades

A API de criação de entrelaçamento aceita um objeto que é serializado em uma descrição JSON válida das propriedades de entrelaçamento. Consulte [*Concepts: digital gêmeos e o gráfico de bispersão*](concepts-twins-graph.md) para obter uma descrição do formato JSON para um "r". 

Primeiro, você pode criar um objeto de dados para representar os dados de propriedade e de propriedades. Em seguida, você pode usar `JsonSerializer` para passar uma versão serializada desse objeto para a chamada à API para o `initdata` parâmetro, desta forma:

```csharp
await client.CreateDigitalTwinAsync(srcId, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
```
Você pode criar um objeto de parâmetro manualmente ou usando uma classe auxiliar fornecida. Aqui está um exemplo de cada um.

#### <a name="create-twins-using-manually-created-data"></a>Criar gêmeos usando dados criados manualmente

Sem o uso de classes auxiliares personalizadas, você pode representar as propriedades de uma folha em um `Dictionary<string, object>` , em que `string` é o nome da propriedade e `object` é um objeto que representa a propriedade e seu valor.

[!INCLUDE [Azure Digital Twins code: create twin](../../includes/digital-twins-code-create-twin.md)]

#### <a name="create-twins-with-the-helper-class"></a>Criar gêmeos com a classe auxiliar

A classe auxiliar do `BasicDigitalTwin` permite que você armazene campos de propriedade em um objeto "" ". Talvez você ainda queira criar a lista de propriedades usando um `Dictionary<string, object>` , que pode então ser adicionado ao objeto de entrelaçamento `CustomProperties` diretamente.

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
props.Add("Humidity", 50.0);
twin.CustomProperties = props;

client.CreateDigitalTwinAsync("myRoomId", JsonSerializer.Serialize<BasicDigitalTwin>(twin));
Console.WriteLine("The twin is created successfully");
```

>[!NOTE]
> `BasicDigitalTwin` os objetos vêm com um `Id` campo. Você pode deixar esse campo vazio, mas se você adicionar um valor de ID, ele precisará corresponder ao parâmetro de ID passado para a `CreateDigitalTwin()` chamada. Por exemplo:
>
>```csharp
>twin.Id = "myRoomId";
>```

## <a name="get-data-for-a-digital-twin"></a>Obter dados de uma teledigital

Você pode acessar os detalhes de qualquer tipo de informações digitais chamando o `GetDigitalTwin()` método como este:

```csharp
object result = await client.GetDigitalTwin(id);
```
Essa chamada retorna dados de texto como uma cadeia de caracteres JSON. Aqui está um exemplo de como usar isso para exibir os detalhes de entrelaçamento:

```csharp
Response<string> res = client.GetDigitalTwin("myRoomId");
twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.CustomProperties.Keys)
{
  if (twin.CustomProperties.TryGetValue(prop, out object value))
  Console.WriteLine($"Property '{prop}': {value}");
}
```
Somente as propriedades que foram definidas pelo menos uma vez são retornadas quando você recupera um conjunto de entrelaçar com o `GetDigitalTwin()` método.

>[!TIP]
>O `displayName` para um papel de cópia é parte de seus metadados de modelo, portanto, ele não será exibido ao obter dados para a instância de a. Para ver esse valor, você pode [recuperá-lo a partir do modelo](how-to-manage-model.md#retrieve-models).

Para recuperar vários gêmeos usando uma única chamada à API, consulte os exemplos de API de consulta em [*instruções: consultar o gráfico de entrelaçamento*](how-to-query-graph.md).

Considere o modelo a seguir (escrito na [DTDL (digital gêmeos Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL)) que define uma *lua*:

```json
{
    "@id": "dtmi:example:Moon;1",
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
O resultado da chamada `object result = await client.GetDigitalTwinAsync("my-moon");` em um tipo de *lua*de texto pode ser semelhante ao seguinte:

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:example:Moon;1",
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
* `$etag`, um campo HTTP padrão atribuído pelo servidor Web.
* Outras propriedades em uma `$metadata` seção. Elas incluem:
    - O DTMI do modelo do teledigital.
    - Status de sincronização para cada propriedade gravável. Isso é mais útil para dispositivos, em que é possível que o serviço e o dispositivo tenham status divergente (por exemplo, quando um dispositivo estiver offline). Atualmente, essa propriedade só se aplica a dispositivos físicos conectados ao Hub IoT. Com os dados na seção de metadados, é possível entender o status completo de uma propriedade, bem como os últimos carimbos de data/hora modificados. Para obter mais informações sobre o status de sincronização, consulte [este tutorial do Hub IOT](../iot-hub/tutorial-device-twins.md) sobre como sincronizar o estado do dispositivo.
    - Metadados específicos do serviço, como do Hub IoT ou do gêmeos digital do Azure. 

Você pode analisar o JSON retornado para o tipo de entrelaçamento usando uma biblioteca de análise de JSON de sua escolha, como `System.Text.Json` .

Você também pode usar a classe auxiliar de serialização `BasicDigitalTwin` que está incluída no SDK, que retornará os metadados e as propriedades principais de entrelaçamento no formulário previamente analisado. Veja um exemplo:

```csharp
Response<string> res = client.GetDigitalTwin(twin_Id);
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

Para atualizar as propriedades de uma troca digital, você escreve as informações que deseja substituir no formato de [patch JSON](http://jsonpatch.com/) . Dessa forma, você pode substituir várias propriedades de uma só vez. Em seguida, você passa o documento de patch JSON para um `UpdateDigitalTwin()` método:

```csharp
await client.UpdateDigitalTwin(id, patch);
```

Uma chamada de patch pode atualizar tantas propriedades em um único número de atualizações que você deseja (até mesmo todas elas). Se precisar atualizar as propriedades em vários gêmeos, você precisará de uma chamada de atualização separada para cada um.

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

await client.UpdateDigitalTwinAsync(twin_Id, JsonSerializer.Serialize(twinData));
Console.WriteLine("Updated twin properties");
FetchAndPrintTwin(twin_Id, client);
}
```

#### <a name="create-patches-using-the-helper-class"></a>Criar patches usando a classe auxiliar

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", 25.0);
await client.UpdateDigitalTwinAsync(twin_Id, uou.Serialize());
```

### <a name="update-properties-in-digital-twin-components"></a>Atualizar propriedades em componentes de atualização digitais

Lembre-se de que um modelo pode conter componentes, permitindo que ele seja composto por outros modelos. 

Para aplicar as propriedades de patch em um dos componentes digitais do r, você pode usar a sintaxe de caminho no patch JSON:

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

A `UpdateDigitalTwin()` função também pode ser usada para migrar uma migração digital para um modelo diferente. 

Por exemplo, considere o seguinte documento de patch JSON que substitui o campo de metadados do digital bificado `$model` :

```json
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:example:foo;1"
  }
]
```

Esta operação só terá sucesso se o tipo de atualização digital estiver sendo modificado pelo patch estiver em conformidade com o novo modelo. 

Considere o seguinte exemplo:
1. Imagine um "up digital" com um modelo de *foo_old*. *foo_old* define uma propriedade necessária *em massa*.
2. O novo modelo *foo_new* define uma propriedade em massa e adiciona uma nova *temperatura*de propriedade necessária.
3. Após o patch, o FileUp digital deve ter uma propriedade Mass e de temperatura. 

O patch para essa situação precisa atualizar o modelo e a propriedade de temperatura de entrelaçamento, desta forma:

```json
[
  {
    "op": "replace",
    "path": "$metadata.$model",
    "value": "dtmi:example:foo_new"
  },
  {
    "op": "add",
    "path": "temperature",
    "value": 60
  }
]
```

### <a name="handle-conflicting-update-calls"></a>Tratar chamadas de atualização conflitantes

O Azure digital gêmeos garante que todas as solicitações de entrada sejam processadas uma após a outra. Isso significa que, mesmo que várias funções tentem atualizar a mesma propriedade em um entrelaçamento ao mesmo tempo, não há **necessidade** de escrever código de bloqueio explícito para lidar com o conflito.

Esse comportamento está em uma base cada vez mais. 

Como exemplo, imagine um cenário no qual essas três chamadas chegam ao mesmo tempo: 
*   Gravar A propriedade A em *Twin1*
*   Gravar a propriedade B em *Twin1*
*   Gravar A propriedade A em *Twin2*

As duas chamadas que modificam *Twin1* são executadas uma após a outra e as mensagens de alteração são geradas para cada alteração. A chamada para Modify *Twin2* pode ser executada simultaneamente sem conflito, assim que ela chega.

## <a name="delete-a-digital-twin"></a>Excluir uma teledigital

Você pode excluir gêmeos usando o `DeleteDigitalTwin()` método. No entanto, você só pode excluir um "n" quando ele não tiver mais relações. Portanto, exclua as relações de entrada e saída do "s" primeiro.

Aqui está um exemplo do código para excluir gêmeos e suas relações:

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
        Log.Error($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
    }
}

async Task FindAndDeleteIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        await foreach (IncomingRelationship incomingRel in incomingRels)
        {
            await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
            Log.Ok($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"_*_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
    }
}
```
### <a name="delete-all-digital-twins"></a>Excluir todos os gêmeos digitais

Para obter um exemplo de como excluir todos os gêmeos de uma vez, baixe o aplicativo de exemplo usado no [_Tutorial: Explore os conceitos básicos com um aplicativo cliente de exemplo *](tutorial-command-line-app.md). O arquivo *CommandLoop.cs* faz isso em uma `CommandDeleteAllTwins()` função.

## <a name="manage-twins-using-runnable-code-sample"></a>Gerenciar gêmeos usando o exemplo de código executável

Você pode usar o exemplo de código executável abaixo para criar um entrelaçar, atualizar seus detalhes e excluir o. 

O trecho de código usa o [Room.jsna](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) definição do modelo do [*tutorial: explorar o gêmeos digital do Azure com um aplicativo cliente de exemplo*](tutorial-command-line-app.md). Você pode usar esse link para ir diretamente para o arquivo ou baixá-lo como parte do projeto de exemplo completo de ponta a ponta [aqui](/samples/azure-samples/digital-twins-samples/digital-twins-samples/).

Substitua o espaço reservado `<your-instance-hostname>` pelos detalhes da instância do gêmeos digital do Azure e execute o exemplo.

```csharp
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {

        static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            Console.WriteLine();
            Console.WriteLine($"Upload a model");
            BasicDigitalTwin twin = new BasicDigitalTwin();
            var typeList = new List<string>();
            string twin_Id = "myRoomId";
            string dtdl = File.ReadAllText("Room.json");
            typeList.Add(dtdl);
            // Upload the model to the service
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            Console.WriteLine($"Service client created – ready to go");
            await client.CreateModelsAsync(typeList);
            twin.Metadata = new DigitalTwinMetadata();
            twin.Metadata.ModelId = "dtmi:example:Room;1";
            // Initialize properties
            Dictionary<string, object> props = new Dictionary<string, object>();
            props.Add("Temperature", 35.0);
            props.Add("Humidity", 55.0);
            twin.CustomProperties = props;
            await client.CreateDigitalTwinAsync(twin_Id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
            Console.WriteLine("Twin created successfully");
            twin = FetchAndPrintTwin(twin_Id, client);
            List<object> twinData = new List<object>();
            twinData.Add(new Dictionary<string, object>() 
            {
                { "op", "add"},
                { "path", "/Temperature"},
                { "value", 25.0}
            });

            await client.UpdateDigitalTwinAsync(twin_Id, JsonSerializer.Serialize(twinData));
            Console.WriteLine("Updated Twin Properties");
            FetchAndPrintTwin(twin_Id, client);
            await DeleteTwin(client, twin_Id);
        }

        private static BasicDigitalTwin FetchAndPrintTwin(string twin_Id, DigitalTwinsClient client)
        {
            BasicDigitalTwin twin;
            Response<string> res = client.GetDigitalTwin(twin_Id);
            twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
            Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
            foreach (string prop in twin.CustomProperties.Keys)
            {
                if (twin.CustomProperties.TryGetValue(prop, out object value))
                    Console.WriteLine($"Property '{prop}': {value}");
            }

            return twin;
        }
        static async Task DeleteTwin(DigitalTwinsClient client, string id)
        {
            await FindAndDeleteOutgoingRelationshipsAsync(client, id);
            await FindAndDeleteIncomingRelationshipsAsync(client, id);
            try
            {
                await client.DeleteDigitalTwinAsync(id);
                Console.WriteLine("Twin deleted successfully");
                FetchAndPrintTwin(id, client);
            }
            catch (RequestFailedException exc)
            {
                Console.WriteLine($"*** Error:{exc.Message}");
            }
        }

        public static async Task FindAndDeleteOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
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
                    Console.WriteLine($"Deleted relationship {rel.Id} from {dtId}");
                }
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
            }
        }

       static async Task FindAndDeleteIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin

            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
                    Console.WriteLine($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
                }
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"_*_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
            }
        }

    }
}

```
Aqui está a saída do console do programa acima: 

:::image type="content" source="./media/how-to-manage-twin/console-output-manage-twins.png" alt-text="Saída do console mostrando que o &quot;atualizar&quot; é criado, atualizado e excluído" lightbox="./media/how-to-manage-twin/console-output-manage-twins.png":::

## <a name="manage-twins-with-cli"></a>Gerenciar o gêmeos com a CLI

O gêmeos também pode ser gerenciado usando a CLI do gêmeos digital do Azure. Os comandos podem ser encontrados em [_How-to: Use a CLI do gêmeos digital do Azure *](how-to-use-cli.md).

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

## <a name="view-all-digital-twins"></a>Exibir todos os gêmeos digitais

Para exibir todas as gêmeos digitais em sua instância, use uma [consulta](how-to-query-graph.md). Você pode executar uma consulta com as [APIs de consulta](how-to-use-apis-sdks.md) ou os [comandos da CLI](how-to-use-cli.md).

Aqui está o corpo da consulta básica que retornará uma lista de todos os gêmeos digitais na instância:

```sql
SELECT *
FROM DIGITALTWINS
``` 

## <a name="next-steps"></a>Próximas etapas

Consulte como criar e gerenciar relações entre seu gêmeos digital:
* [*Como: gerenciar o grafo de entrelaçamento com relações*](how-to-manage-graph.md)