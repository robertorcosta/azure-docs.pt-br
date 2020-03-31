---
title: Use a pesquisa cognitiva do Azure em .NET
titleSuffix: Azure Cognitive Search
description: Aprenda a usar o Azure Cognitive Search em um aplicativo .NET usando C# e o .NET SDK. As tarefas baseadas em códigos incluem a conexão com o serviço, conteúdo de índice e consulta a um índice.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b31a4e40c1e9095499faf265673ab4213ad6bde0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283063"
---
# <a name="how-to-use-azure-cognitive-search-from-a-net-application"></a>Como usar o Azure Cognitive Search a partir de um aplicativo .NET

Este artigo é um passo a passo para colocá-lo em funcionamento com o [Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk). Você pode usar o .NET SDK para implementar uma rica experiência de pesquisa em seu aplicativo usando o Azure Cognitive Search.

## <a name="whats-in-the-azure-cognitive-search-sdk"></a>O que há no Azure Cognitive Search SDK
O SDK consiste em algumas bibliotecas de clientes que permitem que você gerencie seus índices, fontes de dados, indexadores e mapas de sinônimos, carregue e gerencie documentos e execute consultas, sem a necessidade de lidar com os detalhes de HTTP e JSON. Essas bibliotecas de cliente são distribuídas como pacotes NuGet.

O principal pacote NuGet é `Microsoft.Azure.Search`, o que é um pacote meta que inclui todos os outros pacotes, como dependências. Use este pacote se você está apenas começando ou se você sabe que seu aplicativo precisará de todos os recursos do Azure Cognitive Search.

Os outros pacotes NuGet no SDK são:
 
  - `Microsoft.Azure.Search.Data`: Use este pacote se estiver desenvolvendo um aplicativo .NET usando o Azure Cognitive Search e você só precisa consultar ou atualizar documentos em seus índices. Se você também precisa criar ou atualizar índices, mapas de sinônimo ou outros recursos de nível de serviço, use o `Microsoft.Azure.Search` pacote em vez disso.
  - `Microsoft.Azure.Search.Service`: Use este pacote se estiver desenvolvendo automação no .NET para gerenciar índices de pesquisa cognitiva do Azure, mapas de sinônimos, indexadores, fontes de dados ou outros recursos de nível de serviço. Se você precisar apenas consultar ou atualizar em seus índices, use o `Microsoft.Azure.Search.Data` pacote em vez disso. Se você precisar de toda a funcionalidade do `Microsoft.Azure.Search` Azure Cognitive Search, use o pacote em vez disso.
  - `Microsoft.Azure.Search.Common`: Tipos comuns necessários pelas bibliotecas Azure Cognitive Search .NET. Você não precisa usar este pacote diretamente em seu aplicativo. Destina-se apenas a ser usado como uma dependência.

