---
title: Gerenciar o grafo gêmeo com relacionamentos
titleSuffix: Azure Digital Twins
description: Veja como gerenciar um grafo de gêmeos digital conectando-os com relações.
author: baanders
ms.author: baanders
ms.date: 11/03/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 78e0bfb0af494ecae2865fcc42679b8fcce44916
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359571"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Gerenciar um grafo de gêmeos digital usando relações

O coração do Azure digital gêmeos é o [grafo de entrelaçamento](concepts-twins-graph.md) que representa todo o seu ambiente. O gráfico de entrelaçamento é feito de gêmeos digitais individuais conectados por meio de **relações**. 

Depois que você tiver uma [instância do gêmeos do Azure digital](how-to-set-up-instance-portal.md) em funcionamento e tiver configurado o código de [autenticação](how-to-authenticate-client.md) em seu aplicativo cliente, poderá usar as [**APIs do DigitalTwins**](/rest/api/digital-twins/dataplane/twins) para criar, modificar e excluir gêmeos digitais e suas relações em uma instância do gêmeos digital do Azure. Você também pode usar o [SDK do .net (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)ou a [CLI do gêmeos digital do Azure](how-to-use-cli.md).

Este artigo se concentra no gerenciamento de relações e no grafo como um todo; para trabalhar com gêmeos digitais individuais, consulte [*como gerenciar o digital gêmeos*](how-to-manage-twin.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]
    
[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Criar relações

As relações descrevem como diferentes gêmeos digitais são conectadas entre si, que formam a base do grafo de entrelaçamento.

As relações são criadas usando a `CreateOrReplaceRelationshipAsync()` chamada. 

Para criar uma relação, você precisa especificar:
* A ID de fonte de origem ( `srcId` no exemplo de código abaixo): a ID do "My" para onde a relação se origina.
* O ID de entrelaçamento de destino ( `targetId` no exemplo de código abaixo): a ID do ' filetreme "onde a relação chega.
* Um nome de relação ( `relName` no exemplo de código abaixo): o tipo genérico de relationship, algo como _Contains_.
* Uma ID de relação ( `relId` no exemplo de código abaixo): o nome específico para essa relação, algo como _Relationship1_.

A ID da relação deve ser exclusiva dentro da fonte fornecida. Ele não precisa ser globalmente exclusivo.
Por exemplo, para o *filefoo* , cada ID de relação específica deve ser exclusiva. No entanto, outra *barra* de entrelaçamento pode ter uma relação de saída que corresponde à mesma ID de uma relação *foo* .

O exemplo de código a seguir ilustra como criar uma relação em sua instância de gêmeos digital do Azure.

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateOrReplaceRelationshipAsync<BasicRelationship>(srcId, relId, relationship);
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
            
        }
```
Em seu método principal, agora você pode chamar a `CreateRelationship()` função para criar uma relação _Contains_ como esta: 

```csharp
await CreateRelationship(client, srcId, targetId, "contains");
```
Se você quiser criar várias relações, poderá repetir as chamadas para o mesmo método, passando tipos de relação diferentes para o argumento. 

Para obter mais informações sobre a classe auxiliar `BasicRelationship` , consulte [*How-to: Use the Azure digital gêmeos APIs and SDKs*](how-to-use-apis-sdks.md#serialization-helpers).

### <a name="create-multiple-relationships-between-twins"></a>Criar várias relações entre gêmeos

As relações podem ser classificadas como: 

* Relações de saída: relações que pertencem a esta conexão que aponta para fora para conectá-lo a outros gêmeos. O `GetRelationshipsAsync()` método é usado para obter relações de saída de um entrelaçamento.
* Relações de entrada: relações que pertencem a outros gêmeos que apontam para esse entrelaçamento para criar um link de "entrada". O `GetIncomingRelationshipsAsync()` método é usado para obter relações de entrada de um entrelaçamento.

Não há nenhuma restrição quanto ao número de relações que você pode ter entre dois gêmeos — você pode ter quantas relações entre gêmeos desejar. 

Isso significa que você pode expressar vários tipos diferentes de relações entre duas gêmeos ao mesmo tempo. Por exemplo, *a* bificar a pode ter uma relação *armazenada* e uma relação *fabricada* com a *n º B*.

Você pode até mesmo criar várias instâncias do mesmo tipo de relação entre os mesmos dois gêmeos, se desejar. Neste exemplo, a "n" pode ter duas relações *armazenadas* diferentes com a *n* º de cima, desde que as relações tenham *IDs de relação* diferentes.

## <a name="list-relationships"></a>Listar relações

Para acessar a lista de relações de **saída** para um determinado tenda no grafo, você pode usar o `GetRelationships()` método como este:

```csharp
await client.GetRelationships()
```

Isso retorna um `Azure.Pageable<T>` ou `Azure.AsyncPageable<T>` , dependendo se você usa a versão síncrona ou assíncrona da chamada.

Aqui está um exemplo que recupera uma lista de relações:

```csharp
public static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            try
            {
                // GetRelationshipsAsync will throw if an error occurs
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);
                List<BasicRelationship> results = new List<BasicRelationship>();
                await foreach (BasicRelationship rel in rels)
                {
                    results.Add(rel);
                    Console.WriteLine($"Found relationship-{rel.Name}->{rel.TargetId}");
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

```
Agora você pode chamar esse método para ver as relações de saída do gêmeos como esta:

```csharp
await FindOutgoingRelationshipsAsync(client, twin_Id);
```
Você pode usar as relações recuperadas para navegar para outros gêmeos em seu grafo. Para fazer isso, leia o `target` campo da relação retornada e use-o como a ID para a próxima chamada para `GetDigitalTwin()` .

### <a name="find-incoming-relationships-to-a-digital-twin"></a>Localizar relações de entrada para um email digital

O Azure digital gêmeos também tem uma API para localizar todas as _ relações * de *entrada* para um determinado 1. Isso geralmente é útil para navegação reversa ou ao excluir um "r".

O exemplo de código anterior se concentrou na localização de relações de saída de um ".". O exemplo a seguir é estruturado da mesma forma, mas localiza as relações de *entrada* para a.

Observe que as `IncomingRelationship` chamadas não retornam o corpo completo da relação.

```csharp
public static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine($"Found incoming relationship-{incomingRel.RelationshipId}");

                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }
```

Agora você pode chamar esse método para ver as relações de entrada do gêmeos assim:

```csharp
await FindIncomingRelationshipsAsync(client, twin_Id);
```
### <a name="list-all-twin-properties-and-relationships"></a>Listar todas as propriedades e relações de entrelaçamento

Usando os métodos acima para listar as relações de saída e de entrada para um tipo de dados, você pode criar um método que imprima as informações de cópias completas, incluindo as propriedades de ' s ' e os dois tipos de suas relações. Aqui está um método de exemplo, chamado `FetchAndPrintTwinAsync()` , mostrando como fazer isso.

```csharp  
private static async Task FetchAndPrintTwinAsync(DigitalTwinsClient client, string twin_Id)
        {
            BasicDigitalTwin twin;
            Response<BasicDigitalTwin> res = client.GetDigitalTwin(twin_Id);
            
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }
```

Agora você pode chamar essa função em seu método principal como este: 

```csharp
await FetchAndPrintTwinAsync(client, targetId);
```
## <a name="delete-relationships"></a>Excluir relações

O primeiro parâmetro especifica a fonte de los (o nome do entrelaçado no qual a relação se origina). O outro parâmetro é a ID da relação. Você precisa da ID de FileUp e da ID de relação, pois as IDs de relação são exclusivas apenas no escopo de um entrelaçamento.

```csharp
private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId, string relId)
        {
            try
            {
                Response response = await client.DeleteRelationshipAsync(srcId, relId);
                await FetchAndPrintTwinAsync(srcId, client);
                Console.WriteLine("Deleted relationship successfully");
            }
            catch (RequestFailedException Ex)
            {
                Console.WriteLine($"Error {Ex.ErrorCode}");
            }
        }
