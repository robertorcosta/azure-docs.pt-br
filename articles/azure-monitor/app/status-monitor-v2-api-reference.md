---
title: Referência de Azure Application Insights .Net Agent API
description: Referência de API do Agente de Insights de Aplicativos. Monitore o desempenho do site sem reimplantar o site. Funciona com ASP.NET aplicativos web hospedados no local, em VMs ou no Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 02762c4b3af735eb0b4c19aaf450b2b3a416a2be
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733668"
---
# <a name="azure-monitor-application-insights-agent-api-reference"></a>Referência de API do agente de insights do aplicativo do Azure Monitor

Este artigo descreve um cmdlet que é um membro do [módulo Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!NOTE] 
> - Para começar, você precisa de uma chave de instrumentação. Para obter mais informações, consulte [Criar um recurso](create-new-resource.md#copy-the-instrumentation-key).
> - Este cmdlet exige que você revise e aceite nossa declaração de licença e privacidade.

> [!IMPORTANT] 
> Este cmdlet requer uma sessão PowerShell com permissões de admin e uma política de execução elevada. Para obter mais informações, consulte [Executar o PowerShell como administrador com uma política de execução elevada](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).
> - Este cmdlet exige que você revise e aceite nossa declaração de licença e privacidade.
> - O motor de instrumentação adiciona sobrecarga adicional e está desligado por padrão.


## <a name="enable-instrumentationengine"></a>Mecanismo de ativação de instrumentação

Habilita o mecanismo de instrumentação definindo algumas teclas de registro.
Reinicie o IIS para que as alterações entrem em vigor.

O mecanismo de instrumentação pode complementar os dados coletados pelos SDKs .NET.
Ele coleta eventos e mensagens que descrevem a execução de um processo gerenciado. Esses eventos e mensagens incluem códigos de resultado de dependência, verbos HTTP e [texto de comando SQL](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query).

Habilite o mecanismo de instrumentação se:
- Você já habilitou o monitoramento com o cmdlet Enable, mas não habilitou o mecanismo de instrumentação.
- Você instrumentou manualmente seu aplicativo com os SDKs .NET e deseja coletar telemetria adicional.

### <a name="examples"></a>Exemplos

```powershell
PS C:\> Enable-InstrumentationEngine
```

### <a name="parameters"></a>Parâmetros

#### <a name="-acceptlicense"></a>-Licença de Aceitação
**Opcional.** Use este switch para aceitar a declaração de licença e privacidade em instalações sem cabeça.

#### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Use este interruptor para produzir logs detalhados de saída.

### <a name="output"></a>Saída


##### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Exemplo de saída de habilitação com sucesso do mecanismo de instrumentação

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="enable-applicationinsightsmonitoring"></a>Aplicativo-aplicativoInsightsMonitoring

Permite o monitoramento de anexar sem código de aplicativos IIS em um computador de destino.

Este cmdlet modificará o aplicativo IISHost.config e definirá algumas chaves de registro.
Ele também criará um arquivo applicationinsights.ikey.config, que define a chave de instrumentação usada por cada aplicativo.
O IIS carregará o RedfieldModule na inicialização, que injetará o Application Insights SDK em aplicativos à medida que os aplicativos começarem.
Reinicie o IIS para que suas alterações surtam efeito.

Depois de ativar o monitoramento, recomendamos que você use [métricas ao vivo](live-stream.md) para verificar rapidamente se seu aplicativo está nos enviando telemetria.

### <a name="examples"></a>Exemplos

#### <a name="example-with-a-single-instrumentation-key"></a>Exemplo com uma única chave de instrumentação
Neste exemplo, todos os aplicativos no computador atual recebem uma única chave de instrumentação.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Exemplo com um mapa-chave de instrumentação
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


### <a name="parameters"></a>Parâmetros

#### <a name="-instrumentationkey"></a>-Chave de Instrumentação
**Necessário.** Use este parâmetro para fornecer uma única chave de instrumentação para uso por todos os aplicativos no computador de destino.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Necessário.** Use este parâmetro para fornecer várias teclas de instrumentação e um mapeamento das teclas de instrumentação usadas por cada aplicativo.
Você pode criar um único script `MachineFilter`de instalação para vários computadores definindo .

> [!IMPORTANT]
> Os aplicativos corresponderão às regras na ordem de que as regras sejam fornecidas. Então você deve especificar as regras mais específicas primeiro e as regras mais genéricas por último.

##### <a name="schema"></a>Esquema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** é um regex C# necessário do computador ou nome VM.
    - '.*' vai corresponder a todos
    - 'ComputerName' corresponderá apenas aos computadores com o nome exato especificado.
- **AppFilter** é um regex C# necessário do nome do site do IIS. Você pode obter uma lista de sites em seu servidor executando o comando [get-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite).
    - '.*' vai corresponder a todos
    - 'SiteName' corresponderá apenas ao Site IIS com o nome exato especificado.
- **O InstrumentationKey** é necessário para permitir o monitoramento de aplicativos que correspondam aos dois filtros anteriores.
    - Deixe esse valor nulo se quiser definir regras para excluir o monitoramento.


#### <a name="-enableinstrumentationengine"></a>-HabilitarinstrumentationEngine
**Opcional.** Use este interruptor para permitir que o mecanismo de instrumentação colete eventos e mensagens sobre o que está acontecendo durante a execução de um processo gerenciado. Esses eventos e mensagens incluem códigos de resultado de dependência, verbos HTTP e texto de comando SQL.

O motor de instrumentação adiciona sobrecarga e está desligado por padrão.

#### <a name="-acceptlicense"></a>-Licença de Aceitação
**Opcional.** Use este switch para aceitar a declaração de licença e privacidade em instalações sem cabeça.

#### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Quando você tem um cluster de servidores web, você pode estar usando uma [configuração compartilhada](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
O HttpModule não pode ser injetado nesta configuração compartilhada.
Este script falhará com a mensagem de que são necessárias etapas extras de instalação.
Use este interruptor para ignorar essa verificação e continuar instalando pré-requisitos. Para obter mais informações, consulte [a configuração compartilhada de conflito com-iis](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

#### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Use este interruptor para exibir registros detalhados.

#### <a name="-whatif"></a>-WhatIf 
**Parâmetro comum.** Use este switch para testar e validar seus parâmetros de entrada sem realmente ativar o monitoramento.

### <a name="output"></a>Saída

#### <a name="example-output-from-a-successful-enablement"></a>Exemplo de saída de uma habilitação bem-sucedida

```powershell
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

## <a name="disable-instrumentationengine"></a>Mecanismo de desativação de instrumentação

Desativa o mecanismo de instrumentação removendo algumas teclas de registro.
Reinicie o IIS para que as alterações entrem em vigor.

### <a name="examples"></a>Exemplos

```powershell
PS C:\> Disable-InstrumentationEngine
```

### <a name="parameters"></a>Parâmetros 

#### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Use este interruptor para produzir logs detalhados de saída.

### <a name="output"></a>Saída


##### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Saída de exemplo de desativar com sucesso o mecanismo de instrumentação

```powershell
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```

## <a name="disable-applicationinsightsmonitoring"></a>Desativar-ApplicationInsightsMonitoring

Desativa o monitoramento no computador de destino.
Este cmdlet removerá edições do aplicativo IISHost.config e removerá as chaves do registro.

### <a name="examples"></a>Exemplos

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

### <a name="parameters"></a>Parâmetros 

#### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Use este interruptor para exibir registros detalhados.

### <a name="output"></a>Saída


##### <a name="example-output-from-successfully-disabling-monitoring"></a>Saída de exemplo de desativação com sucesso do monitoramento

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-00z'
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
```


## <a name="get-applicationinsightsmonitoringconfig"></a>Get-ApplicationInsightsMonitoringConfig

Obtém o arquivo de configuração e imprime os valores para o console.

### <a name="examples"></a>Exemplos

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

### <a name="parameters"></a>Parâmetros

Não são necessários parâmetros.

### <a name="output"></a>Saída


##### <a name="example-output-from-reading-the-config-file"></a>Saída de exemplo da leitura do arquivo de configuração

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="get-applicationinsightsmonitoringstatus"></a>Get-ApplicationInsightsMonitoringStatus

Este cmdlet fornece informações sobre a solução de problemas sobre o Monitor de Status.
Use este cmdlet para investigar o status de monitoramento, a versão do Módulo PowerShell e para inspecionar o processo de execução.
Este cmdlet informará informações de versão e informações sobre os arquivos-chave necessários para monitoramento.

### <a name="examples"></a>Exemplos

#### <a name="example-application-status"></a>Exemplo: Status do aplicativo

Execute o `Get-ApplicationInsightsMonitoringStatus` comando para exibir o status de monitoramento dos sites.

```powershell

PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus

IIS Websites:

SiteName               : Default Web Site
ApplicationPoolName    : DefaultAppPool
SiteId                 : 1
SiteState              : Stopped

SiteName               : DemoWebApp111
ApplicationPoolName    : DemoWebApp111
SiteId                 : 2
SiteState              : Started
ProcessId              : not found

SiteName               : DemoWebApp222
ApplicationPoolName    : DemoWebApp222
SiteId                 : 3
SiteState              : Started
ProcessId              : 2024
Instrumented           : true
InstrumentationKey     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123

SiteName               : DemoWebApp333
ApplicationPoolName    : DemoWebApp333
SiteId                 : 4
SiteState              : Started
ProcessId              : 5184
AppAlreadyInstrumented : true
```

Neste exemplo;
- **Identificador de máquina** é um ID anônimo usado para identificar exclusivamente seu servidor. Se você criar uma solicitação de suporte, precisaremos deste ID para encontrar logs para o seu servidor.
- **O site padrão** está parado no IIS
- **O DemoWebApp111** foi iniciado no IIS, mas não recebeu nenhuma solicitação. Este relatório mostra que não há processo de execução (ProcessId: não encontrado).
- **DemoWebApp222** está em execução e está sendo monitorado (Instrumentado: verdadeiro). Com base na configuração do usuário, a Chave de Instrumentação xxxxxxxx-xxxx-xxxx-xxxx-xxxxxx123 foi combinada para este site.
- **DemoWebApp333** foi manualmente instrumentado usando o Application Insights SDK. O Monitor de Status detectou o SDK e não monitorará este site.


#### <a name="example-powershell-module-information"></a>Exemplo: Informações do módulo PowerShell

Execute o `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` comando para exibir informações sobre o módulo atual:

```powershell

PS C:\> Get-ApplicationInsightsMonitoringStatus -PowerShellModule

PowerShell Module version:
0.4.0-alpha

Application Insights SDK version:
2.9.0.3872

Executing PowerShell Module Assembly:
Microsoft.ApplicationInsights.Redfield.Configurator.PowerShell, Version=2.8.14.11432, Culture=neutral, PublicKeyToken=31bf3856ad364e35

PowerShell Module Directory:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\PowerShell

Runtime Paths:
ParentDirectory (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content

ConfigurationPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config

ManagedHttpModuleHelperPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll

RedfieldIISModulePath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

InstrumentationEngine86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\MicrosoftInstrumentationEngine_x86.dll

InstrumentationEngine64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll

InstrumentationEngineExtensionHost86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.ApplicationInsights.ExtensionsHost_x86.dll

InstrumentationEngineExtensionHost64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll

InstrumentationEngineExtensionConfig86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.InstrumentationEngine.Extensions.config

InstrumentationEngineExtensionConfig64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.InstrumentationEngine.Extensions.config

ApplicationInsightsSdkPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll
```

#### <a name="example-runtime-status"></a>Exemplo: Status de tempo de execução

Você pode inspecionar o processo no computador instrumentado para ver se todas as DLLs estão carregadas. Se o monitoramento estiver funcionando, pelo menos 12 DLLs devem ser carregados.

Execute o `Get-ApplicationInsightsMonitoringStatus -InspectProcess`comando:


```
PS C:\> Get-ApplicationInsightsMonitoringStatus -InspectProcess

iisreset.exe /status
Status for IIS Admin Service ( IISADMIN ) : Running
Status for Windows Process Activation Service ( WAS ) : Running
Status for Net.Msmq Listener Adapter ( NetMsmqActivator ) : Running
Status for Net.Pipe Listener Adapter ( NetPipeActivator ) : Running
Status for Net.Tcp Listener Adapter ( NetTcpActivator ) : Running
Status for World Wide Web Publishing Service ( W3SVC ) : Running

handle64.exe -accepteula -p w3wp
BF0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.ServerTelemetryChannel.dll
C58: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.AzureAppServices.dll
C68: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.DependencyCollector.dll
C78: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.WindowsServer.dll
C98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Web.dll
CBC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.PerfCounterCollector.dll
DB0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Agent.Intercept.dll
B98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll
BB4: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.Contracts.dll
BCC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll
BE0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll

listdlls64.exe -accepteula w3wp
0x0000000019ac0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
0x00000000198b0000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
0x000000000c460000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
0x000000000ad60000  0x108000  C:\Windows\TEMP\2.4.0.0.Microsoft.ApplicationInsights.Extensions.Intercept_x64.dll
```

### <a name="parameters"></a>Parâmetros

#### <a name="no-parameters"></a>(Sem parâmetros)

Por padrão, este cmdlet informará o status de monitoramento de aplicações web.
Use esta opção para revisar se sua aplicação foi instrumentada com sucesso.
Você também pode rever qual tecla de instrumentação foi compatível com o seu site.


#### <a name="-powershellmodule"></a>-PowerShellModule
**Opcional**. Use este switch para relatar os números de versão e os caminhos dos DLLs necessários para monitoramento.
Use esta opção se você precisar identificar a versão de qualquer DLL, incluindo o Application Insights SDK.

#### <a name="-inspectprocess"></a>-InspectProcess

**Opcional**. Use este switch para informar se o IIS está sendo executado.
Ele também baixará ferramentas externas para determinar se as DLLs necessárias estão carregadas no tempo de execução do IIS.


Se esse processo falhar por qualquer motivo, você pode executar esses comandos manualmente:
- iisreset.exe /status
- [handle64.exe](https://docs.microsoft.com/sysinternals/downloads/handle) -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
- [listdlls64.exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"


#### <a name="-force"></a>-Force

**Opcional**. Usado apenas com InspectProcess. Use este switch para pular o prompt do usuário que aparece antes que outras ferramentas sejam baixadas.


## <a name="set-applicationinsightsmonitoringconfig"></a>Set-ApplicationInsightsMonitoringConfig

Define o arquivo de configuração sem fazer uma reinstalação completa.
Reinicie o IIS para que suas alterações surtam efeito.

> [!IMPORTANT] 
> Este cmdlet requer uma sessão PowerShell com permissões de admin.


### <a name="examples"></a>Exemplos

#### <a name="example-with-a-single-instrumentation-key"></a>Exemplo com uma única chave de instrumentação
Neste exemplo, todos os aplicativos no computador atual serão atribuídos a uma única chave de instrumentação.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Exemplo com um mapa-chave de instrumentação
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

### <a name="parameters"></a>Parâmetros

#### <a name="-instrumentationkey"></a>-Chave de Instrumentação
**Necessário.** Use este parâmetro para fornecer uma única chave de instrumentação para uso por todos os aplicativos no computador de destino.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Necessário.** Use este parâmetro para fornecer várias teclas de instrumentação e um mapeamento das teclas de instrumentação usadas por cada aplicativo.
Você pode criar um único script `MachineFilter`de instalação para vários computadores definindo .

> [!IMPORTANT]
> Os aplicativos corresponderão às regras na ordem de que as regras sejam fornecidas. Então você deve especificar as regras mais específicas primeiro e as regras mais genéricas por último.

##### <a name="schema"></a>Esquema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** é um regex C# necessário do computador ou nome VM.
    - '.*' vai corresponder a todos
    - 'ComputerName' corresponderá apenas computadores com o nome especificado.
- **AppFilter** é um regex C# necessário do computador ou nome VM.
    - '.*' vai corresponder a todos
    - 'ApplicationName' corresponderá apenas aos aplicativos IIS com o nome especificado.
- **O InstrumentationKey** é necessário para habilitar o monitoramento dos aplicativos que correspondem aos dois filtros anteriores.
    - Deixe esse valor nulo se quiser definir regras para excluir o monitoramento.


#### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Use este interruptor para exibir registros detalhados.


### <a name="output"></a>Saída

Por padrão, nenhuma saída.

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Exemplo de saída verbose a partir da configuração do arquivo de configuração via -InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Exemplo de saída verbose a partir da configuração do arquivo de configuração via -InstrumentationKeyMap

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

## <a name="start-applicationinsightsmonitoringtrace"></a>Start-ApplicationInsightsMonitoringTrace

Coleta [eventos ETW](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) do tempo de execução de attach sem código. Este cmdlet é uma alternativa para executar [o PerfView](https://github.com/microsoft/perfview).

Os eventos coletados serão impressos no console em tempo real e salvos em um arquivo ETL. O arquivo ETL de saída pode ser aberto pelo [PerfView](https://github.com/microsoft/perfview) para uma investigação mais aprofundada.

Este cmdlet será executado até atingir a duração do tempo (padrão`Ctrl + C`de 5 minutos) ou é parado manualmente ().

### <a name="examples"></a>Exemplos

#### <a name="how-to-collect-events"></a>Como coletar eventos

Normalmente pedimos que você colete eventos para investigar por que sua aplicação não está sendo instrumentada.

O tempo de execução de attach sem código emite eventos DeTW quando o IIS for iniciado e quando a aplicação for iniciada.

Para coletar esses eventos:
1. Em um console cmd com privilégios `iisreset /stop` de admin, execute Para desativar o IIS e todos os aplicativos web.
2. Execute este cmdlet
3. Em um console cmd com privilégios `iisreset /start` de admin, execute Para iniciar o IIS.
4. Tente navegar para o seu aplicativo.
5. Depois que seu aplicativo terminar de carregar,`Ctrl + C`você pode pará-lo manualmente ou esperar pelo intervalo.

#### <a name="what-events-to-collect"></a>Quais eventos coletar

Você tem três opções ao coletar eventos:
1. Use o `-CollectSdkEvents` switch para coletar eventos emitidos do Application Insights SDK.
2. Use o `-CollectRedfieldEvents` switch para coletar eventos emitidos pelo Monitor de Status e pelo Redfield Runtime. Esses registros são úteis ao diagnosticar iIS e inicialização de aplicativos.
3. Use ambos os interruptores para coletar os dois tipos de eventos.
4. Por padrão, se nenhum switch for especificado, ambos os tipos de evento serão coletados.


### <a name="parameters"></a>Parâmetros

#### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Opcional.** Use este parâmetro para definir quanto tempo este script deve coletar eventos. O padrão é de 5 minutos.

#### <a name="-logdirectory"></a>-LogDirectory
**Opcional.** Use este switch para definir o diretório de saída do arquivo ETL. Por padrão, esse arquivo será criado no diretório Módulos PowerShell. O caminho completo será exibido durante a execução do script.


#### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Opcional.** Use este switch para coletar eventos SDK do Application Insights.

#### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Opcional.** Use este switch para coletar eventos do Monitor de Status e do tempo de execução redfield.

#### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Use este interruptor para produzir logs detalhados de saída.



### <a name="output"></a>Saída


#### <a name="example-of-application-startup-logs"></a>Exemplo de logs de inicialização de aplicativos
```powershell
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

  Exiba sua telemetria:
 - [Explorar métricas](../../azure-monitor/app/metrics-explorer.md) para monitorar o desempenho e o uso.
- [Pesquise eventos e registros](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
- Use [análises](../../azure-monitor/app/analytics.md) para consultas mais avançadas.
- [Criar painéis](../../azure-monitor/app/overview-dashboard.md).
 
 Adicione mais telemetria:
 - [Crie testes na Web](monitor-web-app-availability.md) para ter a certeza de que seu site continua ativo.
- [Adicione telemetria do cliente web](../../azure-monitor/app/javascript.md) para ver exceções do código da página da Web e para ativar chamadas de rastreamento.
- [Adicione o Application Insights SDK ao seu código para](../../azure-monitor/app/asp-net.md) que você possa inserir chamadas de rastreamento e registro.
 
 Faça mais com o Agente de Insights de Aplicativos:
 - Use nosso guia para [solucionar problemas](status-monitor-v2-troubleshoot.md) do Agente de Insights de Aplicativos.






