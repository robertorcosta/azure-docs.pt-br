---
title: Azure Application Insights Agent solução de problemas e problemas conhecidos | Microsoft Docs
description: Os problemas conhecidos do Application Insights Agent e exemplos de solução de problemas. Monitore o desempenho do site sem reimplantar o site. Funciona com ASP.NET aplicativos web hospedados no local, em VMs ou no Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 9bb22b12a7b3e972ff144bd121db4288801e2488
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732945"
---
# <a name="troubleshooting-application-insights-agent-formerly-named-status-monitor-v2"></a>Agente de insights de aplicativos para solução de problemas (anteriormente chamado de Monitor de Status v2)

Quando você habilita o monitoramento, você pode ter problemas que impedem a coleta de dados.
Este artigo lista todos os problemas conhecidos e fornece exemplos de solução de problemas.
Se você se deparar com um problema que não está listado aqui, você pode entrar em contato conosco no [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).

## <a name="known-issues"></a>Problemas conhecidos

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>DLLs conflitantes no diretório bin de um aplicativo

Se algum desses DLLs estiver presente no diretório bin, o monitoramento pode falhar:

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetriaCorrelação.dll
- System.Diagnostics.DiagnosticSource.dll

Alguns desses DLLs estão incluídos nos modelos padrão de aplicativos do Visual Studio, mesmo que seu aplicativo não os use.
Você pode usar ferramentas de solução de problemas para ver o comportamento sintomático:

- PerfView:
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- IISReset e carga de aplicativo (sem telemetria). Investigue com Sysinternals (Handle.exe e ListDLLs.exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Conflito com configuração compartilhada do IIS

Se você tiver um cluster de servidores web, você pode estar usando uma [configuração compartilhada](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
O HttpModule não pode ser injetado nesta configuração compartilhada.
Execute o comando Ativar em cada servidor web para instalar o DLL no GAC de cada servidor.

Depois de executar o comando Ativar, complete estas etapas:
1. Vá para o diretório de configuração compartilhada e encontre o arquivo applicationHost.config.
2. Adicione esta linha à seção de módulos da sua configuração:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>Aplicações aninhadas do IIS

Não instrumentamos aplicações aninhados no IIS na versão 1.0.
Estamos rastreando esse problema [aqui.](https://github.com/microsoft/ApplicationInsights-Home/issues/369)

### <a name="advanced-sdk-configuration-isnt-available"></a>A configuração avançada do SDK não está disponível.

A configuração do SDK não é exposta ao usuário final na versão 1.0.
Estamos rastreando esse problema [aqui.](https://github.com/microsoft/ApplicationInsights-Home/issues/375)

    
    
## <a name="troubleshooting"></a>Solução de problemas
    
### <a name="troubleshooting-powershell"></a>Solução de problemas PowerShell

#### <a name="determine-which-modules-are-available"></a>Determine quais módulos estão disponíveis
Você pode `Get-Module -ListAvailable` usar o comando para determinar quais módulos estão instalados.

#### <a name="import-a-module-into-the-current-session"></a>Importe um módulo para a sessão atual
Se um módulo não tiver sido carregado em uma sessão PowerShell, `Import-Module <path to psd1>` você pode carregá-lo manualmente usando o comando.


### <a name="troubleshooting-the-application-insights-agent-module"></a>Solução de problemas do módulo Agent do Application Insights

#### <a name="list-the-commands-available-in-the-application-insights-agent-module"></a>Liste os comandos disponíveis no módulo Agente de Insights de Aplicativo
Execute o `Get-Command -Module Az.ApplicationMonitor` comando para obter os comandos disponíveis:

```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.4.0      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.4.0      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Start-ApplicationInsightsMonitoringTrace           0.4.0      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-application-insights-agent-module"></a>Determine a versão atual do módulo Agent do Application Insights
Execute `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` o comando para exibir as seguintes informações sobre o módulo:
   - Versão do módulo PowerShell
   - Versão SDK do Application Insights
   - Caminhos de arquivo do módulo PowerShell
    
Revise a [referência da API](status-monitor-v2-api-reference.md) para obter uma descrição detalhada de como usar este cmdlet.


### <a name="troubleshooting-running-processes"></a>Solução de problemas executando processos

Você pode inspecionar os processos no computador instrumentado para determinar se todas as DLLs estão carregadas.
Se o monitoramento estiver funcionando, pelo menos 12 DLLs devem ser carregados.

Use `Get-ApplicationInsightsMonitoringStatus -InspectProcess` o comando para verificar os DLLs.

Revise a [referência da API](status-monitor-v2-api-reference.md) para obter uma descrição detalhada de como usar este cmdlet.


### <a name="collect-etw-logs-by-using-perfview"></a>Coletar registros de ETW usando perfView

#### <a name="setup"></a>Instalação

1. Baixe PerfView.exe e PerfView64.exe do [GitHub](https://github.com/Microsoft/perfview/releases).
2. Inicie perfView64.exe.
3. Expanda **Opções Avançadas**.
4. Limpe essas caixas de seleção:
    - **Zip**
    - **Mesclagem**
    - **.NET Symbol Collection**
5. Defina esses **provedores adicionais:**`61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Coleta de registros

1. Em um console de comando com privilégios `iisreset /stop` de Admin, execute o comando para desativar o IIS e todos os aplicativos da Web.
2. Em PerfView, selecione **Iniciar coleção**.
3. Em um console de comando com privilégios `iisreset /start` de Admin, execute o comando para iniciar o IIS.
4. Tente navegar para o seu aplicativo.
5. Depois que seu aplicativo for carregado, retorne ao PerfView e selecione **Stop Collection**.



## <a name="next-steps"></a>Próximas etapas

- Revise a [referência da API](status-monitor-v2-overview.md#powershell-api-reference) para saber sobre parâmetros que você pode ter perdido.
- Se você se deparar com um problema que não está listado aqui, você pode entrar em contato conosco no [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).
