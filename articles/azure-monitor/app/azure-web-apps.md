---
title: Monitorar desempenho dos serviços de aplicativos do Azure | Microsoft Docs
description: Monitoramento do desempenho de aplicativos para serviços de aplicativos do Azure. Tempo de resposta e carregamento do gráfico, informações de dependência e definir alertas sobre o desempenho.
ms.topic: conceptual
ms.date: 12/11/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: dd0d3be6ed7e5185183618cc2bdeff5ee8d749f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81729800"
---
# <a name="monitor-azure-app-service-performance"></a>Monitorar o desempenho do Serviço de Aplicativo do Azure

Habilitar o monitoramento em seus ASP.NET e ASP.NET Core aplicativos Web com base em execução em [serviços de Azure app](https://docs.microsoft.com/azure/app-service/) agora está mais fácil do que nunca. Enquanto anteriormente você precisava instalar manualmente uma extensão de site, a extensão/agente mais recente agora é compilado na imagem do serviço de aplicativo por padrão. Este artigo o orientará na habilitação do monitoramento de Application Insights, bem como no fornecimento de diretrizes preliminares para automatizar o processo para implantações em larga escala.

> [!NOTE]
> A adição manual de uma extensão de site Application insights por meio de**extensões** de **ferramentas** > de desenvolvimento foi preterida. Esse método de instalação de extensão foi dependente de atualizações manuais para cada nova versão. A versão estável mais recente da extensão agora é [pré-instalado](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) como parte da imagem do serviço de aplicativo. Os arquivos estão localizados no `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` e são atualizados automaticamente com cada versão estável. Se você seguir as instruções baseadas em agente para habilitar o monitoramento abaixo, ela removerá automaticamente a extensão preterida para você.

## <a name="enable-application-insights"></a>Habilitar o Application Insights

Há duas maneiras de habilitar o monitoramento de aplicativos para aplicativos hospedados de serviços Azure Apps:

* **Monitoramento de aplicativo baseado em agente** (ApplicationInsightsAgent).  
    * Esse método é o mais fácil de habilitar e nenhuma configuração avançada é necessária. Ele é geralmente chamado de monitoramento de "tempo de execução". Para Azure App serviços, recomendamos, no mínimo, habilitar esse nível de monitoramento e, em seguida, com base em seu cenário específico, você pode avaliar se é necessário um monitoramento mais avançado por meio da instrumentação manual.

* **Instrumentar manualmente o aplicativo por meio do código** instalando o SDK do Application insights.

    * Essa abordagem é muito mais personalizável, mas requer [a adição de uma dependência nos pacotes NuGet do SDK Application insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Esse método, também significa que você precisa gerenciar as atualizações para a versão mais recente dos pacotes por conta própria.

    * Se você precisar fazer chamadas à API personalizada para acompanhar eventos/dependências não capturadas por padrão com o monitoramento baseado em agente, você precisará usar esse método. Confira o [artigo API para eventos e métricas personalizados](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) para saber mais. Essa também é a única opção com suporte para cargas de trabalho baseadas em Linux.

> [!NOTE]
> Se o monitoramento baseado em agente e a instrumentação baseada em SDK manual forem detectados, somente as configurações de instrumentação manual serão respeitadas. Isso é para evitar que dados duplicados sejam enviados. Para saber mais sobre isso, confira a [seção solução de problemas](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) abaixo.

## <a name="enable-agent-based-monitoring"></a>Habilitar o monitoramento baseado em agente

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> Não há suporte para a combinação de APPINSIGHTS_JAVASCRIPT_ENABLED e urlCompression. Para obter mais informações, consulte a explicação na [seção solução de problemas](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).


1. **Selecione Application insights** no painel de controle do Azure para seu serviço de aplicativo.

    ![Em Configurações, escolha Insights do aplicativo](./media/azure-web-apps/settings-app-insights-01.png)

   * Opte por criar um novo recurso, a menos que você já tenha configurado um recurso do Application Insights para este aplicativo. 

     > [!NOTE]
     > Ao clicar em **OK** para criar o novo recurso, você será solicitado a **Aplicar configurações de monitoramento**. Se você selecionar **Continuar**, vinculará seu novo recurso do Application Insights ao seu serviço de aplicativo. Fazer isso também **disparará uma reinicialização do seu serviço de aplicativo**. 

     ![Instrumentar seu aplicativo Web](./media/azure-web-apps/create-resource-01.png)

2. Depois de especificar qual recurso usar, você pode escolher como deseja que o Application Insights colete dados por plataforma para seu aplicativo. O monitoramento de aplicativos ASP.NET é ativado por padrão com dois níveis diferentes de coleção.

    ![Escolha as opções por plataforma](./media/azure-web-apps/choose-options-new.png)
 
 Veja abaixo um resumo dos dados coletados para cada rota:
        
|  | Coleção do .NET Basic | Coleção recomendada do .NET |
| --- | --- | --- |
| Adiciona as tendências de uso de CPU, memória e E/S |Sim |Sim |
| Coleta as tendências de uso e permite a correlação entre resultados de disponibilidade e transações | Sim |Sim |
| Coleta as exceções não tratadas pelo processo de host | Sim |Sim |
| Aumenta a precisão de métricas de APM com carga quando a amostragem é usada | Sim |Sim |
| Correlaciona microsserviços entre limites de solicitação/dependência | Não (somente recursos APM de instância única) |Sim |

3. Para definir configurações como amostragem, que você poderia controlar anteriormente por meio do arquivo applicationinsights. config, agora você pode interagir com essas mesmas configurações por meio de configurações do aplicativo com um prefixo correspondente. 

    * Por exemplo, para alterar a porcentagem de amostragem inicial, você pode criar uma configuração de aplicativo `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` de: e um `100`valor de.

    * Para obter a lista de configurações de processador de telemetria de amostragem adaptável com suporte, você pode consultar o [código](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) e a [documentação associada](https://docs.microsoft.com/azure/azure-monitor/app/sampling).

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Há suporte para as seguintes versões do .NET Core: ASP.NET Core 2,0, ASP.NET Core 2,1, ASP.NET Core 2,2 ASP.NET Core 3,0

No momento, **não há suporte para** a estrutura completa do .NET Core, implantação independente e aplicativos baseados em Linux com monitoramento baseado em agente/extensão. (A[Instrumentação manual](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) por meio de código funcionará em todos os cenários anteriores.)

1. **Selecione Application insights** no painel de controle do Azure para seu serviço de aplicativo.

    ![Em Configurações, escolha Insights do aplicativo](./media/azure-web-apps/settings-app-insights-01.png)

   * Opte por criar um novo recurso, a menos que você já tenha configurado um recurso do Application Insights para este aplicativo. 

     > [!NOTE]
     > Ao clicar em **OK** para criar o novo recurso, você será solicitado a **Aplicar configurações de monitoramento**. Se você selecionar **Continuar**, vinculará seu novo recurso do Application Insights ao seu serviço de aplicativo. Fazer isso também **disparará uma reinicialização do seu serviço de aplicativo**. 

     ![Instrumentar seu aplicativo Web](./media/azure-web-apps/create-resource-01.png)

2. Depois de especificar qual recurso usar, você pode escolher como deseja que Application Insights colete dados por plataforma para seu aplicativo. O .NET Core oferece uma **coleção recomendada** ou **desabilitada** para o .net Core 2,0, 2,1, 2,2 e 3,0.

    ![Escolha as opções por plataforma](./media/azure-web-apps/choose-options-new-net-core.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Em seu aplicativo Web do serviço de aplicativo em **configurações** > ,**selecione Application insights** > **habilitar**. O monitoramento baseado em agente do node. js está atualmente em versão prévia.

# <a name="java"></a>[Java](#tab/java)

Os aplicativos Web baseados em serviço de aplicativo Java atualmente não dão suporte ao monitoramento automático baseado em agente/extensão. Para habilitar o monitoramento para seu aplicativo Java, você precisa [instrumentar manualmente seu aplicativo](https://docs.microsoft.com/azure/azure-monitor/app/java-get-started).

# <a name="python"></a>[Python](#tab/python)

Os aplicativos Web baseados no serviço de aplicativo Python atualmente não dão suporte ao monitoramento automático baseado em agente/extensão. Para habilitar o monitoramento para seu aplicativo Python, você precisa [instrumentar manualmente seu aplicativo](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python).

---

## <a name="enable-client-side-monitoring"></a>Habilitar monitoramento do lado do cliente

# <a name="net"></a>[.NET](#tab/net)

O monitoramento no lado do cliente é opcional para ASP.NET. Para habilitar o monitoramento do lado do cliente:

* Selecione **configurações** > * * * * configurações do aplicativo * * * *
   * Em configurações do aplicativo, adicione um novo nome e **valor**de **configuração de aplicativo** :

     Nome: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valor: `true`

   * **Salve** as configurações e **Reinicie** seu aplicativo.

![Captura de tela da interface do usuário de configurações do aplicativo](./media/azure-web-apps/appinsights-javascript-enabled.png)

Para desabilitar o monitoramento do lado do cliente, remova o par de valor de chave associado das configurações do aplicativo ou defina o valor como false.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

O monitoramento no lado do cliente é **habilitado por padrão** para aplicativos .NET Core com a **coleção recomendada**, independentemente de a configuração do aplicativo ' APPINSIGHTS_JAVASCRIPT_ENABLED ' estar presente.

Se por algum motivo você quiser desabilitar o monitoramento no lado do cliente:

* Selecione **configurações** > **configurações do aplicativo**
   * Em configurações do aplicativo, adicione um novo nome e **valor**de **configuração de aplicativo** :

     nomes`APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valor: `false`

   * **Salve** as configurações e **Reinicie** seu aplicativo.

![Captura de tela da interface do usuário de configurações do aplicativo](./media/azure-web-apps/appinsights-javascript-disabled.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Para habilitar o monitoramento do lado do cliente para seu aplicativo node. js, você precisa [adicionar manualmente o SDK JavaScript do lado do cliente ao seu aplicativo](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

# <a name="java"></a>[Java](#tab/java)

Para habilitar o monitoramento do lado do cliente para seu aplicativo Java, você precisa [adicionar manualmente o SDK JavaScript do lado do cliente ao seu aplicativo](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

# <a name="python"></a>[Python](#tab/python)

Para habilitar o monitoramento do lado do cliente para seu aplicativo Python, você precisa [adicionar manualmente o SDK JavaScript do lado do cliente ao seu aplicativo](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

---

## <a name="automate-monitoring"></a>Automatizar monitoramento

Para habilitar a coleta de telemetria com Application Insights, somente as configurações do aplicativo precisam ser definidas:

   ![Configurações de aplicativo do serviço de aplicativo com configurações de Application Insights disponíveis](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definições de configurações do aplicativo

|Nome da configuração do aplicativo |  Definição | Valor |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Extensão principal, que controla o monitoramento do tempo de execução. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Somente no modo padrão, os recursos essenciais são habilitados para garantir o desempenho ideal. | `default` ou `recommended` |
|InstrumentationEngine_EXTENSION_VERSION | Controla se o mecanismo `InstrumentationEngine` de regravação binária será ativado. Essa configuração tem implicações de desempenho e afeta o tempo de início/inicialização frio. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Controla se o SQL & texto da tabela do Azure será capturado junto com as chamadas de dependência. Aviso de desempenho: o tempo de inicialização a frio do aplicativo será afetado. Essa configuração requer o `InstrumentationEngine`. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Configurações de aplicativo do serviço de aplicativo com Azure Resource Manager

As configurações de aplicativo para serviços de aplicativos podem ser gerenciadas e configuradas com [modelos de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Esse método pode ser usado ao implantar novos recursos do serviço de aplicativo com a automação Azure Resource Manager ou para modificar as configurações de recursos existentes.

A estrutura básica do JSON de configurações do aplicativo para um serviço de aplicativo está abaixo:

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]
```

Para obter um exemplo de um modelo de Azure Resource Manager com configurações de aplicativo definidas para Application Insights, esse [modelo](https://github.com/Andrew-MSFT/BasicImageGallery) pode ser útil, especificamente a seção que começa na [linha 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatize a criação de um recurso Application Insights e vincule ao seu serviço de aplicativo recém-criado.

Para criar um modelo de Azure Resource Manager com todas as configurações de Application Insights padrão configuradas, inicie o processo como se estivesse criando um novo aplicativo Web com Application Insights habilitado.

Selecionar **Opções de automação**

   ![Menu de criação do aplicativo Web do serviço de aplicativo](./media/azure-web-apps/create-web-app.png)

Essa opção gera o modelo de Azure Resource Manager mais recente com todas as configurações necessárias definidas.

  ![Modelo de aplicativo Web do serviço de aplicativo](./media/azure-web-apps/arm-template.png)

Abaixo está um exemplo, substitua todas as instâncias `AppMonitoredSite` de pelo nome do seu site:

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

### <a name="enabling-through-powershell"></a>Habilitando por meio do PowerShell

Para habilitar o monitoramento de aplicativos por meio do PowerShell, somente as configurações de aplicativo subjacentes precisam ser alteradas. Abaixo está um exemplo, que habilita o monitoramento de aplicativos para um site chamado "AppMonitoredSite" no grupo de recursos "AppMonitoredRG" e configura os dados a serem enviados para a chave de instrumentação "012345678-ABCD-EF01-2345-6789abcd".

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} # preserve non Application Insights application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights instrumentation key
$newAppSettings["APPLICATIONINSIGHTS_CONNECTION_STRING"] = "InstrumentationKey=012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights connection string
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Atualizar a extensão/agente de monitoramento

### <a name="upgrading-from-versions-289-and-up"></a>Atualizando das versões 2.8.9 e up

A atualização da versão 2.8.9 ocorre automaticamente, sem nenhuma ação adicional. Os novos bits de monitoramento são entregues em segundo plano ao serviço de aplicativo de destino e, na reinicialização do aplicativo, serão coletados.

Para verificar qual versão da extensão você está executando, visite`http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Captura de tela do caminho da URLhttp://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Atualização das versões 1.0.0-2.6.5

A partir da versão 2.8.9, a extensão de site pré-instalada é usada. Se você for uma versão anterior, poderá atualizar por meio de uma das duas maneiras:

* [Atualizar habilitando por meio do portal](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights). (Mesmo que você tenha a extensão de Application Insights para Azure App Service instalado, a interface do usuário mostrará apenas o botão **habilitar** . Nos bastidores, a extensão de site particular antigo será removida.)

* [Atualizar por meio do PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell):

    1. Defina as configurações do aplicativo para habilitar a extensão de site pré-instalada ApplicationInsightsAgent. Consulte [habilitando por meio do PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell).
    2. Remova manualmente a extensão de site particular chamada Application Insights extensão para Azure App serviço.

Se a atualização for feita a partir de uma versão anterior à 2.5.1, verifique se as DLLs ApplicationInsigths são removidas da pasta bin do aplicativo [consulte as etapas de solução de problemas](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).

## <a name="troubleshooting"></a>Solução de problemas

Abaixo está nosso guia de solução de problemas passo a passo para o monitoramento baseado em extensão/agente para aplicativos .NET e .NET Core em execução em serviços de Azure App.

> [!NOTE]
> Os aplicativos Java só têm suporte em serviços Azure App por meio da instrumentação baseada em SDK manual e, portanto, as etapas a seguir não se aplicam a esses cenários.

1. Verifique se o aplicativo é monitorado `ApplicationInsightsAgent`via.
    * Verifique se `ApplicationInsightsAgent_EXTENSION_VERSION` a configuração do aplicativo está definida com um valor de "~ 2".
2. Verifique se o aplicativo atende aos requisitos a serem monitorados.
    * Navegue para `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Captura de https://yoursitename.scm.azurewebsites/applicationinsights tela da página de resultados](./media/azure-web-apps/app-insights-sdk-status.png)

    * Confirme se o `Application Insights Extension Status` é`Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * Se não estiver em execução, siga as [instruções de monitoramento habilitar Application insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * Confirme se a origem do status existe e se tem a seguinte aparência:`Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Se um valor semelhante não estiver presente, significa que o aplicativo não está em execução no momento ou não tem suporte. Para garantir que o aplicativo esteja em execução, tente visitar manualmente os pontos de extremidade do aplicativo/URL do aplicativo, o que permitirá que as informações de tempo de execução fiquem disponíveis.

    * Confirme se `IKeyExists` é`true`
        * Se for `false`, adicione `APPINSIGHTS_INSTRUMENTATIONKEY` e `APPLICATIONINSIGHTS_CONNECTION_STRING` com seu GUID iKey às configurações do aplicativo.

    * Confirme se não há entradas para `AppAlreadyInstrumented`, `AppContainsDiagnosticSourceAssembly`e. `AppContainsAspNetTelemetryCorrelationAssembly`
        * Se qualquer uma dessas entradas existir, remova os seguintes pacotes do seu aplicativo: `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource`e `Microsoft.AspNet.TelemetryCorrelation`.

A tabela a seguir fornece uma explicação mais detalhada do que esses valores significam, suas causas subjacentes e correções recomendadas:

|Valor do problema|Explicação|Fix
|---- |----|---|
| `AppAlreadyInstrumented:true` | Esse valor indica que a extensão detectou que algum aspecto do SDK já está presente no aplicativo e será retirada. Pode ser devido a uma referência a `System.Diagnostics.DiagnosticSource`, ou `Microsoft.AspNet.TelemetryCorrelation``Microsoft.ApplicationInsights`  | Remova as referências. Algumas dessas referências são adicionadas por padrão de determinados modelos do Visual Studio, e as versões mais antigas do Visual Studio podem `Microsoft.ApplicationInsights`adicionar referências ao.
|`AppAlreadyInstrumented:true` | Se o aplicativo estiver direcionando o .NET Core 2,1 ou 2,2 e se referir a [Microsoft. AspNetCore. All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) metapackage, ele levará em Application insights e a extensão será retirada. | Os clientes no .NET Core 2.1, 2.2 são [recomendados](https://github.com/aspnet/Announcements/issues/287) a usar o meta-Package Microsoft. AspNetCore. app em vez disso.|
|`AppAlreadyInstrumented:true` | Esse valor também pode ser causado pela presença das DLLs acima na pasta do aplicativo de uma implantação anterior. | Limpe a pasta do aplicativo para garantir que essas DLLs sejam removidas. Verifique o diretório bin do seu aplicativo local e o diretório wwwroot no serviço de aplicativo. (Para verificar o diretório wwwroot do seu aplicativo Web do serviço de aplicativo: ferramentas avançadas (kudu) > console de depuração > CMD > home\site\wwwroot).
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Esse valor indica que a extensão detectou referências a `Microsoft.AspNet.TelemetryCorrelation` no aplicativo e será retirada. | Remova a referência.
|`AppContainsDiagnosticSourceAssembly**:true`|Esse valor indica que a extensão detectou referências a `System.Diagnostics.DiagnosticSource` no aplicativo e será retirada.| Remova a referência.
|`IKeyExists:false`|Esse valor indica que a chave de instrumentação não está presente em AppSetting, `APPINSIGHTS_INSTRUMENTATIONKEY`. Possíveis causas: os valores podem ter sido acidentalmente removidos, esquecidos para definir os valores no script de automação, etc. | Verifique se a configuração está presente nas configurações do aplicativo do serviço de aplicativo.

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>Não há suporte para APPINSIGHTS_JAVASCRIPT_ENABLED e urlCompression

Se você usar APPINSIGHTS_JAVASCRIPT_ENABLED = true nos casos em que o conteúdo é codificado, poderá obter erros como: 

- erro de regravação de URL 500
- 500,53 erro de módulo de regravação de URL com regras de regravação de mensagem de saída não podem ser aplicadas quando o conteúdo da resposta HTTP é codificado (' gzip '). 

Isso ocorre porque a configuração do aplicativo APPINSIGHTS_JAVASCRIPT_ENABLED está sendo definida como true e a codificação de conteúdo está presente ao mesmo tempo. Este cenário ainda não tem suporte. A solução alternativa é remover APPINSIGHTS_JAVASCRIPT_ENABLED das configurações do aplicativo. Infelizmente, isso significa que se a instrumentação de JavaScript do lado do cliente/navegador ainda for necessária, as referências manuais do SDK serão necessárias para suas páginas da Web. Siga as [instruções](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) para instrumentação manual com o SDK do JavaScript.

Para obter as informações mais recentes sobre a extensão/agente de Application Insights, Confira as [notas de versão](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

### <a name="php-and-wordpress-are-not-supported"></a>Não há suporte para PHP e WordPress

Não há suporte para sites PHP e WordPress. Atualmente, não há um SDK/agente com suporte oficialmente para o monitoramento do lado do servidor dessas cargas de trabalho. No entanto, instrumentar manualmente as transações do lado do cliente em um site do PHP ou do WordPress adicionando o JavaScript do lado do cliente às suas páginas da Web pode ser feito usando o [SDK do JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript). 

## <a name="next-steps"></a>Próximas etapas
* [Executar o criador de perfil em seu aplicativo ativo](../app/profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – monitorar o Azure Functions com o Application Insights
* [Permita que o diagnóstico do Azure](../platform/diagnostics-extension-to-application-insights.md) seja enviado ao Application Insights.
* [Monitore as métricas de integridade do serviço](../platform/data-platform.md) para se certificar de que o serviço esteja disponível e responsivo.
* [Receba notificações de alerta](../platform/alerts-overview.md) sempre que ocorrerem eventos operacionais ou métricas ultrapassarem um limite.
* Use o [Application Insights para aplicativos JavaScript e páginas Web](javascript.md) para obter a telemetria do cliente dos navegadores que visitam uma página da Web.
* [Configure testes de disponibilidade da Web](monitor-web-app-availability.md) para ser alertado se o seu site for desativado.
