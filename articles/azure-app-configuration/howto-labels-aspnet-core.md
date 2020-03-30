---
title: Use a configuração por ambiente
titleSuffix: Azure App Configuration
description: Use etiquetas para fornecer valores de configuração por ambiente
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: d1a3323154fc73b453e041a064cfcd36299b8a08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056796"
---
# <a name="use-labels-to-enable-different-configurations-for-different-environments"></a>Use rótulos para habilitar diferentes configurações para diferentes ambientes

Muitos aplicativos precisam usar configurações diferentes para diferentes ambientes. Suponha que um aplicativo tenha um valor de configuração que defina a seqüência de conexões a ser usada para seu banco de dados back-end. Os desenvolvedores do aplicativo usam um banco de dados diferente do usado na produção. A seqüência de conexão de banco de dados usada pelo aplicativo deve mudar à medida que o aplicativo passa do desenvolvimento para a produção.

Na configuração do aplicativo Azure, você pode usar rótulos para definir valores *diferentes* para a mesma chave. Por exemplo, você pode definir uma única chave com valores diferentes para *desenvolvimento* e *produção.* Você pode especificar quais rótulos carregar ao se conectar à configuração do aplicativo.

Para demonstrar essa funcionalidade, modificaremos o aplicativo web criado no [Quickstart: Crie um aplicativo ASP.NET Core com configuração do aplicativo Azure](./quickstart-aspnet-core-app.md) para usar diferentes configurações para desenvolvimento versus produção. Por favor, complete o quickstart antes de prosseguir.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Especifique um rótulo ao adicionar um valor de configuração

No portal Azure, vá para o **Configuration Explorer** e localize o *TestApp:Configurações:Chave FontColor* que você criou no quickstart. Selecione seu menu de contexto e clique **em Adicionar valor**.

> [!div class="mx-imgBorder"]
> ![Adicionar item do menu Valor](media/labels-add-value.png)

Na tela **Adicionar valor,** digite um **valor** de **vermelho** e um **rótulo** de **desenvolvimento**. Deixar **o tipo de conteúdo** vazio. Selecione **Aplicar**.

## <a name="loading-configuration-values-with-a-specified-label"></a>Carregando valores de configuração com um rótulo especificado

Por padrão, a configuração do aplicativo Azure só carrega valores de configuração sem rótulo. Se você definiu rótulos para seus valores de configuração, você deseja especificar os rótulos para usar ao se conectar à configuração do aplicativo.

Na última seção, você criou um valor de configuração diferente para o ambiente *de desenvolvimento.* Você usa `HostingEnvironment.EnvironmentName` a variável para determinar dinamicamente em qual ambiente o aplicativo está sendo executado no momento. Para saber mais, [consulte Use vários ambientes em ASP.NET Core](/aspnet/core/fundamentals/environments).

Valores de configuração de carga com o rótulo `Select` correspondente ao ambiente atual, passando o nome do ambiente para o método:

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
> O trecho de código acima carrega a seqüência `AppConfigConnectionString`de conexão configuração do aplicativo a partir de uma variável de ambiente chamada . Certifique-se de que essa variável de ambiente está definida corretamente.

O método `Select` é chamado duas vezes. Na primeira vez, ele carrega valores de configuração sem rótulo. Em seguida, ele carrega valores de configuração com o rótulo correspondente ao ambiente atual. Esses valores específicos do ambiente sobrepõem quaisquer valores correspondentes sem rótulo. Você não precisa definir valores específicos do ambiente para cada chave. Se uma tecla não tiver um valor com um rótulo correspondente ao ambiente atual, o valor sem rótulo é usado.

## <a name="testing-in-different-environments"></a>Testes em diferentes ambientes

Para testar os diferentes `launchSettings.json` valores `Properties` de configuração, abra o arquivo sob o diretório. Localize `config` a `profiles`entrada em . Na `environmentVariables` seção, `ASPNETCORE_ENVIRONMENT` defina `Production`a variável como .

Com os novos valores definidos, construa e execute sua aplicação.

```dotnetcli
dotnet build
dotnet run
```

Use um navegador da `http://localhost:5000`Web para navegar para . Você notará que a cor da fonte é preta.

![Aplicativo web em execução com configuração de produção](media/labels-website-prod.png)

Agora `launchSettings.json` atualize `ASPNETCORE_ENVIRONMENT` para `Development`definir a variável como . Execute `dotnet run` novamente. Você notará que a cor da fonte agora é vermelha. Isso porque o aplicativo agora `TestApp:Settings:FontColor` usa o `Development` valor do que tem o rótulo. Todos os outros valores de configuração permanecem os mesmos que seus valores de produção.

![Aplicativo web em execução com configuração de desenvolvimento](media/labels-website-dev.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configuração no ASP.NET Core](/aspnet/core/fundamentals/configuration/)
