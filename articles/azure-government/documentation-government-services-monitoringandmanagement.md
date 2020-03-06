---
title: Gerenciamento + monitoramento do Azure Governamental | Microsoft Docs
description: Este guia oferece uma comparação das funcionalidades e das orientações sobre como desenvolver aplicativos para o Azure Governamental.
services: azure-government
cloud: gov
author: gsacavdm
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: azure-government
ms.topic: article
ms.workload: azure-government
ms.date: 12/11/2019
ms.author: gsacavdm
ms.openlocfilehash: b6f395964c286aca1bc8a1c190edeea00ba6e15c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361777"
---
# <a name="azure-government-monitoring--management"></a>Gerenciamento + monitoramento do Azure Governamental
Este artigo descreve as variações e as considerações de serviços para o ambiente do Azure Governamental.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="advisor"></a>Advisor
O assistente está geralmente disponível no Azure governamental.

Para obter mais informações, consulte [documentação pública do Advisor](../advisor/advisor-overview.md).

### <a name="variations"></a>Variações
As seguintes recomendações do Advisor não estão disponíveis atualmente no Azure governamental:

* Alta disponibilidade
  * Configurar o gateway de VPN para ativo-ativo para resiliência de conexão
  * Criar alertas de Integridade do Serviço do Azure para ser notificado quando problemas do Azure afetarem você
  * Configurar pontos de extremidade do Gerenciador de Tráfego para resiliência
  * Usar exclusão reversível para sua conta de armazenamento do Azure
* Desempenho
  * Melhorar o desempenho e a confiabilidade do Serviço de Aplicativo
  * Reduza o tempo de DNS para viver no seu perfil do Gerenciador de Tráfego para fazer o failover para endpoints saudáveis mais rapidamente
  * Melhorar o desempenho de SQL Data Warehouse
  * Usar o Armazenamento Premium
  * Migre sua conta de armazenamento para Azure Resource Manager
* Custo
  * Comprar instâncias de máquinas virtuais reservadas para economizar dinheiro nos custos pagos conforme o uso
  * Eliminar circuitos de ExpressRoute não provisionados
  * Excluir ou reconfigurar gateways de rede virtual ociosos

O cálculo usado para recomendar que você deve ter o tamanho correto ou desligar as máquinas virtuais subutilizadas é o seguinte no Azure governamental:

O Advisor monitora o uso de máquinas virtuais por 7 dias e identifica máquinas virtuais de baixa utilização. As máquinas virtuais são consideradas de baixa utilização se a utilização da CPU for de 5% ou menos e sua utilização de rede for menor que 2% ou se a carga de trabalho atual puder ser acomodada por um tamanho de máquina virtual menor. Se você quiser ser mais agressivo na identificação de máquinas virtuais subutilizadas, poderá ajustar a regra de utilização da CPU por assinatura.

## <a name="automation"></a>Automação
A Automação está geralmente disponível no Azure Governamental.

Para saber mais, veja [documentação pública da Automação](../automation/automation-intro.md).

## <a name="azure-migrate"></a>Migrações para Azure

As migrações para Azure estão geralmente disponíveis no Azure governamental.

Para obter mais informações, consulte a [documentação de migrações para Azure](../migrate/migrate-overview.md).

### <a name="variations"></a>Variações
Os seguintes recursos de migrações para Azure atualmente não estão disponíveis no Azure governamental:

* A funcionalidade de visualização de dependência não está disponível no Azure governamental, pois as migrações para Azure dependem Mapa do Serviço para visualização de dependência que não está disponível no Azure governamental no momento.
* Você só pode criar Avaliações para o Azure governamental como regiões de destino e usar as ofertas do Azure governamental.

## <a name="backup"></a>Backup
O backup está totalmente disponível no Azure Governamental.

Para saber mais, confira [Backup do Azure Governamental](documentation-government-services-backup.md).

## <a name="policy"></a>Política
A política está geralmente disponível no Azure governamental.

Para saber mais, veja [Azure Policy](../governance/policy/overview.md).

## <a name="site-recovery"></a>Site Recovery
O Azure Site Recovery está totalmente disponível no Azure Governamental.

Para obter mais informações, consulte [site Recovery documentação comercial](../site-recovery/site-recovery-overview.md).

