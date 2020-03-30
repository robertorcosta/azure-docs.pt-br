---
title: Configure aplicativos Linux ASP.NET Core
description: Saiba como configurar um contêiner ASP.NET Core pré-construído para o seu aplicativo. Este artigo mostra as tarefas de configuração mais comuns.
ms.devlang: dotnet
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: b1d9e59109f5ace25abb9840b48e44ff03d394e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255903"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Configure um aplicativo Linux ASP.NET Core para o Azure App Service

ASP.NET os aplicativos Core devem ser implantados como binários compilados. A ferramenta de publicação Visual Studio constrói a solução e, em seguida, implanta os binários compilados diretamente, enquanto o mecanismo de implantação do App Service implanta o repositório de código primeiro e depois compila os binários.

Este guia fornece conceitos e instruções importantes para desenvolvedores ASP.NET Core que usam um contêiner Linux integrado no App Service. Se você nunca usou o Azure App Service, siga o [ASP.NET Core quickstart](quickstart-dotnetcore.md) e [ASP.NET Core com tutorial de banco de dados SQL](tutorial-dotnetcore-sqldb-app.md) primeiro.

## <a name="show-net-core-version"></a>Mostrar a versão .NET Core

Para mostrar a versão atual do .NET Core, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões suportadas do .NET Core, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>Definir a versão .NET Core

Execute o seguinte comando no [Cloud Shell](https://shell.azure.com) para definir a versão .NET Core como 2.1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="customize-build-automation"></a>Personalize a automação de compilação

Se você implantar seu aplicativo usando pacotes Git ou zip com automação de compilação ligada, o App Service construirá etapas de automação através da seguinte seqüência:

1. Executar o script personalizado, se especificado por `PRE_BUILD_SCRIPT_PATH`.
1. Execute `dotnet restore` para restaurar as dependências do NuGet.
1. Corra `dotnet publish` para construir um binário para a produção.
1. Executar o script personalizado, se especificado por `POST_BUILD_SCRIPT_PATH`.

`PRE_BUILD_COMMAND`e `POST_BUILD_COMMAND` são variáveis de ambiente que estão vazias por padrão. Para executar comandos de `PRE_BUILD_COMMAND`pré-construção, defina . Para executar comandos pós-compilação, defina `POST_BUILD_COMMAND`.

O exemplo a seguir especifica as duas variáveis para uma série de comandos, separados por commas.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Para obter variáveis adicionais de ambiente para personalizar a automação de construção, consulte [a configuração Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Para obter mais informações sobre como o App Service executa e constrói aplicativos ASP.NET Core no Linux, consulte [a documentação oryx: como os aplicativos .NET Core são detectados e construídos](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md).

## <a name="access-environment-variables"></a>Acessar variáveis de ambiente

No Serviço de Aplicativo, você pode [definir configurações de aplicativo](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) fora do código do aplicativo. Em seguida, você pode acessá-los em qualquer classe usando o padrão ASP.NET padrão de injeção de dependência do Núcleo:

```csharp
using Microsoft.Extensions.Configuration;

namespace SomeNamespace 
{
    public class SomeClass
    {
        private IConfiguration _configuration;
    
        public SomeClass(IConfiguration configuration)
        {
            _configuration = configuration;
        }
    
        public SomeMethod()
        {
            // retrieve App Service app setting
            var myAppSetting = _configuration["MySetting"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

Se você configurar uma configuração de aplicativo com o mesmo nome no App Service e em *appsettings.json*, por exemplo, o valor do Serviço de Aplicativo tem precedência sobre o valor *appsettings.json.* O valor local *appsettings.json* permite depurar o aplicativo localmente, mas o valor do App Service permite que você execute o aplicativo em produto com configurações de produção. As seqüências de conexão funcionam da mesma maneira. Dessa forma, você pode manter seus segredos de aplicativo fora do seu repositório de código e acessar os valores apropriados sem alterar seu código.

## <a name="get-detailed-exceptions-page"></a>Obter página de exceções detalhadas

Quando seu aplicativo ASP.NET gera uma exceção no depurador do Visual Studio, o navegador exibe uma página de exceção detalhada, mas no App Service essa página é substituída por um erro genérico **HTTP 500** ou um erro ocorreu durante o **processamento de sua solicitação.** mensagem. Para exibir a página de exceção `ASPNETCORE_ENVIRONMENT` detalhada no App Service, adicione a configuração do aplicativo ao seu aplicativo, executando o seguinte comando no <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Detectar sessão HTTPS

No Serviço de Aplicativo, a [Terminação SSL](https://wikipedia.org/wiki/TLS_termination_proxy) ocorre nos balanceadores de carga de rede de modo que todas as solicitações HTTPS cheguem ao seu aplicativo como solicitações HTTP não criptografadas. Se a lógica do aplicativo precisar saber se as solicitações do usuário estão criptografadas ou não, configure o Middleware de cabeçalho encaminhado em *Startup.cs*:

- Configure o middleware com [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) para encaminhar os cabeçalhos `X-Forwarded-For` e `X-Forwarded-Proto` em `Startup.ConfigureServices`.
- Adicione faixas de endereço IP privadas às redes conhecidas, para que o middleware possa confiar no balanceador de carga do App Service.
- Invoque o método [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) antes de `Startup.Configure` chamar outros middlewares.

Juntando os três elementos, seu código parece o seguinte exemplo:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

Para obter mais informações, veja [Configurar o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer).

## <a name="deploy-multi-project-solutions"></a>Implantar soluções multiprojetos

Quando você implanta um repositório de ASP.NET no mecanismo de implantação com um arquivo *.csproj* no diretório raiz, o mecanismo implanta o projeto. Quando você implanta um repositório ASP.NET com um arquivo *.sln* no diretório raiz, o mecanismo escolhe o primeiro Projeto de Aplicativo da Web ou web que ele encontra como o aplicativo App Service. É possível para o motor não escolher o projeto que você quer.

Para implantar uma solução de vários projetos, você pode especificar o projeto a ser usado no App Service de duas maneiras diferentes:

### <a name="using-deployment-file"></a>Usando arquivo .deployment

Adicione um arquivo *.deployment* à raiz do repositório e adicione o seguinte código:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Usando configurações de aplicativos

No <a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a>, adicione uma configuração de aplicativo ao seu aplicativo App Service executando o seguinte comando CLI. Substitua * \<o nome do aplicativo>, * * \<o nome do grupo de recursos>* e * \<o nome do projeto>* com os valores apropriados.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Acessar logs de diagnóstico

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Abra a sessão SSH aberta no navegador

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: ASP.NET aplicativo Core com banco de dados SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Perguntas frequentes sobre o Serviço de Aplicativo no Linux](app-service-linux-faq.md)
