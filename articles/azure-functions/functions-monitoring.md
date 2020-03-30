---
title: Monitorar Azure Functions
description: Aprenda a usar o Azure Application Insights com funções do Azure para monitorar a execução da função.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 04/04/2019
ms.openlocfilehash: 08da17f1ef023676aa0c499cf4e7e1bb9687f1c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257846"
---
# <a name="monitor-azure-functions"></a>Monitorar Azure Functions

[O Azure Functions](functions-overview.md) oferece integração incorporada com [o Azure Application Insights](../azure-monitor/app/app-insights-overview.md) para monitorar funções. Este artigo mostra como configurar funções do Azure para enviar arquivos de log gerados pelo sistema para o Application Insights.

Recomendamos o uso do Application Insights porque coleta dados de registro, desempenho e erro. Ele detecta automaticamente anomalias de desempenho e inclui poderosas ferramentas de análise para ajudá-lo a diagnosticar problemas e entender como suas funções são usadas. Ele foi projetado para ajudar você a aprimorar continuamente o desempenho e a usabilidade do seu aplicativo. Você pode até mesmo usar o Application Insights durante o desenvolvimento de projetos de aplicativos de função local. Para obter mais informações, consulte [O que é o Application Insights?](../azure-monitor/app/app-insights-overview.md).

