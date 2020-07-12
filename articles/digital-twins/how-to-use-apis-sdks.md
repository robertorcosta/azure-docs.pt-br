---
title: Usar as APIs e os SDKs dos Gêmeos Digitais do Azure
titleSuffix: Azure Digital Twins
description: Veja como trabalhar com as APIs do gêmeos digital do Azure, incluindo o por meio do SDK.
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 522aed07d850dfdb2782ee8263f33bd3f42eb8c2
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86258034"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>Usar as APIs e os SDKs dos Gêmeos Digitais do Azure

O Azure digital gêmeos vem equipado com APIs de **plano de controle** e APIs de **plano de dados** para gerenciar sua instância e seus elementos. Este artigo fornece uma visão geral das APIs disponíveis e os métodos para interagir com elas. Você pode usar as APIs REST diretamente com seus swaggers associados ou por meio de um SDK.

## <a name="overview-control-plane-apis"></a>Visão geral: APIs do plano de controle

As APIs do plano de controle são usadas para gerenciar sua instância do gêmeos digital do Azure como um todo, para que elas abranjam operações como criar ou excluir toda a sua instância. Você também vai usá-los para criar e excluir pontos de extremidade.

A versão mais atual da API do plano de controle para visualização pública é _**2020-03-01-Preview**_.

