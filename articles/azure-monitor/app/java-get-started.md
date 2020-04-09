---
title: 'Quickstart: Análise de aplicativos web java com insights de aplicativos do Azure'
description: 'Monitoramento de desempenho de aplicativos usando o Application Insights para aplicativos Web Java. '
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 05/24/2019
ms.openlocfilehash: 2355cb7ab995cab3060c7a94c9e7ea344bd9e92b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984633"
---
# <a name="quickstart-get-started-with-application-insights-in-a-java-web-project"></a>Quickstart: Comece com o Application Insights em um projeto web Java

Neste quickstart, você usa o Application Insights para instrumentalar automaticamente solicitações, rastrear dependências e coletar contadores de desempenho, diagnosticar problemas de desempenho e exceções e escrever código para rastrear o que os usuários fazem com seu aplicativo.

Application Insights é um serviço de análise extensível para desenvolvedores da Web que ajuda você a entender o desempenho e o uso de seu aplicativo em tempo real. O Application Insights oferece suporte a aplicativos Java em execução no Windows, no Unix ou no Linux.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Uma aplicação Java em funcionamento.

## <a name="get-an-application-insights-instrumentation-key"></a>Obter uma chave de instrumentação do Application Insights

1. Entre no [portal do Azure](https://portal.azure.com/).
2. No portal do Azure, crie um recurso Application Insights. Defina o tipo de aplicativo para aplicativo Web Java.

3. Localize a chave de instrumentação do novo recurso. Você precisará colar essa chave no código de seu projeto em breve.

    ![Na visão geral do novo recurso, clique em Propriedades e copie a chave de instrumentação](./media/java-get-started/instrumentation-key-001.png)

## <a name="add-the-application-insights-sdk-for-java-to-your-project"></a>Adicionar o SDK do Application Insights para Java a seu projeto

*Escolha seu tipo de projeto.*

# <a name="maven"></a>[Maven](#tab/maven)

Se o seu projeto já estiver configurado para usar o Maven para build, mescle o seguinte código ao seu arquivo *pom.xml.*

Em seguida, atualize as dependências do projeto para obter os binários baixados.

```XML
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web-auto</artifactId>
        <!-- or applicationinsights-web for manual web filter registration -->
        <!-- or applicationinsights-core for bare API -->
        <version>2.5.0</version>
      </dependency>
    </dependencies>
```

# <a name="gradle"></a>[Gradle](#tab/gradle)

Se o seu projeto já estiver configurado para usar gradle para build, mescle o seguinte código ao seu arquivo *build.gradle.*

Em seguida, atualize as dependências do projeto para obter os binários baixados.

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.5.0'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

# <a name="other-types"></a>[Outros tipos](#tab/other)

Baixe a [versão mais recente](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) e copie os arquivos necessários para o projeto substituindo as versões anteriores.

---

### <a name="questions"></a>Perguntas
* *Qual é a relação `-web-auto` `-web` entre `-core` os componentes e componentes?*
  * `applicationinsights-web-auto`fornece métricas que rastreiam contagens e tempos de resposta de solicitação de servlet HTTP, registrando automaticamente o filtro servlet Application Insights em tempo de execução.
  * `applicationinsights-web`também fornece métricas que rastreiam contagens de solicitação de servlet HTTP e tempos de resposta, mas requer registro manual do filtro servlet Application Insights em seu aplicativo.
  * `applicationinsights-core`dá-lhe apenas a API nua, por exemplo, se o seu aplicativo não for baseado em servlet.
  
* *Como fazer para atualizar o SDK para a versão mais recente?*
  * Se você está usando Gradle ou Maven...
    * Atualize seu arquivo de compilação para especificar a versão mais recente.
  * Se você está gerenciando dependências manualmente...
    * Baixe o [SDK do Application Insights para Java](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) mais recente e substitua os antigos. As alterações descritas nas [notas de versão do SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).

## <a name="add-an-applicationinsightsxml-file"></a>Adicione um arquivo *ApplicationInsights.xml*
Adicione *ApplicationInsights.xml* à pasta de recursos do seu projeto ou certifique-se de que ele seja adicionado ao caminho da classe de implantação do seu projeto. Copie o XML a seguir nele.

Substitua a chave de instrumentação pela que você tem do portal Azure.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">

   <!-- The key from the portal: -->
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>

   <!-- HTTP request component (not required for bare API) -->
   <TelemetryModules>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
   </TelemetryModules>

   <!-- Events correlation (not required for bare API) -->
   <!-- These initializers add context data to each event -->
   <TelemetryInitializers>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
   </TelemetryInitializers>

</ApplicationInsights>
```

Opcionalmente, o arquivo de configuração pode estar em qualquer local acessível ao seu aplicativo.  A propriedade `-Dapplicationinsights.configurationDirectory` do sistema especifica o diretório que contém *ApplicationInsights.xml*. Por exemplo, um arquivo de configuração localizado em `E:\myconfigs\appinsights\ApplicationInsights.xml` seria configurado com a propriedade `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"`.

* A chave de instrumentação é enviada junto com todos os itens de telemetria e orienta o Application Insights a exibi-los em seu recurso.
* O componente de solicitação HTTP é opcional. Ele envia automaticamente a telemetria sobre solicitações e tempos de resposta para o portal.
* A correlação de eventos é uma adição ao componente de solicitação HTTP. Ele atribui um identificador a cada solicitação recebida pelo servidor. Em seguida, adiciona esse identificador como propriedade a cada item de telemetria como a propriedade 'Operation.Id'. Ele permite que você correlacione a telemetria associada com cada solicitação, definindo um filtro na [pesquisa de diagnóstico][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Maneiras alternativas para definir a chave de instrumentação
O SDK do Application Insights procura a chave nesta ordem:

1. Propriedade do sistema: -DAPPINSIGHTS_INSTRUMENTATIONKEY=your_ikey
2. Variável de ambiente: APPINSIGHTS_INSTRUMENTATIONKEY
3. Arquivo de configuração: *ApplicationInsights.xml*

Você também pode [defini-lo no código](../../azure-monitor/app/api-custom-events-metrics.md#ikey):

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="add-agent"></a>Adicionar agente

[Instale o Agente Java](java-agent.md) para capturar chamadas HTTP de saída, consultas JDBC, registro de aplicativos e melhor nomeação de operação.

## <a name="run-your-application"></a>Execute seu aplicativo.
Execute-o no modo de depuração no computador de desenvolvimento ou publique em seu servidor.

## <a name="view-your-telemetry-in-application-insights"></a>Exibir sua telemetria no Application Insights
Retorne para seu recurso do Application Insights no [Portal do Microsoft Azure](https://portal.azure.com).

Dados de solicitações HTTP são exibidos na folha de visão geral. (Se não estiverem lá, aguarde alguns segundos e, em seguida, clique em Atualizar.)

![Captura de tela de visão geral de dados de exemplo](./media/java-get-started/overview-graphs.png)

[Saiba mais sobre métricas.][metrics]

Clique em qualquer gráfico para ver métricas agregadas mais detalhadas.

![Falhas do Application Insights com gráficos](./media/java-get-started/006-barcharts.png)

<!--
[TODO update image with 2.5.0 operation naming provided by agent]
-->

### <a name="instance-data"></a>Dados de instância
Clique em um tipo de solicitação específica para ver instâncias individuais.

![Perfurar em uma visão de amostra específica](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Análise: linguagem de consulta poderosa
À medida que acumular mais dados, você poderá executar consultas para agregar dados e localizar instâncias individuais.  [Análise](../../azure-monitor/app/analytics.md) é uma ferramenta poderosa para entender o desempenho e o uso e para fins de diagnóstico.

![Exemplo de Análise](./media/java-get-started/0025.png)

## <a name="install-your-app-on-the-server"></a>Instalar aplicativo no servidor
Agora, publique seu aplicativo no servidor, permita que as pessoas o usem e observe a telemetria mostrada no portal.

* Verifique se o firewall permite que seu aplicativo envie telemetria para estas portas:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Se o tráfego de saída precisar passar por um firewall, defina as propriedades do sistema `http.proxyHost` e `http.proxyPort`.

* Nos servidores Windows, instale:

  * [Microsoft Visual C++ redistribuível](https://www.microsoft.com/download/details.aspx?id=40784)

    (Esse componente habilita contadores de desempenho.)

## <a name="azure-app-service-config-spring-boot"></a>Configuração do serviço do aplicativo azure (bota de mola)

Os aplicativos Spring Boot em execução no Windows exigem configuração adicional para serem executados no Azure App Services. Modifique **web.config** e adicione a seguinte configuração:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified"/>
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\AzureWebAppExample-0.0.1-SNAPSHOT.jar&quot;">
        </httpPlatform>
    </system.webServer>
</configuration>
```

## <a name="exceptions-and-request-failures"></a>Falhas de solicitação e exceções
Exceções não tratadas e falhas de solicitação são coletadas automaticamente pelo filtro web Application Insights.

Para coletar dados sobre outras exceções, você pode [inserir chamadas para rastrearException() em seu código][apiexceptions].

## <a name="monitor-method-calls-and-external-dependencies"></a>Monitorar chamadas de método e dependências externas
[Instale o Agente Java](java-agent.md) para registrar métodos internos especificados e chamadas feitas por meio de JDBC, com dados de tempo.

E para nomeação automática de operação.

## <a name="w3c-distributed-tracing"></a>Rastreamento distribuído do W3C

O SDK do Java do Application Insights passou a dar suporte ao [rastreamento distribuído do W3C](https://w3c.github.io/trace-context/).

A configuração do SDK de entrada será mais bem explicada em nosso artigo sobre [correlação](correlation.md).

A configuração do SDK de saída é definida no arquivo [AI-Agent.xml](java-agent.md).

## <a name="performance-counters"></a>Contadores de desempenho
Abrir **investigação,** **métricas,** para ver uma variedade de contadores de desempenho.

![Captura de tela do painel de métricas com bytes privados de processo selecionados](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Personalizar a coleta do contador de desempenho
Para desativar a coleta do conjunto padrão de contadores de desempenho, adicione o seguinte código sob o nó raiz do arquivo *ApplicationInsights.xml:*

```XML
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>Coletar contadores de desempenho adicionais
Você pode especificar contadores de desempenho adicionais a serem coletados.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>Contadores JMX (expostos pela Máquina Virtual Java)

```XML
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName` – o nome exibido no portal do Application Insights.
* `objectName` – o nome do objeto JMX.
* `attribute` – o atributo do nome do objeto JMX a buscar
* `type`(opcional) - O tipo de atributo do objeto JMX:
  * Padrão: um tipo simples como “int” ou “long”.
  * `composite`: os dados do contador de desempenho estão no formato “Attribute.Data”
  * `tabular`: os dados do contador de desempenho estão no formato de uma linha de tabela

#### <a name="windows-performance-counters"></a>Contadores de desempenho do Windows
Cada [contador de desempenho do Windows](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) é membro de uma categoria (do mesmo modo que um campo é um membro de uma classe). Categorias podem ser globais, ou podem ter instâncias numeradas ou nomeadas.

```XML
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName - o nome exibido no portal do Application Insights.
* categoryName – a categoria de contador de desempenho (objeto de desempenho) a qual este contador de desempenho está associado
* counterName – o nome do contador de desempenho
* instanceName – o nome da instância da categoria do contador de desempenho ou uma cadeia de caracteres vazia (""), se a categoria contém uma única instância. Se categoryName é o processo, e o contador de desempenho que você gostaria de coletar faz parte do processo atual da JVM em que seu aplicativo está sendo executado, especifique `"__SELF__"`.

### <a name="unix-performance-counters"></a>Contadores de desempenho do Unix
* [Instale o collectd com o plug-in do Application Insights](java-collectd.md) para obter uma ampla variedade de dados de sistema e rede.

## <a name="get-user-and-session-data"></a>Obter dados de usuário e de sessão
OK, você está enviando a telemetria do seu servidor Web. Agora, para ver o panorama completo do seu aplicativo, você pode adicionar um monitoramento mais:

* [Adicione telemetria às suas páginas da Web][usage] para monitorar exibições de página e métricas de usuário.
* [Configure os testes da Web][availability] para certificar-se de manter seu aplicativo operante e responsivo.

## <a name="send-your-own-telemetry"></a>Enviar sua própria telemetria
Agora que você instalou o SDK, você pode usar a API para enviar sua próprias telemetrias.

* [Acompanhe eventos personalizados e métricas][api] para saber o que os usuários estão fazendo com seu aplicativo.
* [Pesquise eventos e logs][diagnostic] para ajudar a diagnosticar problemas.

## <a name="availability-web-tests"></a>Testes de disponibilidade na Web
O Application Insights pode testar seu site em intervalos regulares para verificar ele está operante e respondendo bem.

[Saiba mais sobre como configurar testes web de disponibilidade.][availability]

## <a name="questions-problems"></a>Perguntas? Problemas?
[Solucionar problemas de Java](java-troubleshoot.md)

## <a name="next-steps"></a>Próximas etapas
* [Monitorar chamadas de dependência](java-agent.md)
* [Monitorar os contadores de desempenho do Unix](java-collectd.md)
* Adicionar [monitoramento a suas páginas da Web](javascript.md) para monitorar tempos de carregamento de página, chamadas AJAX e exceções do navegador.
* Gravar [telemetria personalizada](../../azure-monitor/app/api-custom-events-metrics.md) para controlar o uso no navegador ou no servidor.
* Use [analytics](../../azure-monitor/app/analytics.md) para consultas poderosas sobre telemetria do seu aplicativo
* Para saber mais, visite [Azure para desenvolvedores Java](/java/azure).

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#trackexception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[usage]: javascript.md