A diversas bibliotecas de clientes definem classes como `Index`, `Field` e `Document`, e operações como `Indexes.Create` e `Documents.Search` nas classes `SearchServiceClient` e `SearchIndexClient`. Essas classes são organizadas nos namespaces a seguir:

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Se você quiser fornecer feedback para uma futura atualização do SDK, consulte nossa página de [feedback](https://feedback.azure.com/forums/263029-azure-search/) ou crie um problema no [GitHub](https://github.com/azure/azure-sdk-for-net/issues) e mencione "Azure Cognitive Search" no título do problema.

O .NET SDK `2019-05-06` suporta a versão da [API Azure Cognitive Search REST](https://docs.microsoft.com/rest/api/searchservice/). Esta versão inclui suporte para [tipos complexos,](search-howto-complex-data-types.md) [enriquecimento de IA,](cognitive-search-concept-intro.md) [autopreenchimento](https://docs.microsoft.com/rest/api/searchservice/autocomplete)e [modo de análise JsonLines](search-howto-index-json-blobs.md) ao indexar Blobs Azure. 

Esse SDK não oferece suporte a [Operações de Gerenciamento](https://docs.microsoft.com/rest/api/searchmanagement/), como criar e dimensionar os serviços do Search e gerenciar chaves de API. Se você precisar gerenciar seus recursos de Pesquisa a partir de um aplicativo .NET, você pode usar o [Azure Cognitive Search .NET Management SDK](https://aka.ms/search-mgmt-sdk).

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Atualizando para a última versão do SDK
Se você já está usando uma versão mais antiga do Azure Cognitive Search .NET SDK e gostaria de atualizar para a versão mais recente geralmente disponível, [este artigo](search-dotnet-sdk-migration-version-9.md) explica como.

## <a name="requirements-for-the-sdk"></a>Requisitos para o SDK
1. Visual Studio 2017 ou posterior.
2. Seu próprio serviço de busca cognitiva do Azure. Para usar o SDK, você precisará do nome do serviço e de uma ou mais chaves de API. [Criar um serviço no portal](search-create-service-portal.md) ajudará você com estas etapas.
3. Baixe o pacote Azure Cognitive Search .NET SDK [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search) usando "Gerenciar pacotes NuGet" no Visual Studio. Basta procurar o nome do pacote `Microsoft.Azure.Search` em NuGet.org (ou um dos outros nomes de pacote acima se você precisar somente de um subconjunto da funcionalidade).

O Azure Cognitive Search .NET SDK suporta aplicativos direcionados ao .NET Framework 4.5.2 e superior, bem como .NET Core 2.0 e superior.

## <a name="core-scenarios"></a>Principais cenários
Há várias coisas que você precisará fazer em seu aplicativo de pesquisa. Neste tutorial, abordaremos os principais cenários:

* Criando um índice
* Preenchimento do índice com documentos
* Procura por documentos usando filtros e pesquisa de texto completo

O código amostral a seguir ilustra cada um desses cenários. Fique à vontade para usar os snippets de código em seu próprio aplicativo.

### <a name="overview"></a>Visão geral
O exemplo de aplicativo que vamos explorar cria um novo índice chamado "hotéis", preenche-o com alguns documentos e, em seguida, executa algumas consultas de pesquisa. Este é o programa principal, mostrando o fluxo geral:

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, serviceClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, serviceClient);

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);

    ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

> [!NOTE]
> Você pode encontrar o código-fonte completo do aplicativo de exemplo usado neste passo a passo no [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).
> 
>

Vamos examinar isso passo a passo. Primeiro, é necessário criar um novo `SearchServiceClient`. Esse objeto permite o gerenciamento de índices. Para construir um, você precisa fornecer o nome do seu serviço de pesquisa cognitiva do Azure, bem como uma chave aPI de administrador. Você pode inserir essas informações no arquivo `appsettings.json` do [aplicativo de exemplo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

> [!NOTE]
> Se você fornecer uma chave incorreta (por exemplo, uma chave de consulta quando era necessário fornecer uma chave de administrador), o `SearchServiceClient` lançará uma `CloudException` com a mensagem de erro "Forbidden" na primeira vez que você chamar um método de operação, como `Indexes.Create`. Se isso ocorrer, verifique novamente a chave da API.
> 
> 

As próximas linhas chamam métodos para criação de um índice chamado "hotéis," excluindo-o primeiro caso ele já exista. Abordaremos esses métodos um pouco mais tarde.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

Em seguida, o índice precisa ser preenchido. Para fazer o índice, precisaremos de um `SearchIndexClient`. Há duas maneiras de obter um: construindo ou chamando `Indexes.GetClient` no `SearchServiceClient`. Usaremos o último por conveniência.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> Em um aplicativo de pesquisa típico, o gerenciamento de índices e a população podem ser tratados por um componente separado das consultas de pesquisa. `Indexes.GetClient`é conveniente para preencher um índice porque economiza o `SearchCredentials`problema de fornecer adicional . Ele faz isso passando a chave de administrador que você usou para criar o `SearchServiceClient` ao novo `SearchIndexClient`. No entanto, na parte do seu aplicativo que executa `SearchIndexClient` consultas, é melhor criar o diretamente para que você possa passar em uma chave de consulta, que só permite ler dados, em vez de uma chave de admin. Isso está de acordo com o princípio de privilégios mínimos e ajuda a tornar seu aplicativo mais seguro. Você pode saber mais sobre chaves de administração e chaves de consulta [aqui](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Agora que temos um `SearchIndexClient`, podemos preencher o índice. O índice populacional é feito por outro método que vamos caminhar mais tarde.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Por fim, executamos algumas consultas de pesquisa e exibimos os resultados. Dessa vez, usamos outro `SearchIndexClient`:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

Posteriormente, faremos uma análise mais detalhada do método `RunQueries`. Este é o código para criar o novo `SearchIndexClient`:

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Dessa vez, usaremos uma chave de consulta, já que não precisamos de acesso de gravação para o índice. Você pode inserir essas informações no arquivo `appsettings.json` do [aplicativo de exemplo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Se você executar este aplicativo com um nome de serviço válido e chaves de API, a saída deve ficar parecida com este exemplo: (Alguma saída do console foi substituída por "..." para fins de ilustração.)

    Deleting index...

    Creating index...

    Uploading documents...

    Waiting for documents to be indexed...

    Search the entire index for the term 'motel' and return only the HotelName field:

    Name: Secret Point Motel

    Name: Twin Dome Motel


    Apply a filter to the index to find hotels with a room cheaper than $100 per night, and return the hotelId and description:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Times Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.

    HotelId: 2
    Description: The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.


    Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

    Name: Triple Landscape Hotel
    Last renovated on: 9/20/2015 12:00:00 AM +00:00

    Name: Twin Dome Motel
    Last renovated on: 2/18/1979 12:00:00 AM +00:00


    Search the hotel names for the term 'hotel':

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

    Complete.  Press any key to end application... 

O código-fonte completo do aplicativo é fornecido ao final deste artigo.

Em seguida, analisaremos com mais detalhes cada um dos métodos chamados pelo `Main`.

### <a name="creating-an-index"></a>Criando um índice
Depois de `SearchServiceClient` `Main` criar um , exclui o índice "hotéis" se ele já existir. Essa exclusão é feita pelo seguinte método:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

Esse método usa o `SearchServiceClient` fornecido para verificar se o índice existe e, nesse caso, excluí-lo.

> [!NOTE]
> O código de exemplo deste artigo usa os métodos síncronos do Azure Cognitive Search .NET SDK para simplicidade. Recomendamos que você use os métodos assíncronos em seus próprios aplicativos para mantê-los escalonáveis e responsivos. Por exemplo, no método acima você pode usar `ExistsAsync` e `DeleteAsync` em vez de `Exists` e `Delete`.
> 
> 

Em seguida, `Main` cria um novo índice "hotéis" chamando este método:

```csharp
private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    
    serviceClient.Indexes.Create(definition);
}
```

Esse método cria um novo objeto `Index` com uma lista de objetos `Field` que definem o esquema do novo índice. Cada campo tem um nome, tipo de dados e vários atributos que definem seu comportamento de pesquisa. A classe `FieldBuilder` usa a reflexão para criar uma lista de objetos `Field` para o índice, examinando os atributos e propriedades públicas da classe do modelo `Hotel` determinada. Posteriormente, faremos uma análise mais detalhada da classe `Hotel`.

> [!NOTE]
> Você pode criar quando desejar a lista de objetos `Field` diretamente, em vez de usar o `FieldBuilder` se necessário. Por exemplo, você pode não querer usar uma classe de modelo ou pode precisar usar uma classe de modelo existente que não deseja modificar adicionando atributos.
>
> 

Além dos campos, você também pode adicionar perfis de pontuação, sugestões ou opções cors ao Índice (esses parâmetros são omitidos da amostra para brevidade). Você pode encontrar mais informações sobre o objeto Index e suas partes constituintes na [referência SDK,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index)bem como na [referência a API de pesquisa cognitiva do Azure](https://docs.microsoft.com/rest/api/searchservice/).

### <a name="populating-the-index"></a>Preenchendo o índice
O próximo `Main` passo preenche o índice recém-criado. Esta população de índice é feita no seguinte método: (Alguns códigos substituídos por "..." para fins de ilustração.  Consulte a solução completa da amostra para o código populacional completo de dados.)

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
        new Hotel()
        {
            HotelId = "1",
            HotelName = "Secret Point Motel",
            ...
            Address = new Address()
            {
                StreetAddress = "677 5th Ave",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Budget Room, 1 Queen Bed (Cityside)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (City View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "2",
            HotelName = "Twin Dome Motel",
            ...
            {
                StreetAddress = "140 University Town Center Dr",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Suite, 2 Double Beds (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Standard Room, 1 Queen Bed (City View)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Waterfront View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "3",
            HotelName = "Triple Landscape Hotel",
            ...
            Address = new Address()
            {
                StreetAddress = "3393 Peachtree Rd",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Standard Room, 2 Queen Beds (Amenities)",
                    ...
                },
                new Room ()
                {
                    Description = "Standard Room, 2 Double Beds (Waterfront View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (Cityside)",
                    ...
                }
            }
        }
    };

    var batch = IndexBatch.Upload(hotels);

    try
    {
        indexClient.Documents.Index(batch);
    }
    catch (IndexBatchException e)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
}
```

Este método tem quatro partes. O primeiro cria uma `Hotel` matriz de `Room` 3 objetos cada um com 3 objetos que servirão como nossos dados de entrada para carregar para o índice. Esses dados são codificados para manter a simplicidade. Em seu próprio aplicativo, provavelmente seus dados virão de uma fonte de dados externa, como um banco de dados SQL.

A segunda parte cria um `IndexBatch` com os documentos. Especifique a operação que você quer aplicar ao lote no momento da criação dele, nesse caso, chamando `IndexBatch.Upload`. O lote é então carregado para o índice `Documents.Index` de Pesquisa Cognitiva do Azure pelo método.

> [!NOTE]
> Neste exemplo, estamos carregando apenas documentos. Se você quiser mesclar alterações em documentos existentes ou excluir documentos, poderá criar lotes chamando `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` ou `IndexBatch.Delete`. Você também pode misturar diferentes operações em um único lote, chamando `IndexBatch.New`, o que leva uma coleção de `IndexAction` objetos, cada um dos quais diz a Azure Cognitive Search para realizar uma operação específica em um documento. Você pode criar cada `IndexAction` com sua própria operação chamando o método correspondente como `IndexAction.Merge`, `IndexAction.Upload` e assim por diante.
> 
> 

A terceira parte desse método é um bloco catch que trata um caso de erro importante para indexação. Se o seu serviço de Pesquisa Cognitiva Do Azure `IndexBatchException` não conseguir `Documents.Index`indexar alguns dos documentos no lote, um é jogado por . Essa exceção pode acontecer se você estiver indexando documentos enquanto seu serviço estiver carga pesada. **É altamente recomendável a manipulação explícita desse caso em seu código.**  Você pode atrasar e repetir a indexação de documentos que falharam, ou você pode registrar em log e continuar, como faz o exemplo, ou pode alguma outra coisa, dependendo dos requisitos de consistência de dados do aplicativo.

> [!NOTE]
> Você pode [`FindFailedActionsToRetry`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) usar o método para construir um novo lote contendo `Index`apenas as ações que falharam em uma chamada anterior para . Há uma discussão sobre como usá-lo corretamente no [StackOverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Por fim, o método `UploadDocuments` atrasa por dois segundos. A indexação acontece de forma assíncrona no seu serviço de Pesquisa Cognitiva Do Azure, portanto, o aplicativo de amostra precisa esperar um pouco para garantir que os documentos estejam disponíveis para pesquisa. Normalmente, atrasos como esses só são necessários em demonstrações, testes e exemplos de aplicativos.

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>Como o SDK do .NET lida com documentos
Você pode estar se perguntando como o Azure Cognitive Search .NET SDK é capaz de carregar instâncias de uma classe definida pelo usuário como `Hotel` para o índice. Para ajudar a responder a essa pergunta, vamos examinar a classe `Hotel` :

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsSearchable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.EnLucene)]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("Description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    // SmokingAllowed reflects whether any room in the hotel allows smoking.
    // The JsonIgnore attribute indicates that a field should not be created 
    // in the index for this property and it will only be used by code in the client.
    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? Rating { get; set; }

    public Address Address { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }

    public Room[] Rooms { get; set; }
}
```

A primeira coisa a notar é que o `Hotel` nome de cada imóvel público da classe será mapeado para um campo com o mesmo nome na definição do índice. Se você quiser que cada campo comece com uma letra minúscula ("caso de camelo"), você pode dizer ao `[SerializePropertyNamesAsCamelCase]` SDK para mapear os nomes da propriedade para caso de camelo automaticamente com o atributo na classe. Esse cenário é comum em aplicativos .NET que executam vinculação de dados onde o esquema de destino está fora do controle do desenvolvedor de aplicativos sem ter que violar as diretrizes de nomeação "Caso Pascal" no .NET.

> [!NOTE]
> O Azure Cognitive Search .NET SDK usa a biblioteca [NewtonSoft JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) para serializar e desserializar seus objetos de modelo personalizados de e para JSON. Se necessário, você pode personalizar essa serialização. Para obter mais informações, consulte [Serialização personalizada com JSON.NET](#JsonDotNet).
> 
> 

A segunda coisa a notar é que cada `IsFilterable`propriedade `IsSearchable` `Key`é `Analyzer`decorada com atributos como, e . Esses atributos mapeiam diretamente para os [atributos de campo correspondentes em um índice de pesquisa cognitiva do Azure](/rest/api/searchservice/create-index). A `FieldBuilder` classe usa essas propriedades para construir definições de campo para o índice.

A terceira coisa `Hotel` importante sobre a classe são os tipos de dados das propriedades públicas. Os tipos .NET dessas propriedades são mapeados para seus tipos de campo equivalentes na definição do índice. Por exemplo, a propriedade de cadeia de caracteres `Category` mapeia para o campo `category`, que é do tipo `Edm.String`. Existem mapeamentos de `bool?` `Edm.Boolean`tipos `DateTimeOffset?`semelhantes entre , e `Edm.DateTimeOffset` assim por diante. As regras específicas para o mapeamento `Documents.Get` do tipo são documentadas com o método na [referência Azure Cognitive Search .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get). A classe `FieldBuilder` cuida desse mapeamento para você, mas ainda pode ser útil para entender caso você precise solucionar problemas de serialização.

Por acaso notou a `SmokingAllowed` propriedade?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

O `JsonIgnore` atributo nesta `FieldBuilder` propriedade diz para não serializá-lo para o índice como um campo.  Esta é uma ótima maneira de criar propriedades calculadas do lado do cliente que você pode usar como ajudantes em sua aplicação.  Neste caso, `SmokingAllowed` a propriedade `Room` reflete `Rooms` se algum da coleção permite fumar.  Se todos são falsos, indica que todo o hotel não permite fumar.

Algumas propriedades, `Address` `Rooms` como e são instâncias de classes .NET.  Essas propriedades representam estruturas de dados mais complexas e, como resultado, requerem campos com um [tipo de dados complexo](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) no índice.

A `Address` propriedade representa um conjunto de `Address` múltiplos valores na classe, definidos abaixo:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Address
    {
        [IsSearchable]
        public string StreetAddress { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string City { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string StateProvince { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string PostalCode { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string Country { get; set; }
    }
}
```

Esta classe contém os valores padrão usados para descrever endereços nos Estados Unidos ou Canadá. Você pode usar tipos como este para agrupar campos lógicos no índice.

A `Rooms` propriedade representa uma `Room` matriz de objetos:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Room
    {
        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string Type { get; set; }

        [IsFilterable, IsFacetable]
        public double? BaseRate { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string BedOptions { get; set; }

        [IsFilterable, IsFacetable]
        public int SleepsCount { get; set; }

        [IsFilterable, IsFacetable]
        public bool? SmokingAllowed { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }
    }
}
```

Seu modelo de dados em .NET e seu esquema de índice correspondente devem ser projetados para suportar a experiência de pesquisa que você gostaria de dar ao seu usuário final. Cada objeto de nível superior em .NET, ou seja, documento no índice, corresponde a um resultado de pesquisa que você apresentaria em sua interface de usuário. Por exemplo, em um aplicativo de pesquisa de hotel, seus usuários finais podem querer pesquisar pelo nome do hotel, características do hotel ou as características de um quarto específico. Vamos cobrir alguns exemplos de consulta um pouco mais tarde.

Essa capacidade de usar suas próprias classes para interagir com documentos no índice funciona em ambas as direções; Você também pode recuperar os resultados da pesquisa e fazer com que o SDK os desserialifique automaticamente para um tipo de sua escolha, como veremos na próxima seção.

> [!NOTE]
> O Azure Cognitive Search .NET SDK também suporta documentos `Document` digitados dinamicamente usando a classe, que é um mapeamento de chave/valor de nomes de campo para valores de campo. Isso é útil em cenários nos quais você não conhece o esquema de índice no momento do design, ou nos quais seria inconveniente associar a classes de modelo específico. Todos os métodos no SDK que lidam com documentos têm sobrecargas que funcionam com a classe `Document` , bem como sobrecargas fortemente tipadas que utilizam um parâmetro de tipo genérico. Somente as últimas são usadas no exemplo de código deste tutorial. A [ `Document` classe](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document) herda de `Dictionary<string, object>`.
> 
>

**Por que você deve usar tipos de dados anuláveis**

Ao projetar suas próprias classes de modelo para mapear um índice de Pesquisa Cognitiva `bool` `int` Do Azure, recomendamos declarar propriedades de tipos de valor como e ser anuladas (por exemplo, `bool?` em vez de `bool`). Se você usar uma propriedade não anulável, será preciso **assegurar** que nenhum documento no índice contenha um valor nulo para o campo correspondente. Nem o SDK nem o serviço de busca cognitiva do Azure ajudarão você a impor isso.

Isso não é apenas uma preocupação hipotética: imagine um cenário em que você adiciona um novo campo a um índice existente do tipo `Edm.Int32`. Após a atualização da definição do índice, todos os documentos terão um valor nulo para esse novo campo (já que todos os tipos são anulados na Pesquisa Cognitiva do Azure). Ao usar uma classe de modelo com uma propriedade não anulável `int` para esse campo, você obterá uma `JsonSerializationException` como esta ao tentar recuperar os documentos:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Por esse motivo, sugerimos que você use tipos anuláveis nas suas classes de modelo como uma prática recomendada.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Serialização personalizada com JSON.NET
O SDK usa JSON.NET para serializar e desserializar documentos. Você pode personalizar serialização e desserialização, se necessário, definindo o seu próprio `JsonConverter` ou `IContractResolver`. Para obter mais informações, consulte a [documentação JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm). Isso pode ser útil quando você deseja adaptar uma classe de modelo existente a partir de seu aplicativo para uso com o Azure Cognitive Search, e outros cenários mais avançados. Por exemplo, com a serialização personalizada, você pode:

* Incluir ou excluir determinadas propriedades da sua classe de modelo para serem armazenadas como campos de documento.
* Mapear os nomes de propriedade no código aos nomes de campo no índice.
* Crie atributos personalizados que podem ser usados para mapear propriedades para campos do documento.

Você pode encontrar exemplos de implementação de serialização personalizada nos testes de unidade para o Azure Cognitive Search .NET SDK no GitHub. Um bom ponto de partida é [esta pasta](https://github.com/Azure/azure-sdk-for-net/tree/4f6f4e4c90200c1b0621c4cead302a91e89f2aba/sdk/search/Microsoft.Azure.Search/tests/Tests/Models). Ela contém classes que são usadas pelos testes de serialização personalizada.

### <a name="searching-for-documents-in-the-index"></a>Pesquisando documentos no índice
A última etapa do aplicativo de amostra é procurar alguns documentos no índice:

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
    SearchParameters parameters;
    DocumentSearchResult<Hotel> results;

    Console.WriteLine("Search the entire index for the term 'motel' and return only the HotelName field:\n");

    parameters =
        new SearchParameters()
        {
            Select = new[] { "HotelName" }
        };

    results = indexClient.Documents.Search<Hotel>("motel", parameters);

    WriteDocuments(results);

    Console.Write("Apply a filter to the index to find hotels with a room cheaper than $100 per night, ");
    Console.WriteLine("and return the hotelId and description:\n");

    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(r: r/BaseRate lt 100)",
            Select = new[] { "HotelId", "Description" }
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    parameters =
        new SearchParameters()
        {
            OrderBy = new[] { "LastRenovationDate desc" },
            Select = new[] { "HotelName", "LastRenovationDate" },
            Top = 2
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.WriteLine("Search the entire index for the term 'hotel':\n");

    parameters = new SearchParameters();
    results = indexClient.Documents.Search<Hotel>("hotel", parameters);

    WriteDocuments(results);
}
```

Cada vez que ele executa uma consulta, esse método cria, primeiro, um novo objeto `SearchParameters`. Esse objeto é usado para especificar opções adicionais para a consulta, como classificação, filtragem, paginação e faceta. Nesse método, estamos definindo as propriedades de `Filter`, `Select`, `OrderBy` e `Top` para consultas diferentes. Todas as propriedades de `SearchParameters` são documentadas [aqui](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters).

A próxima etapa é executar a consulta de pesquisa. A execução da `Documents.Search` pesquisa é feita usando o método. Para cada consulta, passamos o texto de pesquisa a ser usado como uma cadeia de caracteres (ou `"*"` se não houver um texto de pesquisa) e também os parâmetros de pesquisa criados anteriormente. Também podemos especificar `Hotel` como o parâmetro de tipo para `Documents.Search`, que informa ao SDK para desserializar documentos nos resultados da pesquisa em objetos do tipo `Hotel`.

> [!NOTE]
> Você pode saber mais sobre a sintaxe de expressão da consulta de pesquisa [aqui](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).
> 
> 

Por fim, depois de cada consulta, esse método percorre todas as correspondências nos resultados da pesquisa, imprimindo cada documento no console:

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Vejamos cada uma das consultas com mais detalhes. Este é o código para executar a primeira consulta:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

Neste caso, estamos pesquisando todo o índice para a palavra "motel" em qualquer campo pesquisável e `Select` só queremos recuperar os nomes do hotel, conforme especificado pelo parâmetro. Estes são os resultados:

    Name: Secret Point Motel

    Name: Twin Dome Motel

A próxima consulta é um pouco mais interessante.  Queremos encontrar hotéis que tenham um quarto com uma diária inferior a US$ 100 e devolver apenas o ID do hotel e a descrição:

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)",
        Select = new[] { "HotelId", "Description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

Esta consulta usa uma expressão `$filter` do OData, `Rooms/any(r: r/BaseRate lt 100)`, para filtrar os documentos no índice. Isso utiliza qualquer [operador](https://docs.microsoft.com/azure/search/search-query-odata-collection-operators) para aplicar o 'BaseRate lt 100' a cada item da coleção Rooms. Você pode saber mais sobre a sintaxe OData que o Azure Cognitive Search suporta [aqui](https://docs.microsoft.com/azure/search/query-odata-filter-orderby-syntax).

Estes são alguns dos resultados da consulta:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

    HotelId: 2
    Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...

Em seguida, queremos localizar os dois principais hotéis reformados mais recentemente e mostrar o nome dos hotéis e a data da última reforma. Eis o código: 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "LastRenovationDate desc" },
        Select = new[] { "HotelName", "LastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

Neste caso, usaremos novamente a sintaxe do OData para especificar o parâmetro `OrderBy` como `lastRenovationDate desc`. Também vamos definir `Top` como 2 para garantir que só iremos receber os dois principais documentos. Como antes, definimos `Select` para especificar quais campos devem ser retornados.

Estes são os resultados:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Finalmente, queremos encontrar todos os nomes de hotéis que combinem com a palavra "hotel":

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

Estes são os resultados, que incluem todos os campos uma vez que não especificamos a propriedade `Select`:

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

Essa etapa conclui o tutorial, mas não pare aqui. **Os próximos passos fornecem recursos adicionais para aprender mais sobre a Pesquisa Cognitiva do Azure.

## <a name="next-steps"></a>Próximas etapas
* Procure as referências para o [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) e a [API REST](https://docs.microsoft.com/rest/api/searchservice/).
* Revise as [convenções de nomenclatura](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) para saber as regras de nomeação de vários objetos.
* Revisar [os tipos de dados suportados](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) no Azure Cognitive Search.
