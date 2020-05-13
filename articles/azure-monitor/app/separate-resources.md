---
title: Como projetar sua implantação de Application Insights – um versus muitos recursos?
description: Direcione a telemetria para diferentes recursos para stamps de desenvolvimento, teste e produção.
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 6df6622cbba251c221533c3307dc194f08e871fb
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125682"
---
# <a name="how-many-application-insights-resources-should-i-deploy"></a>Quantos recursos de Application Insights devo implantar

Ao desenvolver a próxima versão de um aplicativo Web, não é bom misturar as telemetrias do da nova versão e da versão já lançada do [Application Insights](../../azure-monitor/app/app-insights-overview.md). Para evitar confusão, envie a telemetria de diferentes estágios de desenvolvimento a fim de separar os recursos do Application Insights, com chaves de instrumentação separadas (ikeys). Para facilitar a alteração da chave de instrumentação, quando uma versão muda de um estágio para outro, pode ser útil definir a ikey no código em vez de no arquivo de configuração.

(Se o sistema for um Serviço de Nuvem do Azure, haverá [outro método de configuração de ikeys separados](../../azure-monitor/app/cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Sobre recursos e chaves de instrumentação

Ao configurar o monitoramento do Application Insights para seu aplicativo Web, você cria um *recurso* do Application Insights no Microsoft Azure. Abra esse recurso no portal do Azure para ver e analisar a telemetria coletada de seu aplicativo. O recurso é identificado por uma *chave de instrumentação* (ikey). Ao instalar o pacote do Application Insights para monitorar seu aplicativo, você o configura com a chave de instrumentação, assim ele sabe para onde enviar a telemetria.

Cada recurso de Application Insights vem com métricas que estão disponíveis fora de caixa. Se os componentes completamente separados reportarem para o mesmo recurso de Application Insights, essas métricas talvez não façam sentido para o painel/alerta.

### <a name="when-to-use-a-single-application-insights-resource"></a>Quando usar um único recurso de Application Insights

-   Para componentes de aplicativos que são implantados juntos. Normalmente desenvolvido por uma única equipe, gerenciada pelo mesmo conjunto de usuários DevOps/ITOps.
-   Se fizer sentido agregar KPIs (indicadores chave de desempenho), como durações de resposta, taxas de falha no painel, etc., em todos eles por padrão (você pode optar por segmentar por nome de função na experiência de Metrics Explorer).
-   Se não houver necessidade de gerenciar o controle de acesso baseado em função (RBAC) de forma diferente entre os componentes do aplicativo.
-   Se você não precisar de critérios de alerta de métricas diferentes entre os componentes.
-   Se você não precisar gerenciar exportações contínuas de forma diferente entre os componentes.
-   Se você não precisar gerenciar a cobrança/cotas de forma diferente entre os componentes.
-   Se não houver problema, uma chave de API terá o mesmo acesso aos dados de todos os componentes. E 10 chaves de API são suficientes para as necessidades em todas elas.
-   Se não houver problema com as mesmas configurações de detecção inteligente e de integração de item de trabalho em todas as funções.

### <a name="other-things-to-keep-in-mind"></a>Outras coisas a ter em mente

-   Talvez seja necessário adicionar código personalizado para garantir que valores significativos sejam definidos no atributo [Cloud_RoleName](https://docs.microsoft.com/azure/azure-monitor/app/app-map?tabs=net#set-cloud-role-name) . Sem valores significativos definidos para esse atributo, *nenhuma* das experiências do portal funcionará.
- Para aplicativos Service Fabric e serviços de nuvem clássicos, o SDK lê automaticamente do ambiente de função do Azure e os define. Para todos os outros tipos de aplicativos, provavelmente você precisará definir isso explicitamente.
-   A experiência de métricas ao vivo não dá suporte à divisão por nome de função.

## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a> Chave de instrumentação dinâmica

Para facilitar a alteração da ikey à medida que o código percorre os estágios de produção, a defina no código em vez de no arquivo de configuração.

Defina a chave em um método de inicialização como global.aspx.cs em um serviço ASP.NET:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

Nesse exemplo, as ikeys para os diferentes recursos são colocadas em diferentes versões do arquivo de configuração da Web. Trocar o arquivo de configuração da Web, que pode ser realizado como parte do script versão, alternará o recurso de destino.

### <a name="web-pages"></a>Páginas da Web
O iKey também é usado nas páginas da Web do seu aplicativo, no [script que você obteve no painel de início rápido](../../azure-monitor/app/javascript.md). Em vez de codificá-la literalmente no script, gere-a a partir do estado do servidor. Por exemplo, em um aplicativo ASP.NET:

*JavaScript no Razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="create-additional-application-insights-resources"></a>Criar recursos adicionais do Application Insights

Para criar um recurso do Application insights, siga o [Guia de criação de recursos](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

### <a name="getting-the-instrumentation-key"></a>Obter a chave de instrumentação
A chave de instrumentação identifica o recurso que você criou.

Você precisará das chaves de instrumentação de todos os recursos aos quais seu aplicativo enviará dados.

## <a name="filter-on-build-number"></a>Filtrar por número de compilação
Quando publicar uma nova versão do seu aplicativo, você desejará ser capaz de separar a telemetria das compilações diferentes.

Você pode definir a propriedade de versão do aplicativo para que possa filtrar resultados da [pesquisa](../../azure-monitor/app/diagnostic-search.md) e do [Metrics Explorer](../../azure-monitor/platform/metrics-charts.md).

Há vários métodos diferentes de definir a propriedade de Versão do aplicativo.

* Definir diretamente:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Defina a quebra automática de linha em um [inicializador de telemetria](../../azure-monitor/app/api-custom-events-metrics.md#defaults) para garantir que todas as instâncias de TelemetryClient sejam configuradas de forma consistente.
* [ASP.NET] Definir a versão em `BuildInfo.config`. O módulo da Web selecionará a versão do nó BuildLabel. Inclua esse arquivo no seu projeto e não se esqueça de definir a propriedade Copy Always no Gerenciador de Soluções.

    ```XML

    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] Gerar automaticamente BuildInfo.config no MSBuild. Para fazer isso, adicione algumas linhas ao seu arquivo `.csproj`:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Isso gera um arquivo chamado *yourProjectName*. BuildInfo. config. o processo de publicação o renomeia para BuildInfo. config.

    O rótulo da compilação contém um espaço reservado (AutoGen_...) quando você cria com o Visual Studio. Mas quando compilado com o MSBuild, ele é preenchido com o número de versão correta.

    Para permitir que o MSBuild gere números de versão, defina a versão como `1.0.*` em AssemblyReference.cs

## <a name="version-and-release-tracking"></a>Versão e controle de versão
Para controlar a versão do aplicativo, certifique-se de `buildinfo.config` é gerado pelo processo de Microsoft Build Engine. Em seu `.csproj` arquivo, adicione:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

Quando ele tem as informações de compilação, o módulo da web Application Insights adiciona automaticamente **Versão do aplicativo** como uma propriedade para cada item de telemetria. Isso permite que você filtre por versão ao executar [pesquisas de diagnóstico](../../azure-monitor/app/diagnostic-search.md) ou ao [explorar métricas](../../azure-monitor/platform/metrics-charts.md).

No entanto, observe que o número de versão da compilação é gerado apenas pelo Microsoft Build Engine, não pelo desenvolvedor Build do Visual Studio.

### <a name="release-annotations"></a>Anotações da versão
Se usar o Azure DevOps, você poderá [obter um marcador de anotação](../../azure-monitor/app/annotations.md) adicionado a seus gráficos sempre que lançar uma nova versão. A imagem a seguir mostra como esse marcador é exibido.

## <a name="next-steps"></a>Próximas etapas

* [Recursos compartilhados para várias funções](../../azure-monitor/app/app-map.md)
* [Criar um Inicializador de Telemetria para distinguir variantes A | B](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
