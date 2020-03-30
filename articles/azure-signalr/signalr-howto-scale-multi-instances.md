---
title: Escala com várias instâncias - Azure SignalR Service
description: Em muitos cenários de dimensionamento, muitas vezes o cliente precisa provisionar várias instâncias e configurá-las para usá-las em conjunto, para criar uma implantação em larga escala. Por exemplo, a fragmentação requer suporte a várias instâncias.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: 43d703312cbc1fc067a2d51d5623ed028ba01405
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158165"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Como dimensionar o SignalR Service com várias instâncias?
O SDK do Serviço SignalR mais recente suporta vários pontos finais para instâncias do SignalR Service. Você pode usar esse recurso para dimensionar as conexões simultâneas ou usá-lo para mensagens entre regiões.

## <a name="for-aspnet-core"></a>Para ASP.NET Núcleo

### <a name="how-to-add-multiple-endpoints-from-config"></a>Como adicionar vários pontos finais da configuração?

Configure com `Azure:SignalR:ConnectionString` `Azure:SignalR:ConnectionString:` a tecla ou para a seqüência de conexão SignalR Service.

Se a chave `Azure:SignalR:ConnectionString:`começar com , `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`deve `Name` `EndpointType` ser em `ServiceEndpoint` formato , onde e são propriedades do objeto, e são acessíveis a partir do código.

Você pode adicionar seqüências `dotnet` de conexão de várias instâncias usando os seguintes comandos:

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Como adicionar vários pontos finais do código?

Uma `ServicEndpoint` classe é introduzida para descrever as propriedades de um ponto final do Azure SignalR Service.
Você pode configurar vários pontos finais de instância ao usar o SDK do Azure SignalR Service através de:
```cs
services.AddSignalR()
        .AddAzureSignalR(options => 
        {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString0>"),
                new ServiceEndpoint("<ConnectionString1>", type: EndpointType.Primary, name: "east-region-a"),
                new ServiceEndpoint("<ConnectionString2>", type: EndpointType.Primary, name: "east-region-b"),
                new ServiceEndpoint("<ConnectionString3>", type: EndpointType.Secondary, name: "backup"),
            };
        });
```

### <a name="how-to-customize-endpoint-router"></a>Como personalizar o roteador de ponto final?

Por padrão, o SDK usa o [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) para captar pontos finais.

#### <a name="default-behavior"></a>Comportamento padrão 
1. Roteamento de solicitação de cliente

    Quando `/negotiate` cliente com o servidor de aplicativo. Por padrão, o SDK **seleciona aleatoriamente** um ponto final do conjunto de pontos finais de serviço disponíveis.

2. Roteamento de mensagens do servidor

    Quando *enviar mensagem para uma conexão ****, e a conexão de destino é roteada para o servidor atual, a mensagem vai diretamente para esse ponto final conectado. Caso contrário, as mensagens são transmitidas para todos os pontos finais do Azure SignalR.

#### <a name="customize-routing-algorithm"></a>Personalizar algoritmo de roteamento
Você pode criar seu próprio roteador quando tiver conhecimento especial para identificar para quais pontos finais as mensagens devem ir.

Um roteador personalizado é definido abaixo `east-` como um exemplo `east`quando os grupos que começam sempre vão para o ponto final chamado :

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override IEnumerable<ServiceEndpoint> GetEndpointsForGroup(string groupName, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the group broadcast behavior, if the group name starts with "east-", only send messages to endpoints inside east
        if (groupName.StartsWith("east-"))
        {
            return endpoints.Where(e => e.Name.StartsWith("east-"));
        }

        return base.GetEndpointsForGroup(groupName, endpoints);
    }
}
```

Outro exemplo abaixo, que substitui o comportamento de negociação padrão, para selecionar os pontos finais depende de onde o servidor do aplicativo está localizado.

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(HttpContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (endpointName.Count == 0)
        {
            context.Response.StatusCode = 400;
            var response = Encoding.UTF8.GetBytes("Invalid request");
            context.Response.Body.Write(response, 0, response.Length);
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Não se esqueça de registrar o roteador para contêiner DI usando:

```cs
services.AddSingleton(typeof(IEndpointRouter), typeof(CustomRouter));
services.AddSignalR()
        .AddAzureSignalR(
            options => 
            {
                options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
            });
