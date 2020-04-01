---
title: Monitorar desempenho dos serviços de aplicativos do Azure | Microsoft Docs
description: Monitoramento do desempenho de aplicativos para serviços de aplicativos do Azure. Mapeie o tempo de carga e o tempo de resposta, as informações de dependência e defina alertas sobre o desempenho.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: d2134e059a446c18108e8dd16bcc74504b42b15a
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437162"
---
# <a name="monitor-azure-app-service-performance"></a>Monitorar o desempenho do Serviço de Aplicativo do Azure

Ativar o monitoramento em seus aplicativos web baseados em ASP.NET e ASP.NET Core em execução no [Azure App Services](https://docs.microsoft.com/azure/app-service/) agora é mais fácil do que nunca. Considerando que anteriormente você precisava instalar manualmente uma extensão do site, a extensão/agente mais recente agora está incorporada à imagem do serviço do aplicativo por padrão. Este artigo irá orientar você sobre a habilitação do monitoramento do Application Insights, bem como fornecer orientações preliminares para automatizar o processo para implantações em larga escala.

> [!NOTE]
> A adição manual de uma extensão do site do Application Insights através **de extensões de ferramentas** > **de** desenvolvimento é preterida. Este método de instalação de extensão dependia de atualizações manuais para cada nova versão. A versão estável mais recente da extensão está agora [pré-instalada](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) como parte da imagem do App Service. Os arquivos estão `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` localizados e são atualizados automaticamente a cada versão estável. Se você seguir as instruções baseadas no agente para habilitar o monitoramento abaixo, ele removerá automaticamente a extensão depreciada para você.

## <a name="enable-application-insights"></a>Habilitar o Application Insights

Existem duas maneiras de habilitar o monitoramento de aplicativos para aplicativos hospedados no Azure App Services:

* **Monitoramento de aplicativos baseado em agente** (ApplicationInsightsAgent).  
    * Este método é o mais fácil de habilitar, e nenhuma configuração avançada é necessária. É muitas vezes referido como monitoramento "runtime". Para o Azure App Services, recomendamos, no mínimo, habilitar esse nível de monitoramento e, em seguida, com base no seu cenário específico, você pode avaliar se é necessário um monitoramento mais avançado através de instrumentação manual.

* **Instrumentar manualmente o aplicativo através do código,** instalando o Application Insights SDK.

    * Essa abordagem é muito mais personalizável, mas requer [a adição de uma dependência dos pacotes SDK NuGet do Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Este método, também significa que você tem que gerenciar as atualizações para a versão mais recente dos pacotes você mesmo.

    * Se você precisar fazer chamadas de API personalizadas para rastrear eventos/dependências não capturados por padrão com monitoramento baseado em agente, você precisará usar esse método. Confira a [API para eventos personalizados e artigo de métricas](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) para saber mais. Esta também é atualmente a única opção suportada para cargas de trabalho baseadas em Linux.

> [!NOTE]
> Se for detectado o monitoramento baseado em agente e a instrumentação manual baseada em SDK, apenas as configurações de instrumentação manual serão honradas. Isto é para evitar que dados duplicados sejam enviados. Para saber mais sobre isso, confira a [seção de solução de problemas](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) abaixo.

## <a name="enable-agent-based-monitoring"></a>Habilite o monitoramento baseado em agentes

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> A combinação de APPINSIGHTS_JAVASCRIPT_ENABLED e urlCompression não é suportada. Para obter mais informações, consulte a explicação na [seção de solução de problemas](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).


1. **Selecione Insights de aplicativos** no painel de controle do Azure para o serviço do aplicativo.

    ![Em Configurações, escolha Insights do aplicativo](./media/azure-web-apps/settings-app-insights-01.png)

   * Opte por criar um novo recurso, a menos que você já tenha configurado um recurso do Application Insights para este aplicativo. 

     > [!NOTE]
     > Ao clicar em **OK** para criar o novo recurso, você será solicitado a **Aplicar configurações de monitoramento**. Se você selecionar **Continuar**, vinculará seu novo recurso do Application Insights ao seu serviço de aplicativo. Fazer isso também **disparará uma reinicialização do seu serviço de aplicativo**. 

     ![Instrumentar seu aplicativo Web](./media/azure-web-apps/create-resource-01.png)

2. Depois de especificar qual recurso usar, você pode escolher como deseja que o Application Insights colete dados por plataforma para seu aplicativo. ASP.NET monitoramento de aplicativos está on-default com dois níveis diferentes de coleta.

    ![Escolha as opções por plataforma](./media/azure-web-apps/choose-options-new.png)

   * O nível **de coleta básico** .NET oferece recursos APM essenciais de instância única.

   * O nível **Coleção recomendada** do .NET:
       * Adiciona as tendências de uso de CPU, memória e E/S.
       * Correlaciona microsserviços entre limites de solicitação/dependência.
       * Coleta as tendências de uso e permite a correlação entre resultados de disponibilidade e transações.
       * Coleta as exceções não tratadas pelo processo de host.
       * Aumenta a precisão de métricas de APM com carga quando a amostragem é usada.

3. Para configurar configurações como amostragem, que você pode controlar anteriormente através do arquivo applicationinsights.config, agora você pode interagir com essas mesmas configurações através de configurações de aplicativo com um prefixo correspondente. 

    * Por exemplo, para alterar a porcentagem inicial de amostragem, você pode criar uma configuração de aplicativo de: `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` e um valor de `100`.

    * Para a lista de configurações de processador de telemetria de amostragem adaptativa suportadas, você pode consultar o [código](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) e a [documentação associada](https://docs.microsoft.com/azure/azure-monitor/app/sampling).

# <a name="net-core"></a>[.NET Core](#tab/netcore)

As seguintes versões do .NET Core são suportadas: ASP.NET Core 2.0, ASP.NET Core 2.1, ASP.NET Core 2.2, ASP.NET Core 3.0

O direcionamento completo do framework completo do .NET Core, a implantação independente e os aplicativos baseados no Linux não são **suportados** com monitoramento baseado em agente/extensão. ([Instrumentação manual](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) via código funcionará em todos os cenários anteriores.)

1. **Selecione Insights de aplicativos** no painel de controle do Azure para o serviço do aplicativo.

    ![Em Configurações, escolha Insights do aplicativo](./media/azure-web-apps/settings-app-insights-01.png)

   * Opte por criar um novo recurso, a menos que você já tenha configurado um recurso do Application Insights para este aplicativo. 

     > [!NOTE]
     > Ao clicar em **OK** para criar o novo recurso, você será solicitado a **Aplicar configurações de monitoramento**. Se você selecionar **Continuar**, vinculará seu novo recurso do Application Insights ao seu serviço de aplicativo. Fazer isso também **disparará uma reinicialização do seu serviço de aplicativo**. 

     ![Instrumentar seu aplicativo Web](./media/azure-web-apps/create-resource-01.png)

2. Depois de especificar qual recurso usar, você pode escolher como deseja que o Application Insights colete dados por plataforma para o seu aplicativo. .NET Core oferece **coleta recomendada** ou **desativada** para .NET Core 2.0, 2.1, 2.2 e 3.0.

    ![Escolha as opções por plataforma](./media/azure-web-apps/choose-options-new-net-core.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

A partir de dentro do aplicativo web App Service em **Configurações** > **selecione 'Insights de aplicativos'** > **Habilitar**. O monitoramento baseado em agente node.js está atualmente em visualização.

# <a name="java"></a>[Java](#tab/java)

Os aplicativos web baseados no Java App Service não suportam atualmente o monitoramento automático baseado em agente/extensão. Para habilitar o monitoramento para o aplicativo Java, você precisa [instrumentar manualmente seu aplicativo](https://docs.microsoft.com/azure/azure-monitor/app/java-get-started).

# <a name="python"></a>[Python](#tab/python)

Os aplicativos web baseados no Python App Service não suportam atualmente o monitoramento automático baseado em agente/extensão. Para habilitar o monitoramento para o aplicativo Python, você precisa [instrumentar manualmente sua aplicação](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python).

---

## <a name="enable-client-side-monitoring"></a>Habilitar monitoramento do lado do cliente

# <a name="net"></a>[.NET](#tab/net)

O monitoramento do lado do cliente é opt-in para ASP.NET. Para habilitar o monitoramento do lado do cliente:

* Selecione **Configurações** >** **Configurações do aplicativo****
   * Em Configurações de aplicativo, adicione um novo nome e **valor** **de definição de aplicativo:**

     Nome: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valor: `true`

   * **Salve** as configurações e **Reinicie** seu aplicativo.

![Captura de tela das configurações do aplicativo Interface do UI](./media/azure-web-apps/appinsights-javascript-enabled.png)

Para desativar o monitoramento do lado do cliente, remova o par de valores de chave associado das configurações do aplicativo ou defina o valor como falso.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

O monitoramento do lado do cliente é **habilitado por padrão** para aplicativos .NET Core com **coleta recomendada,** independentemente de a configuração do aplicativo 'APPINSIGHTS_JAVASCRIPT_ENABLED' estar presente.

Se, por algum motivo, você quiser desativar o monitoramento do lado do cliente:

* Selecione **configurações** > **Configurações Configurações Configurações**
   * Em Configurações de aplicativo, adicione um novo nome e **valor** **de definição de aplicativo:**

     Nome:`APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valor: `false`

   * **Salve** as configurações e **Reinicie** seu aplicativo.

![Captura de tela das configurações do aplicativo Interface do UI](./media/azure-web-apps/appinsights-javascript-disabled.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Para habilitar o monitoramento do lado do cliente para o aplicativo Node.js, você precisa [adicionar manualmente o JavaScript SDK do lado do cliente ao seu aplicativo](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

# <a name="java"></a>[Java](#tab/java)

Para habilitar o monitoramento do lado do cliente para o aplicativo Java, você precisa [adicionar manualmente o JavaScript SDK do lado do cliente ao seu aplicativo](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

# <a name="python"></a>[Python](#tab/python)

Para habilitar o monitoramento do lado do cliente para o aplicativo Python, você precisa [adicionar manualmente o JavaScript SDK do lado do cliente ao seu aplicativo](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

---

## <a name="automate-monitoring"></a>Automatizar monitoramento

Para habilitar a coleta de telemetria com o Application Insights, apenas as configurações do aplicativo precisam ser definidas:

   ![Configurações do aplicativo de serviço de aplicativos com configurações disponíveis de insights de aplicativos](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definições de configurações do aplicativo

|Nome da configuração do aplicativo |  Definição | Valor |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Extensão principal, que controla o monitoramento em tempo de execução. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Apenas no modo padrão, os recursos essenciais são ativados para garantir o desempenho ideal. | `default` ou `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Controla se o motor `InstrumentationEngine` binário de reescrita será ligado. Essa configuração tem implicações de desempenho e afeta o tempo de início/inicialização a frio. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Controles se o texto da tabela SQL & azure será capturado juntamente com as chamadas de dependência. Aviso de desempenho: o tempo de início do aplicativo frio será afetado. Esta configuração `InstrumentationEngine`requer o . | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Configurações do aplicativo de serviço de aplicativos com o Azure Resource Manager

As configurações do aplicativo para serviços de aplicativos podem ser gerenciadas e configuradas com [os modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Esse método pode ser usado ao implantar novos recursos do App Service com a automação do Azure Resource Manager ou para modificar as configurações dos recursos existentes.

A estrutura básica das configurações de aplicativo JSON para um serviço de aplicativo está abaixo:

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

Para um exemplo de um modelo do Azure Resource Manager com configurações de aplicativo configuradas para insights de aplicativos, este [modelo](https://github.com/Andrew-MSFT/BasicImageGallery) pode ser útil, especificamente a seção que começa na [linha 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatize a criação de um recurso do Application Insights e vincule ao seu recém-criado Serviço de Aplicativo.

Para criar um modelo do Azure Resource Manager com todas as configurações padrão do Application Insights configuradas, inicie o processo como se você fosse criar um novo Aplicativo da Web com o Application Insights ativado.

Selecione **opções de automação**

   ![Menu de criação de aplicativos web do App Service](./media/azure-web-apps/create-web-app.png)

Essa opção gera o modelo mais recente do Azure Resource Manager com todas as configurações necessárias configuradas.

  ![Modelo de aplicativo de aplicativo web](./media/azure-web-apps/arm-template.png)

Abaixo está uma amostra, `AppMonitoredSite` substitua todas as instâncias com o nome do seu site:

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

### <a name="enabling-through-powershell"></a>Habilitando através do PowerShell

Para permitir o monitoramento do aplicativo através do PowerShell, apenas as configurações subjacentes do aplicativo precisam ser alteradas. Abaixo está uma amostra, que permite o monitoramento de aplicativos para um site chamado "AppMonitoredSite" no grupo de recursos "AppMonitoredRG", e configura dados a serem enviados para a chave de instrumentação "012345678-abcd-ef01-2345-6789abcd".

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

## <a name="upgrade-monitoring-extensionagent"></a>Atualização de extensão/agente de monitoramento

### <a name="upgrading-from-versions-289-and-up"></a>Atualizando das versões 2.8.9 ou superior

A atualização da versão 2.8.9 acontece automaticamente, sem ações adicionais. Os novos bits de monitoramento são entregues em segundo plano para o serviço de aplicativo de destino e, na reinicialização do aplicativo, serão recolhidos.

Para verificar qual versão da extensão você está executando visite`http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Captura de tela do caminho da urlhttp://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Atualização das versões 1.0.0 - 2.6.5

A partir da versão 2.8.9, a extensão do local pré-instalada é usada. Se você é uma versão anterior, você pode atualizar através de uma das duas maneiras:

* [Atualize-se através do portal](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights). (Mesmo que você tenha a extensão Application Insights para o Azure App Service instalada, a interface do usuário mostra apenas o botão **Ativar.** Nos bastidores, a antiga extensão do site privado será removida.)

* [Upgrade através do PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell):

    1. Defina as configurações do aplicativo para ativar a extensão do site pré-instalada ApplicationInsightsAgent. Consulte [Habilitação através de powershell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell).
    2. Remova manualmente a extensão do site privado chamada extensão Application Insights para o Azure App Service.

Se a atualização for feita a partir de uma versão anterior a 2.5.1, verifique se as dlls ApplicationInsigths serão removidas da pasta do bin de [aplicativos, veja etapas de solução de problemas](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).

## <a name="troubleshooting"></a>Solução de problemas

Abaixo está nosso guia passo-a-passo para monitoramento baseado em extensão/agente para aplicativos baseados em .NET e .NET Core em execução no Azure App Services.

> [!NOTE]
> Os aplicativos Java só são suportados no Azure App Services por meio de instrumentação manual baseada em SDK e, portanto, as etapas abaixo não se aplicam a esses cenários.

1. Verifique se a aplicação `ApplicationInsightsAgent`é monitorada via .
    * Verifique `ApplicationInsightsAgent_EXTENSION_VERSION` se a configuração do aplicativo está definida como um valor de "~2".
2. Certifique-se de que o aplicativo atenda aos requisitos a serem monitorados.
    * Navegue para `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Captura https://yoursitename.scm.azurewebsites/applicationinsights de tela da página de resultados](./media/azure-web-apps/app-insights-sdk-status.png)

    * Confirme `Application Insights Extension Status` que o é`Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * Se ele não estiver em execução, siga as [instruções de monitoramento do Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * Confirme se a fonte de status existe e parece:`Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Se um valor semelhante não estiver presente, significa que o aplicativo não está em execução ou não é suportado. Para garantir que o aplicativo esteja em execução, tente visitar manualmente os pontos finais do aplicativo url/application, o que permitirá que as informações em tempo de execução fiquem disponíveis.

    * Confirme `IKeyExists` que é`true`
        * Se for, `false` `APPINSIGHTS_INSTRUMENTATIONKEY` adicione `APPLICATIONINSIGHTS_CONNECTION_STRING` e com sua orientação ikey para as configurações do aplicativo.

    * Confirme se não há `AppAlreadyInstrumented` `AppContainsDiagnosticSourceAssembly`entradas `AppContainsAspNetTelemetryCorrelationAssembly`para , e .
        * Se alguma dessas entradas existir, remova os seguintes pacotes da sua aplicação: `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource`e `Microsoft.AspNet.TelemetryCorrelation`.

A tabela abaixo fornece uma explicação mais detalhada do que esses valores significam, suas causas subjacentes e correções recomendadas:

|Valor do problema|Explicação|Fix
|---- |----|---|
| `AppAlreadyInstrumented:true` | Esse valor indica que a extensão detectou que algum aspecto do SDK já está presente no Aplicativo, e fará o back-off. Pode ser devido a `System.Diagnostics.DiagnosticSource`uma `Microsoft.AspNet.TelemetryCorrelation`referência a, ou`Microsoft.ApplicationInsights`  | Remova as referências. Algumas dessas referências são adicionadas por padrão de certos modelos do Visual `Microsoft.ApplicationInsights`Studio, e versões mais antigas do Visual Studio podem adicionar referências a .
|`AppAlreadyInstrumented:true` | Se o aplicativo estiver mirando o .NET Core 2.1 ou 2.2, e se referir ao [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) meta-package, então ele traz insights de aplicativos e a extensão fará o backup. | Recomenda-se que os clientes do .NET Core 2.1,2.2 [usem](https://github.com/aspnet/Announcements/issues/287) o meta-pacote Microsoft.AspNetCore.App.|
|`AppAlreadyInstrumented:true` | Esse valor também pode ser causado pela presença das dlls acima na pasta do aplicativo a partir de uma implantação anterior. | Limpe a pasta do aplicativo para garantir que essas dlls sejam removidas. Verifique tanto o diretório bin do seu aplicativo local quanto o diretório wwwroot no Serviço de Aplicativos. (Para verificar o diretório wwwroot do seu aplicativo web App Service: Advanced Tools (Kudu) > Console Debug > CMD > home\site\wwwroot).
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Esse valor indica que a `Microsoft.AspNet.TelemetryCorrelation` extensão detectou referências no aplicativo e fará o back-off. | Remova a referência.
|`AppContainsDiagnosticSourceAssembly**:true`|Esse valor indica que a `System.Diagnostics.DiagnosticSource` extensão detectou referências no aplicativo e fará o back-off.| Remova a referência.
|`IKeyExists:false`|Este valor indica que a chave de instrumentação `APPINSIGHTS_INSTRUMENTATIONKEY`não está presente no AppSetting, . Possíveis causas: Os valores podem ter sido removidos acidentalmente, esqueceram de definir os valores no script de automação, etc. | Certifique-se de que a configuração está presente nas configurações do aplicativo App Service.

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>APPINSIGHTS_JAVASCRIPT_ENABLED e urlCompression não são suportados

Se você usar APPINSIGHTS_JAVASCRIPT_ENABLED=verdadeiro nos casos em que o conteúdo é codificado, você pode obter erros como: 

- Erro de regravação de URL de 500
- 500.53 Erro de reescrita de URL com regras de reescrita de saída de mensagem não pode ser aplicado quando o conteúdo da resposta HTTP é codificado ('gzip'). 

Isso se deve à configuração do aplicativo APPINSIGHTS_JAVASCRIPT_ENABLED sendo definida como verdadeira e a codificação de conteúdo estiver presente ao mesmo tempo. Este cenário ainda não está suportado. A solução é remover APPINSIGHTS_JAVASCRIPT_ENABLED das configurações do aplicativo. Infelizmente, isso significa que, se a instrumentação JavaScript do lado do cliente/navegador ainda for necessária, referências manuais de SDK são necessárias para suas páginas da Web. Siga as [instruções](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) de instrumentação manual com o JavaScript SDK.

Para obter as informações mais recentes sobre o agente/extensão Application Insights, confira as [notas de versão](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

### <a name="php-and-wordpress-are-not-supported"></a>PHP e WordPress não são suportados

Os sites PHP e WordPress não são suportados. Atualmente, não há SDK/agente oficialmente suportado para monitoramento do lado do servidor dessas cargas de trabalho. No entanto, instrumentar manualmente transações do lado do cliente em um site PHP ou WordPress adicionando o javascript do lado do cliente às suas páginas da Web pode ser realizado usando o [JavaScript SDK](https://docs.microsoft.com/azure/azure-monitor/app/javascript). 

## <a name="next-steps"></a>Próximas etapas
* [Executar o criador de perfil em seu aplicativo ativo](../app/profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – monitorar o Azure Functions com o Application Insights
* [Permita que o diagnóstico do Azure](../platform/diagnostics-extension-to-application-insights.md) seja enviado ao Application Insights.
* [Monitore as métricas de integridade do serviço](../platform/data-platform.md) para se certificar de que o serviço esteja disponível e responsivo.
* [Receba notificações de alerta](../platform/alerts-overview.md) sempre que ocorrerem eventos operacionais ou métricas ultrapassarem um limite.
* Use o [Application Insights para aplicativos JavaScript e páginas Web](javascript.md) para obter a telemetria do cliente dos navegadores que visitam uma página da Web.
* [Configure testes de disponibilidade da Web](monitor-web-app-availability.md) para ser alertado se o seu site for desativado.