### <a name="variations"></a>Variações
Atualmente, os seguintes recursos do Site Recovery não estão disponíveis no Azure Governamental:
* Notificação por email

| Site Recovery | Classic | Gerenciador de Recursos |
| --- | --- | --- |
| VMware/físico  | GA | GA |
| Hyper-V | GA | GA |
| Site a Site | GA | GA |

As seguintes URLs do Site Recovery são diferentes no Azure Governamental:

| Público do Azure | Azure Government | {1&gt;Observações&lt;1} |
| --- | --- | --- |
| \*.hypervrecoverymanager.windowsazure.com | \*.hypervrecoverymanager.windowsazure.us | Acesso ao serviço Site Recovery |
| \*.backup.windowsazure.com  | \*.backup.windowsazure.us | Acesso ao serviço de proteção |
| \*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net | Para armazenar instantâneos da VM |
| https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | Para baixar o MySQL |

## <a name="monitor"></a>Monitoramento
O Azure Monitor está geralmente disponível no Azure governamental.

Para obter mais informações, consulte [monitorar a documentação comercial](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview).

### <a name="variations"></a>Variações
As seções a seguir detalham as diferenças e soluções alternativas para os recursos de Azure Monitor no Azure governamental:

#### <a name="action-groups"></a>Grupos de Ação
Os grupos de ações estão geralmente disponíveis no Azure governamental sem diferenças do Azure comercial.   

#### <a name="activity-log-alerts"></a>Alertas do log de atividades
Os alertas do log de atividades estão geralmente disponíveis no Azure governamental sem diferenças do Azure comercial.

#### <a name="alerts-experience"></a>Experiência de alertas
A experiência de interface do usuário de alertas unificados está disponível para alertas de métrica e log no Azure governamental.

#### <a name="autoscale"></a>Autoscale
O dimensionamento automático está geralmente disponível no Azure governamental.

Se você estiver usando chamadas do PowerShell/ARM/REST para especificar as configurações, defina o "local" da AutoEscala para "USGov Virgínia" ou "USGov Iowa". O recurso de destino do dimensionamento automático pode existir em qualquer região. Veja abaixo um exemplo da configuração:

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Requests" -MetricResourceId "/subscriptions/S1/resourceGroups/RG1/providers/Microsoft.Web/sites/WebSite1" -Operator GreaterThan -MetricStatistic Average -Threshold 10 -TimeGrain 00:01:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue "1"
$rule2 = New-AzAutoscaleRule -MetricName "Requests" -MetricResourceId "/subscriptions/S1/resourceGroups/RG1/providers/Microsoft.Web/sites/WebSite1" -Operator GreaterThan -MetricStatistic Average -Threshold 10 -TimeGrain 00:01:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue "2"
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1, $rule2 -Name "MyProfile"
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri https://example.com?mytoken=mytokenvalue
$notification1= New-AzAutoscaleNotification -CustomEmails myname@company.com -SendEmailToSubscriptionAdministrator -SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
Add-AzAutoscaleSetting -Location "USGov Virginia" -Name "MyScaleVMSSSetting" -ResourceGroup sdubeys-usgv -TargetResourceId /subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Web/serverFarms/ServerFarm1 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Se você estiver interessado em implementar o dimensionamento automático em seus recursos, use chamadas do PowerShell/ARM/REST para especificar as configurações.

