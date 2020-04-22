---
title: Como usar o WebJobs SDK
description: Saiba mais sobre como escrever código para o WebJobs SDK. Crie trabalhos de processamento de fundo orientados por eventos que acessem dados no Azure e em serviços de terceiros.
author: ggailey777
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 5a8d5f96449cfecd4628c38fa2788a1e06e96b07
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758898"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Como usar o SDK do Azure WebJobs para o processamento em segundo plano controlado por evento

Este artigo fornece orientações sobre como trabalhar com o Azure WebJobs SDK. Para começar com o WebJobs imediatamente, consulte [Comece com o Azure WebJobs SDK para processamento de fundo orientado a eventos](webjobs-sdk-get-started.md). 

## <a name="webjobs-sdk-versions"></a>Versões do SDK do WebJobs

Estas são as principais diferenças entre a versão 3. *x* e versão 2. *x* do WebJobs SDK:

* Versão 3. *x* adiciona suporte para .NET Core.
* Na versão 3. *x*, você precisa instalar explicitamente a extensão de vinculação de armazenamento exigida pelo WebJobs SDK. Na versão 2. *x*, as ligações de armazenamento foram incluídas no SDK.
* Ferramentas do Visual Studio para .NET Core (3.* x*) os projetos diferem das ferramentas para o Framework .NET (2.* x*) projetos. Para saber mais, consulte [Desenvolver e implantar WebJobs usando o Visual Studio - Azure App Service](webjobs-dotnet-deploy-vs.md).

Quando possível, são fornecidos exemplos para ambas as versões 3. *x* e versão 2. *x*.

> [!NOTE]
> [O Azure Functions](../azure-functions/functions-overview.md) é construído no WebJobs SDK, e este artigo fornece links para a documentação de Funções do Azure para alguns tópicos. Observe essas diferenças entre funções e o WebJobs SDK:
> * Funções do Azure versão 2. *x* corresponde ao WebJobs SDK versão 3. *x*, e Funções Azure 1. *x* corresponde ao WebJobs SDK 2. *x*. Os repositórios de código-fonte usam a numeração WebJobs SDK.
> * O código de exemplo para bibliotecas de classe C# do Azure functions `FunctionName` é como o código SDK do WebJobs, exceto que você não precisa de um atributo em um projeto WebJobs SDK.
> * Alguns tipos de vinculação são suportados apenas em Funções, como HTTP (Webhooks) e Event Grid (que é baseado em HTTP).
>
> Para obter mais informações, consulte [Comparar o WebJobs SDK e o Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>Host do WebJobs

O host é um contêiner de runtime para funções.  Ele escuta gatilhos e chamadas de funções. Na versão 3. *x*, o host `IHost`é uma implementação de . Na versão 2. *x*, você `JobHost` usa o objeto. Você cria uma instância do host em seu código e escreve um código para personalizar seu comportamento.

Esta é uma diferença fundamental entre usar o WebJobs SDK diretamente e usá-lo indiretamente através das Funções Azure. Em Funções Azure, o serviço controla o host e você não pode personalizar o host escrevendo código. O Azure Functions permite que você personalize o comportamento de host por meio de configurações no arquivo host.json. Essas configurações são strings, não código, e isso limita os tipos de personalizações que você pode fazer.

### <a name="host-connection-strings"></a>Cadeias de conexão do host

O WebJobs SDK procura as seqüências de conexão Azure Storage e Azure Service Bus no arquivo local.settings.json quando você é executado localmente ou no ambiente do WebJob quando você é executado no Azure. Por padrão, é necessária `AzureWebJobsStorage` uma configuração de string de conexão de armazenamento nomeada.  

Versão 2. *x* do SDK permite que você use seus próprios nomes para essas strings de conexão ou armazená-los em outro lugar. Você pode definir nomes [`JobHostConfiguration`]em código usando o , como mostrado aqui:

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    //// Dashboard logging is deprecated; use Application Insights.
    //var _dashboardConn = ConfigurationManager
    //    .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    //config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Porque a versão 3. *x* usa as APIs de configuração padrão do .NET Core, não há API para alterar nomes de seqüência de seqüência de conexão.

### <a name="host-development-settings"></a>Configurações de desenvolvimento do host

Você pode executar o host no modo de desenvolvimento para tornar o desenvolvimento local mais eficiente. Aqui estão algumas das configurações que são alteradas quando você é executado no modo de desenvolvimento:

| Propriedade | Configuração de desenvolvimento |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` para maximizar a saída de log. |
| `Queues.MaxPollingInterval`  | Um valor baixo para garantir que os métodos de fila sejam disparados imediatamente.  |
| `Singleton.ListenerLockPeriod` | Quinze segundos para ajudar no desenvolvimento iterativo rápido. |

O processo para habilitar o modo de desenvolvimento depende da versão SDK. 

#### <a name="version-3x"></a>Versão 3. *x*

Versão 3. *x* usa as APIs padrão ASP.NET Core. Chame [`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) o método [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) na instância. Passe uma `development`string chamada , como neste exemplo:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.UseEnvironment("development");
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Versão 2. *x*

