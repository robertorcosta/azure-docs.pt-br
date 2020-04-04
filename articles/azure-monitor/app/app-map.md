---
title: Mapa de aplicativos no Azure Application Insights | Microsoft Docs
description: Monitorar topologias complexas de aplicativos com o mapa do aplicativo
ms.topic: conceptual
ms.date: 03/15/2019
ms.reviewer: sdash
ms.openlocfilehash: 0823dd5d880c778f9b7a231ac14f1cbba1940927
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657386"
---
# <a name="application-map-triage-distributed-applications"></a>Mapa do aplicativo: aplicativos distribuídos por triagem

O mapa do aplicativo ajuda você a identificar gargalos de desempenho ou pontos de acesso com falha em todos os componentes dos seus aplicativos distribuídos. Cada nó do mapa representa um componente de aplicativo ou suas dependências e esses nós também têm KPIs de integridade e alertas de status. Você pode clicar em qualquer componente para obter diagnóstico mais detalhado, como eventos do Application Insights. Se seu aplicativo usar os serviços do Azure, você também poderá clicar no diagnóstico do Azure, como nas recomendações do Assistente do Banco de Dados SQL.

## <a name="what-is-a-component"></a>O que é um componente?

Os componentes são partes independentes dos aplicativos de microsserviços/distribuídos. As equipes de operações e desenvolvedores têm acesso ou visibilidade nível de código para telemetria gerada por esses componentes de aplicativos. 

* Os componentes são diferentes das dependências externas "observadas", como SQL, EventHub etc., a qual sua organização/equipe pode não ter acesso (código ou telemetria).
* Os componentes são executados em qualquer número de instâncias de contêiner/função/servidor.
* Os componentes podem ser chaves de instrumentação do Application Insights separadas (mesmo se as assinaturas forem diferentes) ou diferentes funções relatando para uma única chave de instrumentação do Application Insights. A experiência do mapa de visualização mostra os componentes independentemente de como eles estão configurados.

## <a name="composite-application-map"></a>Mapa do aplicativo composto

Você pode ver a topologia do aplicativo completa em vários níveis de componentes de aplicativos relacionados. Os componentes podem ser recursos diferentes do Application Insights ou funções diferentes em um único recurso. O mapa do aplicativo localiza os componentes seguindo qualquer chamada de dependência HTTP feita entre os servidores com o SDK do Application Insights instalado. 

Essa experiência começa com a descoberta progressiva dos componentes. Quando você carrega pela primeira vez o mapa do aplicativo, um conjunto de consultas é acionado para descobrir os componentes relacionados a este componente. Um botão no canto superior esquerdo será atualizado com o número de componentes em seu aplicativo, conforme eles são descobertos. 

Ao clicar em "Atualizar componentes do mapa", o mapa será atualizado com todos os componentes descobertos até aquele momento. Dependendo da complexidade do seu aplicativo, isso pode levar um minuto para carregar.

Se todos os componentes são funções em um único recurso do Application Insights, essa etapa de descoberta não é necessária. A carga inicial para esse tipo de aplicativo terá todos os respectivos componentes.

![Captura de tela do mapa do aplicativo](media/app-map/app-map-001.png)

Um dos principais objetivos com essa experiência é ser capaz de visualizar topologias complexas com centenas de componentes.

Clique em qualquer componente para ver as respectivas informações e acesse a experiência de triagem de desempenho e falha desse componente.