Para usar as APIs do plano de controle:
* Você pode chamar as APIs diretamente referenciando a [pasta Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins)mais recente. Esse repositório também inclui uma pasta de exemplos que mostram o uso.
* Atualmente, você pode acessar SDKs para APIs de controle no...
  - [.Net (C#)](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/1.0.0-preview.1) ([origem](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins)) ([referência [gerada automaticamente]](https://docs.microsoft.com/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet-preview))
  - [Java](https://search.maven.org/artifact/com.microsoft.azure.digitaltwins.v2020_03_01_preview/azure-mgmt-digitaltwins) ([origem](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins)) ([referência [gerada automaticamente]](https://docs.microsoft.com/java/api/overview/azure/digitaltwins/management?view=azure-java-preview))
  - [JavaScript](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([origem](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins))
  - [Python](https://pypi.org/project/azure-mgmt-digitaltwins/) ([origem](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [Fonte de go](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/digitaltwins/mgmt/2020-03-01-preview/digitaltwins)

Você também pode exercitar as APIs do plano de controle interagindo com o gêmeos digital do Azure por meio do [portal do Azure](https://portal.azure.com) e da [CLI](how-to-use-cli.md).

## <a name="overview-data-plane-apis"></a>Visão geral: APIs do plano de dados

As APIs do plano de dados são usadas para gerenciar os elementos em sua instância do gêmeos digital do Azure. Eles incluem operações como a criação de rotas, o carregamento de modelos, a criação de relações e o gerenciamento de gêmeos. Elas podem ser divididas em larga escala nas seguintes categorias:
* **DigitalTwinsModels** -a categoria DigitalTwinsModels contém APIs para gerenciar os [modelos](concepts-models.md) em uma instância do gêmeos digital do Azure. As atividades de gerenciamento incluem carregamento, validação, recuperação e exclusão de modelos criados no DTDL.
* **DigitalTwins** -a categoria DigitalTwins contém as APIs que permitem que os desenvolvedores criem, modifiquem e excluam [gêmeos digitais](concepts-twins-graph.md) e suas relações em uma instância do gêmeos digital do Azure.
* **Consulta** -a categoria de consulta permite que os desenvolvedores [encontrem conjuntos de gêmeos digitais no grafo de entrelaçamento](how-to-query-graph.md) entre relações.
* **EventRoutes** -a categoria EventRoutes contém APIs para [rotear dados](concepts-route-events.md), por meio do sistema e para serviços downstream.

A versão mais atual da API do plano de dados para visualização pública é _**2020-05-31-Preview**_.

Para usar as APIs do plano de dados:
* Você pode chamar as APIs diretamente, por...
   - referenciando a [pasta Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)mais recente. Esse repositório também inclui uma pasta de exemplos que mostram o uso. 
   - exibindo a [documentação de referência da API](https://docs.microsoft.com/rest/api/azure-digitaltwins/).
* Você pode usar o SDK do .NET (C#). Atualmente, esse é o único SDK publicado para interagir com essas APIs. Para usar o SDK do .NET...
   - Você pode exibir o pacote no NuGet: [Azure. DigitalTwins. Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core). 
   - Você pode encontrar a origem do SDK, incluindo uma pasta de exemplos, no GitHub: [biblioteca de cliente do Azure IOT digital gêmeos para .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). 
   - Você pode exibir a [documentação de referência do SDK](https://docs.microsoft.com/dotnet/api/overview/azure/digitaltwins?view=azure-dotnet-preview).
   - Você pode ver informações detalhadas e exemplos de uso, continuando para a seção [SDK (plano de dados) do .net (C#)](#net-c-sdk-data-plane) deste artigo.
* Você pode gerar um SDK para outro idioma usando o REST. Siga as instruções em [como criar SDKs personalizados para o Azure digital gêmeos com o REST](how-to-create-custom-sdks.md).

Você também pode exercitar as APIs do plano de datas interagindo com o gêmeos digital do Azure por meio da [CLI](how-to-use-cli.md).

## <a name="net-c-sdk-data-plane"></a>SDK do .NET (C#) (plano de dados)

O SDK do .NET do Azure digital gêmeos (C#) faz parte do SDK do Azure para .NET. Ele é de software livre e baseia-se nas APIs do plano de dados do gêmeos digital do Azure.

> [!NOTE]
> Para obter informações detalhadas sobre o design do SDK, consulte os [princípios gerais de design para SDKs do Azure](https://azure.github.io/azure-sdk/general_introduction.html) e as [diretrizes de design](https://azure.github.io/azure-sdk/dotnet_introduction.html)específicas do .net.

Para usar o SDK, inclua o pacote NuGet **Azure. DigitalTwins. Core** com seu projeto. Você também precisará do pacote **Azure. Identity** .

* No Visual Studio, você pode adicionar pacotes com o Gerenciador de pacotes NuGet (acessado por meio de *ferramentas > Gerenciador de pacotes nuget > gerenciar pacotes NuGet para solução*). 
* Usando a ferramenta de linha de comando do .NET, você pode executar:

    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.2
    dotnet add package Azure.identity
    ```

Para obter um passo a passo detalhado sobre como usar as APIs na prática, consulte o [tutorial: codificar um aplicativo cliente](tutorial-code.md). 

### <a name="net-sdk-usage-examples"></a>Exemplos de uso do SDK do .NET

Aqui estão alguns exemplos de código que ilustram o uso do SDK do .NET.

Autentique no serviço:

```csharp
// Authenticate against the service and create a client
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
```

Carregar um modelo e modelos de lista:

```csharp
// Upload a model
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
// Read a list of models back from the service
AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
await foreach (ModelData md in modelDataList)
{
    Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
}
```

Criar e consultar gêmeos:

```csharp
// Initialize twin metadata
BasicDigitalTwin twinData = new BasicDigitalTwin();

twinData.Id = $"firstTwin";
twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
twinData.CustomProperties.Add("data", "Hello World!");
try {
    await client.CreateDigitalTwinAsync("firstTwin", JsonSerializer.Serialize(twinData));
} catch(RequestFailedException rex) {
    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
}
 
// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    // Use JSON deserialization to pretty-print
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
    // Or use BasicDigitalTwin for convenient property access
    BasicDigitalTwin btwin = JsonSerializer.Deserialize<BasicDigitalTwin>(twin);
}
```

Consulte o [tutorial: codificar um aplicativo cliente](tutorial-code.md) para obter um passo a passo deste código de aplicativo de exemplo. 

Você também pode encontrar exemplos adicionais no [repositório do GitHub para o SDK do .net (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples).

#### <a name="serialization-helpers"></a>Auxiliares de serialização

Conforme descrito anteriormente, os principais métodos do SDK retornam dados de entrelaçamento como JSON. No entanto, o SDK também contém classes auxiliares para serialização. Essas funções auxiliares permitem criar ou desserializar rapidamente dados de entrelaçamento para acessar informações básicas.

As classes auxiliares disponíveis são:
* `BasicDigitalTwin`: Representa os dados principais de um teledigital
* `BasicRelationship`: Representa os dados principais de uma relação
* `UpdateOperationUtility`: Representa informações de patch JSON usadas em chamadas de atualização
* `WriteableProperty`: Representa metadados de propriedade

##### <a name="deserialize-a-digital-twin"></a>Desserializar um r digital

Você sempre pode desserializar dados de entrelaçamento usando a biblioteca JSON de sua escolha, como `System.Test.Json` ou `Newtonsoft.Json` . Para acesso básico a um número de bits, as classes auxiliares tornam isso um pouco mais conveniente.

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
```

A `BasicDigitalTwin` classe auxiliar também fornece acesso a propriedades definidas em o "My", por meio de a `Dictionary<string, object>` . Para listar as propriedades de myQuery, você pode usar:

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

##### <a name="create-a-digital-twin"></a>Criar um teledigital

Usando a `BasicDigitalTwin` classe, você pode preparar dados para criar uma instância de entrelaçar:

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
twin.CustomProperties = props;

client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<BasicDigitalTwin>(twin));
```

O código acima é equivalente à seguinte variante "manual":

```csharp
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:example:Room;1"}
};
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", 25.0 }
};
client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<Dictionary<string, object>>(twin));
```

##### <a name="deserialize-a-relationship"></a>Desserializar uma relação

Você sempre pode desserializar os dados da relação usando a biblioteca JSON de sua escolha, como `System.Test.Json` ou `Newtonsoft.Json` . Para acesso básico a uma relação, as classes auxiliares tornam isso um pouco mais conveniente.

```csharp
Response<string> res = client.GetRelationship(twin_id, rel_id);
BasicRelationship rel = JsonSerializer.Deserialize<BasicRelationship>(res.Value);
Console.WriteLine($"Relationship Name: {rel.Name}");
```

A `BasicRelationship` classe auxiliar também fornece acesso a propriedades definidas na relação, por meio de um `Dictionary<string, object>` . Para listar Propriedades, você pode usar:

```csharp
Response<string> res = client.GetRelationship(twin_id, rel_id);
BasicRelationship rel = JsonSerializer.Deserialize<BasicRelationship>(res.Value);
Console.WriteLine($"Relationship Name: {rel.Name}");
foreach (string prop in rel.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

##### <a name="create-a-relationship"></a>Criar uma relação

Usando a `BasicDigitalTwin` classe, você também pode preparar dados para criar relações em uma instância de entrelaçar:

```csharp
BasicRelationship rel = new BasicRelationship();
rel.TargetId = "myTargetTwin";
rel.Name = "contains"; // a relationship with this name must be defined in the model
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("active", true);
rel.CustomProperties = props;
client.CreateRelationship("mySourceTwin", "rel001", JsonSerializer.Serialize<BasicRelationship>(rel));
```

##### <a name="create-a-patch-for-twin-update"></a>Criar um patch para atualização de atualizações

As chamadas de atualização para gêmeos e relações usam a estrutura de [patch JSON](http://jsonpatch.com/) . Para criar listas de operações de patch JSON, você pode usar a `UpdateOperationsUtility` classe conforme mostrado abaixo.

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", 25.0);
uou.AppendAddOp("/myComponent/Property", "Hello");
// Un-set a property
uou.AppendRemoveOp("/Humidity");
client.UpdateDigitalTwin("myTwin", uou.Serialize());
```

## <a name="general-apisdk-usage-notes"></a>Notas gerais de uso de API/SDK

> [!NOTE]
> Observe que durante a visualização, o Azure digital gêmeos não dá suporte a **CORS (compartilhamento de recursos entre origens)**. Como resultado, se você estiver chamando uma API REST de um aplicativo de navegador, uma interface de [Gerenciamento de API (APIM)](../api-management/api-management-key-concepts.md) ou um conector de [aplicativos de energia](https://docs.microsoft.com/powerapps/powerapps-overview) , você poderá ver um erro de política.
> Para resolver esse erro, você pode fazer um dos seguintes:
> * Remova o cabeçalho CORS `Access-Control-Allow-Origin` da mensagem. Esse cabeçalho indica se a resposta pode ser compartilhada. 
> * Como alternativa, crie um proxy CORS e faça a solicitação da API REST do Azure digital gêmeos. 

A lista a seguir fornece detalhes adicionais e diretrizes gerais para o uso de APIs e SDKs.

* Para usar o SDK, crie uma instância da `DigitalTwinsClient` classe. O construtor requer credenciais que podem ser obtidas com uma variedade de métodos de autenticação no `Azure.Identity` pacote. Para obter mais informações `Azure.Identity` , consulte a [documentação do namespace](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet). 
* Você pode achar o `InteractiveBrowserCredential` útil ao começar, mas há várias outras opções, incluindo credenciais para [identidade gerenciada](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet), que você provavelmente usará para autenticar as [Azure Functions configuradas com o MSI](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet) no Azure digital gêmeos. Para obter mais informações `InteractiveBrowserCredential` , consulte a [documentação](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet)de sua classe.
* Todas as chamadas à API de serviço são expostas como funções de membro na `DigitalTwinsClient` classe.
* Todas as funções de serviço existem em versões síncronas e assíncronas.
* Todas as funções de serviço geram uma exceção para qualquer status de retorno de 400 ou acima. Certifique-se de encapsular chamadas em uma `try` seção e capturar pelo menos `RequestFailedExceptions` . Para obter mais informações sobre esse tipo de exceção, consulte [aqui](https://docs.microsoft.com/dotnet/api/azure.requestfailedexception?view=azure-dotnet).
* A maioria dos métodos de serviço retorna `Response<T>` ou ( `Task<Response<T>>` para as chamadas assíncronas), em que `T` é a classe de objeto de retorno para a chamada de serviço. A [`Response`](https://docs.microsoft.com/dotnet/api/azure.response-1?view=azure-dotnet) classe encapsula o retorno de serviço e apresenta valores de retorno em seu `Value` campo.  
* Métodos de serviço com resultados paginados retornam `Pageable<T>` ou `AsyncPageable<T>` como resultados. Para obter mais informações sobre a `Pageable<T>` classe, consulte [aqui](https://docs.microsoft.com/dotnet/api/azure.pageable-1?view=azure-dotnet-preview); para obter mais informações `AsyncPageable<T>` , consulte [aqui](https://docs.microsoft.com/dotnet/api/azure.asyncpageable-1?view=azure-dotnet-preview).
* Você pode iterar por meio de resultados paginados usando um `await foreach` loop. Para obter mais informações sobre esse processo, consulte [aqui](https://docs.microsoft.com/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8).
* O SDK subjacente é `Azure.Core` . Consulte a [documentação do namespace do Azure](https://docs.microsoft.com/dotnet/api/azure?view=azure-dotnet-preview) para obter referência sobre os tipos e a infraestrutura do SDK.

Os métodos de serviço retornam objetos fortemente tipados sempre que possível. No entanto, como o Azure digital gêmeos se baseia em modelos personalizados – configurados pelo usuário em tempo de execução (por meio de modelos de DTDL carregados para o serviço), muitas APIs de serviço levam e retornam dados de alto nível no formato JSON.

## <a name="monitor-api-metrics"></a>Monitorar métricas de API

As métricas de API, como solicitações, latência e taxa de falha podem ser exibidas no [portal do Azure](https://portal.azure.com/). 

Na página inicial do portal, pesquise sua instância do gêmeos digital do Azure para obter seus detalhes. Selecione a opção **métricas** no menu da instância do gêmeos digital do Azure para abrir a página de *métricas* .

:::image type="content" source="media/how-to-use-apis-sdks/metrics.png" alt-text="Página métricas de uma instância do gêmeos digital do Azure no portal do Azure":::

A partir daqui, você pode exibir as métricas para sua instância e criar exibições personalizadas.

## <a name="next-steps"></a>Próximas etapas

Consulte como usar as APIs para criar uma instância de gêmeos digital do Azure:
* [Como: criar uma instância de gêmeos digital do Azure](how-to-set-up-instance.md)

Ou então, percorra as etapas para criar um aplicativo cliente como aquele usado nestas instruções:
* [Tutorial: codificar um aplicativo cliente](tutorial-code.md)
