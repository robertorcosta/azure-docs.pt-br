---
title: Comunicação de serviço com o Núcleo ASP.NET
description: Aprenda a usar ASP.NET Core em aplicativos de serviços confiáveis do Azure Service.
author: vturecek
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 0d432bd19d0689ef508fca0bf24eed4406929f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639625"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET Núcleo em Serviços De Serviço Azure Serviços Confiáveis

ASP.NET Core é uma estrutura de código aberto e multiplataforma. Essa estrutura foi projetada para criar aplicativos conectados à internet baseados em nuvem, como aplicativos web, aplicativos ioT e back-ends móveis.

Este artigo é um guia aprofundado para hospedagem de serviços ASP.NET Core em Serviços Confiáveis de Malha de Serviçousando o **Microsoft.ServiceFabric.AspNetCore.** conjunto de pacotes NuGet.

Para obter um tutorial introdutório sobre ASP.NET Core in Service Fabric e instruções sobre como configurar seu ambiente de desenvolvimento, consulte [Tutorial: Crie e implante um aplicativo com um serviço front-end da Core Web API ASP.NET e um serviço de back-end de forma stateful](service-fabric-tutorial-create-dotnet-app.md).

O resto deste artigo pressupõe que você já está familiarizado com ASP.NET Core. Se não, por favor, leia os [fundamentos do ASP.NET Core.](https://docs.microsoft.com/aspnet/core/fundamentals/index)

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core no ambiente do Service Fabric

Ambos os aplicativos ASP.NET Core e Service Fabric podem ser executados no .NET Core ou no .NET Framework completo. Você pode usar ASP.NET Core de duas maneiras diferentes em Service Fabric:
 - **Hospedado como um executável convidado**. Desta forma é usado principalmente para executar aplicativos ASP.NET Core existentes no Service Fabric sem alterações de código.
 - **Executar dentro de um serviço confiável.** Desta forma permite uma melhor integração com o tempo de execução do Service Fabric e permite serviços stateful ASP.NET Core.

O resto deste artigo explica como usar ASP.NET Core dentro de um serviço confiável, através dos componentes de integração ASP.NET Core que são enviados com o Service Fabric SDK.

## <a name="service-fabric-service-hosting"></a>Hospedagem de serviços do Service Fabric

Em Service Fabric, uma ou mais instâncias e/ou réplicas do seu serviço são executadas em um *processo de host de serviço:* um arquivo executável que executa seu código de serviço. Você, como autor de serviço, possui o processo de host de serviço, e o Service Fabric ativa e monitora para você.

O ASP.NET tradicional (até 5 MVC) está estreitamente relacionado ao IIS por meio de System.Web.dll. O Núcleo do ASP.NET fornece uma separação entre o servidor Web e o aplicativo Web. Essa separação permite que os aplicativos web sejam portáteis entre diferentes servidores web. Ele também permite que os servidores web sejam *auto-hospedados*. Isso significa que você pode iniciar um servidor web em seu próprio processo, em oposição a um processo que pertence a um software dedicado de servidor web, como o IIS.

Para combinar um serviço de Malha de Serviço e ASP.NET, seja como um executável convidado ou em um serviço confiável, você deve ser capaz de iniciar ASP.NET dentro do seu processo de host de serviço. A hospedagem interna do Núcleo do ASP.NET permite que você faça isso.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hospedagem ASP.NET Core em um serviço confiável
Normalmente, aplicativos do Núcleo do ASP.NET auto-hospedados criam um WebHost no ponto de entrada do aplicativo, como o método `static void Main()` em `Program.cs`. Neste caso, o ciclo de vida do WebHost está vinculado ao ciclo de vida do processo.

![Hospedando o Núcleo do ASP.NET em um processo][0]

Mas o ponto de entrada do aplicativo não é o lugar certo para criar um WebHost em um serviço confiável. Isso porque o ponto de entrada do aplicativo é usado apenas para registrar um tipo de serviço com o tempo de execução do Service Fabric, para que ele possa criar instâncias desse tipo de serviço. O WebHost deve ser criado em um serviço confiável. Dentro do processo de host de serviço, instâncias de serviço e/ou réplicas podem passar por vários ciclos de vida. 

Uma instância de um serviço confiável é representada por sua classe de serviço derivando de `StatelessService` ou `StatefulService`. A pilha de comunicação para um serviço está contida em uma implementação `ICommunicationListener` em sua classe de serviço. Os `Microsoft.ServiceFabric.AspNetCore.*` pacotes NuGet contêm implementações desse `ICommunicationListener` início e gerenciam o WebHost ASP.NET para Kestrel ou HTTP.sys em um serviço confiável.

![Diagrama para hospedagem ASP.NET Core em um serviço confiável][1]

## <a name="aspnet-core-icommunicationlisteners"></a>Núcleo do ASP.NET ICommunicationListeners
As `ICommunicationListener` implementações para Kestrel e HTTP.sys nos `Microsoft.ServiceFabric.AspNetCore.*` pacotes NuGet têm padrões de uso semelhantes. Mas eles executam ações ligeiramente diferentes específicas para cada servidor web. 

Ambos os ouvintes de comunicação fornecem um construtor que usa os seguintes argumentos:
 - **`ServiceContext serviceContext`**: Este `ServiceContext` é o objeto que contém informações sobre o serviço em execução.
 - **`string endpointName`**: Este é o `Endpoint` nome de uma configuração em ServiceManifest.xml. É principalmente onde os dois ouvintes de comunicação diferem. HTTP.sys *requires* requer `Endpoint` uma configuração, enquanto kestrel não.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: Esta é uma lambda que você implementa, na qual você cria e devolve um `IWebHost`. Ele permite configurar `IWebHost` da maneira que normalmente configuraria em um aplicativo ASP.NET Core. O lambda fornece uma URL gerada para você, dependendo das opções `Endpoint` de integração do Service Fabric que você usa e da configuração que você fornece. Em seguida, você pode modificar ou usar essa URL para iniciar o servidor web.

## <a name="service-fabric-integration-middleware"></a>Middleware de integração do Service Fabric
O `Microsoft.ServiceFabric.AspNetCore` pacote NuGet `UseServiceFabricIntegration` inclui o `IWebHostBuilder` método de extensão que adiciona middleware com reconhecimento de malha de serviço. Este middleware configura o Kestrel ou `ICommunicationListener` HTTP.sys para registrar uma URL de serviço exclusiva com o Service Fabric Naming Service. Em seguida, valida as solicitações do cliente para garantir que os clientes estejam se conectando ao serviço certo. 

Esta etapa é necessária para evitar que os clientes se conectem erroneamente ao serviço errado. Isso porque, em um ambiente de host compartilhado, como o Service Fabric, vários aplicativos da Web podem ser executados na mesma máquina física ou virtual, mas não usam nomes de host exclusivos. Esse cenário é descrito em mais detalhes na próxima seção.

### <a name="a-case-of-mistaken-identity"></a>Um caso de identidade incorreta
Independentemente do protocolo, as réplicas de serviço escutam em uma combinação de IP:porta exclusiva. Uma vez que uma réplica de serviço tenha começado a ouvir em um ponto final IP:port, ele relata esse endereço de ponto final para o Serviço de Nomeação de Malha de Serviço. Lá, clientes ou outros serviços podem descobri-lo. Se os serviços usarem portas de aplicativos atribuídas dinamicamente, uma réplica de serviço pode coincidentemente usar o mesmo ponto final IP:port de outro serviço anteriormente na mesma máquina física ou virtual. Isso pode fazer com que um cliente se conecte por engano ao serviço incorreto. Esse cenário pode resultar se ocorrer a seguinte seqüência de eventos:

 1. O serviço A escuta em 10.0.0.1:30000 via HTTP. 
 2. O cliente resolve o serviço A e recebe o endereço 10.0.0.1:30000.
 3. O Serviço A move-se para um nó diferente.
 4. O serviço B é colocado em 10.0.0.1 e usa coincidentemente a mesma porta 30000.
 5. O cliente tenta se conectar ao serviço A com o endereço de cache 10.0.0.1:30000.
 6. O cliente agora está conectado com sucesso ao serviço B, sem perceber que está conectado ao serviço errado.

Isso pode causar bugs em momentos aleatórios, que podem ser difíceis de diagnosticar.

### <a name="using-unique-service-urls"></a>Usando URLs de serviço exclusivas
Para evitar esses bugs, os serviços podem postar um ponto final no Serviço de Nomeação com um identificador exclusivo e, em seguida, validar esse identificador exclusivo durante as solicitações do cliente. Essa é uma ação cooperativa entre serviços em um ambiente confiável de locatário não hostil. Ele não fornece autenticação de serviço seguro em um ambiente hostil.

Em um ambiente confiável, o middleware `UseServiceFabricIntegration` adicionado pelo método anexa automaticamente um identificador exclusivo ao endereço postado no Serviço de Nomeação. Ele valida esse identificador em cada solicitação. Se o identificador não corresponder, o middleware retorna imediatamente uma resposta HTTP 410 Gone.

Os serviços que usam uma porta atribuída dinamicamente devem fazer uso desse middleware.

Serviços que utilizam uma porta única fixa não têm esse problema em um ambiente cooperativo. Uma porta fixa é normalmente usada para serviços voltados para o exterior que exigem uma porta conhecida para a conexão de aplicativos clientes. Por exemplo, a maioria dos aplicativos web voltados para a Internet usará a porta 80 ou 443 para conexões de navegador esno. Neste caso, o identificador único não deve ser habilitado.

O diagrama a seguir mostra o fluxo de solicitação com o middleware habilitado:

![Integração de núcleo do ASP.NET do Service Fabric][2]

Ambas as implementações kestrel `ICommunicationListener` e HTTP.sys usam esse mecanismo exatamente da mesma maneira. Embora o HTTP.sys possa diferenciar internamente solicitações com base em caminhos de URL exclusivos usando o recurso de `ICommunicationListener` compartilhamento de porta **HTTP.sys** subjacente, essa funcionalidade *não* é usada pela implementação HTTP.sys. Isso porque resulta em códigos de status de erro HTTP 503 e HTTP 404 no cenário descrito anteriormente. Isso, por sua vez, dificulta que os clientes determinem a intenção do erro, já que HTTP 503 e HTTP 404 são comumente usados para indicar outros erros. 

Assim, tanto as implementações do `ICommunicationListener` Kestrel quanto do HTTP.sys padronizam os middleware fornecidos pelo método de `UseServiceFabricIntegration` extensão. Portanto, os clientes só precisam executar uma ação de reresolução de ponto final de serviço nas respostas HTTP 410.

## <a name="httpsys-in-reliable-services"></a>HTTP.sys em Serviços Confiáveis
Você pode usar HTTP.sys em Serviços Confiáveis importando o pacote **Microsoft.ServiceFabric.AspNetCore.HttpSys** NuGet. Este pacote `HttpSysCommunicationListener`contém , `ICommunicationListener`uma implementação de . `HttpSysCommunicationListener`permite que você crie um WebHost ASP.NET dentro de um serviço confiável usando HTTP.sys como servidor web.

HTTP.sys é construído na [API](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx)do Servidor HTTP do Windows . Esta API usa o driver de kernel **HTTP.sys** para processar solicitações HTTP e encaminhá-las para processos que executam aplicativos web. Isso permite que vários processos na mesma máquina física ou virtual hospedem aplicativos web na mesma porta, desamparados por um caminho de URL exclusivo ou nome do host. Esses recursos são úteis no Service Fabric para hospedar vários sites no mesmo cluster.

>[!NOTE]
>A implementação do HTTP.sys funciona apenas na plataforma Windows.

O diagrama a seguir ilustra como http.sys usa o driver de kernel **HTTP.sys** no Windows para compartilhamento de portas:

![Diagrama HTTP.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>HTTP.sys em um serviço apátrida
Para usar `HttpSys` em um serviço sem estado, substitua o método `CreateServiceInstanceListeners` e retorne uma instância `HttpSysCommunicationListener`:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseHttpSys()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="httpsys-in-a-stateful-service"></a>HTTP.sys em um serviço imponente

`HttpSysCommunicationListener`atualmente, não é projetado para uso em serviços estaduais devido a complicações com o recurso de compartilhamento de porta **HTTP.sys** subjacente. Para obter mais informações, consulte a seção a seguir sobre alocação dinâmica de portas com HTTP.sys. Para serviços estaduais, Kestrel é o servidor web sugerido.

### <a name="endpoint-configuration"></a>Configuração de ponto de extremidade

Uma `Endpoint` configuração é necessária para servidores web que usam a API do Servidor HTTP do Windows, incluindo HTTP.sys. Os servidores web que usam a API do Servidor Windows HTTP devem primeiro reservar sua URL com HTTP.sys (isso normalmente é realizado com a ferramenta [netsh).](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) 

Esta ação requer privilégios elevados que seus serviços não têm por padrão. As opções "http" ou `Protocol` "https" `Endpoint` para a propriedade da configuração em ServiceManifest.xml são usadas especificamente para instruir o tempo de execução do Service Fabric a registrar uma URL com HTTP.sys em seu nome. Ele faz isso usando o forte prefixo [*url curinga.*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx)

Por exemplo, `http://+:80` para reservar para um serviço, use a seguinte configuração em ServiceManifest.xml:

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

E o nome do ponto de extremidade deve ser passado para o construtor `HttpSysCommunicationListener`:

```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseHttpSys()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-httpsys-with-a-static-port"></a>Use HTTP.sys com uma porta estática
Para usar uma porta estática com HTTP.sys, forneça o número da porta na configuração: `Endpoint`

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Use HTTP.sys com uma porta dinâmica
Para usar uma porta designada dinamicamente com HTTP.sys, omita a `Port` propriedade na configuração: `Endpoint`

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Uma porta dinâmica alocada por uma `Endpoint` configuração fornece apenas uma porta por processo de *host*. O modelo de hospedagem Service Fabric atual permite que várias instâncias de serviço e/ou réplicas sejam hospedadas no mesmo processo. Isso significa que cada um compartilhará a `Endpoint` mesma porta quando alocado através da configuração. Várias **instâncias HTTP.sys** podem compartilhar uma porta usando o recurso de compartilhamento de porta **HTTP.sys** subjacente. Mas não é suportado `HttpSysCommunicationListener` devido às complicações que introduz para pedidos de clientes. Para o uso dinâmico da porta, Kestrel é o servidor web sugerido.

## <a name="kestrel-in-reliable-services"></a>Kestrel em Serviços Confiáveis
Você pode usar o Kestrel em Serviços Confiáveis importando o pacote **Microsoft.ServiceFabric.AspNetCore.Kestrel** NuGet. Este pacote `KestrelCommunicationListener`contém , `ICommunicationListener`uma implementação de . `KestrelCommunicationListener`permite que você crie um WebHost ASP.NET dentro de um serviço confiável usando o Kestrel como servidor web.

O Kestrel é um servidor Web multiplataforma para o ASP.NET Core. Ao contrário do HTTP.sys, o Kestrel não usa um gerenciador de ponto final centralizado. Também ao contrário do HTTP.sys, o Kestrel não suporta o compartilhamento de portas entre vários processos. Cada instância do Kestrel deve usar uma porta exclusiva. Para obter mais informações sobre kestrel, consulte os [detalhes de implementação](https://docs.microsoft.com/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-2.2).

![Diagrama de Kestrel][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel em um serviço sem estado
Para usar `Kestrel` em um serviço sem estado, substitua o método `CreateServiceInstanceListeners` e retorne uma instância `KestrelCommunicationListener`:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>Kestrel em um serviço com estado
Para usar `Kestrel` em um serviço com estado, substitua o método `CreateServiceReplicaListeners` e retorne uma instância `KestrelCommunicationListener`:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

Neste exemplo, uma instância única de `IReliableStateManager` é fornecida ao contêiner de injeção de dependência do WebHost. Isso não é estritamente necessário, mas `IReliableStateManager` permite que você use e coleções confiáveis em seus métodos de ação controlador mvc.

Um nome de configuração `Endpoint`*não* é fornecido para `KestrelCommunicationListener` em um serviço com estado. Isso será explicado mais detalhadamente na seção a seguir.

### <a name="configure-kestrel-to-use-https"></a>Configurar o Kestrel para usar HTTPS
Ao ativar https com Kestrel em seu serviço, você precisará definir várias opções de escuta. Atualize `ServiceInstanceListener` o para usar um ponto final *EndpointHttps* e ouça em uma porta específica (como a porta 443). Ao configurar o host web para usar o servidor web Kestrel, você deve configurar o Kestrel para ouvir endereços IPv6 em todas as interfaces de rede: 

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

Para obter um exemplo completo em um tutorial, consulte [Configurar Kestrel para usar HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Configuração de ponto de extremidade
Uma `Endpoint` configuração não é necessária para usar Kestrel. 

Kestrel é um simples servidor web autônomo. Ao contrário do HTTP.sys (ou HttpListener), ele não precisa de uma configuração `Endpoint` no ServiceManifest.xml porque não requer registro de URL antes de começar. 

#### <a name="use-kestrel-with-a-static-port"></a>Usar Kestrel com uma porta estática
Você pode configurar uma porta `Endpoint` estática na configuração de ServiceManifest.xml para uso com Kestrel. Embora isso não seja estritamente necessário, oferece dois benefícios potenciais:
 - Se a porta não cair na faixa de porta de aplicativo, ela será aberta através do firewall do SISTEMA OPERACIONAL pela Service Fabric.
 - A URL fornecida através de `KestrelCommunicationListener` usará essa porta.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Se um `Endpoint` for configurado, o nome deverá ser passado para o construtor `KestrelCommunicationListener`: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Se serviceManifest.xml não usar `Endpoint` uma configuração, omita o nome no `KestrelCommunicationListener` construtor. Neste caso, ele usará uma porta dinâmica. Consulte a próxima seção para obter mais informações sobre isso.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Usar Kestrel com uma porta dinâmica
O Kestrel não pode usar a `Endpoint` atribuição automática da porta a partir da configuração em ServiceManifest.xml. Isso porque a atribuição `Endpoint` automática da porta a partir de uma configuração atribui uma porta única por *processo de host,* e um único processo de host pode conter várias instâncias de Kestrel. Isso não funciona com kestrel porque não suporta compartilhamento de portas. Portanto, cada instância kestrel deve ser aberta em uma porta única.

Para usar a atribuição de porta `Endpoint` dinâmica com o Kestrel, omita a configuração no `KestrelCommunicationListener` ServiceManifest.xml inteiramente e não passe um nome de ponto final para o construtor, da seguinte forma:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

Nessa configuração, `KestrelCommunicationListener` selecionará automaticamente uma porta não utilizada do intervalo de portas de aplicativo.

Para HTTPS, ele deve ter o Ponto Final configurado com o protocolo HTTPS sem uma porta especificada em ServiceManifest.xml e passar o nome do ponto final para o construtor KestrelCommunicationListener.


## <a name="service-fabric-configuration-provider"></a>Provedor de configuração de malha de serviço
A configuração do aplicativo no ASP.NET Core é baseada em pares de valor-chave estabelecidos pelo provedor de configuração. Leia [configuração no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) para entender mais sobre o suporte de configuração geral ASP.NET Core.

Esta seção descreve como o provedor de configuração Service `Microsoft.ServiceFabric.AspNetCore.Configuration` Fabric se integra com ASP.NET configuração Core importando o pacote NuGet.

### <a name="addservicefabricconfiguration-startup-extensions"></a>Extensões de inicialização addServiceFabricConfiguration
Depois de `Microsoft.ServiceFabric.AspNetCore.Configuration` importar o pacote NuGet, você precisa registrar a fonte de configuração de malha de serviço com ASP.NET API de configuração Core. Você faz isso verificando **extensões AddServiceFabricConfiguration** `Microsoft.ServiceFabric.AspNetCore.Configuration` `IConfigurationBuilder`no namespace contra .

```csharp
using Microsoft.ServiceFabric.AspNetCore.Configuration;

public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(env.ContentRootPath)
        .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
        .AddServiceFabricConfiguration() // Add Service Fabric configuration settings.
        .AddEnvironmentVariables();
    Configuration = builder.Build();
}

public IConfigurationRoot Configuration { get; }
```

Agora, o serviço ASP.NET Core pode acessar as configurações de configuração do Service Fabric, assim como qualquer outra configuração do aplicativo. Por exemplo, você pode usar o padrão de opções para carregar configurações em objetos fortemente digitados.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Mapeamento de tecla padrão
Por padrão, o provedor de configuração Service Fabric inclui o nome do pacote, o nome da seção e o nome da propriedade. Juntos, estes formam a chave de configuração ASP.NET Core, da seguinte forma:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Por exemplo, se você tiver `MyConfigPackage` um pacote de configuração nomeado com o `IConfiguration` seguinte conteúdo, então o valor de configuração estará disponível no ASP.NET Core através *do MyConfigPackage:MyConfigSection:MyParameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Opções de configuração de malha de serviço
O provedor de configuração `ServiceFabricConfigurationOptions` Service Fabric também suporta alterar o comportamento padrão do mapeamento de chaves.

#### <a name="encrypted-settings"></a>Configurações criptografadas
O Service Fabric suporta configurações criptografadas, assim como o provedor de configuração Service Fabric. As configurações criptografadas não são `IConfiguration` descriptografadas para ASP.NET Core por padrão. Os valores criptografados são armazenados lá. Mas se você quiser descriptografar o valor a ser configurado na IConfiguração do Núcleo ASP.NET, você pode definir o sinalizador *DecryptValue* como falso na `AddServiceFabricConfiguration` extensão, da seguinte forma:

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = false); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>Vários pacotes de configuração
O Service Fabric suporta vários pacotes de configuração. Por padrão, o nome do pacote está incluído na chave de configuração. Mas você pode `IncludePackageName` definir a bandeira como falsa, como segue:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        // exclude package name from key.
        .AddServiceFabricConfiguration(activationContext, (options) => options.IncludePackageName = false); 
    Configuration = builder.Build();
}
```
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Mapeamento de chaves personalizadas, extração de valor e população de dados
O provedor de configuração Service Fabric também suporta `ExtractKeyFunc` cenários mais avançados `ExtractValueFunc`para personalizar o mapeamento de chaves e extrair os valores com . Você pode até mesmo alterar todo o processo de preencher dados `ConfigAction`da configuração de Malha de Serviço para ASP.NET configuração Core usando .

Os exemplos a seguir `ConfigAction` ilustram como usar para personalizar a população de dados:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    
    this.valueCount = 0;
    this.sectionCount = 0;
    var builder = new ConfigurationBuilder();
    builder.AddServiceFabricConfiguration(activationContext, (options) =>
        {
            options.ConfigAction = (package, configData) =>
            {
                ILogger logger = new ConsoleLogger("Test", null, false);
                logger.LogInformation($"Config Update for package {package.Path} started");

                foreach (var section in package.Settings.Sections)
                {
                    this.sectionCount++;

                    foreach (var param in section.Parameters)
                    {
                        configData[options.ExtractKeyFunc(section, param)] = options.ExtractValueFunc(section, param);
                        this.valueCount++;
                    }
                }

                logger.LogInformation($"Config Update for package {package.Path} finished");
            };
        });
  Configuration = builder.Build();
}
```

### <a name="configuration-updates"></a>Atualizações de configuração
O provedor de configuração Service Fabric também suporta atualizações de configuração. Você pode usar `IOptionsMonitor` ASP.NET Core para receber `IOptionsSnapshot` notificações de alteração e, em seguida, usar para recarregar dados de configuração. Para obter mais informações, consulte [ASP.NET opções Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

Essas opções são suportadas por padrão. Nenhuma codificação adicional é necessária para ativar atualizações de configuração.

## <a name="scenarios-and-configurations"></a>Cenários e configurações
Esta seção fornece a combinação de servidor web, configuração de porta, opções de integração de malha de serviço e configurações diversas que recomendamos para solucionar os seguintes cenários:
 - Serviços sem monitoração de estado do Núcleo do ASP.NET expostos externamente
 - Serviços inestatais somente ASP.NET internos
 - Serviços estatais ASP.NET-somente internos

Um **serviço exposto externamente** é aquele que expõe um ponto final que é chamado de fora do cluster, geralmente através de um balanceador de carga.

Um serviço **somente interno** é aquele cujo ponto final é chamado apenas de dentro do cluster.

> [!NOTE]
> Os pontos finais de serviços estaduais geralmente não devem ser expostos à internet. Os clusters por trás dos balanceadores de carga que desconhecem a resolução de serviço satisfaz a malha de serviço, como o Azure Load Balancer, não poderão expor serviços estatais. Isso porque o balanceador de carga não será capaz de localizar e direcionar o tráfego para a réplica de serviço de estado apropriada. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Serviços sem monitoração de estado do Núcleo do ASP.NET expostos externamente
Kestrel é o servidor web sugerido para serviços front-end que expõem pontos finais HTTP externos voltados para a Internet. No Windows, http.sys pode fornecer o recurso de compartilhamento de portas, que permite hospedar vários serviços da Web no mesmo conjunto de nomes usando a mesma porta. Neste cenário, os serviços web são diferenciados por nome ou caminho do host, sem depender de um proxy front-end ou gateway para fornecer roteamento HTTP.
 
Quando exposto à internet, um serviço apátrida deve usar um ponto final bem conhecido e estável que é acessível através de um balanceador de carga. Você fornecerá esta URL aos usuários do seu aplicativo. Recomendamos a seguinte configuração:

|  |  | **Observações** |
| --- | --- | --- |
| Servidor Web | Kestrel | Kestrel é o servidor web preferido, pois é suportado através do Windows e Linux. |
| Configuração de portas | static | Uma porta estática conhecida deve ser configurada na configuração do `Endpoints` de ServiceManifest.XML, como 80 para HTTP ou 443 para HTTPS. |
| ServiceFabricIntegrationOptions | Nenhum | Use `ServiceFabricIntegrationOptions.None` a opção ao configurar middleware de integração service fabric, para que o serviço não tente validar solicitações recebidas para um identificador exclusivo. Os usuários externos do seu aplicativo não saberão as informações únicas de identificação que o middleware usa. |
| Contagem de Instâncias | -1 | Em casos típicos de uso, a configuração de contagem de ocorrências deve ser definida como *-1*. Isso é feito para que uma instância esteja disponível em todos os nós que recebem tráfego de um balanceador de carga. |

Se vários serviços expostos externamente compartilharem o mesmo conjunto de nós, você poderá usar HTTP.sys com um caminho de URL único, mas estável. Você pode fazer isso modificando a URL fornecida ao configurar o IWebHost. Observe que isso se aplica apenas a HTTP.sys.

 ```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseHttpSys()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>Serviço do Núcleo do ASP.NET sem estado somente para uso interno
