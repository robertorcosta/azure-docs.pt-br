---
title: Referência de API do Azure Application Insights Agent
description: Referência de API do Agente de Insights de Aplicativos. Get-ApplicationInsightsMonitoringConfig. Monitore o desempenho do site sem reimplantar o site. Funciona com ASP.NET aplicativos web hospedados no local, em VMs ou no Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 0cb5991b381acc9a0ec36113d0e15a7731b5074d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537484"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringconfig"></a>API do agente de insights de aplicativos: Get-ApplicationInsightsMonitoringConfig

Este artigo descreve um cmdlet que é um membro do [módulo Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Descrição

Obtém o arquivo de configuração e imprime os valores para o console.

> [!IMPORTANT] 
> Este cmdlet requer uma sessão PowerShell com permissões de admin.

## <a name="examples"></a>Exemplos

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>Parâmetros

Não são necessários parâmetros.

## <a name="output"></a>Saída


#### <a name="example-output-from-reading-the-config-file"></a>Saída de exemplo da leitura do arquivo de configuração

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>Próximas etapas

  Exiba sua telemetria:
 - [Explorar métricas](../../azure-monitor/platform/metrics-charts.md) para monitorar o desempenho e o uso.
- [Pesquise eventos e registros](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
- Use [análises](../../azure-monitor/app/analytics.md) para consultas mais avançadas.
- [Criar painéis](../../azure-monitor/app/overview-dashboard.md).
 
 Adicione mais telemetria:
 - [Crie testes na Web](monitor-web-app-availability.md) para ter a certeza de que seu site continua ativo.
- [Adicione telemetria do cliente web](../../azure-monitor/app/javascript.md) para ver exceções do código da página da Web e para ativar chamadas de rastreamento.
- [Adicione o Application Insights SDK ao seu código para](../../azure-monitor/app/asp-net.md) que você possa inserir chamadas de rastreamento e registro.
 
 Faça mais com o Agente de Insights de Aplicativos:
 - Use nosso guia para [solucionar problemas](status-monitor-v2-troubleshoot.md) do Agente de Insights de Aplicativos.
 - Faça alterações na configuração usando o conjunto de cmdlet [de configuração.](status-monitor-v2-api-set-config.md)