Como a instrumentação de Insights de Aplicativos necessária é incorporada às funções do Azure, tudo o que você precisa é de uma chave de instrumentação válida para conectar seu aplicativo de função a um recurso do Application Insights. A chave de instrumentação deve ser adicionada às configurações do aplicativo quando o recurso do aplicativo de função for criado no Azure. Se o seu aplicativo de função ainda não tiver essa chave, você pode [defini-la manualmente](#enable-application-insights-integration).  

## <a name="application-insights-pricing-and-limits"></a>Preços e limites do Application Insights

Você pode testar a integração do Application Insights aos Aplicativos de Funções gratuitamente. Há um limite diário para quantos dados podem ser processados gratuitamente. Você pode atingir este limite durante os testes. O Azure fornece o portal e notificações por email quando está se aproximando do limite diário. Se você perder esses alertas e atingir o limite, novos logs não aparecerão nas consultas do Application Insights. Esteja ciente do limite para evitar tempo de solução de problemas desnecessários. Para obter mais informações, consulte [Gerenciar o preço e o volume de dados no Application Insights](../azure-monitor/app/pricing.md).

A lista completa dos recursos do Application Insights disponíveis para o seu aplicativo de função está detalhada no [Application Insights for Azure Functions recursos suportados](../azure-monitor/app/azure-functions-supported-features.md).

## <a name="view-telemetry-in-monitor-tab"></a>Exibir telemetria na guia Monitor

Com [a integração do Application Insights ativada,](#enable-application-insights-integration)você pode visualizar dados de telemetria na guia **Monitor.**

1. Na página do aplicativo de função, selecione uma função que tenha sido executada pelo menos uma vez após a configuração do Application Insights. Em seguida, selecione a guia **Monitorar.** Selecione **Atualizar** periodicamente, até que a lista de invocações de função seja exibida.

   ![Lista de invocações](media/functions-monitoring/monitor-tab-ai-invocations.png)

    > [!NOTE]
    > Pode levar até cinco minutos para que a lista apareça enquanto o cliente de telemetria emlote dados para transmissão ao servidor. O atraso não se aplica ao [Live Metrics Stream](../azure-monitor/app/live-stream.md). Esse serviço se conecta ao host Funções quando você carrega a página, para que os logs sejam transmitidos diretamente para a página.

1. Para ver os registros de uma invocação de função específica, selecione o link de coluna **Data (UTC)** para essa invocação. A saída de logs para essa invocação aparece em uma nova página.

   ![Detalhes da Invocação](media/functions-monitoring/invocation-details-ai.png)

1. Escolha o link **Executar no Aplicativo Insights** para visualizar a origem da consulta que recupera os dados de log do Azure Monitor no Log do Azure Se esta for a primeira vez que usar o Azure Log Analytics em sua assinatura, você será solicitado a habilitá-lo.

1. Quando você escolher esse link e optar por ativar o Log Analytic. a seguinte consulta é exibida. Você pode ver que os resultados da consulta estão`where timestamp > ago(30d)`limitados aos últimos 30 dias (). Além disso, os resultados não mostram mais`take 20`de 20 linhas ( ). Em contrapartida, a lista de detalhes de invocação para sua função é para os últimos 30 dias sem limite.

   ![Lista de invocações de Análise do Application Insights](media/functions-monitoring/ai-analytics-invocation-list.png)

Para obter mais informações, consulte [Dados de telemetria da consulta](#query-telemetry-data) a seguir neste artigo.

## <a name="view-telemetry-in-application-insights"></a>Exibir a telemetria no Application Insights

Para abrir o Application Insights a partir de um aplicativo de função no portal Azure, acesse a página **visão geral** do aplicativo de função. Em **recursos configurados,** selecione **Insights de aplicativo**.

![Abrir insights de aplicativos a partir da página visão geral do aplicativo de função](media/functions-monitoring/ai-link.png)

Para obter informações sobre como usar o Application Insights, consulte a [documentação do o Application Insights](https://docs.microsoft.com/azure/application-insights/). Esta seção mostra alguns exemplos de como exibir dados no Application Insights. Se você já está familiarizado com o Application Insights, você pode ir diretamente para [as seções sobre como configurar e personalizar os dados de telemetria](#configure-categories-and-log-levels).

![Guia visão geral do Insights do aplicativo](media/functions-monitoring/metrics-explorer.png)

As seguintes áreas do Application Insights podem ser úteis ao avaliar o comportamento, o desempenho e os erros em suas funções:

| Investigar | Descrição |
| ---- | ----------- |
| **[Falhas](../azure-monitor/app/asp-net-exceptions.md)** |  Crie gráficos e alertas com base em falhas de função e exceções de servidor. O **Nome da Operação** é o nome da função. Falhas nas dependências não são mostradas a menos que você implemente telemetria personalizada para dependências. |
| **[Desempenho](../azure-monitor/app/performance-counters.md)** | Analise os problemas de desempenho visualizando a utilização de recursos e o throughput por **instâncias de função Cloud**. Esses dados podem ser úteis para cenários de depuração em que as funções estão travando seus recursos subjacentes. |
| **[Métricas](../azure-monitor/app/metrics-explorer.md)** | Crie gráficos e alertas baseados em métricas. As métricas incluem o número de invocações de função, tempo de execução e taxas de sucesso. |
| **[Métricas ao vivo](../azure-monitor/app/live-stream.md)** | Exibir dados métricos como criados em tempo real. |

## <a name="query-telemetry-data"></a>Dados de telemetria da consulta

[O Application Insights Analytics](../azure-monitor/app/analytics.md) oferece acesso a todos os dados de telemetria na forma de tabelas em um banco de dados. A Análise fornece uma linguagem de consulta para extrair, manipular e visualizar os dados. 

Escolha **Logs** para explorar ou consultar eventos registrados.

![Exemplo de análise](media/functions-monitoring/analytics-traces.png)

Veja um exemplo de consulta que mostra a distribuição de solicitações por trabalhador nos últimos 30 minutos.

<pre>
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
</pre>

As tabelas disponíveis são mostradas na guia **Esquema** à esquerda. Você pode encontrar os dados gerados por invocações de função nas tabelas a seguir:

| Tabela | Descrição |
| ----- | ----------- |
| **traces** | Logs criados pelo tempo de execução e pelo código de função. |
| **solicitações** | Um pedido para cada invocação de função. |
| **Exceções** | Quaisquer exceções lançadas pelo tempo de execução. |
| **customMetrics** | A contagem de invocações bem sucedidas e fracassadas, taxa de sucesso e duração. |
| **customEvents** | Eventos rastreados pelo tempo de execução, por exemplo: solicitações HTTP que acionam uma função. |
| **Performancecounters** | Informações sobre o desempenho dos servidores em que as funções estão sendo em execução. |

As outras tabelas são para testes de disponibilidade e telemetria de clientes e navegadores. Você pode implementar a telemetria personalizada para adicionar dados a ela.

Dentro de cada tabela, alguns dos dados específicos do Functions estão em um campo `customDimensions`.  Por exemplo, a consulta a seguir recupera todos os rastreamentos que têm o nível de log `Error`.

<pre>
traces 
| where customDimensions.LogLevel == "Error"
</pre>

O tempo de `customDimensions.LogLevel` `customDimensions.Category` execução fornece os campos. Você pode fornecer campos adicionais em logs que você escreve em seu código de função. Consulte [Registro em log estruturado](#structured-logging) mais adiante neste artigo.

## <a name="configure-categories-and-log-levels"></a>Configurar categorias e níveis de log

Você pode usar o Application Insights sem qualquer configuração personalizada. A configuração padrão pode resultar em grandes volumes de dados. Se você estiver usando uma assinatura do Azure do Visual Studio, poderá ter atingido o limite de dados para o Application Insights. Mais tarde, neste artigo, você aprende como configurar e personalizar os dados que suas funções enviam para o Application Insights. Para um aplicativo de função, o registro é configurado no arquivo [host.json.]

### <a name="categories"></a>Categorias

O agente do Azure Functions inclui uma *categoria* para cada log. A categoria indica qual parte do código de runtime ou do seu código de função gravou o log. O gráfico a seguir descreve as principais categorias de logs que o tempo de execução cria. 

| Categoria | Descrição |
| ----- | ----- | 
| Host.Results | Esses registros são exibidos como **solicitações** no Application Insights. Elas indicam o sucesso ou a falha de uma função. Todos esses registros estão `Information` escritos em nível. Se você `Warning` filtrar em ou acima, você não verá nenhum desses dados. |
| Host.Agregador | Esses logs fornecem contagens e médias de chamadas de função sobre um período [configurável](#configure-the-aggregator). O período padrão é de 30 segundos ou 1.000 resultados, o que ocorrer primeiro. Os logs estão disponíveis na tabela **customMetrics** no Application Insights. Exemplos são o número de corridas, taxa de sucesso e duração. Todos esses registros estão `Information` escritos em nível. Se você `Warning` filtrar em ou acima, você não verá nenhum desses dados. |

Todos os registros de categorias diferentes delas estão disponíveis na tabela **de rastreamentos** no Application Insights.

Todos os logs com `Host` categorias que começam são escritos pelo tempo de execução Functions. Os **logs de função iniciados** e **de função concluída** têm categoria `Host.Executor`. Para corridas bem sucedidas, esses logs estão `Information` nivelados. Exceções são registradas em `Error` nível. O runtime também cria logs de nível `Warning`, por exemplo: mensagens de fila enviadas para a fila de suspeita.

O tempo de execução Functions cria logs com uma categoria que começa com "Host". Na versão `function started`1.x, `function executed`os `function completed` logs e `Host.Executor`logs têm a categoria . A partir da versão 2.x, `Function.<YOUR_FUNCTION_NAME>`esses logs têm a categoria .

Se você escrever logs em seu código `Function.<YOUR_FUNCTION_NAME>.User` de função, a categoria é e pode ser qualquer nível de log. Na versão 1.x do tempo de execução Functions, a categoria é `Function`.

### <a name="log-levels"></a>Níveis de log

O logger Funções do Azure também inclui um *nível de log* com cada log. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) é uma enumeração e o código inteiro indica a importância relativa:

|LogLevel    |Código|
|------------|---|
|Trace       | 0 |
|Depurar       | 1 |
|Informações | 2 |
|Aviso     | 3 |
|Erro       | 4 |
|Crítico    | 5 |
|Nenhum        | 6 |

Nível de log `None` é explicado na próxima seção. 

### <a name="log-configuration-in-hostjson"></a>Configuração de log em host.json

O arquivo [host.json] configura quanto registro em log um aplicativo de função envia ao Application Insights. Para cada categoria, você deve indicar o nível de log mínimo para enviar. Há dois exemplos: o primeiro exemplo visa a [versão 2.x e posterior](functions-versions.md#version-2x) do tempo de execução Functions (com .NET Core), e o segundo exemplo é para a versão 1.x runtime.

### <a name="version-2x-and-higher"></a>Versão 2.x e superior

As versões v2.x e posteriores do tempo de execução Functions usam a hierarquia do [filtro de registro .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

### <a name="version-1x"></a>Versão 1.x

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

Este exemplo configura as seguintes regras:

* Para logs `Host.Results` com `Function`categoria `Error` ou , envie apenas nível e acima para Insights de aplicativo. Os logs para o nível `Warning` e abaixo são ignorados.
* Para logs com a categoria `Host.Aggregator`, enviar todos os logs para o Application Insights. O `Trace` nível de log é o mesmo que o que chamo de alguns agentes `Verbose`, mas usar `Trace` no [host. JSON] arquivo.
* Para todos os outros logs, envie somente o nível `Information` e acima ao Application Insights.

O valor de categoria em [host.json] controla o registro em log para todas as categorias que começam com o mesmo valor. `Host`em [host.json] controla `Host.General` `Host.Executor`o `Host.Results`registro para , , e assim por diante.

Se [host.json] incluir várias categorias que comecem com a mesma cadeia de caracteres, será feito primeiro a correspondência com as mais longas. Suponha que você queira `Host.Aggregator` tudo `Error` a partir do `Host.Aggregator` tempo de `Information` execução, exceto para registrar em nível, mas você deseja registrar no nível:

### <a name="version-2x-and-later"></a>Versão 2.x e posterior

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

### <a name="version-1x"></a>Versão 1.x 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

Para suprimir todos os logs para uma categoria, você pode usar o nível de log `None`. Nenhum registro é escrito com essa categoria e não há nível de registro acima dela.

## <a name="configure-the-aggregator"></a>Configurar o agregador

Conforme observado na seção anterior, o runtime agrega dados sobre as execuções de função em um período. O período padrão é de 30 segundos ou 1.000 execuções, o que ocorrer primeiro. Você pode definir essa configuração no arquivo [host.json].  Aqui está um exemplo:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Configurar a amostragem

O Application Insights tem um recurso de [amostragem](../azure-monitor/app/sampling.md) que pode protegê-lo de produzir muitos dados de telemetria em execuções concluídas em momentos de pico de carga. Quando a taxa de execuções recebidas excede um limite especificado, o Application Insights começa a ignorar aleatoriamente algumas das execuções recebidas. A configuração padrão para o número máximo de execuções por segundo é de 20 (cinco na versão 1.x). Você pode configurar a amostragem em [host.json].  Aqui está um exemplo:

### <a name="version-2x-and-later"></a>Versão 2.x e posterior

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20
      }
    }
  }
}
```

### <a name="version-1x"></a>Versão 1.x 

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```

> [!NOTE]
> [Amostragem](../azure-monitor/app/sampling.md) é habilitado por padrão. Se você parecer estar faltando dados, talvez seja necessário ajustar as configurações de amostragem para se adequar ao seu cenário de monitoramento particular.

## <a name="write-logs-in-c-functions"></a>Gravar logs em funções C#

Você pode gravar logs no seu código de função que apareçam como rastreamentos no Application Insights.

### <a name="ilogger"></a>ILogger

Use um parâmetro [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) em funções, em vez de um parâmetro `TraceWriter`. Logs criados `TraceWriter` usando ir para O `ILogger` Aplicativo Insights, mas permite que você faça [login estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Com um objeto `ILogger`, você chama os `Log<level>` [ métodos de extensão no ILogger ](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) para criar logs. O código `Information` a seguir grava logs com a categoria "Function.<YOUR_FUNCTION_NAME>. Usuário."

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Registro em log estruturado

A ordem dos espaços reservados, não seus nomes, determina quais parâmetros são usados na mensagem de log. Suponha que você tenha o seguinte código:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Se você mantiver a mesma cadeia de caracteres de mensagem e inverter a ordem dos parâmetros, o texto da mensagem resultante terá os valores nos locais errados.

Os espaços reservados são tratados dessa forma para que você possa fazer registro em log estruturado. O Application Insights armazena os pares de valor de nome do parâmetro e a seqüência de mensagens. O resultado é que os argumentos da mensagem tornam-se campos que você pode consultar.

Se a chamada do método logger parecer com o `customDimensions.prop__rowKey`exemplo anterior, você poderá consultar o campo . O `prop__` prefixo é adicionado para garantir que não haja colisões entre os campos que o tempo de execução adiciona e campos que seu código de função adiciona.

Você também pode consultar a cadeia de caracteres da mensagem original referenciando o campo `customDimensions.prop__{OriginalFormat}`.  

Aqui está uma representação JSON de exemplo de dados `customDimensions`:

```json
{
  customDimensions: {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="custom-metrics-logging"></a>Registro de métricas personalizadas

Em funções de script C#, você pode usar o método de extensão `LogMetric` em `ILogger` para criar métricas personalizadas no Application Insights. Aqui está um exemplo de chamada de método:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Este código é uma `TrackMetric` alternativa à chamada usando a API do Application Insights para .NET.

## <a name="write-logs-in-javascript-functions"></a>Gravar logs em funções de JavaScript

Em funções do Node.js, use `context.log` para gravar logs. O registro estruturado não está habilitado.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Registro de métricas personalizadas

Quando você está executando na [versão 1.x](functions-versions.md#creating-1x-apps) do tempo de execução Functions, as funções node.js podem usar o `context.log.metric` método para criar métricas personalizadas no Application Insights. Este método não é suportado atualmente na versão 2.x e posterior. Aqui está um exemplo de chamada de método:

```javascript
context.log.metric("TestMetric", 1234);
```

Este código é uma `trackMetric` alternativa à chamada usando o Node.js SDK para Insights de Aplicativos.

## <a name="log-custom-telemetry-in-c-functions"></a>Telemetria personalizada em funções C#

Há uma versão específica de funções do Application Insights SDK que você pode usar para enviar dados de telemetria personalizados de suas funções para insights de aplicativos: [Microsoft.Azure.WebJobs.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights). Use o seguinte comando do prompt de comando para instalar este pacote:

# <a name="command"></a>[Comando](#tab/cmd)

```cmd
dotnet add package Microsoft.Azure.WebJobs.Logging.ApplicationInsights --version <VERSION>
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <VERSION>
```

---

Neste comando, `<VERSION>` substitua por uma versão deste pacote que suporte sua versão instalada do [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/). 

Os exemplos C# a seguir usam a [API de telemetria personalizada](../azure-monitor/app/api-custom-events-metrics.md). O exemplo é para uma biblioteca de classes do .NET, mas o código do Application Insights é o mesmo para o script C#.

### <a name="version-2x-and-later"></a>Versão 2.x e posterior

As versões 2.x e posteriores do tempo de execução usam recursos mais recentes no Application Insights para correlacionar automaticamente a telemetria com a operação atual. Não há necessidade de definir manualmente `ParentId`a `Name` operação `Id`ou os campos.

```cs
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using System.Linq;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Track an Event
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            metric.Context.User.Id = name;
            this.telemetryClient.TrackMetric(metric);

            // Track a Dependency
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

[GetMetric](../azure-monitor/app/api-custom-events-metrics.md#getmetric) é a API recomendada atualmente para criar uma métrica.

### <a name="version-1x"></a>Versão 1.x

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
                {
                    Name = "GET api/planets/1/",
                    Target = "swapi.co",
                    Data = "https://swapi.co/api/planets/1/",
                    Timestamp = start,
                    Duration = DateTime.UtcNow - start,
                    Success = true
                };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```

Não ligue `TrackRequest` ou `StartOperation<RequestTelemetry>` porque verá pedidos duplicados para uma invocação de função.  O runtime do Functions controla automaticamente as solicitações.

Não definir `telemetryClient.Context.Operation.Id`. Esta configuração global causa correlação incorreta quando muitas funções estão sendo executados simultaneamente. Em vez disso, crie uma nova instância de telemetria (`DependencyTelemetry`, `EventTelemetry`) e modifique a propriedade `Context`. Em seguida, passe na instância de `Track` telemetria para o método correspondente `TelemetryClient` em (`TrackDependency()`, `TrackEvent()`). `TrackMetric()` Este método garante que a telemetria tenha os detalhes de correlação corretos para a invocação da função atual.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Telemetria personalizada em funções JavaScript

Aqui está um trecho de código de exemplo que envia telemetria personalizada com o [Application Insights Node.js SDK](https://github.com/microsoft/applicationinsights-node.js):

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    client.trackEvent({name: "my custom event", tagOverrides:{"ai.operation.id": context.invocationId}, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackTrace({message: "trace message", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:{"ai.operation.id": context.invocationId}});

    context.done();
};
```

O `tagOverrides` parâmetro define `operation_Id` o ID de invocação da função. Essa configuração permite que você correlacione toda a telemetria gerada automaticamente e a telemetria personalizada para uma dada invocação de função.

## <a name="dependencies"></a>Dependências

As funções v2 coletam automaticamente dependências para solicitações HTTP, ServiceBus, EventHub e SQL.

Você pode escrever código personalizado para mostrar as dependências. Por exemplo, consulte o código de amostra na [seção de telemetria personalizada C#](#log-custom-telemetry-in-c-functions). O código de exemplo resulta em um *mapa de aplicativo* no Application Insights que se parece com a seguinte imagem:

![Mapa do aplicativo](./media/functions-monitoring/app-map.png)

## <a name="enable-application-insights-integration"></a>Habilitar a integração do Application Insights

Para um aplicativo de funções enviar dados ao Application Insights, ele precisa saber a chave de instrumentação de um recurso do Application Insights. A chave deve estar em uma configuração de aplicativo chamada **APPINSIGHTS_INSTRUMENTATIONKEY**.

Quando você cria seu aplicativo de função [no portal Azure,](functions-create-first-azure-function.md)a partir da linha de comando usando as [Ferramentas Principais de Funções do Azure,](functions-create-first-azure-function-azure-cli.md)ou usando [o Visual Studio Code,](functions-create-first-function-vs-code.md)a integração do Application Insights é ativada por padrão. O recurso Application Insights tem o mesmo nome do seu aplicativo de função, e foi criado na mesma região ou na região mais próxima.

### <a name="new-function-app-in-the-portal"></a>Novo aplicativo de função no portal

Para revisar o recurso Application Insights que está sendo criado, selecione-o para expandir a janela **Insights do aplicativo.** Você pode alterar o **nome do recurso Novo** ou escolher um **local** diferente em uma geografia [do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde deseja armazenar seus dados.

![Habilitar o Application Insights ao criar um aplicativo de funções](media/functions-monitoring/enable-ai-new-function-app.png)

Quando você escolhe **Criar**, um recurso Application Insights é `APPINSIGHTS_INSTRUMENTATIONKEY` criado com seu aplicativo de função, que tem o conjunto nas configurações do aplicativo. Tudo está pronto para ir.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Adicione a um aplicativo de função existente 

Quando você cria um aplicativo de função usando [o Visual Studio,](functions-create-your-first-function-visual-studio.md)você deve criar o recurso Application Insights. Em seguida, você pode adicionar a chave de instrumentação desse recurso como uma configuração de aplicativo em seu aplicativo de função.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

As primeiras versões de Functions usavam monitoramento embutido, o que não é mais recomendado. Ao ativar a integração do Application Insights para tal aplicativo de função, você também deve [desativar o login](#disable-built-in-logging).  

## <a name="report-issues"></a>Relatar problemas

Para relatar um problema com a integração do Application Insights em Functions ou para fazer sugestões ou uma solicitação, [crie um problema no GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>Logs de streaming

Ao desenvolver um aplicativo, muitas vezes você quer ver o que está sendo escrito nos logs em quase tempo real ao ser executado no Azure.

Existem duas maneiras de visualizar um fluxo de arquivos de log sendo gerados por suas execuções de função.

* **Streaming de log incorporado**: a plataforma App Service permite que você visualize um fluxo de seus arquivos de registro de aplicativos. Isso equivale à saída vista quando você depura suas funções durante o [desenvolvimento local](functions-develop-local.md) e quando você usa a guia **Teste** no portal. Todas as informações baseadas em log são exibidas. Para obter mais informações, consulte [Stream logs](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Este método de streaming suporta apenas uma única instância, e não pode ser usado com um aplicativo em execução no Linux em um plano de consumo.

* **Live Metrics Stream**: quando seu aplicativo de função estiver [conectado ao Application Insights,](#enable-application-insights-integration)você pode visualizar dados de log e outras métricas em quase tempo real no portal Azure usando o Live [Metrics Stream](../azure-monitor/app/live-stream.md). Use este método ao monitorar funções em execução em várias instâncias ou no Linux em um plano de consumo. Este método usa [dados amostrados](#configure-sampling).

Os fluxos de log podem ser visualizados tanto no portal quanto na maioria dos ambientes de desenvolvimento local. 

### <a name="portal"></a>Portal

Você pode visualizar ambos os tipos de fluxos de log no portal.

#### <a name="built-in-log-streaming"></a>Streaming de log incorporado

Para ver os logs de streaming no portal, selecione a guia Recursos da **Plataforma** no seu aplicativo de função. Em seguida, em **Monitoramento,** escolha **Log streaming**.

![Habilite logs de streaming no portal](./media/functions-monitoring/enable-streaming-logs-portal.png)

Isso conecta seu aplicativo ao serviço de streaming de log e os logs de aplicativos são exibidos na janela. Você pode alternar entre **logs de aplicativos** e **logs de servidor Web**.  

![Veja os registros de streaming no portal](./media/functions-monitoring/streaming-logs-window.png)

#### <a name="live-metrics-stream"></a>Live Metrics Stream

Para visualizar o Fluxo de Métricas ao Vivo para o seu aplicativo, selecione a guia **Visão geral** do aplicativo de função. Quando você tem o App Insights habilitado, você vê um link **de Insights de aplicativo** em Recursos **Configurados**. Este link leva você à página Do aplicativo Insights para o seu aplicativo.

Em Insights de aplicativos, selecione **Live Metrics Stream**. [As entradas de registro amostradas](#configure-sampling) são exibidas em **Telemetria de Amostra**.

![Ver transmissão de métricas ao vivo no portal](./media/functions-monitoring/live-metrics-stream.png) 

### <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

### <a name="core-tools"></a>Ferramentas principais

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

### <a name="azure-cli"></a>CLI do Azure

Você pode habilitar logs de streaming usando o [Azure CLI](/cli/azure/install-azure-cli). Use os seguintes comandos para fazer login, escolha sua assinatura e transmita arquivos de log:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

Você pode habilitar logs de streaming usando [o Azure PowerShell](/powershell/azure/overview). Para o PowerShell, use os seguintes comandos para adicionar sua conta do Azure, escolha sua assinatura e transmita arquivos de log:

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <FUNCTION_APP_NAME> -Tail
```

## <a name="disable-built-in-logging"></a>Desabilitar o registro em log interno

Ao ativar o Application Insights, desative o registro interno que usa o Armazenamento Azure. O registro incorporado é útil para testes com cargas de trabalho leves, mas não é destinado ao uso de produção de alta carga. Para monitoramento de produção, recomendamos insights de aplicativos. Se o registro interno for usado na produção, o registro de registro pode estar incompleto devido ao estrangulamento no Azure Storage.

Para desabilitar o registro em log interno, exclua a configuração de aplicativo `AzureWebJobsDashboard`. Para obter informações sobre como excluir configurações do aplicativo no portal do Azure, consulte a seção **Configurações do aplicativo** em [Como gerenciar um aplicativo de funções](functions-how-to-use-azure-function-app-settings.md#settings). Antes de excluir a configuração do aplicativo, certifique-se de que nenhuma função existente no mesmo aplicativo de função use a configuração para gatilhos ou vinculações do Azure Storage.

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os recursos a seguir:

* [Application Insights](/azure/application-insights/)
* [Registro em log de ASP.NET Core](/aspnet/core/fundamentals/logging/)

[host.json]: functions-host-json.md
