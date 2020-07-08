---
title: Visão geral do agente do insights Aplicativo Azure | Microsoft Docs
description: Uma visão geral do Application Insights Agent. Monitore o desempenho do site sem reimplantar o site. Funciona com aplicativos Web ASP.NET hospedados localmente, em VMs ou no Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 626a8c96fdc247db174e2cbcd2e5c99cec43d2ea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81770957"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Implantar Azure Monitor agente de Application Insights para servidores locais

> [!IMPORTANT]
> Estas diretrizes são recomendadas para implantações de nuvem locais e não Azure do Application Insights Agent. Aqui está a abordagem recomendada para [implantações de máquina virtual e conjunto de dimensionamento de máquinas virtuais do Azure](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps).

Application Insights agente (anteriormente denominado Status Monitor v2) é um módulo do PowerShell publicado no [Galeria do PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Ele substitui [status monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
A telemetria é enviada para a portal do Azure, onde você pode [monitorar](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) seu aplicativo.

> [!NOTE]
> Atualmente, o módulo oferece suporte à instrumentação sem código de aplicativos Web .NET hospedados com o IIS. Use um SDK para instrumentar aplicativos ASP.NET Core, Java e Node.js.

## <a name="powershell-gallery"></a>Galeria do PowerShell

Application Insights agente está localizado aqui: https://www.powershellgallery.com/packages/Az.ApplicationMonitor .

![Galeria do PowerShell](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Instruções
- Consulte as [instruções de introdução](status-monitor-v2-get-started.md) para obter um início com exemplos de código conciso.
- Consulte as [instruções detalhadas](status-monitor-v2-detailed-instructions.md) para obter um aprofundamento sobre como começar.

## <a name="powershell-api-reference"></a>Referência da API do PowerShell
- [Desabilitar-ApplicationInsightsMonitoring](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#disable-applicationinsightsmonitoring)
- [Desabilitar-InstrumentationEngine](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#disable-instrumentationengine)
- [Habilitar-ApplicationInsightsMonitoring](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#enable-applicationinsightsmonitoring)
- [Habilitar-InstrumentationEngine](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#enable-instrumentationengine)
- [Get-ApplicationInsightsMonitoringConfig](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringconfig)
- [Get-ApplicationInsightsMonitoringStatus](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringstatus)
- [Set-ApplicationInsightsMonitoringConfig](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#set-applicationinsightsmonitoringconfig)
- [Start-ApplicationInsightsMonitoringTrace](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#start-applicationinsightsmonitoringtrace)

## <a name="troubleshooting"></a>Solução de problemas
- [Solução de problemas](status-monitor-v2-troubleshoot.md)
- [Problemas conhecidos](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Perguntas frequentes

- O agente Application Insights dá suporte a instalações de proxy?

  *Sim*. Há várias maneiras de baixar o agente de Application Insights. Se o seu computador tiver acesso à Internet, você poderá integrar ao Galeria do PowerShell usando `-Proxy` parâmetros.
Você também pode baixar manualmente o módulo e instalá-lo no seu computador ou usá-lo diretamente.
Cada uma dessas opções é descrita nas [instruções detalhadas](status-monitor-v2-detailed-instructions.md).

- Status Monitor v2 oferece suporte a aplicativos ASP.NET Core?

  *Não*. Para obter instruções para habilitar o monitoramento de aplicativos de ASP.NET Core, consulte [Application insights para aplicativos de ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core). Não é necessário instalar o StatusMonitor para um aplicativo ASP.NET Core. Isso é verdadeiro mesmo se ASP.NET Core aplicativo estiver hospedado no IIS.

- Como fazer verificar se a habilitação foi bem-sucedida?

  - O cmdlet [Get-ApplicationInsightsMonitoringStatus](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringstatus) pode ser usado para verificar se a habilitação foi bem-sucedida.
  - Recomendamos que você use [métricas em tempo real](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) para determinar rapidamente se seu aplicativo está enviando telemetria.

  - Você também pode usar [log Analytics](../log-query/get-started-portal.md) para listar todas as funções de nuvem que estão enviando telemetria no momento:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Próximas etapas

Exiba sua telemetria:

* [Explore as métricas](../../azure-monitor/platform/metrics-charts.md) para monitorar o desempenho e o uso.
* [Pesquise eventos e logs](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
* [Use a análise](../../azure-monitor/app/analytics.md) para consultas mais avançadas.
* [Crie painéis](../../azure-monitor/app/overview-dashboard.md).

Adicione mais telemetria:

* [Crie testes na Web](monitor-web-app-availability.md) para ter a certeza de que seu site continua ativo.
* [Adicione telemetria de cliente Web](../../azure-monitor/app/javascript.md) para ver exceções do código de página da Web e para habilitar chamadas de rastreamento.
* [Adicione o SDK do Application insights ao seu código](../../azure-monitor/app/asp-net.md) para que você possa inserir chamadas de rastreamento e log.