![Submenu](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Investigar falhas

Selecione **investigar falhas** para iniciar o painel de falhas.

![Captura de tela do botão Investigar falhas](media/app-map/investigate-failures.png)

![Captura de tela da experiência de falhas](media/app-map/failures.png)

### <a name="investigate-performance"></a>Investigar o desempenho

Para solucionar problemas de desempenho, selecione **investigar o desempenho**.

![Captura de tela do botão Investigar desempenho](media/app-map/investigate-performance.png)

![Captura de tela da experiência de desempenho](media/app-map/performance.png)

### <a name="go-to-details"></a>Acessar detalhes

Selecione **ir aos detalhes** para explorar a experiência de transação de ponta a ponta, que pode oferecer exibições até o nível de pilha de chamadas.

![Captura de tela do botão Acessar detalhes](media/app-map/go-to-details.png)

![Captura de tela de detalhes da transação de ponta a ponta](media/app-map/end-to-end-transaction.png)

### <a name="view-logs-analytics"></a>Exibir logs (Analytics)

Para consultar e investigar mais os dados de seus aplicativos, clique **em Exibir em Logs (Analytics)**.

![Captura de tela do botão Exibir na análise](media/app-map/view-logs.png)

![Captura de tela da experiência de análise. Gráfico de linha resumindo a duração média de resposta de uma solicitação nas últimas 12 horas.](media/app-map/log-analytics.png)

### <a name="alerts"></a>Alertas

Para exibir alertas ativos e as regras subjacentes que fazem com que os alertas sejam disparados, selecione **alertas**.

![Captura de tela do botão de alertas](media/app-map/alerts.png)

![Captura de tela da experiência de análise](media/app-map/alerts-view.png)

## <a name="set-cloud-role-name"></a>Definir nome da função de nuvem

O Mapa do Aplicativo usa a propriedade **de nome da função nuvem** para identificar os componentes no mapa. O Application Insights SDK adiciona automaticamente a propriedade de nome de função na nuvem à telemetria emitida por componentes. Por exemplo, o SDK adicionará um nome de site ou nome de função de serviço à propriedade de nome da função na nuvem. No entanto, há casos em que você talvez queira substituir o valor padrão. Para substituir o nome da função na nuvem e alterar o que é exibido no Mapa do Aplicativo:

# <a name="netnetcore"></a>[.NET/.NetCore](#tab/net)

**Escreva telemetria inicial personalizada abaixo.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "Custom RoleName";
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

**ASP.NET aplicativos: Inicializador de carga para a configuração de telemetria ativa**

Em ApplicationInsights.config:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

Um método alternativo para ASP.NET aplicativos web é instanciar o inicializador em código, por exemplo, em Global.aspx.cs:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

> [!NOTE]
> Adicionar inicializador `ApplicationInsights.config` `TelemetryConfiguration.Active` usando ou usando não é válido para ASP.NET aplicativos Core. 

**ASP.NET principais aplicativos: inicializador de carga para a configuração de telemetria**

Para [aplicações ASP.NET Core,](asp-net-core.md#adding-telemetryinitializers) `TelemetryInitializer` a adição de um novo é feita adicionando-a ao recipiente Dependency Injection, como mostrado abaixo. Isso é `ConfigureServices` feito no `Startup.cs` método da sua aula.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

# <a name="java"></a>[Java](#tab/java)

Começando com o Application Insights Java SDK 2.5.0, `<RoleName>` você `ApplicationInsights.xml` pode especificar o nome da função na nuvem adicionando ao seu arquivo, por exemplo.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
   <RoleName>** Your role name **</RoleName>
   ...
</ApplicationInsights>
```

Se você usar o Spring Boot com o iniciador do Spring Boot do Application Insights, a única alteração necessária é definir seu nome personalizado para o aplicativo no arquivo application.properties.

`spring.application.name=<name-of-app>`

O starter spring boot atribuirá automaticamente o nome da função na nuvem ao valor que você inserir para a propriedade spring.application.name.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Método alternativo para o Node. js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
appInsights.queue.push(() => {
appInsights.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```
---

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>Entendendo o nome da função na nuvem no contexto do Mapa de Aplicativos

Quanto a como pensar sobre **o nome da função na nuvem,** pode ser útil olhar para um Mapa de Aplicativos que tem vários nomes de função na nuvem presentes:

![Captura de tela do mapa do aplicativo](media/app-map/cloud-rolename.png)

No Mapa de Aplicativos acima, cada um dos nomes em caixas verdes são valores de nome de função na nuvem para diferentes aspectos desta aplicação distribuída em particular. Assim, para este aplicativo suas `Authentication` `acmefrontend`funções consistem em: , , `Inventory Management`a `Payment Processing Worker Role`. 

No caso deste aplicativo, cada um desses nomes de função na nuvem também representa um recurso exclusivo do Application Insights com suas próprias teclas de instrumentação. Uma vez que o proprietário deste aplicativo tem acesso a cada um desses quatro recursos diferentes do Application Insights, o Application Map é capaz de costurar um mapa das relações subjacentes.

Para as [definições oficiais:](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93)

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

Alternativamente, **a instância de função na nuvem** pode ser útil para cenários em que o nome da função na **nuvem** diz que o problema está em algum lugar do front-end da Web, mas você pode estar executando seu front-end da Web em vários servidores balanceados de carga, então ser capaz de perfurar uma camada mais profunda através de consultas kusto e saber se o problema está afetando todos os servidores/instâncias front-end da Web ou apenas um pode ser extremamente importante.

Um cenário em que você pode querer substituir o valor para a instância de função na nuvem pode ser se o seu aplicativo estiver sendo executado em um ambiente contêiner onde apenas saber que o servidor individual pode não ser informação suficiente para localizar um determinado problema.

Para obter mais informações sobre como substituir a propriedade de nome de função na nuvem com iniciadores de telemetria, consulte [Adicionar propriedades: ITelemettryInitializer](api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Solução de problemas

Se você estiver tendo dificuldades para obter o Mapa do aplicativo para trabalhar conforme esperado, tente essas etapas:

### <a name="general"></a>Geral

1. Certifique-se que você está usando um SDK com suporte oficial. SDKs de comunidade/sem suporte podem não dar suporte à correlação.

    Consulte este [artigo](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) para obter uma lista dos SDKs com suporte.

2. Atualize todos os componentes para a versão mais recente do SDK.

3. Se você estiver usando o Azure Functions com C#, atualize para o [Functions V2](https://docs.microsoft.com/azure/azure-functions/functions-versions).

4. Confirme [se o nome da função na nuvem](#set-cloud-role-name) está corretamente configurado.

5. Se estiver faltando uma dependência, verifique se ele está na lista de [dependências coletadas automaticamente](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Se não, você ainda poderá acompanhá-lo manualmente com uma chamada [acompanhar dependência](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>Muitos nós no mapa

O Mapa do Aplicativo constrói um nó de aplicativo para cada nome único de função na nuvem presente em sua telemetria de solicitação e um nó de dependência para cada combinação única de nome de tipo, destino e função na nuvem em sua telemetria de dependência. Se houver mais de 10.000 nós em sua telemetria, o Mapa de Aplicativos não será capaz de buscar todos os nós e links, de modo que seu mapa estará incompleto. Se isso acontecer, uma mensagem de aviso aparecerá ao visualizar o mapa.

Além disso, o Mapa de Aplicativos só suporta até 1000 álos separados não agrupados renderizados de uma só vez. O Mapa do Aplicativo reduz a complexidade visual agrupando dependências que têm o mesmo tipo e chamadores, mas se sua telemetria tiver muitos nomes de funções de nuvem exclusivos ou muitos tipos de dependência, esse agrupamento será insuficiente e o mapa não poderá renderizar.

Para corrigir isso, você precisará alterar sua instrumentação para definir corretamente o nome da função na nuvem, o tipo de dependência e os campos de destino de dependência.

* O alvo de dependência deve representar o nome lógico de uma dependência. Em muitos casos, é equivalente ao servidor ou nome de recurso da dependência. Por exemplo, no caso de dependências HTTP, ele é definido como nome de host. Não deve conter ids ou parâmetros exclusivos que mudem de uma solicitação para outra.

* O tipo de dependência deve representar o tipo lógico de uma dependência. Por exemplo, HTTP, SQL ou Azure Blob são tipos típicos de dependência. Não deve conter iDs exclusivos.

* O propósito do nome da função na nuvem é descrito na [seção acima](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name).

## <a name="portal-feedback"></a>Comentários do Portal

Para fornecer feedback, use a opção de feedback.

![Imagem de MapLink-1](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como funciona a correlação no Application Insights, consulte o [artigo de correlação de telemetria](correlation.md).
* A [experiência de diagnóstico de transações de ponta a ponta](transaction-diagnostics.md) correlaciona a telemetria do lado do servidor de todos os componentes monitorados do Application Insights em uma única exibição.
* Para cenários avançados de correlação em ASP.NET Core e ASP.NET consulte o artigo [de operações personalizadas](custom-operations-tracking.md) da faixa.
