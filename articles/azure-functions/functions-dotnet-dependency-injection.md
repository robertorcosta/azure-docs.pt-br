---
title: Usar injeção de dependência no .NET do Azure Functions
description: Saiba como usar a injeção de dependência para registrar e usar serviços em funções do .NET
author: craigshoemaker
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: 97e8a34f3b8639990f8de736a8f1f7429ebfd448
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83739134"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Usar injeção de dependência no .NET do Azure Functions

O Azure Functions dá suporte ao padrão de design de software injeção de dependência (DI), que é uma técnica para executar a [Inversão de Controle (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.

- A injeção de dependência no Azure Functions é desenvolvida usando como base os recursos de injeção de dependência do .NET Core. Recomendamos conhecer bem a injeção de dependência do [.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Há diferenças em como substituir dependências e como os valores de configuração são lidos com o Azure Functions no plano de consumo.

- O suporte para injeção de dependência começa com Azure Functions 2.x.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder usar a injeção de dependência, você precisa instalar os seguintes pacotes NuGet:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- Pacote [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) versão 1.0.28 ou posterior

## <a name="register-services"></a>Serviços de registro

Para registrar serviços, crie um método para configurar e adicionar componentes a uma instância `IFunctionsHostBuilder`.  O host do Azure Functions cria uma instância `IFunctionsHostBuilder` e a transmite diretamente para o seu método.

Para registrar o método, adicione o atributo de assembly `FunctionsStartup` que especifica o nome de tipo usado durante a inicialização.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

### <a name="caveats"></a>Advertências

Uma série de etapas de registro executada antes e depois que o runtime processa a classe de inicialização. Assim, tenha em mente os seguintes itens:

- *A classe de inicialização destina-se apenas à instalação e ao registro.* Evite usar serviços registrados na inicialização durante o processo de inicialização. Por exemplo, não tente registrar uma mensagem em um agente que está sendo registrado durante a inicialização. Nesse ponto do processo de registro, os serviços ainda não estão disponíveis para uso. Depois que o método `Configure` é executado, o runtime do Azure Functions continua registrando dependências adicionais, o que pode afetar o funcionamento dos serviços.

- *O contêiner de injeção de dependência só mantém tipos explicitamente registrados*. Os únicos serviços disponíveis como tipos injetáveis são os que são configurados no método `Configure`. Como resultado, tipos específicos do Functions, como `BindingContext` e `ExecutionContext`, não estão disponíveis durante a instalação ou como tipos injetáveis.

## <a name="use-injected-dependencies"></a>Usar dependências injetadas

A injeção de construtor é usada para disponibilizar suas dependências em uma função. O uso de injeção de construtor requer que classes estáticas não sejam usadas.

O exemplo a seguir demonstra como as dependências `IMyService` e `HttpClient` são injetadas em uma função disparada por HTTP. Este exemplo usa o pacote [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) necessário para registrar um `HttpClient` na inicialização.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System.Net.Http;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, HttpClient httpClient)
        {
            _service = service;
            _client = httpClient;
        }

        [FunctionName("GetPosts")]
        public async Task<IActionResult> Get(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "posts")] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            var res = await _client.GetAsync("https://microsoft.com");
            await _service.AddResponse(res);

            return new OkResult();
        }
    }
}
```

## <a name="service-lifetimes"></a>Tempos de vida do serviço

Os aplicativos do Azure Functions oferecem os mesmos tempos de vida de serviço que a [injeção de dependência do ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes). Para um aplicativo do Functions, tempos de vida de serviço diferentes se comportam da seguinte maneira:

- **Transitório**: Os serviços transitórios são criados após cada solicitação do serviço.
- **Com escopo**: O tempo de vida do serviço com escopo corresponde a um tempo de vida de execução de função. Serviços com escopo são criados uma vez por execução. Solicitações posteriores para esse serviço durante a execução reutilizam a instância de serviço existente.
- **Singleton**: O tempo de vida do serviço singleton corresponde ao tempo de vida do host e é reutilizado em execuções de função nessa instância. Os serviços de vida útil singleton são recomendados para conexões e clientes, por exemplo `SqlConnection` ou instâncias `HttpClient`.

Veja ou baixe um [exemplo de tempos de vida de serviço diferentes](https://aka.ms/functions/di-sample) no GitHub.

## <a name="logging-services"></a>Serviços de registro em log

Se você precisar do seu próprio provedor de logs, registre um tipo personalizado como uma instância `ILoggerProvider`. O Application Insights é adicionado automaticamente pelo Azure Functions.

> [!WARNING]
> - Não adicione `AddApplicationInsightsTelemetry()` à coleção de serviços porque ele registra os serviços que entram em conflito com os serviços fornecidos pelo ambiente.
> - Não registre seu próprio `TelemetryConfiguration` ou `TelemetryClient` se você estiver usando a funcionalidade interna do Application Insights. Se você precisar configurar sua própria instância `TelemetryClient`, crie uma por meio de `TelemetryConfiguration` injetado, como mostrado em [Monitorar Azure Functions](./functions-monitoring.md#version-2x-and-later-2).

### <a name="iloggert-and-iloggerfactory"></a>ILogger<T> e ILoggerFactory

O host injetará os serviços `ILogger<T>` e `ILoggerFactory` nos construtores.  No entanto, por padrão, esses novos filtros de log serão filtrados dos logs de função.  Você precisará modificar o arquivo `host.json` para aceitar filtros e categorias adicionais.  O exemplo a seguir demonstra como adicionar um `ILogger<HttpTrigger>` com logs que serão expostos pelo host.

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly ILogger<HttpTrigger> _log;

        public HttpTrigger(ILogger<HttpTrigger> log)
        {
            _log = log;
        }

        [FunctionName("HttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req)
        {
            _log.LogInformation("C# HTTP trigger function processed a request.");

            // ...
    }
}
```

