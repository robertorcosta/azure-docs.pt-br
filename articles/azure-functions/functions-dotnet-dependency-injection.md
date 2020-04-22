---
title: Usar injeção de dependência no .NET do Azure Functions
description: Saiba como usar a injeção de dependência para se cadastrar e usar serviços em funções .NET
author: craigshoemaker
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: a1ff8e0aedce5d3a6acc9a39084cf0839efdd88e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678451"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Usar injeção de dependência no .NET do Azure Functions

O Azure Functions suporta o padrão de design de software de injeção de dependência (DI), que é uma técnica para alcançar a [Inversão de Controle (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.

- A injeção de dependência em Funções Azure é construída nos recursos de injeção de dependência do núcleo .NET. Recomenda-se familiaridade com [a injeção de dependência do Núcleo .NET.](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) Existem diferenças na forma como você anula dependências e como os valores de configuração são lidos com funções do Azure no plano de consumo.

- O suporte para injeção de dependência começa com as funções Azure 2.x.

## <a name="prerequisites"></a>Pré-requisitos

Antes de usar a injeção de dependência, você deve instalar os seguintes pacotes NuGet:

- [Microsoft.Azure.Functions.Extensões](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.Functions pacote](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) versão 1.0.28 ou posterior

## <a name="register-services"></a>Serviços de registro

Para registrar serviços, crie um método para `IFunctionsHostBuilder` configurar e adicionar componentes a uma instância.  O host Funções do Azure cria uma instância e `IFunctionsHostBuilder` passa diretamente para o seu método.

Para registrar o método, adicione o atributo `FunctionsStartup` de montagem que especifica o nome de tipo usado durante a inicialização.

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

Uma série de etapas de registro são executadas antes e depois do tempo de execução da classe de inicialização. Portanto, tenha em mente os seguintes itens:

- *A classe de inicialização destina-se apenas à configuração e registro.* Evite utilizar serviços registrados na startup durante o processo de inicialização. Por exemplo, não tente registrar uma mensagem em um logger que está sendo registrado durante a inicialização. Este ponto do processo de registro é muito cedo para que seus serviços estejam disponíveis para uso. Depois `Configure` que o método é executado, o tempo de execução functions continua a registrar dependências adicionais, o que pode afetar a forma como seus serviços operam.

- *O recipiente de injeção de dependência contém apenas tipos explicitamente registrados*. Os únicos serviços disponíveis como tipos injetáveis são os que estão configurados no `Configure` método. Como resultado, tipos específicos de `BindingContext` `ExecutionContext` funções como e não estão disponíveis durante a configuração ou como tipos injetáveis.

## <a name="use-injected-dependencies"></a>Use dependências injetadas

A injeção de construtor é usada para tornar suas dependências disponíveis em uma função. O uso de injeção de construtor requer que você não use classes estáticas.

A amostra a `IMyService` seguir `HttpClient` demonstra como as dependências e dependências são injetadas em uma função acionada por HTTP. Este exemplo usa o pacote [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) necessário para registrar uma `HttpClient` inicialização.

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

Os aplicativos Azure Functions fornecem as mesmas vidas de serviço que [ASP.NET Dependency Injection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes). Para um aplicativo Functions, as diferentes vidas de serviço se comportam da seguinte forma:

- **Transitório**: Os serviços transitórios são criados a cada solicitação do serviço.
- **Escopo**: A vida útil de serviço escopo corresponde a uma vida útil de execução de função. Os serviços escopo são criados uma vez por execução. Solicitações posteriores para esse serviço durante a execução reutilizam a instância de serviço existente.
- **Singleton**: A vida útil do serviço singleton corresponde à vida útil do host e é reutilizada em execuções de função nessa instância. Os serviços de vida útil do Singleton `SqlConnection` `HttpClient` são recomendados para conexões e clientes, por exemplo ou instâncias.

Exibir ou baixar uma [amostra de diferentes vidas de serviço](https://aka.ms/functions/di-sample) no GitHub.

## <a name="logging-services"></a>Serviços de registro

Se você precisar do seu próprio provedor `ILoggerProvider` de registro, registre um tipo personalizado como instância. O Application Insights é adicionado automaticamente pelo Azure Functions.

> [!WARNING]
> - Não acrescente `AddApplicationInsightsTelemetry()` à coleta de serviços, pois registra serviços que conflitam com os serviços prestados pelo meio ambiente.
> - Não registre o `TelemetryConfiguration` `TelemetryClient` seu próprio ou se você estiver usando a funcionalidade do Application Insights incorporado. Se você precisar configurar `TelemetryClient` sua própria instância, crie `TelemetryConfiguration` uma através da injetada como mostrado no [Monitor Azure Functions](./functions-monitoring.md#version-2x-and-later-2).

### <a name="iloggert-and-iloggerfactory"></a>ILogger<T> e ILoggerFactory

O hospedeiro `ILogger<T>` `ILoggerFactory` vai injetar e serviços em construtores.  No entanto, por padrão, esses novos filtros de registro serão filtrados fora dos registros de função.  Você precisará modificar `host.json` o arquivo para optar por filtros e categorias adicionais.  A amostra a `ILogger<HttpTrigger>` seguir demonstra a adição de um com logs que serão expostos pelo host.

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

E `host.json` um arquivo que adiciona o filtro de log.

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

## <a name="function-app-provided-services"></a>Serviços fornecidos pelo aplicativo function

O host função registra muitos serviços. Os seguintes serviços são seguros de tomar como uma dependência em sua aplicação:

|Tipo de Serviço|Tempo de vida|Descrição|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Configuração em tempo de execução|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Responsável por fornecer o ID da instância do host|

Se houver outros serviços que você deseja fazer uma dependência, [crie um problema e proponha-os no GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Sobrepondo serviços de host

A substituição de serviços fornecidos pelo host não é suportada no momento.  Se houver serviços que você deseja substituir, [crie um problema e proponha-os no GitHub](https://github.com/azure/azure-functions-host).

## <a name="working-with-options-and-settings"></a>Trabalhando com opções e configurações

Os valores [definidos](./functions-how-to-use-azure-function-app-settings.md#settings) nas `IConfiguration` configurações do aplicativo estão disponíveis em uma instância, o que permite ler os valores das configurações do aplicativo na classe de inicialização.

Você pode extrair `IConfiguration` valores da instância para um tipo personalizado. Copiar os valores de configurações do aplicativo para um tipo personalizado facilita o teste de seus serviços, tornando esses valores injetáveis. As configurações lidas na instância de configuração devem ser pares simples de chave/valor.

Considere a seguinte classe que inclui uma propriedade nomeada consistente com uma configuração de aplicativo:

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

E `local.settings.json` um arquivo que pode estruturar a configuração personalizada da seguinte forma:
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

De dentro `Startup.Configure` do método, você `IConfiguration` pode extrair valores da instância para o seu tipo personalizado usando o seguinte código:

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.GetSection("MyOptions").Bind(settings);
                                           });
```

Chamar `Bind` cópias de valores que tenham nomes de propriedade correspondentes da configuração na instância personalizada. A instância de opções está agora disponível no recipiente IoC para injetar em uma função.

O objeto de opções é injetado na `IOptions` função como uma instância da interface genérica. Use `Value` a propriedade para acessar os valores encontrados em sua configuração.

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

Consulte o [padrão Opções no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options) para obter mais detalhes sobre como trabalhar com opções.

> [!WARNING]
> Evite tentar ler valores de arquivos como *local.settings.json* ou *appsettings.{ ambiente}.json* no plano de consumo. Os valores lidos a partir desses arquivos relacionados a conexões de gatilho não estão disponíveis como escalas de aplicativos porque a infra-estrutura de hospedagem não tem acesso às informações de configuração.

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os recursos a seguir:

- [Como monitorar seu aplicativo de função](functions-monitoring.md)
- [Melhores práticas para funções](functions-best-practices.md)