Para obter mais informações sobre como usar o PowerShell, consulte a [documentação pública](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples#create-and-manage-autoscale-settings).

#### <a name="metrics"></a>Métricas
As métricas estão geralmente disponíveis no Azure governamental. No entanto, só há suporte para métricas multidimensionais por meio da API REST. A capacidade de [Mostrar métricas multidimensionais](../azure-monitor/platform/metrics-charts.md) está em versão prévia no portal do Azure governamental.

#### <a name="metric-alerts"></a>Alertas de Métricas
A primeira geração de alertas de métricas está geralmente disponível no Azure governamental e no Azure comercial. A primeira geração é chamada de *alertas (clássico)* . A segunda geração de alertas de métrica (também chamada de [experiência de alertas unificados](../azure-monitor/platform/alerts-overview.md)) agora também está disponível, mas com um conjunto reduzido de provedores de recursos [em comparação com a nuvem pública](../azure-monitor/platform/alerts-metric-near-real-time.md). Mais serão adicionados ao longo do tempo. 

Os recursos atualmente compatíveis com a experiência de alertas de segunda geração são:
- Microsoft.ApiManagement/service
- Microsoft.Cache/redis
- Microsoft.Compute/virtualMachines
- Microsoft.DBforMySQL/servers
- Microsoft.DBforPostgreSQL/servers
- Microsoft.DBforMariaDB/servers
- Microsoft.Devices/IotHubs
- Microsoft.EventGrid/domains
- Microsoft.EventGrid/topics
- Microsoft.EventHub/clusters
- Microsoft.EventHub/namespaces
- Microsoft.Insights/components
- Microsoft.Network/dnsZones
- Microsoft.Network/loadBalancers
- Microsoft. Network/natGateways
- Microsoft. Network/privateEndpoints
- Microsoft. Network/privateLinkServices
- Microsoft.Network/trafficManagerProfiles
- Microsoft.OperationalInsights/workspaces
- Microsoft.PowerBIDedicated/capacities
- Microsoft.Relay/namespaces
- Microsoft.ServiceBus/namespaces
- Microsoft.Sql/managedInstances
- Microsoft.Sql/servers/databases
- Microsoft.Sql/servers/elasticPools
- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices
- Microsoft.Storage/storageAccounts/fileServices
- Microsoft.Storage/storageAccounts/queueServices
- Microsoft.Storage/storageAccounts/tableServices
- Microsoft.Web/hostingEnvironments/multiRolePools
- Microsoft.Web/hostingEnvironments/workerPools
- Microsoft.Web/serverfarms
- Microsoft.Web/sites
- Microsoft.Web/sites/slots

Você ainda pode usar [alertas clássicos](../azure-monitor/platform/alerts-classic.overview.md) para recursos que ainda não estão disponíveis na segunda geração de alertas. 

Ao usar chamadas do PowerShell/ARM/REST para criar alertas de métrica, você precisará definir o "local" do alerta de métrica para "USGov Virgínia" ou "USGov Iowa". Veja abaixo um exemplo da configuração:

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "USGov Virginia" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Para obter mais informações sobre como usar o PowerShell, consulte a [documentação pública](../azure-monitor/platform/powershell-quickstart-samples.md).

## <a name="application-insights"></a>Application Insights

> [!NOTE]
> **Não há suporte**para o monitoramento baseado em agente/extensão sem código para serviços Azure apps no momento. Assim que essa funcionalidade for disponibilizada, este artigo será atualizado.

Esta seção descreve a configuração suplementar necessária para usar o Application Insights no Azure governamental. Para saber mais sobre Azure Monitor e Application Insights fazer check-out da [documentação completa](https://docs.microsoft.com/azure/azure-monitor/overview).

### <a name="enable-application-insights-for-aspnet--aspnet-core-with-visual-studio"></a>Habilitar Application Insights para ASP.NET & ASP.NET Core com o Visual Studio

Atualmente, para clientes do Azure governamental, a única maneira de habilitar Application Insights por meio do botão de **telemetria tradicional adicionar aplicativos insights** no Visual Studio requer uma pequena solução manual. Os clientes que estiverem enfrentando o problema associado poderão ver mensagens de erro como "não há _nenhuma assinatura do Azure associada a essa conta_ ou _" a assinatura selecionada não oferece suporte a Application insights_ mesmo que o provedor de recursos de `microsoft.insights` tenha um status registrado para a assinatura. Para atenuar esse problema, você deve executar as seguintes etapas:

1. Alterne o Visual Studio para [direcionar para a nuvem do Azure governamental](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-vs).

2. Crie (ou se já tiver definido) a variável de ambiente do usuário para AzureGraphApiVersion da seguinte maneira: (para criar uma variável de ambiente do usuário, vá para o **painel de controle** > **sistema** > **Configurações avançadas do sistema** > variáveis de **ambiente** > **avançadas** .)

    `Variable name: AzureGraphApiVersion` `Variable value: 2014-04-01`

3. Faça as modificações apropriadas do ponto de extremidade do SDK do Application Insights para [ASP.net](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#net-with-applicationinsightsconfig) ou [ASP.NET Core](#aspnet-core) dependendo do tipo de projeto.

### <a name="snapshot-debugger"></a>Depurador de instantâneo

Depurador de Instantâneos agora está disponível para clientes do Azure governamental. Para usar Depurador de Instantâneos o único pré-requisito adicional é garantir que você esteja usando [snapshot Collector versão 1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5-pre-1906.403) ou posterior. Em seguida, basta seguir a [documentação de depurador de instantâneos](https://docs.microsoft.com/azure/azure-monitor/app/snapshot-debugger)padrão.

### <a name="sdk-endpoint-modifications"></a>Modificações do ponto de extremidade do SDK

Para enviar dados de Application Insights para a região do Azure governamental, você precisará modificar os endereços de ponto de extremidade padrão que são usados pelos SDKs de Application Insights. Cada SDK requer modificações ligeiramente diferentes.

### <a name="net-with-applicationinsightsconfig"></a>.NET com applicationinsights. config

> [!NOTE]
> O arquivo applicationinsights. config é substituído automaticamente sempre que uma atualização do SDK é executada. Depois de executar uma atualização do SDK, certifique-se de inserir novamente os valores de ponto de extremidade específicos da região.

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://quickpulse.applicationinsights.us/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.applicationinsights.us/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.applicationinsights.us/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

### <a name="aspnet-core"></a>ASP.NET Core

Modifique o arquivo appSettings. JSON em seu projeto da seguinte maneira para ajustar o ponto de extremidade principal:

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "https://dc.applicationinsights.us/v2/track"
    }
  }
```

Os valores para métricas ao vivo e o ponto de extremidade de consulta de perfil só podem ser definidos por meio de código. Para substituir os valores padrão para todos os valores de ponto de extremidade por meio de código, faça as seguintes alterações no método `ConfigureServices` do arquivo `Startup.cs`:

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel; //place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="https://quickpulse.applicationinsights.us/QuickPulseService.svc");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "https://dc.applicationinsights.us/v2/track" });

    //place in ConfigureServices method. If present, place this prior to   services.AddApplicationInsightsTelemetry("instrumentation key");
```

### <a name="azure-functions"></a>Funções do Azure

Instale os pacotes a seguir em seu projeto de função:

- Microsoft. ApplicationInsights versão 2.10.0
- Microsoft. ApplicationInsights. PerfCounterCollector versão 2.10.0
- Microsoft. ApplicationInsights. WindowsServer. TelemetryChannel versão 2.10.0

Além disso, adicione (ou modifique) o código de inicialização para seu aplicativo de função:

```csharp
[assembly: FunctionsStartup(typeof(Example.Startup))]
namespace Example
{
  class Startup : FunctionsStartup
  {
      public override void Configure(IFunctionsHostBuilder builder)
      {
          var quickPulseFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryModule) && 
                                               sd.ImplementationType == typeof(QuickPulseTelemetryModule));
          if (quickPulseFactory != null)
          {
              builder.Services.Remove(quickPulseFactory);
          }

          var appIdFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(IApplicationIdProvider));
          if (appIdFactory != null)
          {
              builder.Services.Remove(appIdFactory);
          }

          var channelFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryChannel));
          if (channelFactory != null)
          {
              builder.Services.Remove(channelFactory);
          }

          builder.Services.AddSingleton<ITelemetryModule, QuickPulseTelemetryModule>(_ =>
              new QuickPulseTelemetryModule
              {
                  QuickPulseServiceEndpoint = "https://quickpulse.applicationinsights.us/QuickPulseService.svc"
              });

          builder.Services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId" });

          builder.Services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "https://dc.applicationinsights.us/v2/track" });
      }
  }
}
```

### <a name="java"></a>Java

Modifique o arquivo applicationinsights. xml para alterar o endereço do ponto de extremidade padrão.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>ffffeeee-dddd-cccc-bbbb-aaaa99998888</InstrumentationKey>
  <TelemetryModules>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
  </TelemetryModules>
  <TelemetryInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
  </TelemetryInitializers>
  <!--Add the following Channel value to modify the Endpoint address-->
  <Channel type="com.microsoft.applicationinsights.channel.concrete.inprocess.InProcessTelemetryChannel">
  <EndpointAddress>https://dc.applicationinsights.us/v2/track</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

Modifique o arquivo de `application.properties` e adicione:

```yaml
azure.application-insights.channel.in-process.endpoint-address= https://dc.applicationinsights.us/v2/track
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "https://dc.applicationinsights.us/v2/track"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "https://quickpulse.applicationinsights.us/QuickPulseService.svc"; //live metrics
appInsights.Configuration.start();
```

Os pontos de extremidade também podem ser configurados por meio de variáveis de ambiente:

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "https://dc.applicationinsights.us/api/profiles/{0}/appId"
Live Metrics Endpoint: "https://quickpulse.applicationinsights.us/QuickPulseService.svc"
```

### <a name="javascript"></a>JavaScript

```javascript
<script type="text/javascript">
   var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){
      function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/next/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t
   }({
      instrumentationKey:"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
      endpointUrl: "https://dc.applicationinsights.us/v2/track"
   });

   window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="firewall-exceptions"></a>Exceções de firewall

O serviço de informações Aplicativo Azures usa vários endereços IP. Talvez seja necessário conhecer esses endereços se o aplicativo que você está monitorando estiver hospedado atrás de um firewall.

> [!NOTE]
> Embora esses endereços sejam estáticos, é possível que seja necessário alterá-los de tempos em tempos. Todo o tráfego de Application Insights representa o tráfego de saída com exceção de monitoramento de disponibilidade e WebHooks, que exigem regras de firewall de entrada.

### <a name="outgoing-ports"></a>Portas de saída
Você precisa abrir algumas portas de saída no firewall do servidor para permitir que o SDK do Application Insights e/ou o Monitor de Status envie dados para o portal:

| Finalidade | {1&gt;URL&lt;1} | IP | Portas |
| --- | --- | --- | --- |
| Telemetria | dc.applicationinsights.us | 23.97.4.113 | 443 |

## <a name="azure-monitor-logs"></a>Logs do Azure Monitor
Os logs de Azure Monitor geralmente estão disponíveis no Azure governamental.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

### <a name="variations"></a>Variações

* As soluções que estão disponíveis no Azure governamental incluem:
  * [Monitor de desempenho de rede (NPM)](https://blogs.msdn.microsoft.com/azuregov/2017/09/05/network-performance-monitor-general-availability/) -NPM é uma solução de monitoramento de rede baseada em nuvem para ambientes de nuvem pública e híbrida. As organizações usam o NPM para monitorar a disponibilidade de rede entre ambientes locais e na nuvem.  Monitor de ponto de extremidade-uma subcapacidade do NPM, monitora a conectividade de rede para aplicativos.

Os recursos e soluções de logs de Azure Monitor a seguir não estão disponíveis no Azure governamental no momento.

* As soluções que estão em visualização no Microsoft Azure, incluindo:
  * Mapa do Serviço
  * Solução de Análise de Atualização do Windows 10
  * Solução do Application Insights
  * Solução de análise do grupo de segurança de rede do Azure
  * Solução da Análise da Automação do Azure
  * Solução da Análise do Cofre de Chaves
* Soluções e funcionalidades que exigem atualizações de software local, incluindo:
  * Solução do Surface Hub
* Recursos que estão em versão prévia no Azure global, incluindo:
  * Exportação de dados para o Power BI
* Métricas do Azure e Diagnóstico do Azure

As URLs para logs de Azure Monitor são diferentes no Azure governamental:

| Público do Azure | Azure Government | {1&gt;Observações&lt;1} |
| --- | --- | --- |
| mms.microsoft.com |oms.microsoft.us |Portal de espaços de trabalho Log Analytics |
| *workspaceId*.ods.opinsights.azure.com |*workspaceId*.ods.opinsights.azure.us |[API do coletor de dados](../azure-monitor/platform/data-collector-api.md) |
| \*.ods.opinsights.azure.com |\*.ods.opinsights.azure.us |Comunicação do agente - [definindo as configurações de firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.oms.opinsights.azure.com |\*.oms.opinsights.azure.us |Comunicação do agente - [definindo as configurações de firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.blob.core.windows.net |\*.blob.core.usgovcloudapi.net |Comunicação do agente - [definindo as configurações de firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| portal.loganalytics.io |portal.loganalytics.us |Portal de análise avançada- [definindo configurações de firewall](../azure-monitor/log-query/portals.md) |
| api.loganalytics.io |api.loganalytics.us |Portal de análise avançada- [definindo configurações de firewall](../azure-monitor/log-query/portals.md) |
| docs.loganalytics.io |docs.loganalytics.us |Portal de análise avançada- [definindo configurações de firewall](../azure-monitor/log-query/portals.md) |
| \*.azure-automation.net |\*. azure-automation.us |Automação do Azure- [definindo configurações de firewall](../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements) |
| {1&gt;N/A&lt;1} | *. usgovtrafficmanager.net | Gerenciador de tráfego do Azure – [definindo as configurações de firewall](../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements) |

Os recursos de logs de Azure Monitor a seguir se comportam de modo diferente no Azure governamental:

* Para conectar seu grupo de gerenciamento do System Center Operations Manager a logs do Azure Monitor, você precisa baixar e importar pacotes de gerenciamento atualizados.
  + System Center Operations Manager 2016
    1. Instale o [Pacote Cumulativo de Atualizações 2 do System Center Operations Manager 2016](https://support.microsoft.com/help/3209591).
    2. Importe os pacotes de gerenciamento incluídos como parte do Pacote Cumulativo de Atualizações 2 para o Operations Manager. Para saber mais sobre como importar um pacote de gerenciamento de um disco, veja o tópico [Como importar um pacote de gerenciamento do Operations Manager](https://technet.microsoft.com/library/hh212691.aspx).
    3. Para se conectar Operations Manager aos logs de Azure Monitor, siga as etapas em [conectar Operations Manager aos logs de Azure monitor](../azure-monitor/platform/om-agents.md).
  + System Center Operations Manager 2012 R2 UR3 (ou posterior)/Operations Manager 2012 SP1 UR7 (ou posterior)
    1. Baixe e salve os [pacotes de gerenciamento atualizados](https://go.microsoft.com/fwlink/?LinkId=828749).
    2. Descompacte o arquivo que você baixou.
    3. Importe os pacotes de gerenciamento no Operations Manager. Para saber mais sobre como importar um pacote de gerenciamento de um disco, veja o tópico [Como importar um pacote de gerenciamento do Operations Manager](https://technet.microsoft.com/library/hh212691.aspx).
    4. Para se conectar Operations Manager aos logs de Azure Monitor, siga as etapas em [conectar Operations Manager aos logs de Azure monitor](../azure-monitor/platform/om-agents.md).

* Para obter mais informações sobre como usar grupos de computadores de Configuration Manager, consulte [conectar Configuration Manager ao Azure monitor](../azure-monitor/platform/collect-sccm.md).

### <a name="frequently-asked-questions"></a>Perguntas frequentes
* Posso migrar dados de Azure Monitor logs em Microsoft Azure para o Azure governamental?
  * Não. Não é possível mover os dados ou seu workspace do Microsoft Azure para o Azure Governamental.
* Posso alternar entre os espaços de trabalho do Microsoft Azure e do Azure governamental do portal do Operations Management Suite?
  * Não. Os portais do Microsoft Azure e do Azure Governamental são separados e não compartilham informações.

Para obter mais informações, consulte a [documentação pública de logs de Azure monitor](../log-analytics/log-analytics-overview.md).

## <a name="scheduler"></a>Agendador
Para obter informações sobre esse serviço e como usá-lo, consulte a [documentação do Agendador do Azure](../scheduler/index.md).

## <a name="azure-portal"></a>Portal do Azure
O portal do Azure governamental pode ser acessado [aqui](https://portal.azure.us).

## <a name="azure-resource-manager"></a>Azure Resource Manager
Para obter informações sobre esse serviço e como usá-lo, consulte [Azure Resource Manager documentação](../azure-resource-manager/management/overview.md).

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}
* Assine o [blog do Azure governamental](https://blogs.msdn.microsoft.com/azuregov/)
* Obter ajuda sobre Stack Overflow usando o [Azure-gov](https://stackoverflow.com/questions/tagged/azure-gov)
* Envie comentários ou solicite novos recursos por meio do [Fórum de comentários do Azure governamental](https://feedback.azure.com/forums/558487-azure-government)
