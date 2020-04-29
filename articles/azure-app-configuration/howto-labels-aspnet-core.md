---
title: Usar configuração por ambiente
titleSuffix: Azure App Configuration
description: Usar rótulos para fornecer valores de configuração por ambiente
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: d1a3323154fc73b453e041a064cfcd36299b8a08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80056796"
---
# <a name="use-labels-to-enable-different-configurations-for-different-environments"></a>Use rótulos para habilitar configurações diferentes para ambientes diferentes

Muitos aplicativos precisam usar configurações diferentes para ambientes diferentes. Suponha que um aplicativo tenha um valor de configuração que defina a cadeia de conexão a ser usada para seu banco de dados de back-end. Os desenvolvedores do aplicativo usam um banco de dados diferente daquele usado na produção. A cadeia de conexão do banco de dados usada pelo aplicativo deve mudar à medida que o aplicativo passa do desenvolvimento para a produção.

Na configuração do Azure App, você pode usar *Rótulos* para definir valores diferentes para a mesma chave. Por exemplo, você pode definir uma única chave com valores diferentes para *desenvolvimento* e *produção*. Você pode especificar quais rótulo (s) carregar ao se conectar à configuração do aplicativo.

Para demonstrar essa funcionalidade, modificaremos o aplicativo Web criado no [início rápido: criar um aplicativo ASP.NET Core com Azure app configuração](./quickstart-aspnet-core-app.md) para usar diferentes definições de configuração para desenvolvimento versus produção. Conclua o início rápido antes de continuar.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Especifique um rótulo ao adicionar um valor de configuração

Na portal do Azure, vá para o **Configuration Explorer** e localize a chave *TestApp: Settings: fontcolor* que você criou no guia de início rápido. Selecione seu menu de contexto e clique em **adicionar valor**.

> [!div class="mx-imgBorder"]
> ![Adicionar item de menu de valor](media/labels-add-value.png)

Na tela **adicionar valor** , insira um **valor** **vermelho** e um **rótulo** de **desenvolvimento**. Deixe **tipo de conteúdo** vazio. Escolha **Aplicar**.

## <a name="loading-configuration-values-with-a-specified-label"></a>Carregando valores de configuração com um rótulo especificado

Por padrão, Azure App configuração carrega apenas valores de configuração sem rótulo. Se você definiu rótulos para seus valores de configuração, convém especificar os rótulo a serem usados ao se conectar à configuração do aplicativo.

Na última seção, você criou um valor de configuração diferente para o ambiente de *desenvolvimento* . Você usa a `HostingEnvironment.EnvironmentName` variável para determinar dinamicamente em qual ambiente o aplicativo está sendo executado no momento. Para saber mais, confira [usar vários ambientes no ASP.NET Core](/aspnet/core/fundamentals/environments).

Carregue os valores de configuração com o rótulo correspondente ao ambiente atual passando o nome do ambiente para `Select` o método:

```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options
                        .Connect(Environment.GetEnvironmentVariable("AppConfigConnectionString"))
                        // Load configuration values with no label
                        .Select(KeyFilter.Any, LabelFilter.Null)
                        // Override with any configuration values specific to current hosting env
                        .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
                );
            })
            .UseStartup<Startup>());
```

> [!IMPORTANT]
> O trecho de código acima carrega a cadeia de conexão de configuração de aplicativo de `AppConfigConnectionString`uma variável de ambiente chamada. Certifique-se de que essa variável de ambiente esteja definida corretamente.

O método `Select` é chamado duas vezes. Na primeira vez, ele carrega valores de configuração sem rótulo. Em seguida, ele carrega valores de configuração com o rótulo correspondente ao ambiente atual. Esses valores específicos do ambiente substituem quaisquer valores correspondentes sem rótulo. Você não precisa definir valores específicos do ambiente para cada chave. Se uma chave não tiver um valor com um rótulo correspondente ao ambiente atual, o valor sem rótulo será usado.

## <a name="testing-in-different-environments"></a>Testes em ambientes diferentes

Para testar os valores de configuração diferentes, abra `launchSettings.json` o arquivo no `Properties` diretório. Localize a `config` entrada em `profiles`. Na `environmentVariables` seção, defina a `ASPNETCORE_ENVIRONMENT` variável como. `Production`

Com os novos valores definidos, compile e execute seu aplicativo.

```dotnetcli
dotnet build
dotnet run
```

Use um navegador da Web para navegar `http://localhost:5000`até. Você observará que a cor da fonte é preta.

![Aplicativo Web em execução com a configuração de produção](media/labels-website-prod.png)

Agora, `launchSettings.json` atualize para definir `ASPNETCORE_ENVIRONMENT` a variável `Development`como. Execute `dotnet run` novamente. Você observará que a cor da fonte agora é vermelha. Isso ocorre porque o aplicativo agora usa o valor `TestApp:Settings:FontColor` que tem o `Development` rótulo. Todos os outros valores de configuração permanecem os mesmos como seus valores de produção.

![Aplicativo Web em execução com configuração de desenvolvimento](media/labels-website-dev.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configuração no ASP.NET Core](/aspnet/core/fundamentals/configuration/)
