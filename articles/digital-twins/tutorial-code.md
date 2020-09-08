---
title: 'Tutorial: Codificar um aplicativo cliente'
titleSuffix: Azure Digital Twins
description: Tutorial para escrever o código mínimo de um aplicativo cliente usando o SDK do .NET (C#).
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: c000d48043a46ecdbdfee263cc5c8ce877f66b4b
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923697"
---
# <a name="tutorial-coding-with-the-azure-digital-twins-apis"></a>Tutorial: Codificação com as APIs dos Gêmeos Digitais do Azure

É comum que os desenvolvedores que trabalham com os Gêmeos Digitais do Azure escrevam um aplicativo cliente para interagir com a instância do serviço Gêmeos Digitais do Azure. Este tutorial voltado para o desenvolvedor fornece uma introdução à programação no serviço Gêmeos Digitais do Azure usando a [biblioteca de clientes dos Gêmeos Digitais do IoT do Azure para .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). Ele mostra como escrever um aplicativo cliente de console C# passo a passo do zero.

> [!div class="checklist"]
> * Configurar o projeto
> * Introdução ao código do projeto   
> * Exemplo de código completo
> * Limpar os recursos
> * Próximas etapas

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial usa a linha de comando para a instalação e o trabalho do projeto. Portanto, você pode usar qualquer editor de códigos para acompanhar os exercícios.

O que você precisa para começar:
* Qualquer editor de códigos
* **.NET Core 3.1** no computador de desenvolvimento. Baixe esta versão do SDK do .NET Core para várias plataformas em [Baixar o .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

[!INCLUDE [Azure Digital Twins tutorials: instance prereq](../../includes/digital-twins-tutorial-prereq-instance.md)]

## <a name="set-up-project"></a>Configurar o projeto

Quando estiver pronto para começar a usar sua instância dos Gêmeos Digitais do Azure, inicie a configuração do projeto de aplicativo cliente. 

Abra um prompt de comando ou outra janela do console no computador e crie um diretório de projeto vazio no qual deseja armazenar seu trabalho durante este tutorial. Nomeie o diretório como desejar (por exemplo, *DigitalTwinsCodeTutorial*).

Acesse o novo diretório.

Quando estiver no diretório do projeto, crie um projeto de aplicativo de console .NET vazio. Na janela Comando, execute o seguinte comando para criar um projeto C# mínimo para o console:

```cmd/sh
dotnet new console
```

Isso criará vários arquivos dentro do diretório, incluindo um chamado *Program.cs*, no qual você escreverá a maior parte do código.

Em seguida, adicione duas dependências necessárias para trabalhar com os Gêmeos Digitais do Azure:

```cmd/sh
dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
dotnet add package Azure.identity
```

A primeira dependência é a [biblioteca de clientes dos Gêmeos Digitais de IoT do Azure para .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). A segunda dependência fornece ferramentas para ajudar com a autenticação no Azure.

Mantenha a janela Comando aberta, pois você continuará usando-a em todo o tutorial.

## <a name="get-started-with-project-code"></a>Introdução ao código do projeto

Nesta seção, você começará a escrever o código do novo projeto de aplicativo para trabalhar com os Gêmeos Digitais do Azure. As ações abordadas incluem:
* Autenticação no serviço
* Upload de um modelo
* Captura de erros
* Criação de gêmeos digitais
* Criação de relações
* Consulta de gêmeos digitais

Também há uma seção que mostra o código completo no final do tutorial. Use isso como uma referência para verificar seu programa durante as etapas.

Para começar, abra o arquivo *Program.cs* em qualquer editor de códigos. Você verá um modelo de código mínimo parecido com este:

```csharp
using System;

namespace DigitalTwinsCodeTutorial
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
```

Primeiro, adicione algumas linhas `using` à parte superior do código para extrair as dependências necessárias.

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
```

Em seguida, você adicionará o código a esse arquivo para preencher algumas funcionalidades. 

### <a name="authenticate-against-the-service"></a>Autenticar-se no serviço

A primeira coisa que o seu aplicativo precisará fazer é se autenticar no serviço Gêmeos Digitais do Azure. Em seguida, você poderá criar uma classe de cliente de serviço para acessar as funções do SDK.

Para se autenticar, você precisará de três informações:
* A *ID de Diretório (locatário)* da sua assinatura
* A *ID do Aplicativo (cliente)* criada quando você configurou a instância dos Gêmeos Digitais do Azure anteriormente
* O *nome do host* da sua instância dos Gêmeos Digitais do Azure

>[!TIP]
> Se você não sabe a *ID de Diretório (locatário)* , obtenha-a executando este comando no [Azure Cloud Shell](https://shell.azure.com):
> 
> ```azurecli-interactive
> az account show --query tenantId
> ```

Em *Program.cs*, cole o código a seguir abaixo da linha de cópia impressa "Olá, mundo!" no método `Main`. Defina o valor de `adtInstanceUrl` como o *Nome do host* da instância dos Gêmeos Digitais do Azure, `clientId` como a *ID do Aplicativo* e `tenantId` como a *ID de Diretório*.

```csharp
string clientId = "<your-application-ID>";
string tenantId = "<your-directory-ID>";
string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
Console.WriteLine($"Service client created – ready to go");
```

Salve o arquivo. 

Observe que este exemplo usa uma credencial de navegador interativo:
```csharp
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
```

Esse tipo de credencial fará com que uma janela do navegador seja aberta, solicitando que você forneça suas credenciais do Azure. 

>[!NOTE]
> Para obter informações sobre outros tipos de credenciais, confira a documentação das [bibliotecas de autenticação da plataforma de identidade da Microsoft](../active-directory/develop/reference-v2-libraries.md).

Na janela Comando, execute o código com este comando: 

```cmd/sh
dotnet run
```

Isso vai restaurar as dependências na primeira execução e executar o programa. 
* Se nenhum erro ocorrer, o programa imprimirá *Cliente de serviço criado – pronto para uso*.
* Como ainda não há nenhum tratamento de erro neste projeto, se algo der errado, você verá uma exceção gerada pelo código.

### <a name="upload-a-model"></a>Carregar um modelo

Os Gêmeos Digitais do Azure não têm um vocabulário de domínio intrínseco. Os tipos de elementos no seu ambiente que podem ser representados nos Gêmeos Digitais do Azure são definidos por você por meio de **modelos**. Os [modelos](concepts-models.md) são semelhantes às classes nas linguagens de programação orientadas a objeto; elas fornecem modelos definidos pelo usuário para os [gêmeos digitais](concepts-twins-graph.md) seguirem e criarem uma instância deles posteriormente. Eles são escritos em uma linguagem semelhante a JSON chamada **DTDL (Digital Twins Definition Language)** .

A primeira etapa na criação de uma solução dos Gêmeos Digitais do Azure é definir, pelo menos, um modelo em um arquivo DTDL.

No diretório em que você criou o projeto, crie um arquivo *.json* chamado *SampleModel.json*. Cole-o no corpo do seguinte arquivo: 

```json
{
  "@id": "dtmi:com:contoso:SampleModel;1",
  "@type": "Interface",
  "displayName": "SampleModel",
  "contents": [
    {
      "@type": "Relationship",
      "name": "contains"
    },
    {
      "@type": "Property",
      "name": "data",
      "schema": "string"
    }
  ],
  "@context": "dtmi:dtdl:context;2"
}
```

> [!TIP]
> Se você estiver usando o Visual Studio para este tutorial, o ideal será selecionar o arquivo JSON recém-criado e definir a propriedade *Copiar para o Diretório de Saída* na inspeção de propriedade como *Copiar se for o Mais Recente* ou *Sempre Copiar*. Isso permitirá que o Visual Studio localize o arquivo JSON com o caminho padrão quando você executar o programa com **F5** durante o restante do tutorial.

> [!TIP] 
> Há uma [amostra de Validador DTDL](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator) independente de linguagem que pode ser usada para verificar os documentos do modelo e ver se a DTDL é válida. Ela se baseia na biblioteca do analisador de DTDL, que é explicada mais detalhadamente em [*Como analisar e validar modelos*](how-to-parse-models.md).

Em seguida, adicione mais um código a *Program.cs* para carregar o modelo recém-criado na sua instância dos Gêmeos Digitais do Azure.

Primeiro, adicione algumas instruções `using` à parte superior do arquivo:

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
```

Em seguida, prepare-se para usar os métodos assíncronos no SDK do serviço C#, alterando a assinatura do método `Main` para permitir a execução assíncrona. 

```csharp
static async Task Main(string[] args)
```

> [!NOTE]
> O uso de `async` não é estritamente necessário, pois o SDK também fornece versões síncronas de todas as chamadas. Este tutorial ensina o uso de `async`.

Em seguida, vem o primeiro trecho de código que interage com o serviço Gêmeos Digitais do Azure. Esse código carrega o arquivo DTDL que você criou no disco e carrega-o na instância do serviço Gêmeos Digitais do Azure. 

Cole o código a seguir no código de autorização adicionado anteriormente.

```csharp
Console.WriteLine();
Console.WriteLine($"Upload a model");
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
// Upload the model to the service
await client.CreateModelsAsync(typeList);
```

Na janela Comando, execute o programa com este comando: 

```cmd/sh
dotnet run
```
"Carregar um modelo" será impresso na saída, mas ainda não há nenhuma saída para indicar se os modelos foram carregados com êxito ou não.

Para adicionar uma instrução print que indica se os modelos foram realmente carregados com êxito, adicione o seguinte código logo após a seção anterior:

```csharp
// Read a list of models back from the service
AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
await foreach (ModelData md in modelDataList)
{
    Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
}
```

Antes de executar o programa novamente para testar esse novo código, lembre-se de que a última vez que você executou o programa, você já tinha carregado o modelo. Os Gêmeos Digitais do Azure não permitirão que você carregue o mesmo modelo duas vezes; portanto, espere ver uma exceção ao executar novamente o programa.

Agora, execute o programa novamente com este comando na janela Comando:

```cmd/sh
dotnet run
```

O programa deverá gerar uma exceção. Quando você tenta carregar um modelo que já foi carregado, o serviço retorna um erro de "solicitação inválida" por meio da API REST. Como resultado, o SDK do cliente dos Gêmeos Digitais do Azure, por sua vez, vai gerar uma exceção para cada código de retorno do serviço diferente de êxito. 

A próxima seção aborda exceções como essa e como tratá-las no código.

### <a name="catch-errors"></a>Capturar erros

Para impedir a falha do programa, adicione um código de exceção ao código de upload do modelo. Encapsule a chamada de cliente existente `client.CreateModelsAsync` em um manipulador try/catch desta forma:

```csharp
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
```

Se você executar o programa com `dotnet run` na janela Comando agora, receberá um código de erro. A saída é parecida com esta:

```cmd/sh
Hello World!
Service client created - ready to go

Upload a model
Load model: 409:Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"DocumentAlreadyExists","message":"A document with same identifier already exists.","details":[]}}

Headers:
api-supported-versions: REDACTED
Date: Tue, 05 May 2020 01:57:51 GMT
Content-Length: 115
Content-Type: application/json; charset=utf-8

Type name: : dtmi:com:contoso:SampleModel;1
```

Daqui em diante, o tutorial encapsulará todas as chamadas a métodos de serviço em manipuladores try/catch.

### <a name="create-digital-twins"></a>Criar gêmeos digitais

Agora que você carregou um modelo nos Gêmeos Digitais do Azure, use essa definição de modelo para criar **gêmeos digitais**. Os [gêmeos digitais](concepts-twins-graph.md) são instâncias de um modelo e representam as entidades no seu ambiente de negócios – itens como sensores em um farm, salas em um prédio ou luzes em um carro. Esta seção cria alguns gêmeos digitais com base no modelo carregado anteriormente.

Adicione uma nova instrução `using` à parte superior, pois você precisará do serializador JSON do .NET interno em `System.Text.Json`:

```csharp
using System.Text.Json;
using Azure.DigitalTwins.Core.Serialization;
```

Em seguida, adicione o código a seguir ao final do método `Main` para criar e inicializar três gêmeos digitais com base nesse modelo.

```csharp
// Initialize twin data
BasicDigitalTwin twinData = new BasicDigitalTwin();
twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
twinData.CustomProperties.Add("data", $"Hello World!");

string prefix="sampleTwin-";
for(int i=0; i<3; i++) {
    try {
        twinData.Id = $"{prefix}{i}";
        await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(twinData));
        Console.WriteLine($"Created twin: {prefix}{i}");
    } catch(RequestFailedException rex) {
        Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
    }
}
```

Na janela Comando, execute o programa com `dotnet run`. Em seguida, repita a etapa para executar o programa novamente. 

Observe que nenhum erro é gerado quando os gêmeos são criados na segunda vez, mesmo que eles já existam após a primeira execução. Ao contrário da criação do modelo, a criação de gêmeos é, no nível da REST, uma chamada *PUT* com a semântica de *upsert*. Isso significa que, se um gêmeo já existir, a tentativa de recriá-lo apenas o substituirá. Nenhum erro é necessário.

### <a name="create-relationships"></a>Criar relações

Em seguida, você pode criar **relações** entre os gêmeos criados para conectá-los a um **grafo de gêmeos**. Os [grafos de gêmeos](concepts-twins-graph.md) são usados para representar todo o ambiente.

Para criar relações, adicione uma instrução `using` para o tipo base de relação no SDK (ignore isso caso ela já tenha sido adicionada).
```csharp
using Azure.DigitalTwins.Core.Serialization;
```

Em seguida, adicione um novo método estático à classe `Program`, sob o método `Main`:
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

Em seguida, adicione o seguinte código ao final do método `Main` para chamar o código `CreateRelationship`:
```csharp
// Connect the twins with relationships
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");
```

Na janela Comando, execute o programa com `dotnet run`.

Observe que os Gêmeos Digitais do Azure não permitirão que você crie uma relação se já existir uma com a mesma ID; portanto, se você executar o programa várias vezes, verá exceções na criação da relação. Este código captura as exceções e as ignora. 

### <a name="list-relationships"></a>Listar relações

O próximo código que você adicionará permite ver a lista de relações criadas.

Adicione o seguinte método à classe `Program`:

```csharp
public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
{
    try {
        AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
        Console.WriteLine($"Twin {srcId} is connected to:");
        await foreach (string rel in results)
        {
            var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
            Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
        }
    } catch (RequestFailedException rex) {
        Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
    }
}
```

Em seguida, adicione o seguinte código ao final do método `Main` para chamar o código `ListRelationships`:

```csharp
//List the relationships
await ListRelationships(client, "sampleTwin-0");
```

Na janela Comando, execute o programa com `dotnet run`. Você deverá ver uma lista de todas as relações criadas.

### <a name="query-digital-twins"></a>Consultar os gêmeos digitais

Um dos principais recursos dos Gêmeos Digitais do Azure é a capacidade de [consultar](concepts-query-language.md) o grafo de gêmeos com facilidade e eficiência para responder às perguntas sobre o seu ambiente.

A última seção de código a ser adicionada neste tutorial executa uma consulta na instância dos Gêmeos Digitais do Azure. A consulta usada neste exemplo retorna todos os gêmeos digitais na instância.

Adicione o seguinte código ao final do método `Main`:

```csharp
// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
    Console.WriteLine("---------------");
}
```

Na janela Comando, execute o programa com `dotnet run`. Você deverá ver todas os gêmeos digitais nessa instância na saída.

## <a name="complete-code-example"></a>Exemplo de código completo

Neste ponto do tutorial, você tem um aplicativo cliente completo, que pode executar ações básicas nos Gêmeos Digitais do Azure. Para referência, o código completo do programa em *Program.cs* é listado abaixo:

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
            
            string clientId = "<your-application-ID>";
            string tenantId = "<your-directory-ID>";
            string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
            var credentials = new InteractiveBrowserCredential(tenantId, clientId);
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            Console.WriteLine($"Service client created – ready to go");

            Console.WriteLine();
            Console.WriteLine($"Upload a model");
            var typeList = new List<string>();
            string dtdl = File.ReadAllText("SampleModel.json");
            typeList.Add(dtdl);

            // Upload the model to the service
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

            // Initialize twin data
            BasicDigitalTwin twinData = new BasicDigitalTwin();
            twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
            twinData.CustomProperties.Add("data", $"Hello World!");
    
            string prefix="sampleTwin-";
            for(int i=0; i<3; i++) {
                try {
                    twinData.Id = $"{prefix}{i}";
                    await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(twinData));
                    Console.WriteLine($"Created twin: {prefix}{i}");
                } catch(RequestFailedException rex) {
                    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
                }
            }

            // Connect the twins with relationships
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");

            //List the relationships
            await ListRelationships(client, "sampleTwin-0");

            // Run a query    
            AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
            await foreach (string twin in result)
            {
                object jsonObj = JsonSerializer.Deserialize<object>(twin);
                string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
                Console.WriteLine(prettyTwin);
                Console.WriteLine("---------------");
            }
        }

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
        
        public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
        {
            try {
                AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
                Console.WriteLine($"Twin {srcId} is connected to:");
                await foreach (string rel in results)
                {
                    var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
                    Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
                }
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
            }
        }

    }
}
```
## <a name="clean-up-resources"></a>Limpar os recursos
 
A instância usada neste tutorial pode ser reutilizada no próximo tutorial, [*Tutorial: Explorar os conceitos básicos com um aplicativo cliente de exemplo*](tutorial-command-line-app.md). Se você pretende prosseguir para o próximo tutorial, mantenha a instância dos Gêmeos Digitais do Azure configurada aqui.
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Por fim, exclua a pasta do projeto criada no computador local.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um aplicativo cliente de console .NET do zero. Você escreveu o código para esse aplicativo cliente a fim de executar as ações básicas em uma instância dos Gêmeos Digitais do Azure.

Prossiga para o próximo tutorial e explore as coisas que você pode fazer com um aplicativo cliente de exemplo: 

> [!div class="nextstepaction"]
> [*Tutorial: Explorar os conceitos básicos com um aplicativo cliente de exemplo*](tutorial-command-line-app.md)