Os serviços sem monitoração de estado que são chamados apenas de dentro do cluster devem usar URLs exclusivas e portas atribuídas dinamicamente para garantir a cooperação entre vários serviços. Recomendamos a seguinte configuração:

|  |  | **Observações** |
| --- | --- | --- |
| Servidor Web | Kestrel | Embora você possa usar HTTP.sys para serviços internos apátridas, o Kestrel é o melhor servidor para permitir que várias instâncias de serviço compartilhem um host.  |
| Configuração de portas | atribuídas dinamicamente | Várias réplicas de um serviço de estado podem compartilhar um processo de host ou sistema operacional host e, portanto, precisarão de portas exclusivas. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Com a atribuição dinâmica de portas essa configuração impede o problema de confusão de identidade descrito anteriormente. |
| InstanceCount | any | A configuração de contagem de instâncias pode ser definida como qualquer valor necessário para operar o serviço. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Somente interno serviço com estado do Núcleo do ASP.NET
Os serviços com monitoração de estado que são chamados apenas de dentro do cluster devem usar portas atribuídas dinamicamente para garantir a cooperação entre vários serviços. Recomendamos a seguinte configuração:

|  |  | **Observações** |
| --- | --- | --- |
| Servidor Web | Kestrel | O `HttpSysCommunicationListener` não foi projetado para ser usado por serviços estatais nos quais as réplicas compartilham um processo de host. |
| Configuração de portas | atribuídas dinamicamente | Várias réplicas de um serviço de estado podem compartilhar um processo de host ou sistema operacional host e, portanto, precisarão de portas exclusivas. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Com a atribuição dinâmica de portas essa configuração impede o problema de confusão de identidade descrito anteriormente. |

## <a name="next-steps"></a>Próximas etapas
[Depurar seu aplicativo do Service Fabric usando o Visual Studio](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
