---
title: Remover insights de aplicativos no Visual Studio - Azure Monitor
description: Como remover o Application Insights SDK para ASP.NET e ASP.NET Core no Visual Studio.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 1c9ff8d3d305645ac7d113421e2c6c5f8451bd2b
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80805099"
---
# <a name="how-to-remove-application-insights-in-visual-studio"></a>Como remover insights de aplicativos no Visual Studio

Este artigo mostrará como remover o ASP.NET e ASP.NET O SDK do Aplicativo Principal no Visual Studio.

Para remover o Application Insights, você precisará remover os pacotes e referências do NuGet da API em seu aplicativo. Você pode desinstalar pacotes NuGet usando o Console de Gerenciamento de Pacotes ou gerenciar a solução NuGet no Visual Studio. As seções a seguir mostrarão duas maneiras de remover pacotes NuGet e o que foi adicionado automaticamente em seu projeto. Certifique-se de confirmar que os arquivos adicionados e áreas com em seu próprio código em que você fez chamadas para a API são removidas.

## <a name="uninstall-using-the-package-management-console"></a>Desinstalar usando o console de gerenciamento de pacotes

# <a name="net"></a>[.NET](#tab/net)

1. Para abrir o Console de Gerenciamento de Pacotes, no menu superior selecione Ferramentas > NuGet Package Manager > Console do Gerenciador de Pacotes.
     
    ![No menu superior clique em Ferramentas > NuGet Package Manager > Console do Gerenciador de Pacotes](./media/remove-application-insights/package-manager.png)

    > [!NOTE]
    > Se a coleta de rastreamento estiver ativada, você precisará primeiro desinstalar o Microsoft.ApplicationInsights.TraceListener. Digite `Uninstall-package Microsoft.ApplicationInsights.TraceListener` então siga o passo abaixo para remover microsoft.ApplicationInsights.Web.

1. Digite o seguinte comando: `Uninstall-Package Microsoft.ApplicationInsights.Web -RemoveDependencies`

    Após a entrada no comando, o pacote Application Insights e todas as suas dependências serão desinstalados do projeto.
    
    ![Digite comando no console](./media/remove-application-insights/package-management-console.png)

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. Para abrir o Console de Gerenciamento de Pacotes, no menu superior selecione Ferramentas > NuGet Package Manager > Console do Gerenciador de Pacotes.

    ![No menu superior clique em Ferramentas > NuGet Package Manager > Console do Gerenciador de Pacotes](./media/remove-application-insights/package-manager.png)

1. Digite o seguinte comando: ` Uninstall-Package Microsoft.ApplicationInsights.AspNetCore -RemoveDependencies`

    Após a entrada no comando, o pacote Application Insights e todas as suas dependências serão desinstalados do projeto.

---

## <a name="uninstall-using-the-visual-studio-nugetui"></a>Desinstalar usando a UI Do Estúdio Visual NuGet

# <a name="net"></a>[.NET](#tab/net)

1. No *Solution Explorer* à direita, clique com o botão direito do mouse na **solução** e selecione **Gerenciar pacotes NuGet para solução**.

    Em seguida, você verá uma tela que permite editar todos os pacotes NuGet que fazem parte do projeto.
    
     ![Clique com o botão direito do mouse Solução, no Solution Explorer, e selecione Gerenciar pacotes NuGet para solução](./media/remove-application-insights/manage-nuget-framework.png)

    > [!NOTE]
    > Se a coleta de rastreamento estiver ativada, você precisa primeiro desinstalar o Microsoft.ApplicationInsights.TraceListener sem remover dependências selecionadas e, em seguida, seguir as etapas abaixo para desinstalar o Microsoft.ApplicationInsights.Web com as dependências de remover selecionadas.
    
1. Clique no pacote "Microsoft.ApplicationInsights.Web".À direita, verifique a caixa de seleção ao lado *do Projeto* para selecionar todos os projetos.
    
1. Para remover todas as dependências ao desinstalar, selecione o botão de isto **de opção de opções** abaixo da seção onde você selecionou o projeto.

    Em *Desinstalar Opções,* selecione a caixa de seleção ao lado *de Remover dependências*.

1. Selecionar **Desinstalar**.
    
    ![Verifique remover dependências e, em seguida, desinstale](./media/remove-application-insights/uninstall-framework.png)

    Uma caixa de diálogo será exibida que mostra todas as dependências a serem removidas do aplicativo.Selecione **ok** para desinstalar.
    
    ![Verifique remover dependências e, em seguida, desinstale](./media/remove-application-insights/preview-uninstall-framework.png)
    
1.  Depois que tudo estiver desinstalado, você ainda poderá ver "ApplicationInsights.config" e "AiHandleErrorAttribute.cs" no *Solution Explorer*.Você pode excluir os dois arquivos manualmente.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. No *Solution Explorer* à direita, clique com o botão direito do mouse na **solução** e selecione **Gerenciar pacotes NuGet para solução**.

    Em seguida, você verá uma tela que permite editar todos os pacotes NuGet que fazem parte do projeto.

    ![Clique com o botão direito do mouse Solução, no Solution Explorer, e selecione Gerenciar pacotes NuGet para solução](./media/remove-application-insights/manage-nuget-core.png)

