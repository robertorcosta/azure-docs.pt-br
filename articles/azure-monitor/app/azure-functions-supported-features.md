---
title: Insights de Aplicativo Azure-Azure Functions recursos com suporte
description: Recursos suportados do Application Insights para funções do Azure
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: reference
author: TimothyMothra
ms.author: tilee
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.openlocfilehash: ce8fcc827afdf0102a459f2c6b58db45eff72fa0
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77467380"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Recursos suportados do Application Insights para o Azure Functions

O Azure Functions oferece [integração interna](../../azure-functions/functions-monitoring.md) com o Application Insights, que está disponível por meio da Interface do ILogger. Abaixo está a lista de recursos atualmente suportados. Examine o guia do Azure Functions para [Introdução ao](../../azure-functions/functions-monitoring.md#enable-application-insights-integration).

Para obter mais informações sobre versões de tempo de execução, consulte [aqui](../../azure-functions/functions-versions.md).

## <a name="supported-features"></a>Recursos compatíveis

| Funções do Azure                       | V1                | V2 & v3   | 
|-----------------------------------    |---------------    |------------------ |
| **SDK .NET do Application Insights**   | **2.5.0**       | **2.9.1**         |
| | | | 
| **Coleta automática de**        |                 |                   |               
| &bull; Solicitações                     | Sim             | Sim               | 
| &bull; Exceções                   | Sim             | Sim               | 
| &bull; Contadores de desempenho         | Sim             | Sim               |
| &bull; Dependências                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | Sim               | 
| &nbsp;&nbsp;&nbsp;&mdash; Barramento de serviço|                 | Sim               | 
| &nbsp;&nbsp;&nbsp;&mdash; hub de eventos  |                 | Sim               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | Sim               | 
| | | | 
| **Recursos com suporte**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | Sim             | Sim               | 
| &nbsp;&nbsp;&nbsp;&mdash; Canal de controle seguro|                 | Sim               | 
| &bull; Amostragem                     | Sim             | Sim               | 
| &bull; Pulsações                   |                 | Sim               | 
| | | | 
| **Correlação**                       |                   |                   |               
| &bull; Barramento de serviço                     |                   | Sim               | 
| &bull; hub de eventos                       |                   | Sim               | 
| | | | 
| **Configurável**                      |                   |                   |           
| &bull;Totalmente configurável.<br/>Ver [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) para obter instruções.<br/>Ver [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) para todas as opções.               |                   | Sim                   | 


## <a name="performance-counters"></a>Contadores de desempenho

A coleta automática de contadores de desempenho só funciona em máquinas Windows.


## <a name="live-metrics--secure-control-channel"></a>Live Metrics e canal de controle seguro

Os critérios de filtro personalizados especificados são enviados para o componente de Métricas em tempo real no SDK do Application Insights. Os filtros podem conter informações confidenciais, como customerIDs. Torne o canal seguro com uma chave de API secreta. Confira [Proteger o canal de controle](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel) para obter instruções.

## <a name="sampling"></a>amostragem

As funções do Azure ativam a amostragem por padrão em suas configurações. Para obter mais informações, consulte [configurar amostragem](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).

Se o seu projeto usar uma dependência no SDK do Application Insights para fazer o acompanhamento manual de telemetria, você poderá enfrentar um comportamento estranho se a configuração de amostragem for diferente da configuração de amostragem das funções. 

É recomendável usar a mesma configuração que as funções. Com o **Functions v2**, você pode obter a mesma configuração usando a injeção de dependência em seu construtor:

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;

public class Function1 
{

    private readonly TelemetryClient telemetryClient;

    public Function1(TelemetryConfiguration configuration)
    {
        this.telemetryClient = new TelemetryClient(configuration);
    }

    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger logger)
    {
        this.telemetryClient.TrackTrace("C# HTTP trigger function processed a request.");
    }
}
```
