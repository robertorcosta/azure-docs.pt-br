---
title: Referência de API do Azure Application Insights Agent
description: Referência de API do Agente de Insights de Aplicativos. Start-Trace. Colete registros de ETW do Status Monitor e do Application Insights SDK.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b9680101f1a22dd6d9c1617c8afc13a10ad1c594
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671215"
---
# <a name="application-insights-agent-api-start-applicationinsightsmonitoringtrace"></a>API do agente de insights de aplicativos: Start-ApplicationInsightsMonitoringTrace

Este artigo descreve um cmdlet que é um membro do [módulo Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Descrição

Coleta [eventos ETW](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) do tempo de execução de attach sem código. Este cmdlet é uma alternativa para executar [o PerfView](https://github.com/microsoft/perfview).

Os eventos coletados serão impressos no console em tempo real e salvos em um arquivo ETL. O arquivo ETL de saída pode ser aberto pelo [PerfView](https://github.com/microsoft/perfview) para uma investigação mais aprofundada.

Este cmdlet será executado até atingir a duração do tempo (padrão`Ctrl + C`de 5 minutos) ou é parado manualmente ().

> [!IMPORTANT] 
> Este cmdlet requer uma sessão PowerShell com permissões de admin.

## <a name="examples"></a>Exemplos

### <a name="how-to-collect-events"></a>Como coletar eventos

Normalmente pedimos que você colete eventos para investigar por que sua aplicação não está sendo instrumentada.

O tempo de execução de attach sem código emite eventos DeTW quando o IIS for iniciado e quando a aplicação for iniciada.

Para coletar esses eventos:
1. Em um console cmd com privilégios `iisreset /stop` de admin, execute Para desativar o IIS e todos os aplicativos web.
2. Execute este cmdlet
3. Em um console cmd com privilégios `iisreset /start` de admin, execute Para iniciar o IIS.
4. Tente navegar para o seu aplicativo.
5. Depois que seu aplicativo terminar de carregar,`Ctrl + C`você pode pará-lo manualmente ou esperar pelo intervalo.

### <a name="what-events-to-collect"></a>Quais eventos coletar

Você tem três opções ao coletar eventos:
1. Use o `-CollectSdkEvents` switch para coletar eventos emitidos do Application Insights SDK.
2. Use o `-CollectRedfieldEvents` switch para coletar eventos emitidos pelo Monitor de Status e pelo Redfield Runtime. Esses registros são úteis ao diagnosticar iIS e inicialização de aplicativos.
3. Use ambos os interruptores para coletar os dois tipos de eventos.
4. Por padrão, se nenhum switch for especificado, ambos os tipos de evento serão coletados.


## <a name="parameters"></a>Parâmetros

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Opcional.** Use este parâmetro para definir quanto tempo este script deve coletar eventos. O padrão é de 5 minutos.

### <a name="-logdirectory"></a>-LogDirectory
**Opcional.** Use este switch para definir o diretório de saída do arquivo ETL. Por padrão, esse arquivo será criado no diretório Módulos PowerShell. O caminho completo será exibido durante a execução do script.


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Opcional.** Use este switch para coletar eventos SDK do Application Insights.

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Opcional.** Use este switch para coletar eventos do Monitor de Status e do tempo de execução redfield.

### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Use este interruptor para produzir logs detalhados de saída.



## <a name="output"></a>Saída


### <a name="example-of-application-startup-logs"></a>Exemplo de logs de inicialização de aplicativos
```
PS C:\Windows\system32> Start-ApplicationInsightsMonitoringTrace -ColectRedfieldEvents
Starting...
Log File: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\logs\20190627_144217_ApplicationInsights_ETW_Trace.etl
Tracing enabled, waiting for events.
Tracing will timeout in 5 minutes. Press CTRL+C to cancel.

2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 70 ms
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Current assembly 'Microsoft.ApplicationInsights.RedfieldIISModule, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3' location 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Matched filter '.*'~'STATUSMONITORTE', '.*'~'DemoWithSql'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Lightup assembly calculated path: 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Loaded applicationInsights.config from assembly's resource Microsoft.ApplicationInsights.Redfield.Lightup, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3/Microsoft.ApplicationInsights.Redfield.Lightup.ApplicationInsights-recommended.config
2:42:34 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Successfully attached ApplicationInsights SDK
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.LoadLightupAssemblyAndGetLightupHttpModuleClass, success, 2687 ms
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:34 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 3288 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 0 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 0 ms
Timeout Reached. Stopping...
```


## <a name="next-steps"></a>Próximas etapas

Solução adicional de problemas:

- Revise as etapas adicionais de solução de problemas aqui:https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- Revise a [referência da API](status-monitor-v2-overview.md#powershell-api-reference) para saber sobre parâmetros que você pode ter perdido.
- Se você precisar de ajuda adicional, você pode entrar em contato conosco no [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).



 Faça mais com o Agente de Insights de Aplicativos:
 - Use nosso guia para [solucionar problemas](status-monitor-v2-troubleshoot.md) do Agente de Insights de Aplicativos.
 - [Obtenha a configuração](status-monitor-v2-api-get-config.md) para confirmar que suas configurações foram gravadas corretamente.
 - [Obtenha o status](status-monitor-v2-api-get-status.md) para inspecionar o monitoramento.