```

## <a name="for-aspnet"></a>Por ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>Como adicionar vários pontos finais da configuração?

Configure com `Azure:SignalR:ConnectionString` `Azure:SignalR:ConnectionString:` a tecla ou para a seqüência de conexão SignalR Service.

Se a chave `Azure:SignalR:ConnectionString:`começar com , `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`deve `Name` `EndpointType` ser em `ServiceEndpoint` formato , onde e são propriedades do objeto, e são acessíveis a partir do código.

Você pode adicionar seqüências de conexão de várias instâncias a: `web.config`

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <connectionStrings>
    <add name="Azure:SignalR:ConnectionString" connectionString="<ConnectionString1>"/>
    <add name="Azure:SignalR:ConnectionString:en-us" connectionString="<ConnectionString2>"/>
    <add name="Azure:SignalR:ConnectionString:zh-cn:secondary" connectionString="<ConnectionString3>"/>
    <add name="Azure:SignalR:ConnectionString:Backup:secondary" connectionString="<ConnectionString4>"/>
  </connectionStrings>
  ...
</configuration>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Como adicionar vários pontos finais do código?

Uma `ServicEndpoint` classe é introduzida para descrever as propriedades de um ponto final do Azure SignalR Service.
Você pode configurar vários pontos finais de instância ao usar o SDK do Azure SignalR Service através de:

```cs
app.MapAzureSignalR(
    this.GetType().FullName, 
    options => {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString1>"),
                new ServiceEndpoint("<ConnectionString2>"),
                new ServiceEndpoint("<ConnectionString3>"),
            }
        });
```

### <a name="how-to-customize-router"></a>Como personalizar o roteador?

A única diferença entre ASP.NET SignalR e ASP.NET Core `GetNegotiateEndpoint`SignalR é o tipo de contexto http para . Para ASP.NET SignalR, é do tipo [IOwinContext.](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19)

Abaixo está o exemplo de negociação personalizada para ASP.NET SignalR:

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(IOwinContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (string.IsNullOrEmpty(endpointName))
        {
            context.Response.StatusCode = 400;
            context.Response.Write("Invalid request.");
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Não se esqueça de registrar o roteador para contêiner DI usando:

```cs
var hub = new HubConfiguration();
var router = new CustomRouter();
hub.Resolver.Register(typeof(IEndpointRouter), () => router);
app.MapAzureSignalR(GetType().FullName, hub, options => {
    options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
});
```

## <a name="configuration-in-cross-region-scenarios"></a>Configuração em cenários inter-regionais

O `ServiceEndpoint` objeto `EndpointType` tem uma `primary` `secondary`propriedade com valor ou .

`primary`os pontos finais são pontos finais preferenciais para receber tráfego do cliente e são considerados com conexões de rede mais confiáveis; `secondary` os pontos finais são considerados com conexões de rede menos confiáveis e são usados apenas para levar o servidor ao tráfego do cliente, por exemplo, a transmissão de mensagens, não para levar o cliente ao tráfego do servidor.

Em casos de inter-região, a rede pode ser instável. Para um servidor de aplicativo localizado no *leste dos EUA,* o ponto final do `primary` SignalR Service localizado na mesma região leste dos *EUA* pode ser configurado como e pontos finais em outras regiões marcadas como `secondary`. Nesta configuração, os pontos finais de serviço em outras regiões podem **receber** mensagens deste servidor de aplicativos do *Leste dos EUA,* mas não haverá clientes **inter-regionais** encaminhados para este servidor de aplicativos. A arquitetura é mostrada no diagrama abaixo:

![Infra-geo cross-geo](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

Quando um `/negotiate` cliente tenta com o servidor de aplicativos, com o roteador padrão, `primary` o SDK **seleciona aleatoriamente** um ponto final do conjunto de pontos finais disponíveis. Quando o ponto final principal não está disponível, o `secondary` SDK **seleciona aleatoriamente** de todos os pontos finais disponíveis. O ponto final é marcado como **disponível** quando a conexão entre servidor e o ponto final do serviço estiver viva.

No cenário inter-região, quando `/negotiate` um cliente tenta com o servidor de aplicativos `primary` hospedado no Leste dos *EUA,* por padrão ele sempre retorna o ponto final localizado na mesma região. Quando todos os pontos finais do *Leste dos EUA* não estão disponíveis, o cliente é redirecionado para pontos finais em outras regiões. A seção fail-over abaixo descreve o cenário em detalhes.

![Negociação normal](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Fail-over

Quando `primary` todos os pontos finais não `/negotiate` estiverem disponíveis, as escolhas do cliente dos pontos finais disponíveis. `secondary` Este mecanismo de fail-over requer que `primary` cada ponto final deve servir como ponto final para pelo menos um servidor de aplicativo.

![Fail-over](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu sobre como configurar várias instâncias no mesmo aplicativo para cenários de dimensionamento, fragmentação e região cruzada.

Vários suportes de endpoints também podem ser usados em cenários de alta disponibilidade e recuperação de desastres.

> [!div class="nextstepaction"]
> [Serviço signalr de configuração para recuperação de desastres e alta disponibilidade](./signalr-concept-disaster-recovery.md)