E um arquivo `host.json` que adiciona o filtro de log.

```json
{
    "version": "2.0",
    "logging": {
        "applicationInsights": {
            "samplingExcludedTypes": "Request",
            "samplingSettings": {
                "isEnabled": true
            }
        },
        "logLevel": {
            "MyNamespace.HttpTrigger": "Information"
        }
    }
}
```

## <a name="function-app-provided-services"></a>Serviços oferecidos pelo aplicativo de funções

O host de função registra vários serviços. Os serviços a seguir podem ser dependências no seu aplicativo com segurança:

|Tipo de Serviço|Tempo de vida|Descrição|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Configuração de runtime|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Responsável por fornecer a ID da instância do host|

Se houver outros serviços em que você queira usar uma dependência, [crie um problema e faça uma proposta no GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Substituição dos serviços de host

No momento, não há serviços suportados de substituição pelo host.  Se houver serviços que você queira substituir, [crie um problema e faça uma proposta no GitHub](https://github.com/azure/azure-functions-host).

## <a name="working-with-options-and-settings"></a>Trabalhando com opções e configurações

Os valores definidos nas [configurações do aplicativo](./functions-how-to-use-azure-function-app-settings.md#settings) estão disponíveis em uma instância `IConfiguration`, que permite que você leia os valores das configurações do aplicativo na classe de inicialização.

Você pode extrair valores da instância `IConfiguration` para um tipo personalizado. Copiar os valores das configurações do aplicativo para um tipo personalizado facilita o teste dos seus serviços, tornando esses valores injetáveis. As configurações lidas na instância de configuração devem ser pares simples de chave/valor.

Considere a seguinte classe que inclui uma propriedade nomeada de acordo com uma configuração de aplicativo:

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

E um arquivo `local.settings.json` que possa estruturar a configuração personalizada da seguinte maneira:
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

De dentro do método `Startup.Configure`, você pode extrair valores da instância `IConfiguration` para seu tipo personalizado usando o seguinte código:

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.GetSection("MyOptions").Bind(settings);
                                           });
```

Chamar `Bind` copia os valores da configuração, que têm nomes de propriedade correspondentes, na instância personalizada. A instância das opções agora está disponível no contêiner IoC para ser injetada em uma função.

O objeto das opções é injetado na função como uma instância da interface `IOptions` genérica. Use a propriedade `Value` para acessar os valores encontrados na sua configuração.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _settings = options.Value;
    }
}
```

Consulte o [padrão de opções no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options) para mais informações com relação ao trabalho com opções.

> [!WARNING]
> Evite tentar ler valores de arquivos como *local.settings.json* ou *appsettings.{environment}.json* no plano de consumo. Os valores lidos desses arquivos relacionados a conexões de gatilho não estão disponíveis enquanto o aplicativo é dimensionado porque a infraestrutura de hospedagem não tem acesso às informações de configuração.

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os recursos a seguir:

- [Como monitorar seu aplicativo de funções](functions-monitoring.md)
- [Melhores práticas para funções](functions-best-practices.md)
