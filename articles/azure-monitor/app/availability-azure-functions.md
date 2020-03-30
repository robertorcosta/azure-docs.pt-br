---
title: Crie e execute testes personalizados de disponibilidade usando funções do Azure
description: Este doc cobrirá como criar uma função Azure com TrackAvailability() que será executada periodicamente de acordo com a configuração dada na função TimerTrigger. Os resultados deste teste serão enviados para o recurso Application Insights, onde você poderá consultar e alertar sobre os dados dos resultados de disponibilidade. Testes personalizados permitirão que você escreva testes de disponibilidade mais complexos do que é possível usando a ida e volta do portal, monitore um aplicativo dentro do seu VNET do Azure, altere o endereço do ponto final ou crie um teste de disponibilidade se ele não estiver disponível na sua região.
ms.topic: conceptual
author: morgangrobin
ms.author: mogrobin
ms.date: 11/22/2019
ms.openlocfilehash: 476d66c51c10a5fcfb3cb0319c47b3338d28812c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665792"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Crie e execute testes personalizados de disponibilidade usando funções do Azure

Este artigo abordará como criar uma função Azure com TrackAvailability() que será executada periodicamente de acordo com a configuração dada na função TimerTrigger com sua própria lógica de negócios. Os resultados deste teste serão enviados para o recurso Application Insights, onde você poderá consultar e alertar sobre os dados dos resultados de disponibilidade. Isso permite que você crie testes personalizados semelhantes ao que você pode fazer via [Monitoramento de Disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) no portal. Testes personalizados permitirão que você escreva testes de disponibilidade mais complexos do que é possível usando a ida e volta do portal, monitore um aplicativo dentro do seu VNET do Azure, altere o endereço do ponto final ou crie um teste de disponibilidade mesmo que este recurso não esteja disponível na sua região.

> [!NOTE]
> Este exemplo foi projetado exclusivamente para mostrar a mecânica de como a chamada aPI TrackAvailability() funciona dentro de uma função Azure. Não como escrever a lógica de código de teste HTTP subjacente/business que seria necessária para transformá-lo em um teste de disponibilidade totalmente funcional. Por padrão, se você passar por este exemplo, você estará criando um teste de disponibilidade que sempre gerará uma falha.

## <a name="create-timer-triggered-function"></a>Criar função acionada do temporizador

