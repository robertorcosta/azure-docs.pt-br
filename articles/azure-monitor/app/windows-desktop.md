---
title: Monitorando uso e desempenho de aplicativos de área de trabalho do Windows
description: Analise o uso e o desempenho de seu aplicativo da área de trabalho do Windows com o Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/29/2019
ms.openlocfilehash: a9dfc32a0f33db5639d5f74667a90a248dc358a1
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73052467"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>Monitorando uso e desempenho de aplicativos de área de trabalho clássica do Windows

Aplicativos hospedados localmente, no Azure e em outras nuvens podem tirar proveito do Application Insights. A única limitação é a necessidade de [permitir a comunicação](../../azure-monitor/app/ip-addresses.md) para o serviço do Application Insights. Para monitorar os aplicativos da Plataforma Universal do Windows (UWP), recomendamos o [Visual Studio App Center](../../azure-monitor/learn/mobile-center-quickstart.md).

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>Para enviar telemetria ao Application Insights a partir de um aplicativo do Windows clássico
1. No [portal do Azure](https://portal.azure.com), [crie um recurso Application Insights](../../azure-monitor/app/create-new-resource.md ). Para o tipo de aplicativo, escolha o aplicativo ASP.NET.
2. Faça uma cópia da chave de instrumentação. Localize a chave no menu suspenso Essentials do novo recurso que você acabou de criar. 
3. No Visual Studio, edite os pacotes NuGet do seu projeto de aplicativo e adicione Microsoft.ApplicationInsights.WindowsServer. (Ou escolha Microsoft.ApplicationInsights se você quiser apenas a API básica, sem os módulos de coleta da telemetria padrão.)
4. Defina a chave de instrumentação no seu código:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "` *sua chave* `";`
   
    ou em ApplicationInsights.config (se tiver instalado um dos pacotes de telemetria padrão):
   
    `<InstrumentationKey>`*sua chave*`</InstrumentationKey>` 
   
    Se você usar ApplicationInsights.config, verifique se suas propriedades no Gerenciador de Soluções estão definidas como **Ação de Compilação = Conteúdo, Copiar para Diretório de Saída = Copiar**.
5. [Use a API](../../azure-monitor/app/api-custom-events-metrics.md) para enviar telemetria.
6. Execute seu aplicativo e veja a telemetria no recurso que você criou na portal do Azure.

## <a name="telemetry"></a>Código de exemplo
```csharp
using Microsoft.ApplicationInsights;

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(System.ComponentModel.CancelEventArgs e)
        {
            e.Cancel = true;

            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="override-storage-of-computer-name"></a>Substituir o armazenamento do nome do computador

Por padrão, esse SDK coletará e armazenará o nome do computador do sistema que emite telemetria. Para substituir a coleção, você precisa usar um inicializador de telemetria:

**Escreva personalizada telemetryinitializer personalizado como abaixo.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here, you can pass an empty string if needed.
                  telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```
Crie uma instância do inicializador no método `Program.cs` `Main()` abaixo de definir a chave de instrumentação:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

   static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
        }
```

## <a name="next-steps"></a>Próximos passos
* [Criar um painel](../../azure-monitor/app/overview-dashboard.md)
* [Pesquisa de Diagnóstico](../../azure-monitor/app/diagnostic-search.md)
* [Explorar métricas](../../azure-monitor/app/metrics-explorer.md)
* [Escrever consultas do Analytics](../../azure-monitor/app/analytics.md)