```

Agora você pode chamar esse método para excluir uma relação como esta:

```csharp
await DeleteRelationship(client, srcId, relId);
```
## <a name="create-a-twin-graph"></a>Criar um gráfico de entrelaçamento 

O seguinte trecho de código executável usa as operações de relação deste artigo para criar um grafo de bispersão de gêmeos e relações digitais.

### <a name="set-up-the-runnable-sample"></a>Configurar o exemplo executável

O trecho de código usa o [*Room.js*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) e [*Floor.jsem*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) definições de modelo do [*tutorial: explorar o gêmeos digital do Azure com um aplicativo cliente de exemplo*](tutorial-command-line-app.md). Você pode usar esses links para ir diretamente para os arquivos ou baixá-los como parte do projeto de exemplo completo de ponta a ponta [aqui](/samples/azure-samples/digital-twins-samples/digital-twins-samples/). 

Antes de executar o exemplo, faça o seguinte:
1. Baixe os arquivos de modelo, coloque-os em seu projeto e substitua os `<path-to>` espaços reservados no código abaixo para informar o programa onde encontrá-los.
2. Substitua o espaço reservado `<your-instance-hostname>` pelo nome de host da instância do gêmeos digital do Azure.
3. Adicione estes pacotes ao seu projeto:
    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity
    ```

