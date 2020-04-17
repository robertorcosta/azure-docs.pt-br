---
title: Referência de API do Azure Application Insights Agent
description: Referência de API do Agente de Insights de Aplicativos. Ativar-aplicativoInsightsMonitoring. Monitore o desempenho do site sem reimplantar o site. Funciona com ASP.NET aplicativos web hospedados no local, em VMs ou no Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: f5d66c5c21f7491e3bdc6bb70f693c3b98bf62dd
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536736"
---
# <a name="application-insights-agent-api-enable-applicationinsightsmonitoring"></a>API do Agente de Insights de Aplicativos: Enable-ApplicationInsightsMonitoring

Este artigo descreve um cmdlet que é um membro do [módulo Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Descrição

Permite o monitoramento de anexar sem código de aplicativos IIS em um computador de destino.

Este cmdlet modificará o aplicativo IISHost.config e definirá algumas chaves de registro.
Ele também criará um arquivo applicationinsights.ikey.config, que define a chave de instrumentação usada por cada aplicativo.
O IIS carregará o RedfieldModule na inicialização, que injetará o Application Insights SDK em aplicativos à medida que os aplicativos começarem.
Reinicie o IIS para que suas alterações surtam efeito.

Depois de ativar o monitoramento, recomendamos que você use [métricas ao vivo](live-stream.md) para verificar rapidamente se seu aplicativo está nos enviando telemetria.


> [!NOTE] 
> - Para começar, você precisa de uma chave de instrumentação. Para obter mais informações, consulte [Criar um recurso](create-new-resource.md#copy-the-instrumentation-key).
> - Este cmdlet exige que você revise e aceite nossa declaração de licença e privacidade.

> [!IMPORTANT] 
> Este cmdlet requer uma sessão PowerShell com permissões de admin e uma política de execução elevada. Para obter mais informações, consulte [Executar o PowerShell como administrador com uma política de execução elevada](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).

## <a name="examples"></a>Exemplos

### <a name="example-with-a-single-instrumentation-key"></a>Exemplo com uma única chave de instrumentação
Neste exemplo, todos os aplicativos no computador atual recebem uma única chave de instrumentação.

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
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
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
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** é um regex C# necessário do computador ou nome VM.
    - '.*' vai corresponder a todos
    - 'ComputerName' corresponderá apenas aos computadores com o nome exato especificado.
- **AppFilter** é um regex C# necessário do nome do site do IIS. Você pode obter uma lista de sites em seu servidor executando o comando [get-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite).
    - '.*' vai corresponder a todos
    - 'SiteName' corresponderá apenas ao Site IIS com o nome exato especificado.
- **O InstrumentationKey** é necessário para permitir o monitoramento de aplicativos que correspondam aos dois filtros anteriores.
    - Deixe esse valor nulo se quiser definir regras para excluir o monitoramento.


### <a name="-enableinstrumentationengine"></a>-HabilitarinstrumentationEngine
**Opcional.** Use este interruptor para permitir que o mecanismo de instrumentação colete eventos e mensagens sobre o que está acontecendo durante a execução de um processo gerenciado. Esses eventos e mensagens incluem códigos de resultado de dependência, verbos HTTP e texto de comando SQL.

O motor de instrumentação adiciona sobrecarga e está desligado por padrão.

### <a name="-acceptlicense"></a>-Licença de Aceitação
**Opcional.** Use este switch para aceitar a declaração de licença e privacidade em instalações sem cabeça.

### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Quando você tem um cluster de servidores web, você pode estar usando uma [configuração compartilhada](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
O HttpModule não pode ser injetado nesta configuração compartilhada.
Este script falhará com a mensagem de que são necessárias etapas extras de instalação.
Use este interruptor para ignorar essa verificação e continuar instalando pré-requisitos. Para obter mais informações, consulte [a configuração compartilhada de conflito com-iis](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Use este interruptor para exibir registros detalhados.

### <a name="-whatif"></a>-WhatIf 
**Parâmetro comum.** Use este switch para testar e validar seus parâmetros de entrada sem realmente ativar o monitoramento.

## <a name="output"></a>Saída


#### <a name="example-output-from-a-successful-enablement"></a>Exemplo de saída de uma habilitação bem-sucedida

```
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
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
- [Adicione o Application Insights SDK ao seu código para](../../azure-monitor/app/asp-net.md) que você possa inserir chamadas de rastreamento e registro.
 
 Faça mais com o Agente de Insights de Aplicativos:
 - Use nosso guia para [solucionar problemas](status-monitor-v2-troubleshoot.md) do Agente de Insights de Aplicativos.
 - [Obtenha a configuração](status-monitor-v2-api-get-config.md) para confirmar que suas configurações foram gravadas corretamente.
 - [Obtenha o status](status-monitor-v2-api-get-status.md) para inspecionar o monitoramento.
