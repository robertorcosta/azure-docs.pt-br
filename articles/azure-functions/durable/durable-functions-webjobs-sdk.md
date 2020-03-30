---
title: Como executar funções duráveis como WebJobs - Azure
description: Saiba como codificar e configurar funções durável para executar em trabalhos Web usando o SDK do WebJobs.
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: d8dd0c86fbc520d0bd3ef6034891bd9871774b4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232735"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Como executar funções duráveis como WebJobs

Por padrão, as Funções Duráveis usam o tempo de execução funções do Azure para hospedar orquestrações. No entanto, pode haver certos cenários onde você precisa de mais controle sobre o código que ouve os eventos. Este artigo mostra como implementar sua orquestração usando o WebJobs SDK. Para ver uma comparação mais detalhada entre Funções e WebJobs, consulte [Compare Functions e WebJobs](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

[Azure Functions](../functions-overview.md) e extensão de [Funções Duráveis](durable-functions-overview.md) baseiam-se no [SDK do WebJobs](../../app-service/webjobs-sdk-how-to.md). O host de trabalho no WebJobs SDK é o tempo de execução em Funções Azure. Se você precisar controlar o comportamento de maneiras não possíveis nas funções do Azure, você pode desenvolver e executar funções duráveis usando o WebJobs SDK você mesmo.

Na versão 3.x do WebJobs SDK, o `IHost`host é uma implementação `JobHost` de , e na versão 2.x você usa o objeto.

A amostra de funções duráveis de encadeamento está disponível em uma versão do WebJobs SDK 2.x: baixe ou clone o [repositório Durable Functions](https://github.com/azure/azure-functions-durable-extension/)e vá para a pasta *\\de encadeamento webjobssdk\\* amostras.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você está familiarizado com os conceitos básicos do SDK do WebJobs, desenvolvimento de bibliotecas de classe C# para funções do Azure Functions e as funções duráveis. Se você precisar obter uma introdução a esses tópicos, consulte os seguintes recursos:

* [Introdução ao WebJobs SDK](../../app-service/webjobs-sdk-get-started.md)
* [Criar sua primeira função usando o Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Funções duráveis](durable-functions-sequence.md)

Para completar as etapas neste artigo:

* [Instale o Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/) com a carga de trabalho **de desenvolvimento do Azure.**

  Se você já tem o Visual Studio, mas não tem essa carga de trabalho, adicione a carga de trabalho selecionando **Ferramentas** > **Obter Ferramentas e Recursos**.

  (Você pode usar o [código do Visual Studio](https://code.visualstudio.com/) em vez disso, mas algumas das instruções são específicas para o Visual Studio Code.)

* Instale e execute o [Emulador de Armazenamento do Azure](../../storage/common/storage-use-emulator.md) 5.2 ou posterior. Uma alternativa é atualizar o arquivo *App. config* com uma cadeia de caracteres de conexão de armazenamento do Microsoft Azure.

## <a name="webjobs-sdk-versions"></a>Versões do SDK do WebJobs

Este artigo explica como desenvolver um projeto de 2. x do SDK do WebJobs (equivalente a versão do Azure Functions 1. x). Para obter informações sobre versão 3. x, consulte [SDK do WebJobs 3. x](#webjobs-sdk-3x) posteriormente neste artigo.

## <a name="create-a-console-app"></a>Criar um aplicativo de console

Para executar funções duráveis como WebJobs, você deve primeiro criar um aplicativo de console. Um projeto do SDK do WebJobs é apenas um projeto de aplicativo de console com os pacotes do NuGet apropriados instalados.

Na caixa de diálogo Visual Studio **New Project,** selecione **o Aplicativo para** > Console de Desktop Clássico do Windows **(.NET Framework)**. No arquivo de projeto, o `TargetFrameworkVersion` deve ser `v4.6.1`.

O Visual Studio também tem um modelo de projeto WebJob, que você pode usar selecionando **Cloud** > **Azure WebJob (.NET Framework)**. Este modelo instala muitos pacotes, alguns dos quais você talvez não precise.

## <a name="install-nuget-packages"></a>Instalar os pacotes NuGet

Você precisará dos pacotes do NuGet para o SDK do WebJobs, associações de núcleo, a estrutura do registro em log e a extensão de tarefa durável. Aqui estão os comandos **do Package Manager Console** para esses pacotes, com os números de versão estáveis mais recentes a partir da data em que este artigo foi escrito:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

Você também precisa de provedores de log. Os seguintes comandos instalam o provedor `ConfigurationManager`Azure Application Insights e o . O `ConfigurationManager` permite que você obtenha a chave de instrumentação do Application Insights de configurações do aplicativo.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

O comando a seguir instala o provedor de console:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>Código de JobHost

Tendo criado o aplicativo do console e instalado os pacotes NuGet que você precisa, você está pronto para usar Funções Duráveis. Você faz isso usando o código JobHost.

Para usar a extensão de Funções Duráveis, chame `UseDurableTask` no `JobHostConfiguration` do objeto em seu `Main` método:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Para obter uma lista de propriedades que podem ser definidas no `DurableTaskExtension` de objeto, consulte [host.json](../functions-host-json.md#durabletask).

O `Main` método também é o local para configurar os provedores de log. O exemplo a seguir configura os provedores de console e Application Insights.

```cs
static void Main(string[] args)
{
    using (var loggerFactory = new LoggerFactory())
    {
        var config = new JobHostConfiguration();

        config.DashboardConnectionString = "";

        var instrumentationKey =
            ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];

        config.LoggerFactory = loggerFactory
            .AddApplicationInsights(instrumentationKey, null)
            .AddConsole();

        config.UseTimers();
        config.UseDurableTask(new DurableTaskExtension
        {
            HubName = "MyTaskHub",
        });
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="functions"></a>Funções

Funções duráveis no contexto do WebJobs diferem um pouco das Funções Duráveis no contexto das Funções Azure. É importante estar ciente das diferenças enquanto você escreve seu código.

O SDK do WebJobs não oferece suporte para os seguintes recursos do Azure Functions:

* [Atributo FunctionName](#functionname-attribute)
* [Gatilho HTTP](#http-trigger)
* [API de gerenciamento de funções HTTP duráveis](#http-management-api)

### <a name="functionname-attribute"></a>Atributo FunctionName

Em um projeto do SDK do WebJobs, o nome do método de uma função é o nome da função. O `FunctionName` atributo é usado somente no Azure Functions.

### <a name="http-trigger"></a>Gatilho HTTP

O SDK do WebJobs não tem um gatilho HTTP. O cliente de orquestração do projeto de exemplo usa um gatilho de timer:

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>API de Gerenciamento HTTP

Por não haver nenhum gatilho HTTP, o SDK do WebJobs não tem [API de gerenciamento HTTP](durable-functions-http-api.md).

Em um projeto WebJobs SDK, você pode chamar métodos no objeto cliente de orquestração, em vez de enviar solicitações HTTP. Os métodos a seguir correspondem a três tarefas que você pode fazer com a API de gerenciamento HTTP:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

A função cliente de orquestração no projeto de amostra inicia a `GetStatusAsync` função orquestradora e, em seguida, entra em um loop que liga a cada 2 segundos:

```cs
string instanceId = await client.StartNewAsync(nameof(HelloSequence), input: null);
logger.LogInformation($"Started new instance with ID = {instanceId}.");

DurableOrchestrationStatus status;
while (true)
{
    status = await client.GetStatusAsync(instanceId);
    logger.LogInformation($"Status: {status.RuntimeStatus}, Last update: {status.LastUpdatedTime}.");

    if (status.RuntimeStatus == OrchestrationRuntimeStatus.Completed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Failed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        break;
    }

    await Task.Delay(TimeSpan.FromSeconds(2));
}
```

## <a name="run-the-sample"></a>Execute o exemplo

Você tem funções duráveis configuradas para serem executadas como um WebJob, e agora você tem uma compreensão de como isso irá diferir de executar funções duráveis como funções autônomas do Azure. Neste ponto, vê-lo funcionar em uma amostra pode ser útil.

Esta seção fornece uma visão geral de como executar o [projeto de exemplo](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Para obter instruções detalhadas que explicam como executar um projeto do SDK do WebJobs localmente e implantá-lo em um Webjob do Azure, consulte [começar com o SDK do WebJobs](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Executar localmente

1. Verifique se o Emulador de armazenamento está em execução (consulte [pré-requisitos](#prerequisites)).

1. Se você quiser ver logs no Application Insights quando executar o projeto localmente:

    a. Crie um recurso application Insights e use o tipo de aplicativo **geral** para ele.

    b. Salvar a chave de instrumentação no arquivo *App.config*.

1. Execute o projeto.

### <a name="run-in-azure"></a>Executar no Azure

1. Criar um aplicativo Web e uma conta de armazenamento.

1. No aplicativo web, salve a seqüência `AzureWebJobsStorage`de conexão de armazenamento em uma configuração de aplicativo chamada .

1. Crie um recurso application Insights e use o tipo de aplicativo **geral** para ele.

1. Salve a chave de instrumentação `APPINSIGHTS_INSTRUMENTATIONKEY`em uma configuração de aplicativo chamada .

1. Implantar como um WebJob.

## <a name="webjobs-sdk-3x"></a>SDK de WebJobs 3.x

Este artigo explica como desenvolver um projeto WebJobs SDK 2.x. Se você está desenvolvendo um projeto [WebJobs SDK 3.x,](../../app-service/webjobs-sdk-get-started.md) esta seção ajuda você a entender as diferenças.

A principal mudança introduzida é o uso do .NET Core em vez do .NET Framework. Para criar um projeto WebJobs SDK 3.x, as instruções são as mesmas, com essas exceções:

1. Criar um aplicativo de console do aplicativo .NET Core. Na caixa de diálogo Visual Studio **New Project,** selecione **.NET Core** > **Console App (.NET Core)**. O arquivo de projeto especifica que `TargetFramework` é `netcoreapp2.x`.

1. Escolha a versão de versão WebJobs SDK 3.x dos seguintes pacotes:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Defina a seqüência de conexão de armazenamento e a chave de instrumentação Do aplicativo Insights em um arquivo *appsettings.json,* usando a estrutura de configuração .NET Core. Aqui está um exemplo:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Altere `Main` o código do método para fazer isso. Aqui está um exemplo:

   ```cs
   static void Main(string[] args)
   {
        var hostBuilder = new HostBuilder()
            .ConfigureWebJobs(config =>
            {
                config.AddAzureStorageCoreServices();
                config.AddAzureStorage();
                config.AddTimers();
                config.AddDurableTask(options =>
                {
                    options.HubName = "MyTaskHub";
                    options.AzureStorageConnectionStringName = "AzureWebJobsStorage";
                });
            })
            .ConfigureLogging((context, logging) =>
            {
                logging.AddConsole();
                logging.AddApplicationInsights(config =>
                {
                    config.InstrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                });
            })
            .UseConsoleLifetime();

        var host = hostBuilder.Build();

        using (host)
        {
            host.Run();
        }
   }
   ```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o SDK do WebJobs, consulte [como usar o SDK do WebJobs](../../app-service/webjobs-sdk-how-to.md).