Você também precisará configurar as credenciais locais se desejar executar o exemplo diretamente. A próxima seção descreve isso.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>Execute o exemplo

Depois de concluir as etapas acima, você poderá executar diretamente o código de exemplo a seguir.

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

        public static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");

            //Create the Azure Digital Twins client for API calls
            DigitalTwinsClient client = createDTClient();
            Console.WriteLine($"Service client created – ready to go");
            Console.WriteLine();

            //Upload models
            Console.WriteLine($"Upload models");
            Console.WriteLine();
            string dtdl = File.ReadAllText("<path-to>/Room.json");
            string dtdl1 = File.ReadAllText("<path-to>/Floor.json");
            var typeList = new List<string>();
            typeList.Add(dtdl);
            typeList.Add(dtdl1);
            // Upload the models to the service
            await client.CreateModelsAsync(typeList);

            //Create new (Floor) digital twin
            BasicDigitalTwin floorTwin = new BasicDigitalTwin();
            string srcId = "myFloorID";
            floorTwin.Metadata = new DigitalTwinMetadata();
            floorTwin.Metadata.ModelId = "dtmi:example:Floor;1";
            //Floor twins have no properties, so nothing to initialize
            //Create the twin
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(srcId, floorTwin);
            Console.WriteLine("Twin created successfully");

            //Create second (Room) digital twin
            BasicDigitalTwin roomTwin = new BasicDigitalTwin();
            string targetId = "myRoomID";
            roomTwin.Metadata = new DigitalTwinMetadata();
            roomTwin.Metadata.ModelId = "dtmi:example:Room;1";
            // Initialize properties
            Dictionary<string, object> props = new Dictionary<string, object>();
            props.Add("Temperature", 35.0);
            props.Add("Humidity", 55.0);
            roomTwin.Contents = props;
            //Create the twin
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(targetId, roomTwin);
            
            //Create relationship between them
            await CreateRelationship(client, srcId, targetId, "contains");
            Console.WriteLine();

            //Print twins and their relationships
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Delete the relationship
            Console.WriteLine("Deleting the relationship");
            await DeleteRelationship(client, srcId, $"{srcId}-contains->{targetId}");
            Console.WriteLine();

            //Print twins and their relationships again
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();
        }

        private static DigitalTwinsClient createDTClient()
        {
            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            return client;
        }
        private async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            // Create relationship between twins
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateOrReplaceRelationshipAsync<BasicRelationship>(srcId, relId, relationship);
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }

        }

        private static async Task FetchAndPrintTwinAsync(string twin_Id, DigitalTwinsClient client)
        {
            BasicDigitalTwin twin;
            Response<BasicDigitalTwin> res = client.GetDigitalTwin(twin_Id);
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }

        private static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            
            try
            {
                // GetRelationshipsAsync will throw if an error occurs
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);
                List<BasicRelationship> results = new List<BasicRelationship>();
                await foreach (BasicRelationship rel in rels)
                {
                    results.Add(rel);
                    Console.WriteLine($"Found relationship-{rel.Name}->{rel.TargetId}");
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        private static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine($"Found incoming relationship-{incomingRel.RelationshipId}");
                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId, string relId)
        {
            try
            {
                Response response = await client.DeleteRelationshipAsync(srcId, relId);
                await FetchAndPrintTwinAsync(srcId, client);
                Console.WriteLine("Deleted relationship successfully");
            }
            catch (RequestFailedException Ex)
            {
                Console.WriteLine($"Error {Ex.ErrorCode}");
            }
        }
    }
}
```

Aqui está a saída do console do programa acima: 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="Saída do console mostrando os detalhes de entrelaçamento, as relações de entrada e saída do gêmeos." lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> O gráfico de entrelaçamento é um conceito de criação de relações entre gêmeos. Se você quiser exibir a representação visual do grafo de entrelaçamento, consulte a seção [_Visualization *](how-to-manage-graph.md#visualization) deste artigo. 

### <a name="create-a-twin-graph-from-a-csv-file"></a>Criar um gráfico de entrelaçamento de um arquivo CSV

Em casos de uso prático, muitas vezes, as hierarquias de entrelaçamento serão criadas a partir dos dados armazenados em um banco de dado diferente, ou talvez em uma planilha ou em um arquivo CSV. Esta seção ilustra como ler dados de um arquivo CSV e criar um gráfico de cima para fora dele.

Considere a tabela de dados a seguir, descrevendo um conjunto de gêmeos e relações digitais.

|  ID do Modelo    | ID de entrelaça (deve ser exclusivo) | Nome da Relação  | ID do entrelaçado de destino  | Dados de inicialização de entrelaçamento |
| --- | --- | --- | --- | --- |
| dtmi: exemplo: Floor; 1    | Floor1 | contém | Room1 | |
| dtmi: exemplo: Floor; 1    | Floor0 | contém | Room0 | |
| dtmi: exemplo: sala; 1    | Room1 | | | {"Temperatura": 80} |
| dtmi: exemplo: sala; 1    | Room0 | | | {"Temperatura": 70} |

Uma maneira de obter esses dados no Azure digital gêmeos é converter a tabela em um arquivo CSV e gravar o código para interpretar o arquivo em comandos para criar gêmeos e relações. O exemplo de código a seguir ilustra a leitura dos dados do arquivo CSV e a criação de um grafo de entrelaçamento no Azure digital gêmeos.

No código abaixo, o arquivo CSV é chamado de *data.csv* , e há um espaço reservado que representa o **nome do host** da instância de gêmeos digital do Azure. O exemplo também usa vários pacotes que você pode adicionar ao seu projeto para ajudar com esse processo.

```csharp
using System;
using System.Collections.Generic;
using System.Text.Json;
using System.Threading.Tasks;
using Azure;
using Azure.DigitalTwins.Core;
using Azure.Identity;

