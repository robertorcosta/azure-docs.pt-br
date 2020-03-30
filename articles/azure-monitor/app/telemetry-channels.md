---
title: Canais de telemetria no Azure Application Insights | Microsoft Docs
description: Como personalizar canais de telemetria em SDKs do Azure Application Insights para .NET e .NET Core.
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 9c292246f947e4d3a364f79b31fe7a1deebd33d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275692"
---
# <a name="telemetry-channels-in-application-insights"></a>Canais de telemetria em Insights de Aplicativos

Os canais de telemetria são parte integrante dos [SDKs do Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Eles gerenciam buffering e transmissão de telemetria para o serviço Application Insights. As versões .NET e .NET Core dos SDKs têm `InMemoryChannel` dois `ServerTelemetryChannel`canais de telemetria incorporados: e . Este artigo descreve cada canal em detalhes, incluindo como personalizar o comportamento do canal.

## <a name="what-are-telemetry-channels"></a>O que são canais de telemetria?

Os canais de telemetria são responsáveis por tamponar itens de telemetria e enviá-los para o serviço Application Insights, onde são armazenados para consulta e análise. Um canal de telemetria é [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) qualquer classe que implementa a interface.

O `Send(ITelemetry item)` método de um canal de telemetria é chamado depois que todas as iniciais de telemetria e processadores de telemetria são chamados. Assim, todos os itens descartados por um processador de telemetria não chegarão ao canal. `Send()`normalmente não envia os itens para o back-end instantaneamente. Normalmente, ele os tampona na memória e os envia em lotes, para uma transmissão eficiente.

[Live Metrics Stream](live-stream.md) também tem um canal personalizado que alimenta a transmissão ao vivo da telemetria. Este canal é independente do canal regular de telemetria, e este documento não se aplica a ele.

## <a name="built-in-telemetry-channels"></a>Canais de telemetria incorporados

O Application Insights .NET e o .NET Core SDKs são enviados com dois canais integrados:

* `InMemoryChannel`: Um canal leve que tampona itens na memória até que sejam enviados. Os itens são protegidos na memória e lavados uma vez a cada 30 segundos, ou sempre que 500 itens são protegidos. Este canal oferece garantias mínimas de confiabilidade porque não tenta novamente enviar telemetria após uma falha. Este canal também não mantém itens no disco, então quaisquer itens não enviados são perdidos permanentemente após o desligamento do aplicativo (gracioso ou não). Este canal implementa um `Flush()` método que pode ser usado para lavar à força qualquer item de telemetria na memória de forma sincronizada. Este canal é adequado para aplicações de curto prazo onde um flush síncrono é ideal.

    Este canal faz parte do pacote maior microsoft.ApplicationInsights NuGet e é o canal padrão que o SDK usa quando nada mais é configurado.

* `ServerTelemetryChannel`: Um canal mais avançado que tem políticas de repetição e a capacidade de armazenar dados em um disco local. Este canal tenta novamente enviar telemetria se ocorrerem erros transitórios. Este canal também usa o armazenamento em disco local para manter itens em disco durante paralisações de rede ou altos volumes de telemetria. Devido a esses mecanismos de repetição e armazenamento em disco local, este canal é considerado mais confiável e é recomendado para todos os cenários de produção. Este canal é o padrão para [aplicativos ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) e [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) que são configurados de acordo com a documentação oficial. Este canal é otimizado para cenários de servidor com processos de longa duração. O [`Flush()`](#which-channel-should-i-use) método implementado por este canal não é síncrono.

    Este canal é enviado como o pacote Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel NuGet e é adquirido automaticamente quando você usa o Microsoft.ApplicationInsights.Web ou Microsoft.ApplicationInsights.AspNetCore NuGet Pacote.

## <a name="configure-a-telemetry-channel"></a>Configure um canal de telemetria

Você configura um canal de telemetria definindo-o para a configuração de telemetria ativa. Para ASP.NET aplicativos, a configuração envolve a `TelemetryConfiguration.Active`definição da instância `ApplicationInsights.config`do canal de telemetria para , ou modificando . Para aplicações ASP.NET Core, a configuração envolve adicionar o canal ao recipiente de injeção de dependência.

As seções a seguir mostram `StorageFolder` exemplos de configuração da configuração do canal em vários tipos de aplicativos. `StorageFolder`é apenas uma das configurações configuráveis. Para obter a lista completa de configurações, consulte [a seção configurações](telemetry-channels.md#configurable-settings-in-channels) mais tarde neste artigo.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Configuração usando ApplicationInsights.config para aplicativos ASP.NET

A seção a seguir do [ApplicationInsights.config](configuration-with-applicationinsights-config.md) mostra o `ServerTelemetryChannel` canal configurado com `StorageFolder` definido como um local personalizado:

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!-- Telemetry processors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

### <a name="configuration-in-code-for-aspnet-applications"></a>Configuração em código para aplicativos de ASP.NET

O código a seguir configura uma instância 'ServerTelemetryChannel' com `StorageFolder` definido como um local personalizado. Adicione este código no início da aplicação, normalmente no `Application_Start()` método em Global.aspx.cs.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
protected void Application_Start()
{
    var serverTelemetryChannel = new ServerTelemetryChannel();
    serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
    serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
}
```

### <a name="configuration-in-code-for-aspnet-core-applications"></a>Configuração em código para aplicativos ASP.NET Core

Modifique `ConfigureServices` o `Startup.cs` método da classe como mostrado aqui:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

public void ConfigureServices(IServiceCollection services)
{
    // This sets up ServerTelemetryChannel with StorageFolder set to a custom location.
    services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel() {StorageFolder = @"d:\temp\applicationinsights" });

    services.AddApplicationInsightsTelemetry();
}

```

> [!IMPORTANT]
> A configuração do `TelemetryConfiguration.Active` canal usando não é recomendada para ASP.NET aplicativos Core.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Configuração em código para aplicativos de console .NET/.NET Core

Para aplicativos de console, o código é o mesmo para .NET e .NET Core:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Detalhes operacionais do ServerTelemetryChannel

`ServerTelemetryChannel`armazena itens que chegam em um buffer na memória. Os itens são serializados, compactados e `Transmission` armazenados em uma instância uma vez a cada 30 segundos ou quando 500 itens foram protegidos. Uma `Transmission` única instância contém até 500 itens e representa um lote de telemetria que é enviado por uma única chamada HTTPS para o serviço Application Insights.

Por padrão, um máximo `Transmission` de 10 instâncias podem ser enviadas em paralelo. Se a telemetria estiver chegando a taxas mais rápidas ou se a `Transmission` rede ou o back-end do Application Insights for lento, as instâncias serão armazenadas na memória. A capacidade padrão deste buffer `Transmission` na memória é de 5 MB. Quando a capacidade de memória é `Transmission` excedido, as instâncias são armazenadas em disco local até um limite de 50 MB. `Transmission`as instâncias são armazenadas no disco local também quando há problemas de rede. Apenas os itens armazenados em um disco local sobrevivem a uma falha de aplicativo. Eles são enviados sempre que a aplicação começa novamente.

## <a name="configurable-settings-in-channels"></a>Configurações configuráveis nos canais

Para obter a lista completa de configurações configuráveis para cada canal, consulte:

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Aqui estão as configurações mais `ServerTelemetryChannel`usadas para:

1. `MaxTransmissionBufferCapacity`: A quantidade máxima de memória, em bytes, utilizada pelo canal para tampão de transmissões na memória. Quando essa capacidade é atingida, novos itens são armazenados diretamente no disco local. O valor padrão é 5 MB. Definir um valor mais alto leva a menos uso de disco, mas lembre-se que itens na memória serão perdidos se o aplicativo falhar.

1. `MaxTransmissionSenderCapacity`: O número `Transmission` máximo de instâncias que serão enviadas ao Application Insights ao mesmo tempo. O valor padrão é 10. Esta configuração pode ser configurada para um número maior, o que é recomendado quando um grande volume de telemetria é gerado. O alto volume geralmente ocorre durante o teste de carga ou quando a amostragem é desligada.

1. `StorageFolder`: A pasta que é usada pelo canal para armazenar itens em disco conforme necessário. No Windows, %LOCALAPPDATA% ou %TEMP% são usados se nenhum outro caminho for especificado explicitamente. Em ambientes diferentes do Windows, você deve especificar que um local válido ou telemetria não será armazenado no disco local.

## <a name="which-channel-should-i-use"></a>Qual canal devo usar?

`ServerTelemetryChannel`é recomendado para a maioria dos cenários de produção envolvendo aplicativos de longa duração. O `Flush()` método `ServerTelemetryChannel` implementado por não é síncrono, e também não garante o envio de todos os itens pendentes da memória ou disco. Se você usar este canal em cenários onde o aplicativo está prestes `Flush()`a ser desligado, recomendamos que você introduza algum atraso após a chamada . A quantidade exata de atraso que você pode precisar não é previsível. Depende de fatores como quantos `Transmission` itens ou instâncias estão na memória, quantos estão no disco, quantos estão sendo transmitidos para o back-end e se o canal está no meio de cenários de back-off exponenciais.

Se você precisar fazer um flush síncrono, recomendamos que você use `InMemoryChannel`.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>O canal Application Insights garante a entrega da telemetria? Se não, quais são os cenários em que a telemetria pode ser perdida?

A resposta curta é que nenhum dos canais embutidos oferece uma garantia de tipo de transação de entrega de telemetria para o back-end. `ServerTelemetryChannel`é mais avançado `InMemoryChannel` em comparação com a entrega confiável, mas também faz apenas uma tentativa de melhor esforço para enviar telemetria. A telemetria ainda pode ser perdida em várias situações, incluindo estes cenários comuns:

1. Os itens na memória são perdidos quando o aplicativo falha.

1. A telemetria é perdida durante longos períodos de problemas de rede. A telemetria é armazenada em disco local durante paralisações de rede ou quando ocorrem problemas com o back-end do Application Insights. No entanto, itens com mais de 48 horas são descartados.

1. Os locais de disco padrão para armazenar telemetria no Windows são %LOCALAPPDATA% ou %TEMP%. Esses locais são tipicamente locais para a máquina. Se o aplicativo migrar fisicamente de um local para outro, qualquer telemetria armazenada no local original será perdida.

1. Em Aplicativos da Web no Windows, o local padrão de armazenamento de disco é D:\local\LocalAppData. Este local não é persistido. É eliminado em reinicializações de aplicativos, scale-outs e outras operações desse tipo, levando à perda de qualquer telemetria armazenada lá. Você pode substituir o padrão e especificar o armazenamento para um local persistente como D:\home. No entanto, tais locais persistidos são servidos por armazenamento remoto e por isso podem ser lentos.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>O ServerTelemetryChannel funciona em outros sistemas além do Windows?

Embora o nome do seu pacote e namespace inclua "WindowsServer", este canal é suportado em sistemas diferentes do Windows, com a seguinte exceção. Em sistemas diferentes do Windows, o canal não cria uma pasta de armazenamento local por padrão. Você deve criar uma pasta de armazenamento local e configurar o canal para usá-lo. Depois que o armazenamento local é configurado, o canal funciona da mesma forma em todos os sistemas.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>O SDK cria armazenamento local temporário? Os dados estão criptografados no armazenamento?

O SDK armazena itens de telemetria no armazenamento local durante problemas de rede ou durante o estrangulamento. Esses dados não são criptografados localmente.

Para sistemas Windows, o SDK cria automaticamente uma pasta local temporária no diretório %TEMP% ou %LOCALAPPDATA% e restringe o acesso aos administradores e apenas ao usuário atual.

Para sistemas diferentes do Windows, nenhum armazenamento local é criado automaticamente pelo SDK e, portanto, nenhum dado é armazenado localmente por padrão. Você pode criar um diretório de armazenamento você mesmo e configurar o canal para usá-lo. Neste caso, você é responsável por garantir que o diretório esteja protegido.
Leia mais sobre [proteção de dados e privacidade.](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage)

## <a name="open-source-sdk"></a>SDK do código-fonte aberto
Como todo SDK para Insights de Aplicativos, os canais são de código aberto. Leia e contribua para o código, ou reporte problemas, [no repo oficial do GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Próximas etapas

* [Amostragem](../../azure-monitor/app/sampling.md)
* [Solução de problemas do SDK](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