A classe `JobHostConfiguration` tem um método `UseDevelopmentSettings` que habilita o modo de desenvolvimento.  O exemplo a seguir mostra como usar as configurações de desempenho. Para `config.IsDevelopment` fazer `true` o retorno quando for executado localmente, defina uma variável de ambiente local nomeada `AzureWebJobsEnv` com o valor `Development`.

```cs
static void Main()
{
    config = new JobHostConfiguration();

    if (config.IsDevelopment)
    {
        config.UseDevelopmentSettings();
    }

    var host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="managing-concurrent-connections-version-2x"></a><a name="jobhost-servicepointmanager-settings"></a>Gerenciamento de conexões simultâneas (versão 2.* x*)

Na versão 3. *x*, o limite de conexão padrão para conexões infinitas. Se por algum motivo você precisar alterar esse [`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) limite, [`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler) você pode usar a propriedade da classe.

Na versão 2. *x*, você controla o número de conexões simultâneas a um host usando a API [ServicePointManager.DefaultConnectionLimit.](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) Em dois. *x*, você deve aumentar esse valor a partir do padrão de 2 antes de iniciar seu host WebJobs.

Todas as solicitações HTTP que você `HttpClient` faz `ServicePointManager`a partir de uma função usando flow through . Depois de atingir o `DefaultConnectionLimit` `ServicePointManager` valor definido, inicia a fila de solicitações antes de enviá-las. Suponha que seu `DefaultConnectionLimit` seja definido como 2 e seu código faça 1.000 solicitações HTTP. Inicialmente, apenas duas solicitações são permitidas por meio do sistema operacional. Os outros 998 estão na fila até que haja espaço para eles. Isso significa `HttpClient` que o seu tempo de saída pode porque parece ter feito a solicitação, mas a solicitação nunca foi enviada pelo SISTEMA OPERACIONAL para o servidor de destino. Para que você possa ver o comportamento que não faz sentido: o `HttpClient` local está demorando 10 segundos para concluir uma solicitação, mas o serviço está retornando cada solicitação em 200 ms. 

O valor padrão para `Int32.MaxValue`ASP.NET aplicativos é , e isso provavelmente funcionará bem para o WebJobs em execução em um Plano básico ou superior de serviço de aplicativo. Os WebJobs normalmente precisam da configuração Always On, e isso é suportado apenas por Planos básicos e mais altos de serviço de aplicativos.

Caso o WebJob esteja em execução em um Plano do Serviço de Aplicativo Gratuito ou Compartilhado, seu aplicativo ficará restrito à área restrita do Serviço de Aplicativo, que atualmente tem um [limite de conexão de 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Com um limite de `ServicePointManager`conexão não vinculado, é mais provável que o limite de conexão da caixa de areia seja atingido e o site seja desligado. Nesse caso, a configuração de `DefaultConnectionLimit` para algo inferior, como 50 ou 100, pode evitar que isso aconteça e ainda permitir uma taxa de transferência suficiente.

A configuração deve ser definida antes de todas as solicitações HTTP serem feitas. Por essa razão, o host WebJobs não deve ajustar a configuração automaticamente. Pode haver solicitações HTTP que ocorrem antes do host começar, o que pode levar a um comportamento inesperado. A melhor abordagem é definir o `Main` valor imediatamente `JobHost`em seu método antes de inicializar, como mostrado aqui:

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it's used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>Gatilhos

As funções devem ser métodos públicos e [`NoAutomaticTrigger`](#manual-triggers) devem ter um atributo de gatilho ou o atributo.

### <a name="automatic-triggers"></a>Gatilhos automáticos

Os disparadores automáticos chamam uma função em resposta a um evento. Considere este exemplo de uma função que é desencadeada por uma mensagem adicionada ao armazenamento do Azure Queue. Ele responde lendo uma bolha do armazenamento Azure Blob:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

O `QueueTrigger` atributo informa o tempo de execução para `myqueue-items` chamar a função sempre que uma mensagem de fila aparecer na fila. O `Blob` atributo informa o tempo de execução para usar a mensagem de fila para ler uma bolha no recipiente *de itens de trabalho de amostra.* O nome do item blob no `samples-workitems` recipiente é obtido diretamente do gatilho`{queueTrigger}`da fila como uma expressão de vinculação ( ).

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>Gatilhos manuais

Para acionar uma função manualmente, use o atributo, `NoAutomaticTrigger` como mostrado aqui:

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
ILogger logger,
string value,
[Queue("outputqueue")] out string message)
{
    message = value;
    logger.LogInformation("Creating queue message: ", message);
}
```

O processo para acionar manualmente a função depende da versão SDK.

#### <a name="version-3x"></a>Versão 3. *x*

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage();
    });
    var host = builder.Build();
    using (host)
    {
        var jobHost = host.Services.GetService(typeof(IJobHost)) as JobHost;
        var inputs = new Dictionary<string, object>
        {
            { "value", "Hello world!" }
        };

        await host.StartAsync();
        await jobHost.CallAsync("CreateQueueMessage", inputs);
        await host.StopAsync();
    }
}
```

#### <a name="version-2x"></a>Versão 2. *x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>Associações de entrada e saída

As associações de entrada e saída fornecem uma maneira declarativa para criar dados a partir do Azure ou de serviços externos disponíveis para seu código. As associações de saída fornecem uma maneira de atualizar os dados. O artigo [Get started](webjobs-sdk-get-started.md) mostra um exemplo de cada um.

Você pode usar um valor de retorno do método para uma vinculação de saída aplicando o atributo ao valor de retorno do método. Veja o exemplo em [Usar o valor de retorno da função Azure](../azure-functions/functions-bindings-return-value.md).

## <a name="binding-types"></a>Tipos de associação

O processo de instalação e gerenciamento de tipos de vinculação depende se você está usando a versão 3. *x* ou versão 2. *x* do SDK. Você pode encontrar o pacote para instalar para um determinado tipo de vinculação na seção "Pacotes" do artigo de [referência](#binding-reference-information)Funções Azure desse tipo de vinculação . Uma exceção é o gatilho e a vinculação de arquivos (para o sistema de arquivos local), que não é suportado pelas Funções Azure.

#### <a name="version-3x"></a>Versão 3. *x*

Na versão 3. *x*, as amarras de `Microsoft.Azure.WebJobs.Extensions.Storage` armazenamento estão incluídas no pacote. Chame `AddAzureStorage` o método `ConfigureWebJobs` de extensão no método, como mostrado aqui:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddAzureStorage();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Para usar outros tipos de gatilho e associação, instale o pacote do NuGet que os contém e chame o método de extensão `Add<binding>` implementado na extensão. Por exemplo, se você quiser usar uma vinculação `Microsoft.Azure.WebJobs.Extensions.CosmosDB` DB `AddCosmosDB`do Azure Cosmos, instale e ligue, como esta:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddCosmosDB();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Para usar o gatilho de Temporizador ou a associação de Arquivos, que fazem parte dos serviços principais, chame os métodos de extensão `AddTimers` ou `AddFiles`, respectivamente.

#### <a name="version-2x"></a>Versão 2. *x*

Estes tipos de gatilho e vinculação estão incluídos na versão 2. *x* do `Microsoft.Azure.WebJobs` pacote:

* Armazenamento de Blobs
* Armazenamento de filas
* Armazenamento de tabelas

Para usar outros tipos de associação e gatilho, instale o pacote do NuGet que os contém e chame um método `Use<binding>` no objeto `JobHostConfiguration`. Por exemplo, se você quiser usar um `Microsoft.Azure.WebJobs.Extensions` gatilho `UseTimers` temporizador, instale e chame no `Main` método, conforme mostrado aqui:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

Para usar a associação de Arquivos, instale `Microsoft.Azure.WebJobs.Extensions` e chame `UseFiles`.

### <a name="executioncontext"></a>ExecutionContext

WebJobs permite que você [`ExecutionContext`]se vincule a um . Com esta vinculação, [`ExecutionContext`] você pode acessar o como um parâmetro na sua assinatura de função. Por exemplo, o código a seguir usa o objeto de contexto para acessar a ID de invocação, que você pode usar para correlacionar todos os logs produzidos por uma determinada chamada de função.  

```cs
public class Functions
{
    public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        ExecutionContext executionContext,
        ILogger logger)
    {
        logger.LogInformation($"{message}\n{executionContext.InvocationId}");
    }
}
```

O processo de [`ExecutionContext`] vinculação ao depende da sua versão SDK.

#### <a name="version-3x"></a>Versão 3. *x*

Chame `AddExecutionContextBinding` o método `ConfigureWebJobs` de extensão no método, como mostrado aqui:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddExecutionContextBinding();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Versão 2. *x*

O pacote `Microsoft.Azure.WebJobs.Extensions` mencionado anteriormente também fornece um tipo especial de associação que você pode registrar chamando o método `UseCore`. Esta vinculação permite [`ExecutionContext`] definir um parâmetro na assinatura da função, que está habilitado assim:

```cs
class Program
{
    static void Main()
    {
        config = new JobHostConfiguration();
        config.UseCore();
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="binding-configuration"></a>Configuração de associação

Você pode configurar o comportamento de alguns gatilhos e ligações. O processo para configurá-los depende da versão SDK.

* **Versão 3. *x:*** Definir configuração quando `ConfigureWebJobs`o `Add<Binding>` método é chamado .
* **Versão 2. *x*:** Definir a configuração definindo propriedades `JobHost`em um objeto de configuração para o que você passa .

Essas configurações específicas de vinculação são equivalentes às configurações do [arquivo de projeto host.json](../azure-functions/functions-host-json.md) em Funções Azure.

Você pode configurar as seguintes vinculações:

* [Gatilho Do Azure CosmosDB](#azure-cosmosdb-trigger-configuration-version-3x)
* [Gatilho dos Hubs de Eventos](#event-hubs-trigger-configuration-version-3x)
* [Gatilho de armazenamento de filas](#queue-storage-trigger-configuration)
* [Vinculação sendgrid](#sendgrid-binding-configuration-version-3x)
* [Gatilho do Barramento de Serviço](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Configuração do gatilho Do Azure CosmosDB (versão 3.* x*)

Este exemplo mostra como configurar o gatilho Azure Cosmos DB:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddCosmosDB(a =>
        {
            a.ConnectionMode = ConnectionMode.Gateway;
            a.Protocol = Protocol.Https;
            a.LeaseOptions.LeasePrefix = "prefix1";

        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Para obter mais detalhes, consulte o artigo de vinculação do [Azure CosmosDB.](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings)

### <a name="event-hubs-trigger-configuration-version-3x"></a>Event Hubs acionam a configuração (versão 3.* x*)

Este exemplo mostra como configurar o gatilho do Event Hubs:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddEventHubs(a =>
        {
            a.BatchCheckpointFrequency = 5;
            a.EventProcessorOptions.MaxBatchSize = 256;
            a.EventProcessorOptions.PrefetchCount = 512;
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Para obter mais detalhes, consulte o artigo [de vinculação do Event Hubs.](../azure-functions/functions-bindings-event-hubs-trigger.md#host-json)

### <a name="queue-storage-trigger-configuration"></a>Configuração do gatilho de armazenamento na fila

Esses exemplos mostram como configurar o gatilho de armazenamento da fila:

#### <a name="version-3x"></a>Versão 3. *x*

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage(a => {
            a.BatchSize = 8;
            a.NewBatchThreshold = 4;
            a.MaxDequeueCount = 4;
            a.MaxPollingInterval = TimeSpan.FromSeconds(15);
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Para obter mais detalhes, consulte o artigo [de vinculação de armazenamento de fila.](../azure-functions/functions-bindings-storage-queue-trigger.md#hostjson-properties)

#### <a name="version-2x"></a>Versão 2. *x*

```cs
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.NewBatchThreshold = 4;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Para obter mais detalhes, consulte a [referência host.json v1.x](../azure-functions/functions-host-json-v1.md#queues).

### <a name="sendgrid-binding-configuration-version-3x"></a>Configuração de vinculação sendgrid (versão 3.* x*)

Este exemplo mostra como configurar a vinculação de saída sendgrid:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddSendGrid(a =>
        {
            a.FromAddress.Email = "samples@functions.com";
            a.FromAddress.Name = "Azure Functions";
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Para obter mais detalhes, consulte o artigo [de vinculação do SendGrid.](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings)

### <a name="service-bus-trigger-configuration-version-3x"></a>Configuração do gatilho do ponto de serviço (versão 3.* x*)

Este exemplo mostra como configurar o gatilho do Service Bus:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddServiceBus(sbOptions =>
        {
            sbOptions.MessageHandlerOptions.AutoComplete = true;
            sbOptions.MessageHandlerOptions.MaxConcurrentCalls = 16;
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Para obter mais detalhes, consulte o artigo [de vinculação do Service Bus.](../azure-functions/functions-bindings-service-bus-output.md#hostjson-settings)

### <a name="configuration-for-other-bindings"></a>Configuração de outras associações

Alguns tipos de gatilho e vinculação definem seus próprios tipos de configuração personalizadas. Por exemplo, o gatilho Arquivo permite especificar o caminho raiz para monitorar, como nestes exemplos:

#### <a name="version-3x"></a>Versão 3. *x*

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddFiles(a => a.RootPath = @"c:\data\import");
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Versão 2. *x*

```cs
static void Main()
{
    config = new JobHostConfiguration();
    var filesConfig = new FilesConfiguration
    {
        RootPath = @"c:\data\import"
    };
    config.UseFiles(filesConfig);
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

## <a name="binding-expressions"></a>Expressões de associação

Nos parâmetros do construtor de atributo, você pode usar expressões que são resolvidas para valores de várias fontes. Por exemplo, no código a seguir, o caminho para o atributo `BlobTrigger` cria uma expressão nomeada `filename`. Quando usado para a associação de saída, `filename` é resolvido para o nome do blob de disparo.

```cs
public static void CreateThumbnail(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger logger)
{
    logger.Info($"Blob trigger processing: {filename}");
    // ...
}
```

Para obter mais informações sobre expressões de associação, consulte [Padrões e expressões de associação](../azure-functions/functions-bindings-expressions-patterns.md) na documentação do Azure Functions.

### <a name="custom-binding-expressions"></a>Expressões de associação personalizadas

Às vezes, você deseja especificar um nome de fila, um nome ou contêiner blob ou um nome de tabela em código em vez de codifica-lo duramente. Por exemplo, você talvez queira especificar o nome da fila para o atributo `QueueTrigger` em um arquivo de configuração ou uma variável de ambiente.

Você pode fazer isso `NameResolver` passando um `JobHostConfiguration` objeto para o objeto. Você inclui espaços reservados no gatilho ou parâmetros do construtor de atributo de associação, e seu código `NameResolver` fornece os valores reais a serem usados no lugar desses espaços reservados. Você identifica espaços reservados ao cercar-os com porcentagem (%) sinais, como mostrado aqui:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Este código lhe permite usar uma fila denominada `logqueuetest` no ambiente de teste e uma denominada `logqueueprod` na produção. Em vez de um nome de fila embutido em código, você especifica o nome de uma entrada na coleção `appSettings`.

Há um padrão `NameResolver` que entra em vigor se você não fornecer um personalizado. O padrão obtém valores de configurações de aplicativo ou variáveis de ambiente.

Sua `NameResolver` classe recebe o `appSettings`nome da fila, como mostrado aqui:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>Versão 3. *x*

Você configura o resolver usando injeção de dependência. Esses exemplos exigem a seguinte instrução `using`:

```cs
using Microsoft.Extensions.DependencyInjection;
```

Você adiciona o resolver [`ConfigureServices`] chamando [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)o método de extensão, como neste exemplo:

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    var resolver = new CustomNameResolver();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureServices(s => s.AddSingleton<INameResolver>(resolver));
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Versão 2. *x*

Passe `NameResolver` sua classe `JobHost` para o objeto, como mostrado aqui:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

O Azure Functions implementa `INameResolver` para obter valores de configurações do aplicativo, conforme mostrado no exemplo. Quando usa o WebJobs SDK diretamente, você pode escrever uma implementação personalizada que obtém valores de substituição de espaço reservado de qualquer fonte preferida.

## <a name="binding-at-runtime"></a>Associando no runtime

Se você precisar fazer algum trabalho em sua função `Queue` `Blob`antes `Table`de usar um `IBinder` atributo de vinculação como , ou , você pode usar a interface.

O exemplo a seguir usa uma mensagem da fila de entrada e cria uma nova mensagem com o mesmo conteúdo em uma fila de saída. O nome da fila de saída é definido pelo código no corpo da função.

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessageAsync(new CloudQueueMessage(queueMessage));
}
```

Para obter mais informações, consulte [Associação no runtime](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) na documentação do Azure Functions.

## <a name="binding-reference-information"></a>Informações de referência de associação

A documentação Funções do Azure fornece informações de referência sobre cada tipo de vinculação. Você encontrará as seguintes informações em cada artigo de referência vinculante. (Este exemplo é baseado na fila de armazenamento.)

* [Pacotes](../azure-functions/functions-bindings-storage-queue.md). O pacote que você precisa instalar para incluir o suporte para a vinculação em um projeto WebJobs SDK.
* [Exemplos](../azure-functions/functions-bindings-storage-queue-trigger.md). Amostras de código. O exemplo da biblioteca de classe C# se aplica ao WebJobs SDK. Apenas omita o atributo. `FunctionName`
* [Atributos](../azure-functions/functions-bindings-storage-queue-trigger.md#attributes-and-annotations). Os atributos a serem usados para o tipo de vinculação.
* [Configuração](../azure-functions/functions-bindings-storage-queue-trigger.md#configuration). Explicações das propriedades do atributo e parâmetros do construtor.
* [Uso](../azure-functions/functions-bindings-storage-queue-trigger.md#usage). Os tipos que você pode vincular e informações sobre como a ligação funciona. Por exemplo: algoritmo de sondagem, processamento de fila de mensagens suspeita.
  
Para obter uma lista de artigos de referência vinculativos, consulte "Vinculações suportadas" no artigo [Gatilhos e vinculações](../azure-functions/functions-triggers-bindings.md#supported-bindings) para Funções Azure. Nessa lista, as vinculações HTTP, Webhooks e Event Grid são suportadas apenas pelo Azure Functions, não pelo WebJobs SDK.

## <a name="disable-attribute"></a>Atributo Desabilitar 

O [`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) atributo permite controlar se uma função pode ser acionada. 

No exemplo a seguir, `Disable_TestJob` se a `1` configuração do aplicativo tiver um valor ou `True` (caso insensível), a função não será executada. Nesse caso, o runtime cria uma mensagem de log *A função 'Functions.TestJob' está desabilitada*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Quando você altera os valores de configuração do aplicativo no portal Azure, o WebJob reinicia para pegar a nova configuração.

O atributo pode ser declarado no nível de classe, método ou parâmetro. O nome da configuração pode também conter expressões de associação.

## <a name="timeout-attribute"></a>Atributo de tempo limite

O [`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) atributo faz com que uma função seja cancelada se não for concluída dentro de um período de tempo especificado. No exemplo a seguir, a função seria executada por um dia sem o atributo Timeout. O tempo hábeis faz com que a função seja cancelada após 15 segundos.

```cs
[Timeout("00:00:15")]
public static async Task TimeoutJob(
    [QueueTrigger("testqueue2")] string message,
    CancellationToken token,
    TextWriter log)
{
    await log.WriteLineAsync("Job starting");
    await Task.Delay(TimeSpan.FromDays(1), token);
    await log.WriteLineAsync("Job completed");
}
```

Você pode aplicar o atributo Timeout na classe ou no nível `JobHostConfiguration.FunctionTimeout`do método, e você pode especificar um tempo mais alto usando . Os tempos outs em nível de classe ou de método sobrepõem os tempos outs globais.

## <a name="singleton-attribute"></a>Atributo Singleton

O [`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) atributo garante que apenas uma instância de uma função seja executada, mesmo quando há várias instâncias do aplicativo web host. Ele faz isso usando [bloqueio distribuído](#viewing-lease-blobs).

Neste exemplo, apenas uma única `ProcessImage` instância da função é executada a qualquer momento:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Alguns gatilhos têm suporte interno para gerenciamento de simultaneidade:

* **QueueTrigger**. Defina `JobHostConfiguration.Queues.BatchSize` como `1`.
* **ServiceBusTrigger**. Defina `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` como `1`.
* **FileTrigger**. Defina `FileProcessor.MaxDegreeOfParallelism` como `1`.

Você pode usar essas configurações para garantir que sua função seja executada como um singleton em uma única instância. Para garantir que apenas uma única instância da função esteja sendo executado quando o aplicativo web for dimensionado para várias instâncias, aplique um bloqueio singleton em nível de ouvinte na função (`[Singleton(Mode = SingletonMode.Listener)]`). Os bloqueios do ouvinte são adquiridos quando o JobHost é iniciado. Se três instâncias expandidas forem iniciadas ao mesmo tempo, somente uma das instâncias adquirirá o bloqueio e somente um ouvinte será iniciado.

### <a name="scope-values"></a>Valores de escopo

Você pode especificar uma *expressão/valor de escopo* em um singleton. A expressão/valor garante que todas as execuções da função em um escopo específico sejam serializadas. Implementar travamento mais granular desta forma pode permitir algum nível de paralelismo para sua função enquanto serializa outras invocações conforme ditada por suas necessidades. Por exemplo, no código a seguir, a `Region` expressão de escopo se liga ao valor da mensagem recebida. Quando a fila contém três mensagens nas regiões Leste, Leste e Oeste, respectivamente, as mensagens que têm a região Leste são executadas em série enquanto a mensagem com a região Oeste é executada em paralelo com as do Leste.

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item.
}

public class WorkItem
{
     public int ID { get; set; }
     public string Region { get; set; }
     public int Category { get; set; }
     public string Description { get; set; }
}
```

### <a name="singletonscopehost"></a>SingletonScope.Host

O escopo padrão para `SingletonScope.Function`um bloqueio é , o que significa que o escopo de bloqueio (o caminho de locação blob) está vinculado ao nome da função totalmente qualificado. Para bloquear funções, `SingletonScope.Host` especifique e use um nome de ID de escopo que seja o mesmo em todas as funções que você não deseja executar simultaneamente. No exemplo a seguir, apenas uma instância de `AddItem` ou `RemoveItem` é executada por vez:

```csharp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation.
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation.
}
```

### <a name="viewing-lease-blobs"></a>Exibição de blobs de concessão

O WebJobs SDK usa [concessões de blob do Azure](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) nos bastidores para implementar o bloqueio distribuído. As bolhas de locação usadas pela `azure-webjobs-host` Singleton podem `AzureWebJobsStorage` ser encontradas no contêiner na conta de armazenamento sob o caminho "locks". Por exemplo, o caminho de blob de concessão para o primeiro exemplo `ProcessImage` mostrado anteriormente pode ser `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Todos os caminhos incluem a ID de JobHost, 061851c758f04938a4426aa9ab3869c0 neste caso.

## <a name="async-functions"></a>Funções assíncronas

Para obter informações sobre como codificar funções de assincronia, consulte [a documentação funções do Azure](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Tokens de cancelamento

Para obter informações sobre como lidar com tokens de cancelamento, consulte a documentação do Azure Functions nos [tokens de cancelamento e desligamento normal](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Várias instâncias

Se o seu aplicativo Web for executado em várias instâncias, um WebJob contínuo será executado em cada instância, ouvindo os gatilhos e chamando funções. As várias associações de gatilho são projetadas para compartilhar com eficiência o trabalho de forma colaborativa entre instâncias, para que a expansão para mais instâncias permita que você manipule mais carga.

Embora alguns gatilhos possam resultar em duplo processamento, os gatilhos de armazenamento de filas e bolhas impedem automaticamente uma função de processar uma mensagem de fila ou bolha mais de uma vez. Para obter mais informações, consulte [Designing for identical input](../azure-functions/functions-idempotent.md) in the Azure Functions documents.

O gatilho de timer garante que apenas uma instância do timer seja executada, portanto você não terá mais de uma instância de função em execução em um determinado horário agendado.

Se você quiser garantir que apenas uma instância de uma função seja executada mesmo quando [`Singleton`](#singleton-attribute) houver várias instâncias do aplicativo web host, você pode usar o atributo.

## <a name="filters"></a>Filtros

Filtros de função (visualização) fornecem uma maneira de personalizar o pipeline de execução de WebJobs com sua própria lógica. Os filtros são semelhantes [aos filtros ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). Você pode implementá-los como atributos declarativos que são aplicados às suas funções ou classes. Para obter mais informações, consulte [Filtros de Função](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Log e monitoramento

Recomendamos a estrutura madeireira desenvolvida para ASP.NET. O artigo [Get started](webjobs-sdk-get-started.md) mostra como usá-lo. 

### <a name="log-filtering"></a>Filtragem de linha

Cada log criado por uma instância de `ILogger` possui um `Category` e `Level` associados. [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)é uma enumeração, e o código inteiro indica importância relativa:

|LogLevel    |Código|
|------------|---|
|Trace       | 0 |
|Depurar       | 1 |
|Informações | 2 |
|Aviso     | 3 |
|Erro       | 4 |
|Crítico    | 5 |
|Nenhum        | 6 |

Você pode filtrar independentemente [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)cada categoria para um determinado . Por exemplo, você talvez queira ver todos os logs para o processamento de gatilho de blob, mas apenas `Error` e superiores para todo o resto.

#### <a name="version-3x"></a>Versão 3. *x*

Versão 3. *x* do SDK depende da filtragem incorporada ao .NET Core. A classe `LogCategories` permite que você defina categorias para funções, gatilhos e usuários específicos. Também define filtros para estados de `Startup` `Results`hospedagem específicos, como e . Isso permite ajustar a saída de registro. Se nenhuma correspondência for encontrada nas categorias definidas, o filtro reverterá para o valor `Default` ao decidir se deseja filtrar a mensagem.

`LogCategories` requer a seguinte declaração de uso:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

O exemplo a seguir constrói um filtro que, por `Warning` padrão, filtra todos os logs no nível. As `Function` `results` categorias (equivalente `Host.Results` à versão 2.* x*) são filtrados no `Error` nível. O filtro compara a categoria atual com todos os níveis registrados na instância `LogCategories` e escolhe a maior correspondência. Isso significa `Debug` que o `Host.Triggers` nível `Host.Triggers.Queue` `Host.Triggers.Blob`registrado para partidas ou . Isso permite que você controle categorias mais amplas sem a necessidade de adicionar cada uma delas.

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging(logging =>
            {
                logging.SetMinimumLevel(LogLevel.Warning);
                logging.AddFilter("Function", LogLevel.Error);
                logging.AddFilter(LogCategories.CreateFunctionCategory("MySpecificFunctionName"),
                    LogLevel.Debug);
                logging.AddFilter(LogCategories.Results, LogLevel.Error);
                logging.AddFilter("Host.Triggers", LogLevel.Debug);
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Versão 2. *x*

Na versão 2. *x* do SDK, você `LogCategoryFilter` usa a classe para controlar a filtragem. O `LogCategoryFilter` tem `Default` uma propriedade com `Information`um valor inicial de `Information` `Warning`, `Error`o `Critical` que significa que quaisquer mensagens `Debug` `Trace` no , , ou níveis são registrados, mas quaisquer mensagens nos níveis ou níveis são filtradas.

Como `LogCategories` na versão 3. *x*, `CategoryLevels` a propriedade permite especificar níveis de log para categorias específicas para que você possa ajustar a saída de registro. Se nenhuma correspondência for encontrada no dicionário `CategoryLevels`, o filtro reverterá para o valor `Default` ao decidir se deseja filtrar a mensagem.

O exemplo a seguir constrói um filtro que, por padrão, filtra todos os logs no nível de `Warning`. As `Function` `Host.Results` categorias e categorias são `Error` filtradas no nível. O `LogCategoryFilter` compara a categoria atual com todos os `CategoryLevels` registrados e escolhe a maior correspondência. Assim, `Debug` o nível `Host.Triggers` registrado `Host.Triggers.Queue` para `Host.Triggers.Blob`vai corresponder ou . Isso permite que você controle categorias mais amplas sem a necessidade de adicionar cada uma delas.

```csharp
var filter = new LogCategoryFilter();
filter.DefaultLevel = LogLevel.Warning;
filter.CategoryLevels[LogCategories.Function] = LogLevel.Error;
filter.CategoryLevels[LogCategories.Results] = LogLevel.Error;
filter.CategoryLevels["Host.Triggers"] = LogLevel.Debug;

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(instrumentationKey, filter.Filter)
    .AddConsole(filter.Filter);
```

### <a name="custom-telemetry-for-application-insights"></a>Telemetria personalizada do Application Insights​

O processo de implementação da telemetria personalizada para [insights de aplicativos](../azure-monitor/app/app-insights-overview.md) depende da versão SDK. Para saber como configurar o Application Insights, confira [Adicionar registro em log do Application Insights](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>Versão 3. *x*

Porque a versão 3. *x* do WebJobs SDK conta com o host genérico .NET Core, uma fábrica de telemetria personalizada não é mais fornecida. Mas você pode adicionar telemetria personalizada ao pipeline usando injeção de dependência. Os exemplos nesta seção exigem as seguintes declarações `using`:

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

A seguinte implementação personalizada permite [`ITelemetryInitializer`] [`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) adicionar a [`TelemetryConfiguration`]sua própria ao padrão .

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

Chame [`ConfigureServices`] o construtor para [`ITelemetryInitializer`] adicionar seu costume ao pipeline.

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging((context, b) =>
    {
        // Add logging providers.
        b.AddConsole();

        // If this key exists in any config, use it to enable Application Insights.
        string appInsightsKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
        if (!string.IsNullOrEmpty(appInsightsKey))
        {
            // This uses the options callback to explicitly set the instrumentation key.
            b.AddApplicationInsights(o => o.InstrumentationKey = appInsightsKey);
        }
    });
    builder.ConfigureServices(services =>
        {
            services.AddSingleton<ITelemetryInitializer, CustomTelemetryInitializer>();
        });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Quando [`TelemetryConfiguration`] o for construído, todos [`ITelemetryInitializer`] os tipos registrados de são incluídos. Para saber mais, consulte [a API do Application Insights para obter eventos e métricas personalizados.](../azure-monitor/app/api-custom-events-metrics.md)

Na versão 3. *x*, você não precisa [`TelemetryClient`] mais dar descarga quando o host parar. O sistema de injeção de dependência do Núcleo `ApplicationInsightsLoggerProvider`.NET elimina [`TelemetryClient`]automaticamente o registrado, que libera o .

#### <a name="version-2x"></a>Versão 2. *x*

Na versão 2. *x*, [`TelemetryClient`] o criado internamente pelo provedor Application Insights para [`ServerTelemetryChannel`](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)o WebJobs SDK usa . Quando o ponto de extremidade do Application Insights está indisponível ou limitando solicitações de entrada, este canal [salva as solicitações no sistema de arquivos do aplicativo Web e reenvia-as depois](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

O [`TelemetryClient`] é criado por uma `ITelemetryClientFactory`classe que implementa . Por padrão, este [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs)é o .

Se você quiser modificar qualquer parte do pipeline application Insights, você pode fornecer a sua própria `ITelemetryClientFactory`, e o host usará sua classe para construir um [`TelemetryClient`]. Por exemplo, este `DefaultTelemetryClientFactory` código substitui para `ServerTelemetryChannel`modificar uma propriedade de:

```csharp
private class CustomTelemetryClientFactory : DefaultTelemetryClientFactory
{
    public CustomTelemetryClientFactory(string instrumentationKey, Func<string, LogLevel, bool> filter)
        : base(instrumentationKey, new SamplingPercentageEstimatorSettings(), filter)
    {
    }

    protected override ITelemetryChannel CreateTelemetryChannel()
    {
        ServerTelemetryChannel channel = new ServerTelemetryChannel();

        // Change the default from 30 seconds to 15 seconds.
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

O `SamplingPercentageEstimatorSettings` objeto configura [amostragem adaptativa](https://docs.microsoft.com/azure/application-insights/app-insights-sampling). Isso significa que, em certos cenários de alto volume, o Applications Insights envia um subconjunto selecionado de dados de telemetria para o servidor.

Depois de criar a fábrica de telemetria, você a repassa ao provedor de registro Application Insights:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a name="next-steps"></a><a id="nextsteps"></a> Próximas etapas

Este artigo forneceu trechos de código que mostram como lidar com cenários comuns para trabalhar com o WebJobs SDK. Para obter exemplos completos, consulte [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost).

['ExecutionContext']: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
['ITelemetriaInicializador']: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
['Configuração de jobhost']: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