namespace creating_twin_graph_from_csv
{
    class Program
    {
        static async Task Main(string[] args)
        {
            List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
            List<BasicDigitalTwin> TwinList = new List<BasicDigitalTwin>();
            List<List<string>> data = ReadData();
            DigitalTwinsClient client = createDTClient();

            // Interpret the CSV file data, by each row
            foreach (List<string> row in data)
            {
                string modelID = row.Count > 0 ? row[0].Trim() : null;
                string srcID = row.Count > 1 ? row[1].Trim() : null;
                string relName = row.Count > 2 ? row[2].Trim() : null;
                string targetID = row.Count > 3 ? row[3].Trim() : null;
                string initProperties = row.Count > 4 ? row[4].Trim() : null;
                Console.WriteLine($"ModelID: {modelID}, TwinID: {srcID}, RelName: {relName}, TargetID: {targetID}, InitData: {initProperties}");
                Dictionary<string, object> props = new Dictionary<string, object>();
                // Parse properties into dictionary (left out for compactness)
                // ...

                // Null check for source and target ID's
                if (srcID != null && srcID.Length > 0 && targetID != null && targetID.Length > 0)
                {
                    BasicRelationship br = new BasicRelationship()
                    {
                        SourceId = srcID,
                        TargetId = targetID,
                        Name = relName
                    };
                    RelationshipRecordList.Add(br);
                }
                BasicDigitalTwin srcTwin = new BasicDigitalTwin();
                srcTwin.Id = srcID;
                srcTwin.Metadata = new DigitalTwinMetadata();
                srcTwin.Metadata.ModelId = modelID;
                srcTwin.Contents = props;
                TwinList.Add(srcTwin);
            }

            // Create digital twins 
            foreach (BasicDigitalTwin twin in TwinList)
            {
                try
                {
                    await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(twin.Id, twin);
                    Console.WriteLine("Twin is created");
                }
                catch (RequestFailedException e)
                {
                    Console.WriteLine($"Error {e.Status}: {e.Message}");
                }
            }
            // Create relationships between the twins
            foreach (BasicRelationship rec in RelationshipRecordList)
            {
                try
                {
                    string relId = $"{rec.SourceId}-{rec.Name}->{rec.TargetId}";
                    await client.CreateOrReplaceRelationshipAsync<BasicRelationship>(rec.SourceId, relId, rec);
                    Console.WriteLine("Relationship is created");
                }
                catch (RequestFailedException e)
                {
                    Console.WriteLine($"Error {e.Status}: {e.Message}");
                }
            }
        }

        // Method to ingest data from the CSV file
        public static List<List<string>> ReadData()
        {
            string path = "<path-to>/data.csv";
            string[] lines = System.IO.File.ReadAllLines(path);
            List<List<string>> data = new List<List<string>>();
            int count = 0;
            foreach (string line in lines)
            {
                if (count++ == 0)
                    continue;
                List<string> cols = new List<string>();
                data.Add(cols);
                string[] columns = line.Split(',');
                foreach (string column in columns)
                {
                    cols.Add(column);
                }
            }
            return data;
        }
        // Method to create the digital twins client
        private static DigitalTwinsClient createDTClient()
        {

            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            return client;
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