1. Clique no pacote "Microsoft.ApplicationInsights.AspNetCore". À direita, marque a caixa de seleção ao lado de *Projeto* para selecionar todos os projetos e **selecione Desinstalar**.

    ![Verifique remover dependências e, em seguida, desinstale](./media/remove-application-insights/uninstall-core.png)

---

## <a name="what-is-created-when-you-add-application-insights"></a>O que é criado quando você adiciona Insights de aplicativos

Quando você adiciona Insights de aplicativo ao seu projeto, ele cria arquivos e adiciona código a alguns de seus arquivos. A única desinstalação dos Pacotes NuGet nem sempre descartará os arquivos e o código. Para remover totalmente os Insights do aplicativo, você deve verificar e excluir manualmente o código adicionado ou os arquivos, juntamente com quaisquer chamadas de API adicionadas em seu projeto.

# <a name="net"></a>[.NET](#tab/net)

Quando você adiciona telemetria de insights de aplicativos a um projeto de ASP.NET do Visual Studio, ele adiciona os seguintes arquivos:

- ApplicationInsights.config
- AiHandleErrorAttribute.cs

As seguintes peças de código são adicionadas:

- [Nome do seu projeto].csproj

    ```C#
     <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4</ApplicationInsightsResourceId>
    ```

- Pacotes.config

    ```xml
    <packages>
    ...
    
      <package id="Microsoft.ApplicationInsights" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Agent.Intercept" version="2.4.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.DependencyCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.PerfCounterCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Web" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" version="2.12.0" targetFramework="net472" />
    
      <package id="Microsoft.AspNet.TelemetryCorrelation" version="1.0.7" targetFramework="net472" />
    
      <package id="System.Buffers" version="4.4.0" targetFramework="net472" />
      <package id="System.Diagnostics.DiagnosticSource" version="4.6.0" targetFramework="net472" />
      <package id="System.Memory" version="4.5.3" targetFramework="net472" />
      <package id="System.Numerics.Vectors" version="4.4.0" targetFramework="net472" />
      <package id="System.Runtime.CompilerServices.Unsafe" version="4.5.2" targetFramework="net472" />
    ...
    </packages>
    ```

- Layout.cshtml

    Se o seu projeto tiver um arquivo Layout.cshtml, o código abaixo será adicionado.
    
    ```html
    <head>
    ...
        <script type = 'text/javascript' >
            var appInsights=window.appInsights||function(config)
            {
                function r(config){ t[config] = function(){ var i = arguments; t.queue.push(function(){ t[config].apply(t, i)})} }
                var t = { config:config},u=document,e=window,o='script',s=u.createElement(o),i,f;for(s.src=config.url||'//az416426.vo.msecnd.net/scripts/a/ai.0.js',u.getElementsByTagName(o)[0].parentNode.appendChild(s),t.cookie=u.cookie,t.queue=[],i=['Event','Exception','Metric','PageView','Trace','Ajax'];i.length;)r('track'+i.pop());return r('setAuthenticatedUserContext'),r('clearAuthenticatedUserContext'),config.disableExceptionTracking||(i='onerror',r('_'+i),f=e[i],e[i]=function(config, r, u, e, o) { var s = f && f(config, r, u, e, o); return s !== !0 && t['_' + i](config, r, u, e, o),s}),t
            }({
                instrumentationKey:'00000000-0000-0000-0000-000000000000'
            });
            
            window.appInsights=appInsights;
            appInsights.trackPageView();
        </script>
    ...
    </head>
    ```

- ConnectedService.json

    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=613413"
      }
    }
    ```

- FilterConfig.cs

    ```csharp
            public static void RegisterGlobalFilters(GlobalFilterCollection filters)
            {
                filters.Add(new ErrorHandler.AiHandleErrorAttribute());// This line was added
            }
    ```

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Quando você adiciona telemetria de insights de aplicativos a um projeto de modelo do Visual Studio ASP.NET Core, ele adiciona o seguinte código:

- [Nome do seu projeto].csproj

    ```csharp
      <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4core</ApplicationInsightsResourceId>
      </PropertyGroup>
    
      <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.12.0" />
      </ItemGroup>
    
      <ItemGroup>
        <WCFMetadata Include="Connected Services" />
      </ItemGroup>
    ```

- Appsettings.json:

    ```json
    "ApplicationInsights": {
        "InstrumentationKey": "00000000-0000-0000-0000-000000000000"
    ```

- ConnectedService.json
    
    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
      }
    }
    ```
- Startup.cs

    ```csharp
       public void ConfigureServices(IServiceCollection services)
            {
                services.AddRazorPages();
                services.AddApplicationInsightsTelemetry(); // This is added
            }
    ```

---

## <a name="next-steps"></a>Próximas etapas

- [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)