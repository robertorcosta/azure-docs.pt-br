---
title: Visão geral do Azure Application Insights Agent | Microsoft Docs
description: Uma visão geral do Application Insights Agent. Monitore o desempenho do site sem reimplantar o site. Funciona com ASP.NET aplicativos web hospedados no local, em VMs ou no Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 4042fc05f278915fe72bf8fc4e6afd69a5bbb4b9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537416"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Implantar o Azure Monitor Application Insights Agent para servidores locais

> [!IMPORTANT]
> Essa orientação é recomendada para implantações em nuvem on-premises e não-Azure do Application Insights Agent. Aqui está a abordagem recomendada para [implantações de conjuntos de máquinas virtuais e máquinas virtuais do Azure.](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps)

Application Insights Agent (anteriormente chamado de Monitor de Status V2) é um módulo PowerShell publicado na [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Ele substitui [o Monitor de Status](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
A telemetria é enviada para o portal Azure, onde você pode [monitorar](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) seu aplicativo.

> [!NOTE]
> O módulo só suporta atualmente instrumentação sem código de aplicativos web .NET hospedados com IIS. Use um SDK para instrumentar aplicações ASP.NET Core, Java e Node.js.

## <a name="powershell-gallery"></a>Galeria do PowerShell

O Agente de Insights https://www.powershellgallery.com/packages/Az.ApplicationMonitordo Aplicativo está localizado aqui: .

![Galeria do PowerShell](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Instruções
- Veja as [instruções de início](status-monitor-v2-get-started.md) para começar com amostras de código concisos.
- Veja as [instruções detalhadas](status-monitor-v2-detailed-instructions.md) para um mergulho profundo sobre como começar.

## <a name="powershell-api-reference"></a>Referência da API powershell
- [Desativar-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Mecanismo de desativação de instrumentação](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Aplicativo-aplicativoInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Mecanismo de ativação de instrumentação](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)
- [Start-ApplicationInsightsMonitoringTrace](status-monitor-v2-api-start-trace.md)

## <a name="troubleshooting"></a>Solução de problemas
- [Solução de problemas](status-monitor-v2-troubleshoot.md)
- [Problemas conhecidos](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Perguntas frequentes

- O Application Insights Agent suporta instalações proxy?

  *Sim, eu sei.* Existem várias maneiras de baixar o Application Insights Agent. Se o seu computador tiver acesso à internet, você `-Proxy` pode acessar a Galeria PowerShell usando parâmetros.
Você também pode baixar manualmente o módulo e instalá-lo no computador ou usá-lo diretamente.
Cada uma dessas opções está descrita nas [instruções detalhadas.](status-monitor-v2-detailed-instructions.md)

- O Monitor de Status v2 suporta ASP.NET aplicativos Core?

  *No*. Para obter instruções para permitir o monitoramento de aplicativos ASP.NET Core, consulte [Insights de aplicativos para ASP.NET principais](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core). Não há necessidade de instalar o StatusMonitor para um aplicativo ASP.NET Core. Isso é verdade mesmo se ASP.NET aplicativo Core estiver hospedado no IIS.

- Como posso verificar se a habilitação foi bem sucedida?

  - O [cmdlet Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md) pode ser usado para verificar se a habilitação foi bem sucedida.
  - Recomendamos que você use [métricas ao vivo](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) para determinar rapidamente se seu aplicativo está enviando telemetria.

  - Você também pode usar [o Log Analytics](../log-query/get-started-portal.md) para listar todas as funções de nuvem que atualmente enviam telemetria:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Próximas etapas

Exiba sua telemetria:

* [Explorar métricas](../../azure-monitor/platform/metrics-charts.md) para monitorar o desempenho e o uso.
* [Pesquise eventos e registros](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
* [Use analytics](../../azure-monitor/app/analytics.md) para consultas mais avançadas.
* [Criar painéis](../../azure-monitor/app/overview-dashboard.md).

Adicione mais telemetria:

* [Crie testes na Web](monitor-web-app-availability.md) para ter a certeza de que seu site continua ativo.
* [Adicione telemetria do cliente web](../../azure-monitor/app/javascript.md) para ver exceções do código da página da Web e para ativar chamadas de rastreamento.
* [Adicione o Application Insights SDK ao seu código para](../../azure-monitor/app/asp-net.md) que você possa inserir chamadas de rastreamento e registro.

