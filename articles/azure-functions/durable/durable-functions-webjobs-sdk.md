---
title: How to run Durable Functions as WebJobs - Azure
description: Saiba como codificar e configurar funções durável para executar em trabalhos Web usando o SDK do WebJobs.
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: d8dd0c86fbc520d0bd3ef6034891bd9871774b4a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232735"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>How to run Durable Functions as WebJobs

By default, Durable Functions uses the Azure Functions runtime to host orchestrations. However, there may be certain scenarios where you need more control over the code that listens for events. This article shows you how to implement your orchestration using the WebJobs SDK. To see a more detailed comparison between Functions and WebJobs, see [Compare Functions and WebJobs](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

[Azure Functions](../functions-overview.md) e extensão de [Funções Duráveis](durable-functions-overview.md) baseiam-se no [SDK do WebJobs](../../app-service/webjobs-sdk-how-to.md). The job host in the WebJobs SDK is the runtime in Azure Functions. If you need to control behavior in ways not possible in Azure Functions, you can develop and run Durable Functions by using the WebJobs SDK yourself.

In version 3.x of the WebJobs SDK, the host is an implementation of `IHost`, and in version 2.x you use the `JobHost` object.

The chaining Durable Functions sample is available in a WebJobs SDK 2.x version: download or clone the [Durable Functions repository](https://github.com/azure/azure-functions-durable-extension/), and go to the *samples\\webjobssdk\\chaining* folder.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você está familiarizado com os conceitos básicos do SDK do WebJobs, desenvolvimento de bibliotecas de classe C# para funções do Azure Functions e as funções duráveis. Se você precisar obter uma introdução a esses tópicos, consulte os seguintes recursos:

* [Introdução ao WebJobs SDK](../../app-service/webjobs-sdk-get-started.md)
* [Criar sua primeira função usando o Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Funções duráveis](durable-functions-sequence.md)

Para completar as etapas neste artigo:

* [Install Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/) with the **Azure development** workload.

  If you already have Visual Studio, but don't have that workload, add the workload by selecting **Tools** > **Get Tools and Features**.

  (Você pode usar o [código do Visual Studio](https://code.visualstudio.com/) em vez disso, mas algumas das instruções são específicas para o Visual Studio Code.)

* Instale e execute o [Emulador de Armazenamento do Azure](../../storage/common/storage-use-emulator.md) 5.2 ou posterior. Uma alternativa é atualizar o arquivo *App. config* com uma cadeia de caracteres de conexão de armazenamento do Microsoft Azure.

## <a name="webjobs-sdk-versions"></a>Versões do SDK do WebJobs

Este artigo explica como desenvolver um projeto de 2. x do SDK do WebJobs (equivalente a versão do Azure Functions 1. x). Para obter informações sobre versão 3. x, consulte [SDK do WebJobs 3. x](#webjobs-sdk-3x) posteriormente neste artigo.

## <a name="create-a-console-app"></a>Criar um aplicativo de console

To run Durable Functions as WebJobs, you must first create a console app. Um projeto do SDK do WebJobs é apenas um projeto de aplicativo de console com os pacotes do NuGet apropriados instalados.

In the Visual Studio **New Project** dialog box, select **Windows Classic Desktop** > **Console App (.NET Framework)** . No arquivo de projeto, o `TargetFrameworkVersion` deve ser `v4.6.1`.

Visual Studio also has a WebJob project template, which you can use by selecting **Cloud** > **Azure WebJob (.NET Framework)** . Este modelo instala muitos pacotes, alguns dos quais você talvez não precise.

## <a name="install-nuget-packages"></a>Instalar os pacotes NuGet

Você precisará dos pacotes do NuGet para o SDK do WebJobs, associações de núcleo, a estrutura do registro em log e a extensão de tarefa durável. Here are **Package Manager Console** commands for those packages, with the latest stable version numbers as of the date this article was written:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

Você também precisa de provedores de log. The following commands install the Azure Application Insights provider and the `ConfigurationManager`. O `ConfigurationManager` permite que você obtenha a chave de instrumentação do Application Insights de configurações do aplicativo.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

O comando a seguir instala o provedor de console:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>Código de JobHost

Having created the console app and installed the NuGet packages you need, you're ready to use Durable Functions. You do so by using JobHost code.

Para usar a extensão de Funções Duráveis, chame `UseDurableTask` no `JobHostConfiguration` do objeto em seu `Main` método:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Para obter uma lista de propriedades que podem ser definidas no `DurableTaskExtension` de objeto, consulte [host.json](../functions-host-json.md#durabletask).

O `Main` método também é o local para configurar os provedores de log. The following example configures the console and Application Insights providers.

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

Durable Functions in the context of WebJobs differs somewhat from Durable Functions in the context of Azure Functions. It's important to be aware of the differences as you write your code.

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

In a WebJobs SDK project, you can call methods on the orchestration client object, instead of by sending HTTP requests. Os métodos a seguir correspondem a três tarefas que você pode fazer com a API de gerenciamento HTTP:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

The orchestration client function in the sample project starts the orchestrator function, and then goes into a loop that calls `GetStatusAsync` every 2 seconds:

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

You've got Durable Functions set up to run as a WebJob, and you now have an understanding of how this will differ from running Durable Functions as standalone Azure Functions. At this point, seeing it work in a sample might be helpful.

Esta seção fornece uma visão geral de como executar o [projeto de exemplo](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Para obter instruções detalhadas que explicam como executar um projeto do SDK do WebJobs localmente e implantá-lo em um Webjob do Azure, consulte [começar com o SDK do WebJobs](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Executar localmente

1. Verifique se o Emulador de armazenamento está em execução (consulte [pré-requisitos](#prerequisites)).

1. If you want to see logs in Application Insights when you run the project locally:

    a. Create an Application Insights resource, and use the **General** app type for it.

    b. Salvar a chave de instrumentação no arquivo *App.config*.

1. Execute o projeto.

### <a name="run-in-azure"></a>Executar no Azure

1. Criar um aplicativo Web e uma conta de armazenamento.

1. In the web app, save the storage connection string in an app setting named `AzureWebJobsStorage`.

1. Create an Application Insights resource, and use the **General** app type for it.

1. Save the instrumentation key in an app setting named `APPINSIGHTS_INSTRUMENTATIONKEY`.

1. Implantar como um WebJob.

## <a name="webjobs-sdk-3x"></a>SDK de WebJobs 3.x

This article explains how to develop a WebJobs SDK 2.x project. If you're developing a [WebJobs SDK 3.x](../../app-service/webjobs-sdk-get-started.md) project, this section helps you understand the differences.

The main change introduced is the use of .NET Core instead of .NET Framework. To create a WebJobs SDK 3.x project, the instructions are the same, with these exceptions:

1. Criar um aplicativo de console do aplicativo .NET Core. In the Visual Studio **New Project** dialog box, select  **.NET Core** > **Console App (.NET Core)** . O arquivo de projeto especifica que `TargetFramework` é `netcoreapp2.x`.

1. Choose the release version WebJobs SDK 3.x of the following packages:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Set the storage connection string and the Application Insights instrumentation key in an *appsettings.json* file, by using the .NET Core configuration framework. Aqui está um exemplo:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Change the `Main` method code to do this. Aqui está um exemplo:

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

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre o SDK do WebJobs, consulte [como usar o SDK do WebJobs](../../app-service/webjobs-sdk-how-to.md).