- Se você tiver um recurso de insights de aplicativo:
    - Por padrão, o Azure Functions cria um recurso application Insights, mas se você quiser usar um dos seus recursos já criados, você precisará especificar isso durante a criação.
    - Siga as instruções sobre como [criar um recurso de funções do Azure e função ativada do Temporizador](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (pare antes de limpar) com as seguintes opções.
        -  Selecione a guia **Monitorar** perto da parte superior.

            ![ Crie um aplicativo de funções do Azure com seu próprio recurso App Insights](media/availability-azure-functions/create-function-app.png)

        - Selecione a caixa de itens de entrada do Application Insights e digite ou selecione o nome do seu recurso.

            ![Selecionando o recurso de insights de aplicativos existentes](media/availability-azure-functions/app-insights-resource.png)

        - Selecione **Revisão + criar**
- Se você ainda não tiver um recurso de insights de aplicativo criado para a função acionada pelo temporizador:
    - Por padrão, quando você estiver criando o aplicativo Funções do Azure, ele criará um recurso application Insights para você.
    - Siga as instruções sobre como [criar um recurso de funções do Azure e função ativada do Temporizador](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (pare antes da limpeza).

## <a name="sample-code"></a>Código de exemplo

Copie o código abaixo no arquivo run.csx (isso substituirá o código pré-existente). Para fazer isso, entre no aplicativo Funções do Azure e selecione a função de gatilho do temporizador à esquerda.

>[!div class="mx-imgBorder"]
>![Função azure run.csx no portal Azure](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> Para o endereço endpoint `EndpointAddress= https://dc.services.visualstudio.com/v2/track`que você usaria: . A menos que seu recurso esteja localizado em uma região como o Azure Government ou o Azure China, nesse caso, consulte este artigo sobre [a substituição dos pontos finais padrão](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification) e selecione o ponto final apropriado do Canal de Telemetria para sua região.

```C#
#load "runAvailabilityTest.csx"
 
using System;
using System.Diagnostics;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
 
// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
// DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string instrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");
 
//[CONFIGURATION_REQUIRED]
// If your resource is in a region like Azure Government or Azure China, change the endpoint address accordingly.
// Visit https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification for more details.
private const string EndpointAddress = "https://dc.services.visualstudio.com/v2/track";
 
private static readonly TelemetryConfiguration telemetryConfiguration = new TelemetryConfiguration(instrumentationKey, new InMemoryChannel { EndpointAddress = EndpointAddress });
private static readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
 
public async static Task Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Entering Run at: {DateTime.Now}");
 
    if (myTimer.IsPastDue)
    {
        log.LogWarning($"[Warning]: Timer is running late! Last ran at: {myTimer.ScheduleStatus.Last}");
    }
 
    // [CONFIGURATION_REQUIRED] provide {testName} accordingly for your test function
    string testName = "AvailabilityTestFunction";
 
    // REGION_NAME is a default environment variable that comes with App Service
    string location = Environment.GetEnvironmentVariable("REGION_NAME");
 
    log.LogInformation($"Executing availability test run for {testName} at: {DateTime.Now}");
    string operationId = Guid.NewGuid().ToString("N");
 
    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = testName,
        RunLocation = location,
        Success = false
    };
 
    var stopwatch = new Stopwatch();
    stopwatch.Start();
 
    try
    {
        await RunAvailbiltyTestAsync(log);
        availability.Success = true;
    }
    catch (Exception ex)
    {
        availability.Message = ex.Message;
 
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", testName);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        telemetryClient.TrackException(exceptionTelemetry);
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;
 
        telemetryClient.TrackAvailability(availability);
        // call flush to ensure telemetry is sent
        telemetryClient.Flush();
    }
}

```

À direita, em arquivos à vista, selecione **Adicionar**. Ligue para a nova **função de arquivo.proj** com a seguinte configuração.

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.8.2" /> <!-- Ensure you’re using the latest version -->
    </ItemGroup>
</Project>

```

>[!div class="mx-imgBorder"]
>![Na seleção à direita, adicione. Nomeie a função de arquivo.proj](media/availability-azure-functions/addfile.png)

À direita, em arquivos à vista, selecione **Adicionar**. Ligue para o novo arquivo **runAvailabilityTest.csx** com a seguinte configuração.

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>Verificar a disponibilidade

Para ter certeza de que tudo está funcionando, você pode olhar para o gráfico na guia Disponibilidade do recurso Application Insights.

> [!NOTE]
> Se você implementou sua própria lógica de negócios em runAvailabilityTest.csx, então você verá resultados bem sucedidos como nas capturas de tela abaixo, se você não o fez, então você verá resultados fracassados.

>[!div class="mx-imgBorder"]
>![Guia de disponibilidade com resultados bem-sucedidos](media/availability-azure-functions/availtab.png)

Quando você configurar seu teste usando funções do Azure, você notará que, ao contrário de usar **adicionar teste** na guia Disponibilidade, o nome do seu teste não aparecerá e você não poderá interagir com ele. Os resultados são visualizados, mas você tem uma exibição sumária em vez da mesma visualização detalhada que você recebe quando cria um teste de disponibilidade através do portal.

Para ver os detalhes de transação de ponta a ponta, **selecione 'Sucesso'** ou **Falha** na broca e selecione uma amostra. Você também pode acessar os detalhes de transação de ponta a ponta selecionando um ponto de dados no gráfico.

>[!div class="mx-imgBorder"]
>![Selecione um teste de disponibilidade de amostra](media/availability-azure-functions/sample.png)

>[!div class="mx-imgBorder"]
>![Detalhes de transação de ponta a ponta](media/availability-azure-functions/end-to-end.png)

Se você executou tudo como está (sem adicionar lógica de negócios), então você verá que o teste falhou.

## <a name="query-in-logs-analytics"></a>Consulta em Logs (Analytics)

Você pode usar logs(analytics) para exibir resultados de disponibilidade, dependências e muito mais. Para saber mais sobre logs, visite [a visão geral da consulta log](../../azure-monitor/log-query/log-query-overview.md).

>[!div class="mx-imgBorder"]
>![Resultados de disponibilidade](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![Dependências](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Próximas etapas

- [Mapa do aplicativo](../../azure-monitor/app/app-map.md)
- [Diagnóstico da transação](../../azure-monitor/app/transaction-diagnostics.md)
