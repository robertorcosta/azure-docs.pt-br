---
title: Habilite o Snapshot Debugger para aplicativos .NET em malha de serviço azure, serviço de nuvem e máquinas virtuais | Microsoft Docs
description: Habilite o Snapshot Debugger para aplicativos .NET em malha de serviço azure, serviço de nuvem e máquinas virtuais
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 194a2da23c8fb405c492df8f6ee173cc97fde4ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671327"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>Habilite o Snapshot Debugger para aplicativos .NET em malha de serviço azure, serviço de nuvem e máquinas virtuais

Se o seu ASP.NET ou ASP.NET aplicativo principal for executado no Azure App Service, é altamente recomendável [ativar o Depurador de Instantâneos através da página do portal Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json). No entanto, se o aplicativo exigir uma configuração personalizada do Depurador de Instantâneos ou uma versão de visualização do núcleo .NET, essa instrução deve ser ***seguida, além*** das instruções para [habilitação através da página do portal Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json).

Se o aplicativo for executado em malha de serviço azure, serviço de nuvem, máquinas virtuais ou máquinas no local, as seguintes instruções devem ser usadas. 
    
## <a name="configure-snapshot-collection-for-aspnet-applications"></a>Configurar a coleta de instantâneo para aplicativos ASP.NET

1. [Habilite o Application Insights no seu aplicativo web](../../azure-monitor/app/asp-net.md), se você ainda não tiver feito isso.

2. Inclua o pacote do NuGet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) em seu aplicativo.

3. Se necessário, personalize a configuração do Snapshot Debugger adicionada ao [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). A configuração padrão do Snapshot Debugger está praticamente vazia e todas as configurações são opcionais. Aqui está um exemplo mostrando uma configuração equivalente à configuração padrão:

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>3</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. Os instantâneos são coletados apenas em exceções que são relatadas ao Application Insights. Em alguns casos (por exemplo, versões mais antigas da plataforma .NET), talvez seja necessário [configurar coleta de exceção](../../azure-monitor/app/asp-net-exceptions.md#exceptions) para ver exceções com instantâneos no portal.


## <a name="configure-snapshot-collection-for-applications-using-aspnet-core-20-or-above"></a>Configure a coleta de instantâneos para aplicativos usando ASP.NET Core 2.0 ou superior

1. [Habilite o Application Insights no seu aplicativo web Núcleo do ASP.NET](../../azure-monitor/app/asp-net-core.md), se você ainda não tiver feito isso.

    > [!NOTE]
    > Verifique se o seu aplicativo faz referência à versão 2.1.1 ou mais recente do pacote Microsoft.ApplicationInsights.AspNetCore.

2. Inclua o pacote do NuGet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) em seu aplicativo.

3. Modifique a classe `Startup` do seu aplicativo para adicionar e configurar o processador de telemetria do Coletor de Instantâneo.
    1. Se [microsoft.applicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet versão 1.3.5 ou superior for usada, `Startup.cs`adicione as seguintes instruções usando .

       ```csharp
            using Microsoft.ApplicationInsights.SnapshotCollector;
       ```

       Adicione o seguinte no final do método `Startup` ConfigureServices na classe em `Startup.cs`.

       ```csharp
            services.AddSnapshotCollector((configuration) => Configuration.Bind(nameof(SnapshotCollectorConfiguration), configuration));
       ```
    2. Se [microsoft.applicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet versão 1.3.4 ou abaixo for usada, `Startup.cs`adicione as seguintes instruções usando .

       ```csharp
       using Microsoft.ApplicationInsights.SnapshotCollector;
       using Microsoft.Extensions.Options;
       using Microsoft.ApplicationInsights.AspNetCore;
       using Microsoft.ApplicationInsights.Extensibility;
       ```
    
       Adicione a classe `SnapshotCollectorTelemetryProcessorFactory` a seguir para a classe `Startup`.
    
       ```csharp
       class Startup
       {
           private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
           {
               private readonly IServiceProvider _serviceProvider;
    
               public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
                   _serviceProvider = serviceProvider;
    
               public ITelemetryProcessor Create(ITelemetryProcessor next)
               {
                   var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
                   return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
               }
           }
           ...
        ```
        Adicione os serviços `SnapshotCollectorConfiguration` e `SnapshotCollectorTelemetryProcessorFactory` para o pipeline de inicialização:
    
        ```csharp
           // This method gets called by the runtime. Use this method to add services to the container.
           public void ConfigureServices(IServiceCollection services)
           {
               // Configure SnapshotCollector from application settings
               services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));
    
               // Add SnapshotCollector telemetry processor.
               services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));
    
               // TODO: Add other services your application needs here.
           }
       }
       ```

4. Se necessário, personalize a configuração do Snapshot Debugger adicionando uma seção SnapshotCollectorConfiguration to appsettings.json. Todas as configurações na configuração do Depurador de instantâneos são opcionais. Aqui está um exemplo mostrando uma configuração equivalente à configuração padrão:

   ```json
   {
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

## <a name="configure-snapshot-collection-for-other-net-applications"></a>Configurar a coleta de instantâneo para outros aplicativos .NET

1. Se seu aplicativo ainda não está instrumentado com o Application Insights, comece [habilitando o Application Insights e definindo a chave de instrumentação](../../azure-monitor/app/windows-desktop.md).

2. Adicione o pacote do NuGet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) em seu aplicativo.

3. Os instantâneos são coletados apenas em exceções que são relatadas ao Application Insights. Talvez seja necessário modificar o código para relatá-los. O código de tratamento de exceção depende da estrutura do seu aplicativo, mas há um exemplo abaixo:
    ```csharp
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```

## <a name="next-steps"></a>Próximas etapas

- Gerar tráfego para o seu aplicativo que pode desencadear uma exceção. Em seguida, aguarde de 10 a 15 minutos para que os instantâneos sejam enviados para a instância Doaplicativo Insights.
- Veja [fotos](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) no portal Azure.
- Para obter ajuda para solucionar problemas do Depurador de problemas, consulte [Solução de problemas do Depurador de instantâneos](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).
