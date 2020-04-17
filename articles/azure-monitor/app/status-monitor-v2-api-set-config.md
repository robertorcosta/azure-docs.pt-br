---
title: Referência de API do Azure Application Insights Agent
description: Referência de API do Agente de Insights de Aplicativos. Set-ApplicationInsightsMonitoringConfig. Monitore o desempenho do site sem reimplantar o site. Funciona com ASP.NET aplicativos web hospedados no local, em VMs ou no Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: c47b9b5f297fa62c474e6c29737d6d11b887130d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537467"
---
# <a name="application-insights-agent-api-set-applicationinsightsmonitoringconfig"></a>API do agente de insights de aplicativos: Set-ApplicationInsightsMonitoringConfig

Este documento descreve um cmdlet que é um membro do [módulo Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Descrição

Define o arquivo de configuração sem fazer uma reinstalação completa.
Reinicie o IIS para que suas alterações surtam efeito.

> [!IMPORTANT] 
> Este cmdlet requer uma sessão PowerShell com permissões de admin.


## <a name="examples"></a>Exemplos

### <a name="example-with-a-single-instrumentation-key"></a>Exemplo com uma única chave de instrumentação
Neste exemplo, todos os aplicativos no computador atual serão atribuídos a uma única chave de instrumentação.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Exemplo com um mapa-chave de instrumentação
Neste exemplo:
- `MachineFilter`corresponde ao computador atual `'.*'` usando o curinga.
- `AppFilter='WebAppExclude'`fornece `null` uma chave de instrumentação. O aplicativo especificado não será instrumentado.
- `AppFilter='WebAppOne'`atribui ao aplicativo especificado uma chave de instrumentação única.
- `AppFilter='WebAppTwo'`atribui ao aplicativo especificado uma chave de instrumentação única.
- Finalmente, `AppFilter` também `'.*'` usa o curinga para combinar com todos os aplicativos da Web que não são compatíveis com as regras anteriores e atribuem uma chave de instrumentação padrão.
- Espaços são adicionados para legibilidade.

```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
       @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
          @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
          @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
          @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})
```

## <a name="parameters"></a>Parâmetros

### <a name="-instrumentationkey"></a>-Chave de Instrumentação
**Necessário.** Use este parâmetro para fornecer uma única chave de instrumentação para uso por todos os aplicativos no computador de destino.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Necessário.** Use este parâmetro para fornecer várias teclas de instrumentação e um mapeamento das teclas de instrumentação usadas por cada aplicativo.
Você pode criar um único script `MachineFilter`de instalação para vários computadores definindo .

> [!IMPORTANT]
> Os aplicativos corresponderão às regras na ordem de que as regras sejam fornecidas. Então você deve especificar as regras mais específicas primeiro e as regras mais genéricas por último.

#### <a name="schema"></a>Esquema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** é um regex C# necessário do computador ou nome VM.
    - '.*' vai corresponder a todos
    - 'ComputerName' corresponderá apenas computadores com o nome especificado.
- **AppFilter** é um regex C# necessário do computador ou nome VM.
    - '.*' vai corresponder a todos
    - 'ApplicationName' corresponderá apenas aos aplicativos IIS com o nome especificado.
- **O InstrumentationKey** é necessário para habilitar o monitoramento dos aplicativos que correspondem aos dois filtros anteriores.
    - Deixe esse valor nulo se quiser definir regras para excluir o monitoramento.


### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Use este interruptor para exibir registros detalhados.


## <a name="output"></a>Saída

Por padrão, nenhuma saída.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Exemplo de saída verbose a partir da configuração do arquivo de configuração via -InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Exemplo de saída verbose a partir da configuração do arquivo de configuração via -InstrumentationKeyMap

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="next-steps"></a>Próximas etapas

  Exiba sua telemetria:
 - [Explorar métricas](../../azure-monitor/platform/metrics-charts.md) para monitorar o desempenho e o uso.
- [Pesquise eventos e registros](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
- [Use analytics](../../azure-monitor/app/analytics.md) para consultas mais avançadas.
- [Criar painéis](../../azure-monitor/app/overview-dashboard.md).
 
 Adicione mais telemetria:
 - [Crie testes na Web](monitor-web-app-availability.md) para ter a certeza de que seu site continua ativo.
- [Adicione telemetria do cliente web](../../azure-monitor/app/javascript.md) para ver exceções do código da página da Web e para ativar chamadas de rastreamento.
- [Adicione o Application Insights SDK ao seu código para](../../azure-monitor/app/asp-net.md) que você possa inserir chamadas de rastreamento e registro
 
 Faça mais com o Agente de Insights de Aplicativos:
 - Use nosso guia para [solucionar problemas](status-monitor-v2-troubleshoot.md) do Agente de Insights de Aplicativos.
 - [Obtenha a configuração](status-monitor-v2-api-get-config.md) para confirmar que suas configurações foram gravadas corretamente.
 - [Obtenha o status](status-monitor-v2-api-get-status.md) para inspecionar o monitoramento.
