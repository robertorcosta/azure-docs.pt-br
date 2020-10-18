---
title: Insights de Aplicativo Azure-Azure Functions recursos com suporte
description: Recursos suportados do Application Insights para funções do Azure
ms.topic: reference
author: TimothyMothra
ms.author: tilee
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 36c5fc93886327c0e3261418343d900ee66cb4eb
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168572"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Recursos suportados do Application Insights para o Azure Functions

O Azure Functions oferece [integração interna](../../azure-functions/functions-monitoring.md) com o Application Insights, que está disponível por meio da Interface do ILogger. Abaixo está a lista de recursos atualmente suportados. Examine o guia do Azure Functions para [Introdução ao](../../azure-functions/configure-monitoring.md#enable-application-insights-integration).

Para obter mais informações sobre as versões de tempo de execução do functions, consulte [aqui](../../azure-functions/functions-versions.md).

Para obter mais informações sobre versões compatíveis do Application Insights, consulte [dependências](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/).

## <a name="supported-features"></a>Recursos compatíveis

| Azure Functions                       | V1                | V2 & V3   | 
|-----------------------------------    |---------------    |------------------ |
| | | | 
| **Coleta automática de**        |                 |                   |               
| &bull; Solicitações                     | Sim             | Sim               | 
| &bull; Exceções                   | Sim             | Sim               | 
| &bull; Contadores de desempenho         | Sim             | Sim               |
| &bull; Dependências                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | Yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; Barramento de serviço|                 | Yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; hub de eventos  |                 | Yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | Yes               | 
| | | | 
| **Recursos compatíveis**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | Sim             | Sim               | 
| &nbsp;&nbsp;&nbsp;&mdash; Canal de controle seguro|                 | Yes               | 
| &bull; Amostragem                     | Sim             | Sim               | 
| &bull; Pulsações                   |                 | Yes               | 
| | | | 
| **Correlação**                       |                   |                   |               
| &bull; Barramento de serviço                     |                   | Yes               | 
| &bull; hub de eventos                       |                   | Yes               | 
| | | | 
| **Configurável**                      |                   |                   |           
| &bull;Totalmente configurável.<br/>Ver [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) para obter instruções.<br/>Consulte [ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) para todas as opções.               |                   | Yes                   | 


## <a name="performance-counters"></a>Contadores de desempenho

A coleta automática de contadores de desempenho só funciona em máquinas Windows.


## <a name="live-metrics--secure-control-channel"></a>Live Metrics e canal de controle seguro

Os critérios de filtro personalizados especificados são enviados para o componente de Métricas em tempo real no SDK do Application Insights. Os filtros podem conter informações confidenciais, como customerIDs. Torne o canal seguro com uma chave de API secreta. Confira [Proteger o canal de controle](./live-stream.md#secure-the-control-channel) para obter instruções.

## <a name="sampling"></a>amostragem

As funções do Azure ativam a amostragem por padrão em suas configurações. Para obter mais informações, consulte [configurar amostragem](../../azure-functions/configure-monitoring.md#configure-sampling).

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
